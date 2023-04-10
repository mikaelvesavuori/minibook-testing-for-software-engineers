# Load testing

{% hint style="info" %}
**Surface area**

Facade of an actual, deployed service or API

**Confidence level**

Medium, given a more "fuzzy" test this will be somewhat more confidence-building than a straight API test

**Granularity**

Low, you know it works and does what should do for you as a consumer

**Pros**

* Good tests will give you confidence that the system (and any behind it) can stand up to more traffic; you can also start doing more fuzzy testing to ensure problematic inputs work as expected

**Cons**

* The intensity of the load test means that you have to be even more careful that there are no adverse side effects created as a result
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Typically when you want to pressure test an application. If you cannot confidently run an API test for your service, then you should not even consider this class of test.
{% endhint %}

The overall category of "load testing" comes in many sizes but generally, the same taste, as evidenced by load testing tool [k6](https://k6.io)'s introduction to various test types:

> **Smoke Test**'s role is to verify that your system can handle minimal load, without any problems.
>
> **Load Test** is primarily concerned with assessing the performance of your system in terms of concurrent users or requests per second.
>
> **Stress Test** and **Spike testing** are concerned with assessing the limits of your system and stability under extreme conditions.
>
> **Soak Test** tells you about reliability and performance of your system over an extended period of time.
>
> **The important thing to understand is that each test can be performed with the same test script.** You can write one script and perform all the above tests with it. The only thing that changes is the test configuration, the logic stays the same.
>
> Different test types will teach you different things about your system and give you the insight needed to understand or optimize performance.
>
> — [k6](https://k6.io/docs/test-types/introduction/)

For the purposes of this book, load tests follow naturally from where smoke and synthetic tests left off, with us this time cranking up the volume of traffic and generally moving to more potent tooling.

{% hint style="info" %}
For Node.js, some of the more popular open-source tooling in this area—besides k6—are [Artillery](https://www.artillery.io) and [Gatling](https://gatling.io/open-source/).
{% endhint %}

A k6 script for an example made for the [Better APIs book and workshop](https://github.com/mikaelvesavuori/better-apis-workshop/blob/main/tests/load/k6.js) looks like below. Here we are attempting to get random variants of users and API versions and see that they will all work without throwing weird errors at us. The volume of traffic will be 10 virtual users looping on the same endpoint for 5 seconds.

{% code lineNumbers="true" %}
```javascript
import http from 'k6/http';
import { sleep } from 'k6';

// Setup
const endpoint = 'https://RANDOM.execute-api.REGION.amazonaws.com/shared/fakeUser'; // TODO: EDIT THIS TO YOUR ENDPOINT
const randomUser = getRandomUser();
const randomVersion = getRandomVersion();

/**
 * @description Helper to get one of the users.
 */
export function getRandomUser() {
  const getRandomInt = (min, max) => {
    min = Math.ceil(min);
    max = Math.floor(max);
    return Math.floor(Math.random() * (max - min) + min); //The maximum is exclusive and the minimum is inclusive
  };

  const users = [
    'erroruser@company.com',
    'legacyuser@company.com',
    'standarduser@company.com',
    'betauser@company.com',
    'qauser@company.com',
    'devuser@company.com',
    'devnewfeatureuser@company.com'
  ];

  return getRandomInt(0, users.length - 1);
}

/**
 * @description Helper to get a random version (or none)
 */
export function getRandomVersion() {
  const random = Math.round(Math.random() * 100);
  if (random <= 50) return 1;
  if (random > 50 && random <= 100) return 2;
}

/**
 * K6 configuration
 */

export const options = {
  vus: 10,
  duration: '5s'
};

const params = {
  headers: {
    Authorization: randomUser,
    'X-Client-Version': randomVersion
  }
};

export default function () {
  http.get(endpoint, params);
  sleep(1);
}
```
{% endcode %}

{% hint style="success" %}
**Could you do all of the smoke testing and API testing with k6?**

Yes, that could absolutely work, especially since they provide competent tools for setting thresholds and such for results.

However, I like to use the ideal, but most minimal, tool needed to do the job so that's why I show you examples in line with that belief.
{% endhint %}

The tiny load provided here fulfills mostly a demonstrational purpose. For a real load test, you might instead consider using a [scenario](https://k6.io/docs/using-k6/scenarios/) to add an increasing amount of load for a few minutes.

Tools like these are brilliant as they are easy to use and script and work well in CI as well.

{% hint style="info" %}
**Limits on network load**

Every machine has natural restrictions on the amount of network load they can generate. For _very high_ loads you may need to use a distributed solution for load generation. Some examples include:

* Running a fan-out of load generation on Lambda;
* Running tests from a high-powered virtual machine (since they typically get higher network speeds as well); or
* Using the SaaS versions of k6 or Artillery or similar.
{% endhint %}

As always, know _why_ you are conducting a test. What load limits do you have, or what hypothesis are you having around this? Also, keep in mind any elasticity you might have in your infrastructural setup; for example, it seems relatively awkward to test on a serverless compute platform that has automatic scaling, unless you have a clear hypothesis that the platform can't handle _spikes_, for example.&#x20;
