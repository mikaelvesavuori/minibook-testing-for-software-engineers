---
description: Some advice for the long road ahead.
---

# Writing good tests

<figure><img src="../.gitbook/assets/typewriter.jpg" alt=""><figcaption><p>Good tests, like good tools, aren't magic; they are well-composed and well-designed.</p></figcaption></figure>

> People love debating what percentage of which type of tests to write, but it's a distraction. **Nearly zero teams write expressive tests that establish clear boundaries, run quickly & reliably, and only fail for useful reasons. Focus on that instead**.
>
> — [Justin Searls](https://twitter.com/searls/status/1393385209089990659?ref_src=twsrc%5Etfw%7Ctwcamp%5Etweetembed%7Ctwterm%5E1393385209089990659%7Ctwgr%5Ea83eede86e611eda1bb154efc1b159d956b82f59%7Ctwcon%5Es1_&ref_url=https%3A%2F%2Fmartinfowler.com%2Farticles%2F2021-test-shapes.html)

Good tests and good code share a lot of similarities. Well, no surprise given that they both _are code_! If you're a half-decent developer this page should help fill in some of the gaps and questions you might have but take note that if you are already adept at writing semantic and clean code, then that should carry over really well to your tests too.

## The FIRST principles of testing

One of the classic books on software engineering is Robert C. Martin's book [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882). A well-known mnemonic, FIRST, came from the book which holds that a test should abide by the following principles:

1. **Fast.**
2. **Independent.**
3. **Repeatable.**
4. **Single assert per test.**
5. **Thorough.** Cover both positive and negative flows. There should be no unhandled exceptions. All failure conditions should be known and tested.

{% hint style="info" %}
See more related advice on TDD Manifesto's [_A Clean Test_](https://tddmanifesto.com/a-clean-test/)_._
{% endhint %}

This is pure rockstar magic and captures the essentials of good tests. If you closed the tab here and now, then already those principles should cover quite a lot. Crystal clear as they might seem, don't be surprised if you see tests that are far removed from this advice. Like chess, it's easy to learn but hard to master.

Good tests come easily given you already write good code. Sometimes, of course, some things are less easy to test, but don't accept brittleness. Even an ugly test, if needed, _should_ _always_ _work as expected_.

Programming in today's environment is less restricted by technology than by our cognitive and communicative abilities. Anything we cannot speak of in clear terms will always end up in sub-standard implementation (code and tests).

The one principle I'd argue that you can be more flexible with is the "single assert" principle, as it's not impossible you will want to assign several minor assertions as a single logical assertion. No one's going to arrest you, and just make sure you don't mess about with that everywhere but use it tactically.

## Use the Arrange Act Assert (3 A's) format

The closest thing we have to a standard in (unit) testing is the [arrange-act-assert pattern](https://robertmarshall.dev/blog/arrange-act-and-assert-pattern-the-three-as-of-unit-testing/). This is what you've seen since the very first demonstration:

```javascript
const expected = 5; // Arrange
const result = add(2, 3); // Act

if (result === expected) console.log("Test passed!"); // Assert
else console.log("Test failed!");
```

Following this convention makes your tests readable and logically sound. It's quite possible that you'll have the odd occasion where there is more (messy?) setup that needs to be done, but as long as you follow this pattern and extract any bulky setup code from your test then you're pretty well set.

Always strive for "dumb tests". The less complicated, the better.

## **Test for behavior, not for implementation**

Google writes about a scenario where we replace the current implementation of a calculator class with a new one. What to do with the current, working, tests?

> None of the existing tests should need to change since you only changed the code's implementation, but its user-facing behavior didn't change. In most cases, tests should focus on testing your code's public API, and your code's implementation details shouldn't need to be exposed to tests.
>
> — Google Testing Blog: [_Testing on the Toilet: Test Behavior, Not Implementation_](https://testing.googleblog.com/2013/08/testing-on-toilet-test-behavior-not.html)

There's a transactional kind of beauty in a good test because we don't really care _how_ the code did what it did, just that it did what we expected.

This is about having eyes on the _only_ important thing: the behavior or response you get, not anything else. The test is a consumer of something (a function, method, class, service, system, whatever) and receives something. Can you evaluate the behavior? Can you see if it’s correct? Yes? Good. You don’t need more than that. Especially in a serverless world where it’s all messages and HTTP, that should come as a very convenient way of modeling your thinking.

Anecdotally I still hear about QA testers precisely testing for implementation…

## Mock as little as possible and avoid specific tools for it as far as possible

Something I've also learned while working with people over the years is that there is sometimes a bigger reliance on tooling and frameworks to handle details like mocking. I find this to add complexity and dependencies for something that ideally should be able to be done in a manual and lightweight manner. There's a lot of reading in the area of[ test doubles (mocks, stubs, fakes, spies)](https://martinfowler.com/bliki/TestDouble.html), particularly on mocks and the risks and issues that come with them.

Primarily I would advise creating "local" mock/fake implementations that you inject into your code to approximate the expected behavior. Prefer using composition to direct how you can test, over mocking.

There will be some more commentary on this later in this chapter.

## Should I have one test per class/function/whatever?

Some smart people would say no.

{% hint style="info" %}
This article is really good and interesting, having a conversational type of format on [test contra-variance](https://blog.cleancoder.com/uncle-bob/2017/10/03/TestContravariance.html) and what you gain by not using the convention of a test file per class.
{% endhint %}

I would say: Write tests from the widest unit tests (use cases) first and then fill in additional tests (in separate files, of course) for each class/function/method that has code branches that are not covered by such wider "use case unit tests". Don't forget to always do negative testing as well!

Given that you probably have the possibility to throw some errors in most classes/functions then it's a realistic assumption that you will end up with individual test files for most of your code base. In those files, you should test the difference or delta, i.e. the "missing parts", not everything from scratch, once again. Using this approach means that you don't do excessive and redundant testing, but that you actually fill in bit-by-bit the missing code branches. In total, you will quickly and expediently move to better and better coverage.

<figure><img src="../.gitbook/assets/testing_scopes.png" alt=""><figcaption><p>Save yourself effort and redundancy by writing tests for the outer scope of your system first.</p></figcaption></figure>

The _farther out_ we are (e.g. testing from the use-cases layer) the coarser-grained our understanding of the system is. That's why I always "fill in the gaps" where branch coverage is not full (or nearly full) in tests that exercise relevant functionality. Typically this would be something like this:

- Tests for use cases -> Behavior focused (general; almost like a unit test-flavored system test)
- Tests for functions (such as infrastructure utils) -> Behavior-focused (typical unit test scope)
- Tests for classes (public methods; refactor private methods to utility functions that can be individually tested, if relevant) -> Behavior focused

## What is the test coverage to go for?

As we've seen in the Robert C. Martin quote previously, 100% should be the goal, whether or not we can reach it. Sometimes it's not practical or possible to get 100%, but anything significantly less than full coverage should make you ask what you are trying to get out of the testing in the first place.

Remember that even 100% test coverage does not rule out all logical, meaningful scenarios you might want to verify. So if I were a worse (but rather realistic) person maybe I'd set up the maxim:

`100% test coverage is not the end. It's where we begin.`

Sorry mate, if you expected another answer. :person_shrugging:

{% hint style="info" %}
While the following link concerns [Jest](https://jestjs.io), I find that [this article is good enough to recommend anyway as it gives solid advice on how to actually reach full test coverage](https://tsmx.net/jest-full-code-coverage/) and what to do about situations like "untestable" code lines. Highly recommended!
{% endhint %}

## **Cultivate a gut feeling for how much testing is “enough”**

[Even Kent Beck, the TDD and Extreme Programming guy himself, says this](https://stackoverflow.com/questions/153234/how-deep-are-your-unit-tests/153565#153565).

Here’s how my thinking (and feeling!) goes:

- Always document usage! Not documenting at least examples is inexcusable. Give API examples and anything needed for someone to test what you built, no matter how small. And how about you? Will you remember in 12 months, if you look at the code then? Nope. You won’t.
- In very simple demos I don’t do any tests at all. (There, I wrote it black-on-white). If the scope is very slim, internal or otherwise “safe”, then just skip them.
- When you do test, test the things that can actually break, not every theoretical combination, or things you know will work (hairy proposition, but again, don’t overdo testing). Your code should ideally behave predictably, which means test cases should not be unending.
- In small production projects, I might just do a small Node (or \`ts-node\`) script that runs functions/classes and verifies them on a functional level. No need for a big framework.
- If APIs are needed, I’ll do kind of integration tests with a mock. There’s a lot of nice tooling for this. From using real APIs with super basic [curl](https://medium.com/beadlist/using-curl-for-api-testing-463803304620) to [node-fetch](https://github.com/node-fetch/node-fetch) or [superagent](https://www.npmjs.com/package/superagent) to [mocking APIs with (for example) Jest](https://dev.to/zaklaughton/the-only-3-steps-you-need-to-mock-an-api-call-in-jest-39mb), to [Mock Service Worker](https://mswjs.io) or contract testing using [Quicktype](https://quicktype.io)-generated schemas through [Pact](https://pact.io).
