# Contract testing

{% hint style="info" %}
**Surface area**

The schema or a representation of a schema for one or more external systems

**Confidence level**

Variable

**Granularity**

Low, you know it _should_ work

**Pros**

* Significantly less risky than API/integration testing
* No side effects
* Fast

**Cons**

* Not well-understood
* Not standardized or strong conventions in place
* May add extra work
* If the contract is separately handled from the service's schema and/or implementation, there is a non-zero risk of these being different and thus leading to wrong results and/or functionality
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Whenever there are significant external systems with considerable breaking changes and update cycles you cannot trust.&#x20;
{% endhint %}

As we've seen, in a distributed technical landscape the piece that connects all of the services is the contract: The thing that expresses what a system expects and provides. Because of contract decoupling, we are free to independently evolve services and their APIs. How do you _test a contract_?

First, let's clear out what I mean by these different terms.

An **API specification**, in general, is an authoritative document that is both human-readable and machine-readable. We might for example use [OpenAPI](https://swagger.io/specification/) or [AsyncAPI](https://www.asyncapi.com) formats to document our API.

The actual API document (JSON or YAML file) that you write, we call your **API definition**. The definition acts as a **schema** and defines inputs and outputs, explaining how the API works and generally answering "what it can do".

{% hint style="info" %}
I've borrowed this definition from [_The Layers of the API Specifications, Definitions, and Schema Onion_](https://apievangelist.com/2020/04/18/the-layers-of-the-api-specifications-definitions-and-schema-onion/).
{% endhint %}

Now, when it's time to test against something, yes, you _can_ test against the schema. But it won't cover all cases, and given the abstract nature of the definition/schema, it's more practical to think of it as a description rather than a technical artifact than is useful for testing.

> Schemas are abstract, contracts are concrete.
>
> — Matt Fellows, [_Schemas are not contracts_](https://pactflow.io/blog/schemas-are-not-contracts/)

See his article for much more on this problem; in short, it's not quite a contract.

However, even just a collection of API requests/responses combined with the definition would be a starting point for a **contract**. A contract "[defines how two systems are able to communicate by agreeing on what interactions (conversations) can be sent between them and providing concrete examples to test the agreed behaviour](https://pactflow.io/blog/schemas-are-not-contracts/)". The bi-directional part, that both consumer (user) and provider (those who build the system), have an arrangement on the expectations towards each other is the semi-revolutionary feature here.

## Examples

Let's say we have an API—let's call it `Greeter`—that returns `Hello world!` as its only output. We might _know_ this fact, but how can we test this?

Our schema will be of typical OpenAPI 3 flavor:

```yaml
openapi: 3.0.3
info:
  title: Greeter
  description: Greeter service
  version: 1.0.0
paths:
  /hello:
    post:
      summary: Greet person
      description: Greet person
      operationId: greet
      responses:
        '200':
          description: Successful operation
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greet'
components:
  schemas:
    Greet:
      type: string
      description: Returns 'Hello World {name}!'
```

In human-speak: Users can make a `GET` request to the path `/hello` and we will return `Hello World!` to them.

### Using Ajv

For a really barebones approach, we could use [Ajv](https://ajv.js.org) for ensuring that crude expectations on a schema work. Below, we are passing in an object with an `id` key. This will work.

{% code title="Ajv validation demo" %}
```javascript
import Ajv from 'ajv';
const ajv = new Ajv();

// JSON Schema style schema we will test against
const schema = {
  type: 'object',
  description: 'Get user name by ID.',
  properties: {
    id: {
      type: 'string'
    }
  },
  required: ['id'],
  additionalProperties: false
};

// Our test input
const data = {
  name: 'Zelda'
};

// Test
const validate = ajv.compile(schema);
if (validate(data)) console.log('Success');
else console.log(validate.errors);
```
{% endcode %}

A solution for running this across teams could be to store schemas in some central location, pull these in CI, and run them against our test inputs (_expectations_). Obviously, here we only get a basic mechanism to compare payloads.

For the reasons mentioned above, and while this is a crude model (and yes, it works even across teams), it's not very sophisticated and we have to hand-crank pretty much everything.

### Pact

The big dog in contract testing is [Pact](https://pact.io), a piece of open-source software available for many languages and it also has a paid, managed version.

Pact also brings in the idea of a **broker**, a central location to store contracts, which starts to solve the problems around how we get to share expectations and contracts in the first place.

{% hint style="info" %}
If not for anything else, then at least take some time to [read their good documentation](https://docs.pact.io), which also sells the good points about contract testing.
{% endhint %}

The following is from the documentation for [Pact JS](https://docs.pact.io/implementation\_guides/javascript/readme). This uses Mocha for the test, and should be able to be replaced with Jest or something else if you want.

{% code title="Pact JS demo" %}
```typescript
import { PactV3, MatchersV3 } from '@pact-foundation/pact';

// Create a 'pact' between the two applications in the integration we are testing
const provider = new PactV3({
  dir: path.resolve(process.cwd(), 'pacts'),
  consumer: 'MyConsumer',
  provider: 'MyProvider',
});

// API Client that will fetch dogs from the Dog API
// This is the target of our Pact test
public getMeDogs = (from: string): AxiosPromise => {
  return axios.request({
    baseURL: this.url,
    params: { from },
    headers: { Accept: 'application/json' },
    method: 'GET',
    url: '/dogs',
  });
};

const dogExample = { dog: 1 };
const EXPECTED_BODY = MatchersV3.eachLike(dogExample);

describe('GET /dogs', () => {
  it('returns an HTTP 200 and a list of docs', () => {
    // Arrange: Setup our expected interactions
    //
    // We use Pact to mock out the backend API
    provider
      .given('I have a list of dogs')
      .uponReceiving('a request for all dogs with the builder pattern')
      .withRequest({
        method: 'GET',
        path: '/dogs',
        query: { from: 'today' },
        headers: { Accept: 'application/json' },
      })
      .willRespondWith({
        status: 200,
        headers: { 'Content-Type': 'application/json' },
        body: EXPECTED_BODY,
      });

    return provider.executeTest((mockserver) => {
      // Act: test our API client behaves correctly
      //
      // Note we configure the DogService API client dynamically to 
      // point to the mock service Pact created for us, instead of 
      // the real one
      dogService = new DogService(mockserver.url);
      const response = await dogService.getMeDogs('today')

      // Assert: check the result
      expect(response.data[0]).to.deep.eq(dogExample);
    });
  });
});
```
{% endcode %}

You'll immediately note that we are back in regular "programmatic testing land" and that we have a mock concept in play. One of the reasons I've never grown fond of Pact is exactly because of the mock-heavy behavior—it's always seemed oddly heavy-handed to me.

### TripleCheck

The approach to contract testing I built a few years ago came from my frustrations with Pact; I call it [TripleCheck](https://github.com/mikaelvesavuori/triplecheck-cli). Like Pact, it also provides a [broker](https://github.com/mikaelvesavuori/triplecheck-broker) to store and retrieve our shared files.

This elaborates on the Ajv approach we looked at before.

Using the CLI to run tests, you can configure it to fetch all contracts and tests from the broker, get the latest distributed state, or decide to run completely off-the-grid from your local machine if you want.

The contracts are stored in an array where we can keep any number of them, and this is loaded into memory and tested when we run TripleCheck. Here we have the `greeter` object with a `1.0.0` specifier and an object with the `name` key: Our actual contract. TripleCheck uses [QuickType](https://quicktype.io) to infer this to be a required string input.

{% code title="TripleCheck demo contract" %}
```json
[
  {
    "greeter": {
      "1.0.0": {
        "name": "Somebody"
      }
    }
  }
]
```
{% endcode %}

The test that goes with the contract is pretty similar. Here we say we have a test called `Greet person` and the input is similar to that in the contract, so we already know this will work.

{% code title="TripleCheck demo test" %}
```json
[
  {
    "greeter": {
      "1.0.0": [
        {
          "Greet person": {
            "name": "Zelda"
          }
        }
      ]
    }
  }
]
```
{% endcode %}

The drawbacks here are very similar to those with Ajv, and it's worth knowing that complex behaviors are logically not supported.

## In closing

Contract testing is one of the more exciting ideas in testing right now, but it carries a complexity (infrastructural, cognitive, competence-wise) that is not worth taking unless you feel you've evolved to a position where your team can truly reap all the benefits.

Similar to when preparing for integration testing, there are auxiliary benefits you get by doing some of the boring preparatory work: Better documentation (and providing schemas to consumers), drawing better lines between "my system" and "other systems", and pushing towards more unit tests. Do look at [Better APIs](https://betterapis.mikaelvesavuori.se/stability/lifecycle-management-and-roadmap) for some other recommendations to drive API stability—despite contract testing being such a compelling notion, you shouldn't have to _only_ resort to contract testing because other systems break for you. Evolve into this if needed, but start with the homework!
