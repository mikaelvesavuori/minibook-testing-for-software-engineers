# Unit testing

{% hint style="info" %}
**Surface area**

Any individual (or combined, such as a higher-level function calling other) classes and/or functions

**Confidence level**

Very high at the logical level

**Granularity**

Can be any combination of assembled, local code

**Pros**

* Cheap
* Fast
* Easy to write
* Helps enforce good code and structure
* Provides very good logical proof of functionality
* In TDD or TDD-inspired approaches unit testing will be done prior to code meaning you always know what you are dealing with

**Cons**

* Covers only the application layer and not other layers
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Always.
{% endhint %}

The beauty of unit tests is that they are really malleable: We can make them wider or more narrow in scope as needed, and they are fast and "cheap" to run.

I am not making the claim that unit tests are the only good tests; I am stating that they are incredibly useful and great confidence providers in well-built systems. Kent Beck recently paraphrased Tim Ottinger on the qualities of good units tests, which are:

> * [Isolated](https://www.youtube.com/watch?v=HApI2cspQus) — Unit tests are completely isolated from each other, creating their own test fixtures from scratch each time. (Note that I’m not saying these are the only useful tests, just that if tests aren’t isolated you’re going to have a hard time making the case that they are “unit tests”.)
> * [Composable](https://www.youtube.com/watch?v=Wf3WXYaMt8E) — Follows from isolation.
> * [Deterministic](https://www.youtube.com/watch?v=PwWyp-wpFiw) — Should be. Code that uses a clock or random numbers should be passed those values by their unit tests.
> * [Specific](https://www.youtube.com/watch?v=8lTfrCtPPNE) — If a unit test fails, the code of interest should be short.
> * [Behavioral](https://www.youtube.com/watch?v=5LOdKDqdWYU) — If the behavior changes accidentally, a unit test should fail.
> * [Structure-insensitive](https://www.youtube.com/watch?v=bvRRbWbQwDU) — This can be a challenge for unit tests. Too much mocking, especially strict mocking, is a structure sensitivity nightmare.
> * [Fast](https://www.youtube.com/watch?v=L0dZ7MmW6xc) — Yep.
> * [Writable](https://www.youtube.com/watch?v=CAttTEUE9HM) — Good interface design makes writing unit tests easier. Alternatively, difficult-to-write unit tests are the canary in the bad interface coal mine.
> * [Readable](https://www.youtube.com/watch?v=bDaFPACTjj8) — It can be challenge to write readable unit tests, because you are seeing so little context compared to the whole system.
> * [Automated](https://www.youtube.com/watch?v=YQlmP08dj6g) — Yep.
> * [Predictive](https://www.youtube.com/watch?v=7o5qxxx7SmI) — Unit tests passing likely gives little confidence that the whole system is working. Unit tests failing should give great confidence that the whole system is **not** working.
> * [Inspiring](https://www.youtube.com/watch?v=2Q1O8XBVbZQ) — A frequently-run unit test suite gives great confidence the programming is progressing. Sometimes I run my unit tests 2 or 3 times, just because it feels good (and they’re wicked fast).
>
> — Kent Beck, [_Desirable Unit Tests: A Test Desiderata Perspective_](https://tidyfirst.substack.com/p/desirable-unit-tests)

## Considering the limits of unit tests

How about any backsides to these tests? As always, yes, there are backsides too. Perhaps the most pressing problems are related to:

* Unit tests may not be able to cover all branches or cases.
* The lack of verification of external components, such as databases.
* Writing tests for systems with unclear and/or poor system interactions and bounded contexts.

{% hint style="info" %}
With this said, I am a firm believer that sometimes people are just being either actively ignorant, not aware of modern software development practices (including staples like version control), or that these folks are more philosophers than pragmatic practitioners.
{% endhint %}

In reality, we can address, to a meaningful degree, most of the hard parts here, given you have some software engineering chops.

### Covering most cases

Modern test tools use some type of visual output that will give you a correct understanding of what lines/statements/branches of the code were running. Good code is written in a way in which it's possible to test it, i.e. the code is "dumb" and provides clear ways to interact with it and finishes with clear outputs.

With this said, indeed it's possible to get 100% test coverage without trying "all" cases. In all honesty, it would be impossible to cover "all" cases of anything other than trivial systems for combinatorial reasons: Given a single boolean condition, you would need two tests at a minimum. Given more complex inputs will significantly raise that test count. What you want to ensure is that you cover all code paths in the _expected_ "success" and "failure" modes. And as any good software engineer knows, you look for patterns and generalizations rather than matching on exact inputs.

For highly complex cases you can potentially make things easier by introducing early-stage validations and other logic to look for anything out of the ordinary, such as too-long strings, use of unexpected characters, too many or too few items in an array, etc. Suddenly you can now build (and test) such checks independently from the higher-level use cases you are better off concentrating on.&#x20;

**Write "simple" and testable code**—it will serve you well.

### Verifying the "big picture"

It is true that we don't want to "test" infrastructure or external components in unit tests. This is not an actual problem, per se, but a necessary constraint. We can, and should, certainly use other types of testing to verify this functionality. In practice, however, this isn't typically a problem: Mocks or similar will be fine enough, and if you can arrive at a solution in which you can use minimalistic mocks (remember the SOLID principles!) then you know that _within the boundary of this system,_ everything is working.

There's also a concept being used called _component testing_. I'll only speak to my own best professional opinion but I am also not hearing a lot of discussion around component testing; ironically it's mostly testers and QA people who have raised this notion in my own vicinity.

The point of a component test is to strike a middle ground between unit tests and the more cumbersome integration tests, which require some actual infrastructure to be up and running. It's a great idea, but even better is that you can (as I have done in this book) simply erase the component test from your mind, as nothing says that unit test can only test individual, "small" functions—in fact, they can absolutely test entire use cases, effectively testing from a functionally higher level.

**Use layers in your source code** (following for example [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)) to make it easy to run tests on both the "higher" and "wider" level (entire functional use cases) as well as on the "lower" and "narrower" level of individual functions (for example ensuring that correct errors are thrown on invalid inputs).

### Testing with unclear boundaries

This of course brings us to building or evolving toward systems with clear interactions and bounded systems. This is a necessary piece of our work, because frankly, why should you test something if you don't know it's yours to test? For some reason, a lot of people accept this as some kind of inevitable fact, but it really isn't. You need to be crystal clear about what goes into your box and what you need to trust, that comes from somebody else. At a minimum, they should provide artifacts like API specifications, service level objectives, support, or whatever it takes for you to cut your ties on the implementation level. You wouldn't test an AWS API, so why would you test another team's things? It's wise to remember that many mistake testing for observability, a very different property and set of tools that will better assist you in understanding if something went _wrong_ rather than _prove that you are doing it right_.

**Draw a hard line between "what you own" and what you don't and consider increased observability efforts for third-party failures** rather than being delusional about being able to "test away" things that don't work in systems you don't manage or have first-hand access to.

{% hint style="info" %}
While there are more articles on this, a quick glance at [this section](https://en.wikipedia.org/wiki/Unit\_testing#Limitations\_and\_disadvantages) on Wikipedia should give you a hint that there are further limitations as well.
{% endhint %}

## Examples

### Positive tests

Sometimes a unit test can be made incredibly short and succinct. This is a test from [Figmagic](https://github.com/mikaelvesavuori/figmagic), written in Jest.

```typescript
// Imports etc. above
test('It should set a negative "quantity" to 0 and return a value based on the two defaults for "quantity" and "scale"', () => {
  expect(roundColorValue(-4.2)).toBe(0);
});
```

If you can, keep your tests that short! No reason to write messy tests.

But, yeah, granted, the function itself was really terse and exact. How about we look at something that's a bit messy, just for kicks? Here's one of the tests for reserving a time slot in [my DDD example project](https://github.com/mikaelvesavuori/get-a-room-ddd-example). This is represented by testing at the outer-most layer, the use case. Note: This uses AVA.

{% code lineNumbers="true" %}
```typescript
import test from 'ava';

import { ReserveSlotUseCase } from '../../../src/application/usecases/ReserveSlotUseCase';
import { setupDependencies } from '../../../src/infrastructure/utils/setupDependencies';

import metadataConfig from '../../../testdata/metadataConfig.json';
import { correctSlots } from '../../../testdata/TestDatabase';

import { setEnv, cleanEnv } from '../../../testUtils';

setEnv();
const dependencies = setupDependencies(metadataConfig as any, true, correctSlots);

test.beforeEach(() => setEnv());
test.afterEach(() => cleanEnv());

/**
 * POSITIVE TESTS
 */

test('It should reserve a slot', async (t) => {
  // Arrange
  process.env.SECURITY_API_ENDPOINT_GENERATE =
    'https://RANDOM.execute-api.eu-north-1.amazonaws.com/shared/generateCode';
  const expected = 'RESERVED';
  const slotId = '0128c8d4-cb2f-460d-8f49-c5587ebbf83a';

  const slot = correctSlots.filter((slot: any) => slot.slotId === slotId)[0];

  // Act
  const response = await ReserveSlotUseCase(dependencies, { ...slot });
  t.is(response.code.length, 8); // Assert!

  const slots = await dependencies.repository.loadSlots();
  const updatedSlot = slots.filter((slot: any) => slot.slotId === slotId)[0];
  const { slotStatus } = updatedSlot;

  // Assert
  t.is(slotStatus, expected);
});
```
{% endcode %}

It's not my most beautiful test, which is part of the reason I demonstrate it. I wouldn't call it bad, but we absolutely see that sometimes things do get more complicated. A benefit here is, as mentioned several times before, that we exercise _a lot_ of our code here, so the test has a very high value for our confidence.

I'm doing two assertions, despite common wisdom. This is because I don't otherwise ensure we have a correct-looking response code, thus packaging that check in here.

There is also dependency injection happening here, in order to remove the real database. Also, the `correctSlots` is a set of test data.

All of this is to ensure that the logical functionality remains stable, rather than testing databases or other infra.

### Negative tests

This is cleaner and focuses on ensuring we can throw the right type of error if we have a slot with an incorrect status.

{% code lineNumbers="true" %}
```typescript
/**
 * NEGATIVE TESTS
 */

test(
  'It should throw a ReservationConditionsNotMetError if attempting to reserve a slot with non-OPEN status',
  async (t) => {
    // Arrange
    const expected = '8613678f-2bbe-4fdd-93b8-97f65674c94f';

    // Act
    const slot = correctSlots.filter((slot: any) => slot.slotId === expected)[0];

    const error = await t.throwsAsync(async () => {
      await ReserveSlotUseCase(dependencies, slot);
    });

    // Assert
    t.is(error?.name, 'ReservationConditionsNotMetError');
  }
);
```
{% endcode %}

Here our use case structure and arrange-act-assert composition shines bright.

## Mocking API dependencies (HTTP calls) with MSW

It tends to be messy when we have to deal with HTTP calls. Given these are actual network requests, it's not always quite as easy as just writing a mock object to deal with this (indeed, it could be, but hang on). [MSW](https://mswjs.io) offers a good way to handle these scenarios.

Again, from the [DDD example project](https://github.com/mikaelvesavuori/get-a-room-ddd-example).

```typescript
import { PathParams, rest, RestRequest } from 'msw';

const SECURITY_API_ENDPOINT_GENERATE =
  'https://RANDOM.execute-api.eu-north-1.amazonaws.com/shared/generateCode';

const MsgSetMswInterceptedDataFromApi = (url: string) =>
  `[MSW] - Mocking intercepted fetch request data from API: ${url}`;

const logInterceptedRequest = (req: RestRequest<any, PathParams>) =>
  console.log(MsgSetMswInterceptedDataFromApi(req.url.href));

export const handlers = [
  rest.post(`${SECURITY_API_ENDPOINT_GENERATE}`, (req, res, ctx) => {
    logInterceptedRequest(req);
    return res(ctx.status(200), ctx.text('1234'));
  }),
  rest.post(`${SECURITY_API_ENDPOINT_GENERATE}-fail`, (req, res, ctx) => {
    logInterceptedRequest(req);
    return res(ctx.status(400), ctx.json({}));
  })
];
```

Above, you see how we intercept the POST request to both one of our API endpoints, as well as a faked endpoint ending with `-fail`. For the first one, we respond with a status 200 and some garbage text, and for the other one, we send a status 400 (user issue) and an empty object.

## In closing

Unit tests are super versatile and once you get the hang of dealing with some of the "boundary" issues, like HTTP calls and mocks, you should be on a good track in your testing prowess.&#x20;
