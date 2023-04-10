---
description: Stitching together a messy reality into a concise, confident whole.
---

# Back-end relying on third parties

{% hint style="info" %}
**Scenario**

You are brought in to do a minor clean-up for the order-taking part of an e-commerce solution. It's requested that you help train the original development team how to richly test their solution as well since their manual end-to-end process is impeding the delivery of new (well-tested!) features.

**Testable surface**

Two back-end solutions use one or more Lambda functions; one solution is publicly exposed, and the other is internal. Also, a message bus and a database.

**Code access**

First-hand access to all code and infrastructure. No access to third-party code but full access to their API documentation.
{% endhint %}

{% hint style="success" %}
**Proposed approach**

Collaborate on the API, document it, and set up request validation.

Write unit tests on the functional (use case) level for all of our functions.

Write basic integration tests to all services backed by APIs; but only if that they produce side-effects that are easily handled or use QA/mock/fake endpoints.
{% endhint %}

In this scenario we come in a senior, advising a team and their existing solution. While we are new to the context, we also are in a bit of a luxury position in that we can focus entirely on improving the quality and testing parts of the solution.

{% hint style="info" %}
This example uses the [microservices testing workshop](https://github.com/mikaelvesavuori/microservices-testing-workshop) codebase.
{% endhint %}

The separation in the solution is already good and very precise. If this weren't the case, we might have wanted to first start cutting out the respective parts better. This time we didn't need to.

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Very simple, but clean microservices architecture.</p></figcaption></figure>

## Improving the API

There's also a chance to talk to the team building the front end (given it's not us!) and define an API collaboratively. By this point, we _already have an API_ but it might be worth checking in if we are both satisfied with it. We should spend some time creating an actual API schema and making it available, to fend off any avoidable misuse from our consumers. Also, let's slap on a validator on the API for good measure.

Next, we can move on to creating a Data Transfer Object wrangler function so that every time we actually operate on "business logic" we do it from a well-defined object and not just some indiscriminate blob passed into the API Gateway.

At this stage, there is already a massively reduced error surface.

## Writing the unit tests

Now we can inspect the current state of unit testing. We should focus, given that the architecture itself is well-separated, to write use case-oriented unit tests. With the DTO wrangler written, it should be pretty easy to write these tests in a way we feel confident about.

The majority of testing seems to concern how the order is correctly produced based on their market and customer type. These tests should be flawless. If the payment providers provide provider-side mocks, test APIs, or something else we can use, then great. If not, let's just mock them out based on what we know from their docs.

For the Database service, the testing should potentially be even simpler as it should now only be able to receive well-defined inputs. Also, they don't interact with nearly as many systems.

We will also create some testable functionality to create correctly-shaped objects for our persistence, so we can mock out DynamoDB while being safe in knowing the objects are always stored correctly. On top of that, we will add the semi-official types and maybe the AWS SDK mock too.

## Integration tests

If it's possible to run integration tests against the third parties, we can do this now after the more pressing parts are handled. If not, then we'll simply stick with the mocks we have and make sure we have good monitoring and/or APM software in place to collect integration errors.
