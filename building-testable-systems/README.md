---
description: Build it well and build it to be testable.
---

# 🥼 Building testable systems

Before we even try to test something, the system itself has to have some basic qualities so it can be deemed testable at all. I will continue to refer to the system as an API: It doesn't have to be a web API, as the term should cover many common types of systems.

{% hint style="info" %}
There's a lot more about this general area in my book [Better APIs](https://betterapis.mikaelvesavuori.se) if you are interested.
{% endhint %}

This section picks up on those things you can (and should) do as a developer before even spending any time on the testing itself.

Remember [Werner Vogels' advice](https://thenewstack.io/werner-vogels-6-rules-for-good-api-design/) and think of API as a broad concept:

1. APIs are Forever
2. Never Break Backward Compatibility
3. Work Backwards from Customer Use Cases
4. Create APIs That are Self Describing and Have a Clear, Specific Purpose
5. Create APIs with Explicit and Well-Documented Failure Modes
6. Avoid Leaking Implementation Details at All Costs

Good code has many qualities that are often spoken of when discussing what "good code" actually even means. Commonly mentioned qualities include being readable, maintained, organized, and structured, and following reasonable semantic naming conventions.

For us to build something that is testable _at all_, the code, therefore, needs to be of a certain level. In this section, I'll go into some of what might go into that.&#x20;

{% hint style="info" %}
In this area, some relevant literature includes:

* [_Principles of Web API Design: Delivering Value with APIs and Microservices_](https://www.goodreads.com/en/book/show/55893836-principles-of-web-api-design)
* [_Building Microservices: Designing Fine-Grained Systems_](https://www.goodreads.com/book/show/22512931-building-microservices)
{% endhint %}
