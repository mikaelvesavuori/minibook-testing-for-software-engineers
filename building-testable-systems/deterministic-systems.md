---
description: Boring, predictable code should be the primary output of your job.
---

# Build deterministic systems

> If the software isn’t perfect, some of the people we go to meetings with might die.
>
> — Bill Pate, NASA engineer

In the overall concept of quality, you'd probably expect _testing_ to take some form of place. However, if you look at the [Consortium for Information & Software Quality](https://www.it-cisq.org/standards/index.htm) and their wording, there is no testing there. What gives? I think it's pretty easy: **Testing is just about verifying the** [**quality attributes**](https://en.wikipedia.org/wiki/List_of_system_quality_attributes) **of what we built**.

And who else should do this, in terms of work ethics and competence, than the software engineer who wrote the code?

{% hint style="danger" %}
This is one of the strongest arguments I hold against testers and other professionals whose work solely relies on that small sliver of the big picture.
{% endhint %}

## It starts with knowing what you are actually trying to do

**Your #1 job is making working software.** “Working” means _verified_ working, and being _predictably_ working.

If you don't know what you build and how it should behave, then you will have unclean and/or unclear code. Alas, such code cannot be meaningfully tested; at best you will have a brittle test, meaning it breaks or works on a whim. Such tests are even more dangerous than having no tests as they provide no clear answers, only more "questions".

{% hint style="success" %}
The [functional programming paradigm](https://www.yld.io/blog/the-not-so-scary-guide-to-functional-programming/) is one way in which we can optimize for highly deterministic systems since it heavily encourages [pure functions](https://en.wikipedia.org/wiki/Pure_function). We don't need to go quite so far to write software that works intentionally.

Also, remember that JavaScript (if that's what you are using) is a multi-paradigm language that can [support many of the features of a functional programming language](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes-func.html).
{% endhint %}

Nowadays, when pretty much everyone is a cloud engineer, the evolution of _DevOps_ has reached the previously called _configuration management_ realm and transformed it into _infrastructure-as-code_. With this notion, not is only the code deterministically built, tested, and deployed—now we can do the same with "hardware" and infrastructural components, just as if they were malleable software. **Therefore, in a modern technology environment, the coding and testing will not only have to do with software, but also with infrastructure**. They are effectively tied together very closely, so old ITIL-style management won't really cut it anymore in the cloud-native world.

This is both good and bad:

- Good because it is an efficiency enabler for the well-versed engineer.
- Bad because it does require new skills that not all engineers find appealing.

Nevertheless, with all of these changes, I would argue that _generally,_ conditions for writing, testing, and deploying good software are rapidly improving.

**Testing to a high, confidence-creating degree should be one of the major parts of an adequate "definition of done".** Anything less will invite making testing and quality-enforcing procedures optional, which to be frank, means they won't get done at all :man_shrugging:.

## No good test without good code

{% hint style="info" %}
Yet again, while the code is TypeScript and it should work just fine, I am doing this frameworkless and very raw so that you have a minimum of distraction in the way. The solutions are not necessarily the "best" either.

If you want to try them out, you can just paste them into the [TypeScript Playground](https://www.typescriptlang.org/play).
{% endhint %}

You'll remember this from the introduction:

{% code title="Basic JavaScript `add` function" %}

```javascript
function add(firstNumber, secondNumber) {
  return firstNumber + secondNumber;
}
```

{% endcode %}

With the test being:

{% code title="Minimal test" %}

```javascript
const expected = 5;
const result = add(2, 3);

if (result === expected) console.log("Test passed!");
else console.log("Test failed!");
```

{% endcode %}

This will be a simple test that outputs 100% test coverage on all aspects: [line, statement, branch, and functions](https://www.atlassian.com/continuous-delivery/software-testing/code-coverage).

The above function implementation is naive as it has no verification of the types of `firstNumber` or `secondNumber`. Let's say we use TypeScript, then we could just do:

{% code title="TypeScript version of the `add` function" %}

```typescript
function add(firstNumber: number, secondNumber: number) {
  return firstNumber + secondNumber;
}
```

{% endcode %}

Voilá, types in action.

No doubt, that is better. In the context of the IDE, we will now be able to create warnings or errors based on misuse of the function, at least in terms of the input types. With a [strict](https://www.typescriptlang.org/tsconfig#strict) configuration, we can even break compiling this code if someone tries to do something naughty.

This however won't stop anyone from _using_ the code in a problematic way, for instance, if what you build is a library that others can use post-compilation time.

Additionally, it takes but a simple `// @ts-ignore` comment above your usage of the function to dispel any such rigor. And if you are allowing others to use the code, such as through a library, then you'll definitely need to ensure the type definitions are retained. What I am saying is not that TypeScript adds _nothing_, I am simply saying that it removes _many_ of the errors that happen in the development phase and library usage or such, but there are still ways to crash the software. Types by themselves do not do all the heavy lifting; we need more.

In the vanilla JavaScript iteration, given that strings and numbers can be easily interchanged, we wouldn't necessarily break anything when it comes to simple additions of a numerary value provided as a string and one provided as an actual number. But what if some evil person starts passing in arrays, objects, symbols, or other things?

One solution could be:

<pre class="language-typescript" data-title="Improved TS version"><code class="lang-typescript"><strong>const NonNumericErrorMessage = "Provided value is not a number!";
</strong>
function add(firstNumber: number, secondNumber: number) {
  if (!isNumber(firstNumber) || !isNumber(secondNumber))
    throw new Error(NonNumericErrorMessage);
  return firstNumber + secondNumber;
}

const isNumber = (item: any) => typeof item === "number";
</code></pre>

This is better. We now have an actual, implemented safeguard to check the correctness of the input type. In turn, we get an additional function, `isNumber()`, that can also be tested. However, **it will already have been tested by default in the existing test**, so no need for extra work. Why is this? It's actually quite logical – your tests exercise (use) the same logical flow, so you will still hit the part of the code that runs `isNumber()`. Given your test inputs, you will end up with lower coverage on the branch statement if you don't also run a test with a non-numerical input.

Regardless, the code is still clean and nothing is "untestable" here.

{% hint style="info" %}
The check we did here is contextually relevant. For private class methods and "deeper" functionality that can't logically be misused as easily we can afford to skimp on this kind of rigor. Your opinion may vary, but it's just about keeping it simple and clean, and if there are firm checks surrounding a bit of code, there is no need to do _yet another_ similar kind of check and end up with unnecessary test duplication.
{% endhint %}

Since we now do check for correctness, let's add a test that checks that it does what it should:

{% code title="Test for improved TS version" %}

```typescript
// Add to previous code

function nonNumericTest() {
  console.log("It should throw an error if passing in non-numeric values");
  try {
    // @ts-ignore
    return add("2", "3");
  } catch (error: any) {
    return error.message;
  }
}

const expected = NonNumericErrorMessage;
const result = nonNumericTest();

if (result === expected) console.log("Test passed!");
else console.log("Test failed!");
```

{% endcode %}

And it does!

## Coupling and cohesion

Separate your business logic from the infrastructure. We will assume you are using AWS Lambda. In parts of the interwebz you might hear people claiming that you'll need Localstack or something else to "test" Lambda functions. No, that is not correct. We can do things far easier and better.

Again I will take to quoting myself:

> A relatively common "misimplementation" is to think of the Lambda handler as the _full extent_ of the function. This is all straightforward in trivial contexts, but we gain a significant improvement by being able to remove the pure setup and boilerplate from the business side of things.
>
> The semantic concept of "handler" is somewhat particular to how we talk about _function handlers_ or _event handlers_. On a more generic software architecture note, this layer could often be translated into what goes into the "controller" term in the [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) school. I've been known to use the "controller" term and set a dedicated folder in the structure at an earlier stage in my career, but I now refrain from it and go with "[adapters](https://alistair.cockburn.us/hexagonal-architecture/)" instead, simply as it's an ever wider concept and since we now open for _any_ type of driver of our functions.
>
> — [Domain Driven Microservices on AWS in Practice](https://ddd.mikaelvesavuori.se/groundwork/lambda-handler)

To be clear: Failing to separate business logic from the Lambda handler (or any other infrastructure) will put a quick end to testability. You end up having to set up a local stack or emulator just to verify basic functionality. You have almost no reason at all to care about the very first layer—"controller" or "adapter" or whichever nomenclature you prefer—if you know that it does nothing important. Expected things for the adapter to do would be to form the primitive shape of the input to, for example, the use cases (business logic). The use cases, being separated and all, are then completely in your control to test with a conventional unit testing framework to your heart's content.

## Inversion of control

Of the SOLID principles, perhaps the D ([dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)) offers the most power, albeit maybe not at first glance. By relying on abstractions rather than concretions and providing concrete implementations to our objects we can assemble our systems more Lego-like, making principally every object interchangeable. **If they are interchangeable, they become testable and mockable (if needed)**.

## In summary

We saw how TypeScript, compared to JavaScript, enforces strictness and makes the surface error for issues smaller. If you primarily work with another language the takeaway should be that strictness is a great quality and makes writing testable code easier as we let the compiler and language work for us. A smaller surface error with less logic is easier to test and typically relates to most conventional notions of code quality too.

<figure><img src="../.gitbook/assets/books.jpg" alt=""><figcaption><p>Various books on better programming, design, and testing.</p></figcaption></figure>

This is an area that without a doubt leads me to believe that it is only logical that a developer writes their own tests: It makes for a good litmus test of the structure and design of the code, as well as enforces the verification of the functionality according to expectations. Thus, tests help us write better code or at least qualify the code we wrote against some external force.

Further, by writing both "positive" (happy flow) and "negative" (unhappy flow) tests, we also improved the code itself and made sure we have tests that accurately exercise the code for intended as well as unintended cases. In terms of celestial beings, unhandled exceptions should be treated as Satan – to be avoided at all costs, not by closing our eyes, but by preemptively handling such cases.
