---
description: All I know about testing on one page.
---

# 🏖 In summary

The way I think of testing could be summarized as this:

* **Remember the five rules of testing**:
  * One assert per test
  * Readable
  * Fast
  * Independent
  * Repeatable
* [**Write good, deterministic code.**](https://medium.com/better-programming/just-write-dumb-boring-predictable-code-91279a969b42)
* **Good software architecture will make your code easier to test**, by being cleaner, better formatted, and layered in a way where you know what is important and what is less important.
* The key thing for testable code is already stated in the SOLID principles, more exactly the `S`—the **Single Responsibility Principle**. If a good test asserts one thing, and your _system under test_ has a big bag of tricks and all kinds of wizardry, then how will you be able to write any good tests?
* **Make sure your code is observable, i.e. it's possible to know what went wrong with it.**
* **Nothing is untestable, but do remember that poor code is very hard to test**. Refactoring code to be "good" and testable is commendable and a real part of your job as a developer. Just do it, don't ask for permission.
* **Accept that tests are only a part of your software quality strategy.**
* **Use "testing in production"** as a way to observe and learn from failures in your actual production system**.**
* **Always write unit tests**. Never skip them.
* **The correct level of unit test coverage is 100% (overall)**. See this as a definition of done. Deviations may be accepted when it comes to testing side effects (which we can't or won't).
* **Full coverage does not preclude that other types of issues may not occur.** However, in keeping with building qualitative, deterministic systems, then full coverage should be a very good indicator of overall stability.
* **Tests should be handled as code**, with the same semantics, standards, care, and maintenance, and they should be colocated with the code that it checks. Never divorce tests into their own repositories or contexts.
* **Catch as much as you can already in the IDE**. Enable testing locally, as a pre-commit hook, and in CI to ensure testing and quality are adhered to at every stage. Also, add stricter compilation options and IDE tooling to help you normalize the code to expectations so there is no environmental drift.
* **Push as much testing burden "to the left" as possible**, i.e. prioritize fast, lightweight tests (unit tests and sprinkles of strategic integration tests) over heavier, slower, frail tests like end-to-end tests.
* If you feel like TDD is too much for you, consider a variant in which you **simply write and run a test as the actual input for your development work**. While this is a marginally different approach, it is not quite as dogmatic. And you will have your test(s) written! Not even saying that you will have a much quicker feedback loop while developing.
* Testing models (pyramids etc.) are useful as a theoretical construct but they are just that: Theory. **Adapt what tests you use based on your actual use cases**. Remember that any useful models _will_ discourage the use of system tests and end-to-end tests if at all possible. Prioritize easier, faster, stable test types over others to an extreme degree.
* **Optimize for a good build chain** with high-quality code analysis tools and stable testing tools.
* **If building APIs, always write an API schema**. If you have to go for a leaner approach, for example during early development, then write basic endpoints and example requests/responses in your `README.md`.
* **If you can, then always use request validation at the API level to offload some testing**. While this may sound like a security feature (as it ensures incoming input follows the right convention) or a quality add-on (making the API easier to consume) it also ensures that the surface area of your testing grows much smaller. You can focus on what is actually meant to happen.
