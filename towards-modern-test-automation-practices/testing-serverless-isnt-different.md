---
description: >-
  Serverless might be "different" in several ways, but for testing it needn't be
  that dissimilar.
---

# Testing serverless isn't that different

Testing serverless isn't different. Mostly, at least.

There seems to be a widespread notion that testing serverless applications, or microservices in general, is very different from testing other applications. While it's true that there is a lot more surface area and integration area in a microservice-dominated technical landscape when seen service-by-service, the differences shouldn't be that big for actual testing. At the very least they shouldn't necessitate a great deal of stress!

A web application written in Node, using a Fastify server will not be leagues different from the same application broken into several Lambda functions with an API Gateway exposing them.

## How serverless (and the cloud in general) clarifies responsibility

### Overall responsibility

One of the clear differences between serverless and most other paradigms is that serverless services (like DynamoDB, Lambda, and Firebase) are managed services; sometimes effectively being fully managed.

{% hint style="info" %}
See [this article](https://cloud.google.com/blog/topics/developers-practitioners/serverless-vs-fully-managed-whats-difference) by Google's Priyanka Vergadia for more on _serverless_ versus _fully managed_.
{% endhint %}

In short, this means that the provider (i.e. AWS, Google, Microsoft, or whomever it may be) takes care of the majority or all of the management, such as making sure the service is available, in a useful state, securing some of it, and generally automating the heavy lifting involved in dealing with computer systems. Your benefit is that a lot of that headache is dealt with. By leveraging serverless—which does have a slightly higher cost premium compared to unmanaged "raw" hardware—we can actually win back most of the maintenance and operations costs we would have to deal with otherwise. We can also generally trust the providers to provide a more secure and well-performing bottom line than if we did all of the work ourselves.

Serverless is, as I am writing this in 2023, one of the current pinnacles of truly being "cloud native". We offload all of the hard specialist bits to someone else and can interact with resources dealing with, for example, compute and storage globally simply by using APIs. Using the cloud in any capacity, we commit to the [shared responsibility model](https://aws.amazon.com/compliance/shared-responsibility-model/) and how it divides activities expected of the provider and from us, the consumer. This model is used with minor differences in all (big) clouds. This all leads to us having to cater to a lot fewer concerns. For example, the security model of the cloud dramatically changes the old "castle-and-moat" models.

Ultimately, we are abstracting hardware into APIs, that is...software! But our business software itself has not really changed all that much.

### Logical responsibility

Without going into a big "microservices vs monoliths", or "which is best" argument—in which the only real, but boring, answer is "it depends"—the single clearest difference is that microservices, well, at least ought to do less than a classic all-in-one monolith.

If you are new to serverless functions, have no doubt in your heart that you _can_ certainly create a [monolithic Lambda](https://dev.to/cdkpatterns/learn-the-3-aws-lambda-states-today-the-single-purpose-function-the-fat-lambda-and-the-lambda-lith-361j), or worse, a [distributed monolith](https://www.gremlin.com/blog/is-your-microservice-a-distributed-monolith/). All too many have walked that path: some oblivious to the negatives, and some who swear to never use serverless functions again. Stack Overflow and Medium are full of these folks.

How big a Lambda should be is something you will have to read more about elsewhere.

{% hint style="info" %}
Some good sources to make an informed decision on that would be:

* [_Learning Domain-Driven Design_](https://www.oreilly.com/library/view/learning-domain-driven-design/9781098100124/) by Vlad Khononov (2021)
* [_Building Microservices_](https://samnewman.io/books/building\_microservices\_2nd\_edition/) (2nd edition) by Sam Newman
* [_Identify microservice boundaries_](https://learn.microsoft.com/en-us/azure/architecture/microservices/model/microservice-boundaries) in the Azure Architecture Center
* The chapter [_Domain driven Lambdas with a use case approach_](https://ddd.mikaelvesavuori.se/tactical-ddd/modules/domain-driven-lambdas-with-a-use-case-approach) from my free online book [_Domain Driven Microservices on AWS in Practice_](https://ddd.mikaelvesavuori.se)
{% endhint %}

It's very typical that a **service** (the logical composition and mass; the thing-as-a-whole) involves multiple **microservices** (the executable sub-part of that entirety), meaning a given deployment may entail several functions being changed.

My own thinking and accumulated wisdom lead me to recommend choosing either of these approaches:

* **Workflow-oriented**, meaning all functions are strictly contained logically. Think of this as a function that does only a single, unambiguous thing. This works well for many simple use cases and for piecemeal code. More often than not, this will suffice, especially for typical event-driven flows. In this approach, no function overlaps (meaningfully) functionally or logically with another.
* **Entity-oriented**, (which taxonomically _may_ be misleading) in which we may be building more typical, complex systems. I call it this because we tend to operate on things such as entities and we require orchestration of classes and pieces of code that may be reused between functions. Some call this the "fat Lambda". I think this is a very good compromise between traditional programming and fending off a too-chatty, minimalistic function landscape.

Before [Lambdas with function URLs](https://docs.aws.amazon.com/lambda/latest/dg/lambda-urls.html), the only way to make a Lambda function become publicly accessible would be to host it behind an API Gateway. With this said, AWS API Gateway (or whatever similar capability your cloud offers) is—and will most likely remain for many years— the most general-purpose way to expose functions. One major benefit is that by using an API Gateway, it becomes very clear how your functions map to the exposed URLs. With a good software architecture, you can easily draw a direct line between the endpoint's path names their logical functionalities, and their respective function code. That itself could be informed by the use cases and/or requirements for the system.

Regardless of choice, in any non-trivial situation, practically no single microservice will exercise all code paths/branches, conditions, lines, or statements. I would say that in the microservices world, this is a lot easier and more intuitive to understand than with a monolith because it's so ingrained in the very notion of microservices. Monoliths can become daunting as they tend to not offer as expressive interfaces and system boundaries (since they have fewer of them!).

The intellectual labor (and complexity) increases somewhat with microservices. All in all, as Yan Cui has written, serverless and microservices may ostensibly feel to be more complicated than a monolith but in fact, that is not correct—microservices are truer as they do not hide these details quite as much as classical monoliths do.

## Impact of microservices and serverless on testing

### Don't test the "cloud" or its side effects

The "cloud" is a third party, and as you likely remember, one does not test third-party code. Care about your business logic and test that instead. As with any other testing, don't test for side effects ("did the SQS get my message?"). No difference, compared to any other scenario.

### Don't emulate the "cloud", instead use actual infrastructure or mock if needed

While I respect that we want the "feel" of a local development environment, there are good and less good ways to deal with this.

Instead of something like [LocalStack](https://localstack.cloud), which only complicates your workflow and has to keep pace with all the changes in the cloud, use something like the lightweight [Serverless Offline plugin](https://www.serverless.com/plugins/serverless-offline) to get the API and function parts working (so you can run against them), and if needed, use [aws-sdk-mock](https://www.npmjs.com/package/aws-sdk-mock) (or a similar capability) to mock rather than to emulate other functionality, like queues and storage.

{% hint style="info" %}
As I already wrote in the chapter on handling and mocking side effects, always prefer composition and your own local mock implementations over heavy-handed, potentially problematic mocking if possible.
{% endhint %}

Also, there's nothing saying you can't deploy your own infrastructure to test and work against. I highly advise you to do this if possible—this is a big change against the traditional world in which deploying infra was only permitted by the basement wizards.

### Microservices are APIs, so test the APIs why don't you

Request/reply, streams, or events... Whatever your input format, all of these can be tested, ideally with test data you have collected, using their correct formats. For TypeScript, [there are also types you can use](https://www.npmjs.com/package/@types/aws-lambda). Prefer unit tests (business logic) over integration tests (infrastructure).

Overall this is not very different from any other unit testing and integration/API testing:

* Collect test data for the relevant input type (since they have different shapes).
* Decouple the handler and the use case from each other.
* Make sure your code wrangles the input into a well-known, well-defined Data Transfer Object.
* Write unit tests against your use cases (infrastructure-independent) with the Data Transfer Object.
* Write integration/API tests against anything that is relevant and does not simply duplicate your business logic tests.

![All of the green areas can be individually developed, tested, and deployed. Make the most of that fact!](<../.gitbook/assets/image (11).png>)

In the above diagram, there is no need to check that _all parts_ are working, at least for the test scope of each individual service (mint-green box). Ensuring the business logic is working, that our interactions with mocks work, that types are correctly used, and that data wrangling (incl. validation, sanitization) is done will go a long way in the scope of a specific service.&#x20;

## In closing

Some notes for you to take away from this:

* Testing with microservices and serverless means having clear boundaries.
* Stay true to the microservices mentality: keep your test boundaries equally tight and clear.
* Only test _your_ code and services, not infrastructure or third parties.
* The internal logic of a microservice is tested with unit tests.
* Each microservice is exposed through an API: That’s the actual “integration testing” part! Only test through the API if there is good reason to believe there are classes of issues you can rectify through such tests.
