# API (integration) testing

{% hint style="info" %}
**Surface area**

Facade of an actual, deployed service or API (your service or some external service)

**Confidence level**

Medium; good enough if you trust there are no adverse side effects or similar

**Granularity**

Low, you know it works and does what should do for you as a consumer

**Pros**

* Probably the most intuitive and easy way to actually test something

**Cons**

* Side effects from using an actual system
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Your services: Any time you are building something that provides an API, then consider a low degree of these tests to verify complete upfront functionality.

External services: Always mock these, if for no other reason than because your unit tests will be able to do any outside calls without actually creating side effects.
{% endhint %}

Let it be known that the classic notion of integration testing—testing several software components at the same time—is practically useless. That could just be covered by a wider unit test.

{% hint style="info" %}
A related notion is the one of [sociable vs solitary tests](https://dev.to/dylanwatsonsoftware/socialise-your-unit-tests-5da0).
{% endhint %}

For the intents and purposes of this chapter, we will think of integration testing and API testing as essentially the same: We test something "from the outside", whether that's an HTTP API or something else matters less.

**Because we are going to use an actual system we don't really have to do more than simply use it!** It wouldn't _not_ be testing if you'd use Postman or Insomnia on every deployment to call an API with some prepackaged payloads, however, we'd of course not have it automated either. So let's discard that idea even if it's valid to some extent in factual terms.

## Running tool-centric tests

What you can do, however, with Postman and Insomnia is to use their CLI counterparts to run the tests during CI:

* [Postman, using `newman`](https://learning.postman.com/docs/collections/using-newman-cli/continuous-integration/)
* [Insomnia, using `inso`](https://docs.insomnia.rest/inso-cli/cli-command-reference/inso-run-test)

I don't have any deeper experience with these, but it's definitely possible and makes sense if you already use such API clients to store payloads.

Moving on to our DIY solution...

## Writing our own integration testing tool

To do a bit of lightweight integration testing we need two things:

* Some kind of evaluator engine.
* A set of assertions to test.

For the evaluator, we'll make life a little easier by using [Ajv](https://ajv.js.org) to perform JSON validation on our behalf, which could otherwise easily escalate into a major pain. While `fetch` is nowadays native in Node 18 and upwards, for compatibility reasons we will also pull in [node-fetch](https://www.npmjs.com/package/node-fetch) to handle requests.

{% hint style="danger" %}
There are breaking changes between versions 2 and 3 of `node-fetch`. If you are using Webpack to bundle your application, consider using the most recent 2-series version of `node-fetch` or you may face bundling errors.
{% endhint %}

While this isn't strictly zero-dependency, it's lightweight enough and allows us the required flexibility to test _actual_ APIs with payloads or assertions that we define without surrendering to specific testing frameworks.

Let's browse the code.

{% code title="integration-tests.ts" overflow="wrap" lineNumbers="true" %}
```typescript
// import fetch from 'node-fetch'; ---> Only needed when using less than Node 18
import Ajv from 'ajv';

import { assertions } from './assertions';

const INTEGRATION_ENDPOINT = 'https://my-web-api.acmecorp.xyz';
const AUTH_TOKEN = 'something-here';

async function runIntegrationTests() {
  if (!INTEGRATION_ENDPOINT) throw new Error('Missing INTEGRATION_ENDPOINT!');
  let testsFailed = false;

  const tests = assertions.map(async (assertion: any) => {
    return new Promise(async (resolve, reject) => {
      const { name, payload, schema, expected } = assertion;
      const { method, path, headers, body, urlParams } = payload;

      // Use auth header if needed
      headers.Authorization = AUTH_TOKEN;

      console.log(`Running integration test: "${name}"`);

      const response = await fetchData(
        `${INTEGRATION_ENDPOINT}/${path}`,
        headers,
        method,
        body,
        urlParams
      );
      if (!response) throw new Error('❌ No response!');

      /**
       * If there is an Ajv matching schema use that to check,
       * else use an exact comparison to check.
       */
      const isMatch = schema
        ? test(schema, response)
        : JSON.stringify(response) === JSON.stringify(expected);
      if (isMatch) resolve(true);
      else {
        testsFailed = true;
        reject({ name, response });
      }
    });
  });

  Promise.all(tests)
    .catch((error) => error)
    .then((result) => {
      if (testsFailed) {
        console.log(
          `❌ Failed integration test: "${result.name}" --> ${JSON.stringify(result.response)}`
        );
        process.exit(1);
      } else {
        console.log('✅ Passed all integration tests');
      }
    });
}

/**
 * @description Wrapper for fetching data.
 */
async function fetchData(
  url: string,
  headers: Record<string, any>,
  method: 'POST' | 'PATCH' | 'GET',
  body: any,
  urlParams: Record<string, any>
): Promise<any> {
  /**
   * If we have `urlParams` (which we can infer meaning that it's a GET case), then
   * manually spread these (known) properties first into a full URL.
   *
   * Else just use it as-is.
   */
  const fetchUrl = urlParams
    ? `${url}${getParamsString(urlParams)}`
    : url;

  const response = await fetch(fetchUrl, {
    headers,
    body: body ? JSON.stringify(body) : undefined,
    method
  });

  // If this is OK and status 204 ("No content") then we can safely return
  if (response.ok && response.status === 204) return 'OK';

  const text = await response.text();

  // Return text or JSON depending on what it actually was
  try {
    const data = JSON.parse(text);
    return data;
  } catch (error) {
    return text;
  }
}

const escapeString = (value: any) => {
  if (typeof value === 'string') return value;
  return JSON.stringify(value).replace(/\s/g, '%20').replace(/"/gi, '\\"');
};

const getParamsString = (urlParams: Record<string, any>) =>
  Object.entries(urlParams).reduce(
    (previousValue: [string, any], currentValue: any[], index: number): any => {
      let paramValue = index === 1 ? `?` : `${previousValue}&`;

      // On the first run this will include the "zeroth" value
      if (index === 1) {
        const [key, value] = previousValue;
        paramValue += `${key}=${escapeString(value)}&`;
      }

      const [key, value] = currentValue;
      paramValue += `${key}=${escapeString(escapeString(value))}`;

      return paramValue;
    }
  );

/**
 * @description Run a test by validating a schema with Ajv.
 */
function test(schema: any, data: any): boolean {
  const isArray = Array.isArray(data);
  if (isArray) data = data[0]; // Use the first item in an array if this is one

  const ajv = new Ajv();
  const validate = ajv.compile(schema);
  const isValid = validate(data);

  return isValid;
}

runIntegrationTests();
```
{% endcode %}

{% hint style="info" %}
[Feel free to copy the code, it's open source on my GitHub Gist.](https://gist.github.com/mikaelvesavuori/9abbb7970aa6720fd5aab37153d2a63b)
{% endhint %}

The critical parts are:

* `runIntegrationTests()` which orchestrates the overall functionality.
* `fetchData()` does just that.
* `test()` uses `ajv` to compile and validate the provided expected schema with what we got

Note that in the above code we also check if the response is simply "OK" in which case it is accepted as a match (for cases in which you may not actually receive any content, such as with [status code 204](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/204)).

The assertions are in a custom, though fairly flexible, format in which we can set our test names and payloads for any HTTP method.

{% code title="assertions.ts" lineNumbers="true" %}
```typescript
export const assertions = [
  {
    name: 'It should DO SOMETHING',
    payload: {
      method: 'POST',
      path: 'DoSomething',
      headers: {
        'X-Client-Version': 1
      },
      body: {
        userName: 'Sam Person',
        actions: [
          {
            Id: '2n022yd',
            ActionType: 'CONFIRMED'
          }
        ]
      }
    },
    schema: {
      type: 'object',
      properties: {
        systemId: { type: 'string' }
      },
      required: ['systemId'],
      additionalProperties: false
    }
  },
  {
    name: 'It should UPDATE SOMETHING',
    payload: {
      method: 'PATCH',
      path: 'UpdateSomething',
      body: {
        id: 'abc123',
        newValue: 'qwerty',
      }
    },
    expected: 'OK'
  },
  {
    name: 'It should GET SOMETHING',
    payload: {
      method: 'GET',
      path: 'GetSomething',
      urlParams: {
        systemId: 'something',
        user: 'something'
      }
    },
    schema: {
      type: 'object',
      properties: {
        id: { type: 'string' },
        version: { type: 'number' },
        hasDoneSomething: { type: 'boolean' }
      },
      required: ['id', 'version', 'hasDoneSomething'],
      additionalProperties: false
    }
  }
];
```
{% endcode %}

Using a `body` means we pass that actual body in a POST request, while `urlParams` are used if we need parameters passed in the URL rather than as a request body.

Lastly, the `schema` is your everyday JSON Schema to validate with `ajv`.

## Are you integration testing someone else's thing?

Doing API (integration) testing in CI of _other's_ services does not make logical sense, as this could lead to cases in which your deployment failed because of a failure _elsewhere_. And if you are thinking that "well, I mean if the _other_ thing ain't working then there's no real use for me to deploy, right?" then the answer is clear: That's plain mixing it up in the wrong way.

Remember that a test is always of _some unit, performing something at that specific moment_. It is not a guarantee for correct functionality at any later point, and especially not if other factors are more fluid, such as completely external dependencies and APIs, and that includes any non-application layer (i.e. network, etc.).

## In closing

Hopefully, this demystifies integration/API testing a bit for you, and that you see why I argue so much more passionately for unit tests.
