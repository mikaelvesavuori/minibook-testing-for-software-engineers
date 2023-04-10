---
description: >-
  An important part of a test is to be precise; testing too much or too little
  is detrimental.
---

# Scoping the test

<figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption><p>Like threading a needle, scoping a test might be picky but it's one and done once it's threaded.</p></figcaption></figure>

Remember one of the basic rules of good testing: We should aim to only make a single assertion per test. In other words, tests need to be _precise_. One obvious way to gain precision is by minimizing the surface area of our tests. We can call the activity of deciding on the boundaries of a test as _**scoping our test**_.

Scoping is not some exhaustive, esoteric process, it's simply about knowing "what does my test cover logically?" (not to be confused with test coverage!).

Terminology time:

* **Test**: The individual test and assertion you want to make.
* **Test suite**: A "family" or related logical collection of tests. For example, given an ATM software scenario, you might want to have a suite of tests for all positive/working cases of withdrawing funds with credit cards.&#x20;
* **Test scope**: The explicit or implicit "range" (scope) of what a test is responsible for in terms of covering and verifying truthfully.

## Example with AVA

Without further ado, let's look at how this might look in a unit test:

```javascript
test('It should get slots', async (t) => {
  const slots = await GetSlotsUseCase(dependencies);
  t.deepEqual(slots, [{ something: 'abc' }, { something: 'xyz' }]);
});
```

This particular example is from the [DDD example project](https://github.com/mikaelvesavuori/get-a-room-ddd-example) and uses [AVA](https://github.com/avajs/ava), a very lean tool for unit testing. I like it a lot, as it is an unencumbered experience with fewer options and bells and whistles and it's typically faster too when compared to a big unit testing framework like Jest.

The test is very clear and to the point. The use case is the "outermost shell" of our application in terms of unit tests, so quite a bit of code will be run under the hood once we run this test.

{% hint style="info" %}
AVA is somewhat less feature-rich compared to many frameworks, something I am not seeing as a problem in the majority of projects I work with. Do give it a try!
{% endhint %}

**Scoping in AVA is just a question of placing all the relevant, related tests in a file (single or multiple files, as you like to organize them).** Name the file what the test is scoped to, such as `GetSlots.test.ts`. That way, we can easily isolate and run selected tests/files if we want to test any certain features (or classes, functions...) of the application.

{% hint style="info" %}
Because my own work often requires only a relatively small number of tests, this does not become a major hassle, but sure, some test files can be a bit long if we collect a lot in a single file.
{% endhint %}

I always write both "positive" (happy flow) and "negative" (unhappy flow) tests and I tend to keep them in the same file. Their location is marked by a multiline comment such as:

```typescript
/**
 * POSITIVE TESTS
 */
```

If there would ever come a time in which further segmentation would be needed, I'd consider breaking positive and negative tests into their own files, but I'd probably rather go for a fuller testing framework if that'd be the case.

## Example with Jest

Another example is the following test from [Figmagic](https://github.com/mikaelvesavuori/figmagic). This time we are using Jest, one of the most well-known and well-used tools in the Node/JavaScript/TypeScript world at the time of writing. It's more full-featured than AVA for sure, and it also has a very nice `describe` construct that we can use.

```typescript
describe('Failure cases', () => {
  test('It should throw an error if no argument is provided', () => {
    expect(() => {
      // @ts-ignore
      convertHexToRgba();
    }).toThrow();
  });

  test('It should throw an error if missing a single parameter', () => {
    expect(() => {
      // @ts-ignore
      convertHexToRgba(1, 1, 1);
    }).toThrow();
  });
});

describe('Success cases', () => {
  test('It should correctly return a CSS standard RGBA string', () => {
    expect(convertHexToRgba('#33ff00')).toBe(`rgba(51, 255, 0, 1)`);
  });
});

```

The `describe` feature is common among most unit testing frameworks, and it helps you to better express in the actual test file any scopes, such as failure/success cases, or to package tests by features within these blocks. **In Jest (and most such frameworks) you can use the `describe` feature as a way to express the scope of the test**. This makes it even easier to read, browse, and grok the tests, especially when there becomes many of them, and they might deal with unrelated use cases.

So there you have it. Scoping should be pretty clear to you by now, given that it boils down to other typical software engineering principles, such as being semantic, clearly organized, and so on.

Use the mechanisms you have available to you but don't forget to also write the shortest and most well-defined tests you can!
