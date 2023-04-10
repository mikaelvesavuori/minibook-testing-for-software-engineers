---
description: >-
  Wrangling all of the test data is sometimes made more complicated than it is.
  Let's see how we can address this in a better way.
---

# Test data management

What would a test be without some input data and expected outputs? Not of much use, that's what!

We can divide test data into two very big overall categories:

* **Test data local to the test**, either _inlined_ with tests (such as `const input = 'Hello world!'`) or as _co-located files_ (the previous example, but in a separate file).
* **Test data remote to the test**, which means any test data that is separately stored, handled, and otherwise managed in a lifecycle that does not necessarily follow that of the code base.

To really mix up things as well we can start thinking of the **malleability of shared test data**. Does someone own/control the data? Does someone update, change, or otherwise manipulate the test data set? Does that carry implications for other users and/or systems—does it make other systems break in functionality and/or testing? Yep, a lot of questions come in when we allow mutating any shared test data. Suddenly we need a **test data management process**. To be frank, that's probably not a good sign. If we can solve the needs in easier ways, then we should.

Continuing, test data can be:

* **Static test data**, which implies there are files and/or data that have been created at some point and are left mostly untouched. This is probably the most common scenario.&#x20;
* **Dynamic test data**, which is generated programmatically for each test run according to some specification.

Based on my experience and what I can assume from my own interactions with others and the great internet, I can only assume that quite a few folks (and organizations) are relying on remote, static test data. Of these two, **you should make all efforts to make test data a local concern**.

Our baseline goals should be that test data is:

* Always trustworthy.
* Always present, that is, co-located in the code base and not in a remote source.
* Additional test data should only ever be needed in a minimum number of cases, if at all.
* If it _is_ shared data, the test data management process needs to be clearly documented and the process followed; It is also unacceptable that shared data should break anything at all, so such aspects need to be refactored away.

{% hint style="info" %}
For more, similar, advice I recommend reading Google's article [DevOps tech: Test data management](https://cloud.google.com/architecture/devops/devops-tech-test-data-management).
{% endhint %}

## What about duplication and/or sharing of test data?

Duplication of test data should be entirely acceptable if that's ever needed.

But, I would much rather, however, point to the logical follow-up question: "If you need to duplicate and/or share test data, are you testing the same system across multiple teams?". The answer should be a resounding "no", which should similarly close down the conversation as it doesn't make any sense.

If you are testing the same things across multiple teams, that should be a clear indication of leaky [bounded contexts](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/domain-analysis) and definitions of responsibility. As per [Domain Driven Design](https://ddd.mikaelvesavuori.se) and most reasoning today, you should never put testing someone else's system into your hands. If there is a clear division of responsibility—i.e. you cover your own systems—then there should be no (wide, at least) need for externalizing test data.

## Advice for test data management

### Creating readily available test data is part of the definition-of-done

As part of your way of working, Agile or otherwise, let test data creation be a clear part of the definition of done (DOD). If the DOD has this expressly stated and everyone understands how to create usable test data, then you are likely on a good way to not require any external test data at all.

{% hint style="info" %}
Of course, an approach such as test-driven development (TDD) will automatically fulfill this goal, and moving over to TDD could be another path to investigate.
{% endhint %}

### Collecting initial test data

To test something, not only do you need to know what it is that you test, you also need to have some expected input and output data. When you build it yourself (i.e. "first party" testing), then test data should be trivial to come up with because the thing that is tested **is the same system**.

My commentary here will instead, therefore, deal with handling test data from third parties. A very convenient way to collect data when, for example, integrating with a third-party service, is to **actually use the API** or service itself.

{% hint style="info" %}
Here are some examples of API documentation. They are all good but differ slightly in presentation and approach.

* [GitHub REST API](https://docs.github.com/en/rest)
* [Figma API](https://www.figma.com/developers/api)
* [Stripe API](https://stripe.com/docs/api)
* [Better APIs Workshop example](https://bump.sh/doc/better-apis-workshop)
{% endhint %}

Using API documentation, schemas, playgrounds, or other documents/tools you get a clear and functional idea of what needs to drive a given endpoint or feature, and what to expect of it.

{% hint style="success" %}
I've found this to be a good basis for setting up an [Insomnia](https://insomnia.rest) [Design Document](https://docs.insomnia.rest/insomnia/design-documents) (similar to [Postman](https://www.postman.com)'s [request collections](https://learning.postman.com/docs/sending-requests/intro-to-collections/)) with these integration points together with any notes, links, and actual, working examples I might need.

These design documents/request collections can then also be documented in `README` files or similar, and/or exported and kept with the source code.

Having done this, not only have you documented the usage of a system or API, you've enabled the team to have exactly the same insights as well!
{% endhint %}

The input and output data can then be stored as actual test data together with the source code to drive unit tests (and any other tests). I normally store the data as JSON files because that's what the input/output is nearly 100% of the time in a modern API. Obviously, if you have other needs, have it your way.

Doing this at the beginning of a project or at the start of integration work greatly increases the momentum, because the sometimes tedious work of correctly accessing the third party gets dealt with using minimal means ([cURL](https://curl.se), a REST client such as Insomnia, or whatever tool floats your boat) before committing to writing any code. So when you start writing the code for real, at least the payloads—and therefore some of the tests—will be clear _prior to there even being any code_ at all! Some call it magic, I call it the lazy man's logic.

So, is this request collection deserving to be dubbed a collection of "tests"? Well, somewhat, but not conclusively, no. First and foremost it's a convenience and developer tool for the extended lifecycle of an application. As a mere request, it's at best a very crude and primitive test. The true value of the design document/request collections is to be an easy-to-access way of troubleshooting and enabling the use of any relevant third parties _in an emergency or similar situations_. **Don't institutionalize the use of these request collections as any type of **_**real**_** tests if you can avoid it**.

### Colocate tests and code

The corollary from the "Test environments" page, as well as from the start of the page, is that tests and code need to be co-located to support an optimal workflow.

If you are currently relying on external test data, consider if it would be logically possible to take any needed data into your own code base and cut the ties with the external source.

### Prefer unit tests

This advice is really about finding the least hard way to do something. And...

Unit tests are the dumbest and most basic tests you can write. If you know that your functionality can be covered with these, then that's the ideal solution. Even if you _think you can't do it_ try again and see if you change your mind.

Unit tests also have the benefit of being clearer in terms of test data. You can usually easily have test data inlined in the tests, or as local files.

### Depend on as little data as possible

Continuing the crusade here. Now that you've removed external sources, you are using unit tests for all (or the vast majority) of your test cases, you can start simply relying on as little data as you can. Do you really need someone else to prepare the test data? Why? Is the system not deterministic, or is the team's understanding of it simply not correct?

This advice works even without any of those potentially dramatic changes. At the end of the day, dependence on _anything_ creates a chokepoint in your process. Always be careful about dependencies, new or existing. History should not be your precedent as you improve the testing conditions.

### Don't use sensitive/private/production data for testing

From a legal perspective, this is the only advice that you _absolutely have to comply with_.

Instead of using real data for your tests, opt for _fake_ or _synthetic_ data. This can be done as easily as:

* Using production data and cleaning it from any sensitive fields, for example by masking them or dropping them altogether. Do this once or if needed, create a script or program that can export clean data from a trusted resource.
* [Generating fake data with custom scripts](https://github.com/mikaelvesavuori/microservices-testing-workshop/blob/master/\_\_tests\_\_/system/utils.ts) or with an open-source tool like [Faker](https://fakerjs.dev).
* Using a commercial service like [Gretel](https://gretel.ai), [Hazy](https://hazy.com), or [Mostly.ai](https://mostly.ai).

{% hint style="success" %}
**Of all these three I highly recommend the second one—generating fake data**— as it is easiest, free, available off-the-shelf, and works perfectly with the overall approach around unit test-centricity.
{% endhint %}

The most important thing here is that more often than not, the _shape of the data_ is a lot more important than the exact contents. If you are dependent on certain contents that might imply a problematic API or interface. If it's simply that certain fields need to follow certain conventions, that part is easily fixed with any of the above approaches.

### Generate test data dummies

This is a direct follow-up on the previous point. Any actual production of fake or synthetic data should be done in a mechanical, automated, predictable way.

Using a tool like Faker, it's ideal to dynamically create test data for a test as you need it. This brings several benefits:

* No reason to save and check in (commit) test data
* Allows more confidently exercising the code as neither you nor the implementation can optimize for a specific dataset
* Lower logical complexity

The primary drawback is that if the volume is very big then doing this dynamically might be more taxing on the test hardware and may push up the test's final run time considerably.

Given that's not the case, the dynamic test option is excellent for configurable cases on a higher level, i.e. for example for integration tests.

{% hint style="info" %}
Related, but different, approaches using dynamic data are possible with [mutation tests](https://en.wikipedia.org/wiki/Mutation\_testing) and [fuzz testing](https://en.wikipedia.org/wiki/Fuzzing).
{% endhint %}

### Use QuickType to generate SDKs to validate against

Lastly, you can use tools like [QuickType](https://quicktype.io) to create models (code bindings/SDKs/packages) that you can directly validate inputs against. I've used this approach in [contract testing](https://martinfowler.com/bliki/ContractTest.html) scenarios and similar, and it is even the glue driving my [TripleCheck](https://github.com/mikaelvesavuori/triplecheck-cli) projects. Doing it this way you can effectively remove the conventional usage for test data as it's generated into a testable model.

This approach is a more particular technique that you might not need for the majority of cases, but keep it in mind!
