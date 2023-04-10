---
description: What to make of models created in the past, present, and future.
---

# Testing models

<figure><img src="https://cdn-images-1.medium.com/max/1600/1*CQudowKNaX7jD2dbBGmFiQ.jpeg" alt=""><figcaption><p>The "one true" model that intuitively comes to my mind. Substitute "bread" with the simplest possible test to provide you confidence. Unknown author. Found on <a href="https://www.pinterest.se/pin/707839266404139008/">https://www.pinterest.se/pin/707839266404139008/</a></p></figcaption></figure>

> All models are wrong, but some are useful.
>
> — George E. P. Box

I’ll keep the theory to a minimum here, as I’m writing this section to cater to any more junior developers who may want some framing of this entire thing.

Note that for an organization, or even a complex multi-part system, we might even want to—like Vladik Khononov writes in his 2021 book on Domain Driven Design—want to default to certain models based on the overall architecture of a particular system. So don't necessarily think of a given model as something that is good for _every_ case.

## **Testing ice cream cone (unknown origin; God?)**

**The primeval model that has been passed down through aeons...**

<figure><img src="../.gitbook/assets/Testing Ice Cream Cone (1).png" alt=""><figcaption><p>Ice cream is delicious – however the testing ice cream cone is a deception meant to lure low-tier consultants into the marketplace.</p></figcaption></figure>

How about we start with _the one no one actually argues for_, but it seems like a lot of organizations pay good money to get: [The testing ice cream cone](https://alisterbscott.com/kb/testing-pyramids/).

In this not-so-uncommon model, we get a small number of unit tests, a larger number of integration tests, a bunch of GUI-oriented tests, and finally, a whole dollop of manual GUI-oriented tests. While indeed this model contains a fair degree of automated tests, their relation is incorrect—by which I mean we write quantitatively more user interface-facing tests than code-facing tests. With the generous filling of manual tests, this is quite a toxic blend, perhaps mostly because this is an _actual model_ (!) rather than just a mere circumstance.

In short, the problem here is that we focus on the least stable, furthest-from-the-code areas. The one single upside of this model is that it is absorbable for someone unfamiliar (or not professionally knowledgable) with code, given that most of the tests happen "on top" of the running system. As I've already written several times, however, this "upside" becomes problematic as we now begin to invite non-developers into testing the quality of the system.

## **Testing pyramid (Mike Cohn)**

The [testing pyramid](https://martinfowler.com/articles/practical-test-pyramid.html), instead, is the sane**r**, more well-functioning version of the ice cream cone, and kind of a classic notion of how to segment testing efforts in a meaningful way.

<figure><img src="../.gitbook/assets/Testing Pyramid (1).png" alt=""><figcaption><p>The inverse of the ice cream cone is a much more reasonable model.</p></figcaption></figure>

It bears mentioning that the relative proportions are of course totally dynamic, but that the focus on automation is at least outspoken in this model and that the tolerance for manual testing is low but not necessarily non-existent.

**This is the model I will still personally recommend in the majority of cases, at least as a starting point**. It brings a sober attitude — which unfortunately is still for many organizations radically different than their current state. Seasoned developers may find it easy to get cushy and think of this as "old hat", but the fact remains that you might struggle even getting this model working everywhere.

Consider starting here and adapting the relative proportions, favoring automation in general. Skew towards the simpler types, such as unit tests.

{% hint style="info" %}
One problem with the testing pyramid is that the proportions may optically _seem_ to be similar in distribution. Mathematically we can clearly understand that this is not true, but the way they are depicted sometimes makes it feel like they are the same.
{% endhint %}

I would modify the diagram to place a _possible_, but not necessary end-to-end testing portion. Given that I am highly critical to its place in most systems it's not something that should be required at any rate, but accepted if needed. At any rate, the number of such tests will be strictly in the lowest range attainable.

## **Testing trophy (Kent C. Dodds)**

A newer model is the [testing trophy](https://kentcdodds.com/blog/the-testing-trophy-and-testing-classifications), that as far as I know, was coined by Kent C. Dodds.

<figure><img src="../.gitbook/assets/Testing Trophy (1).png" alt=""><figcaption><p>This model emphasizes proactive tooling (static tests) and puts systems in a sociable context, interacting with others.</p></figcaption></figure>

This is a model that shares a common concern that many contemporary tech leaders make a big point about: Focus on integration testing. Given that most organizations today run fairly complex systems and most likely have some smattering of microservices, then it makes sense to imagine that integration testing would bubble up as the principal testing concept.

The testing trophy, as opposed to certain similar models such as the [testing honeycomb](https://engineering.atspotify.com/2018/01/testing-of-microservices/), include static code analysis and linting as the "base" of the trophy. I personally love this addition as it's too easy to miss out on that part when discussing testing strategies; good static analysis should be a given today, using a mix of IDE-centric tooling and deeper, CI-side testing.

{% hint style="success" %}
While the other models don't typically point out static tests, please do take static testing to heart as a requirement of a solid test approach, regardless of any specific model you might favor.
{% endhint %}

## My recommended model?

If you ask me for components for a model, I'd answer:

* **A solid basis of static tests (everything that makes sense to use!)**: Run in IDE, as pre-commit hooks, in CI, and add any optional quality tooling running on pushed code. Examples of such tools are Codacy, SonarCloud, and DeepSource.
* **Automated unit tests (\~80%)**: Checks the coherence, logic, and functionality of the system.
* **Automated integration tests (10%, if applicable)**: Tests particulars around the system being exposed as an API or whatever you build; this is more of infrastructural checking than any logical tests which would be covered by unit tests.
* **Key end-to-end flows (10%, if applicable)**: If it makes sense to verify that key UI flows work, then go ahead and test them! But make sure you are not testing the logical functionality of a back-end (third-party system; out-of-scope; already tested in the backend itself) or infrastructural concerns (covered by integration testing).

You will perhaps note that in my model, I allow for only a very slim selection of integration and end-to-end tests and that I even discourage them unless applicable and meaningful.

## Choosing a model

As Mateusz Roth writes in [_Why the test pyramid is a bullsh\*i_](https://medium.com/@mateuszroth/why-the-test-pyramid-is-a-bullshit-guide-to-testing-towards-modern-frontend-and-backend-apps-4246e89b87bd), it is wise to remember that **different models make sense for different types of systems**. A lean process for front-end apps with several end-to-end tests might be a decidedly different notion from a lean, unit test-leaning approach for a server-side library.

The model is just that, a conceptual model. It only gives your strategy and how you approach testing and where you place the relative effort – you can always change!&#x20;
