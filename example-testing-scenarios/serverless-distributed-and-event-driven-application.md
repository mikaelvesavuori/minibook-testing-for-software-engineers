---
description: >-
  Several independent services (and all their related testing!) could be
  intimidating, but it's actually not that bad. Trust me.
---

# Serverless, distributed, event-driven application

{% hint style="info" %}
**Scenario**

You are architecting a new serverless, microservices-oriented solution from scratch. It needs to support good development and testing practices.

**Testable surface**

Multiple independent services consisting of any number of serverless functions and other cloud infrastructure components.

**Code access**

First-hand access to all code.
{% endhint %}

{% hint style="success" %}
**Proposed approach**

Write rich, deep unit tests for every service and Lambda function.

Produce and distribute all contracts (including event shapes) for services in a location all teams have access to. Produce and distribute diagrams (flows and sequences) of events in the same location. Create a repository of shared mock event data to facilitate testing of the independent services, or even consider full-on contract testing. Define a way to separate test data from production data either with separate infrastructure or by tagging test data as such.

Consider a very lightweight system test that can generate a test order and can verify the existence of the final, settled data in the database; do not over-invest in this capability but think of it as a system-wide smoke test.
{% endhint %}

The present scenario is much more dynamic and complex than the other scenarios we've looked at. Also, this time we are entirely first-party and can afford to do some things we might not be able to do when there is a third party involved, such as running system tests. But in that detail is actually a very testable solution!

{% hint style="info" %}
This example uses the [ACME Corp. Potted Plants Web Shop: AWS microservices demo](https://github.com/mikaelvesavuori/acmecorp-microservices-webshop) codebase.
{% endhint %}

It's a well-considered architecture that uses messages on EventBridge to communicate completely asynchronously. The only synchronous actions that happen are from the customer side when they make an order or book a delivery time.

## Define the API

Given the criticality of the API, driving all of those flows with only two commands, it's the first thing we should look at. What should the respective endpoints require in terms of input data? Too much and detail-oriented and we risk making a CRUDdy API; too little it might be too chatty or not able to drive all the flows. Let's talk to the team implementing the front end of this if it's another team.

Same as in the last chapter, we want documentation, request validation, and a good-looking API that people actually want to use.

This is less about testing than putting things in order.

## Define events

The absolute majority of communication happens through events. We'll want to document and collaborate on the events. Ideally, every service is documented. AsyncAPI can do this for this type of event-driven service; in that case, we can describe our events there.

From the events, it would be easy to infer test data, if the owning team does not produce it themselves.

Share the events and test data in a way that is ideally automatable.

Version your events and don't break functionality unless you emit a later event version.&#x20;

<figure><img src="../.gitbook/assets/image (4).png" alt=""><figcaption><p>Six different services connected with lots of events, a public API Gateway, persistence... Oh my!</p></figcaption></figure>

## Unit test the interfaces

Once there is some kind of idea of what events will drive the respective services, we can create (or use the shared) test data and use it as input in our use case-oriented unit tests.

Because the services are small, doing only one or a few things, and because there is only persistence in a single service, then we should quite easily be able to write out tests without any mocking and other such activity other than in the Database service. We can see from the diagrams which events we need to act on, so there should be no surprises here.

Unit tests should, as I nag about a lot, form the vast majority of your strategy here. Especially as you have well-known inputs and interactions.&#x20;

I don't actually think integration is that important in this scenario unless we start having real issues creating conditions for us to question that position. As the events are clearly outlined and presented, and we have a pool of test data, then I find it strange if we would start seeing _change- or evolution-based problems_ in integrating our services.

## Ensuring fit with a system test

It's perhaps worth considering a system test that creates a small number of example orders and ensures they end up in the database. This would have been a risky proposition if we had third-party systems, but here we are completely self-contained.

Due to the high number of connecting parts, a system test may actually not be that dumb of an idea, and we could use it as a kind of elaborate smoke test during our development as well. Don't make the test too unwieldy or force it to assert every interaction—just check that the order was stored, because it is, then well, everything worked out, didn't it?

While not _strictly necessary_ it could fill the role of a development tool (seeing it's all moving towards a working state) and a way to know that everything works in the future, too.

{% hint style="info" %}
Just don't forget to handle all of the test data you create!&#x20;
{% endhint %}
