---
description: >-
  Deferring decisions on code paths to something outside of the code itself is a
  really smart idea if you balance the trade-offs that come with that power.
---

# Separate deployments from releases with feature toggles

To mitigate some of the risks that still might exist (i.e. the unknown-unknown class of problems), we can **separate the deployment, as a technical concept, from the release, as a feature availability concept**.

A **deployment is never more than a technical construct**—this is the point at which new code is pushed out to the hardware that will eventually serve it to users. It is often unfortunately and falsely conflated with a _release,_ a residual aspect from an age of "dumb hardware" in which the hardware simply runs the software on it, being oblivious to any dynamic switching of code paths. However, there are now (and have been for a long time) very simple ways in which this inflexible factor can be overcome: One way is using _feature toggles_.

[Feature toggles (or feature flags)](https://featureflags.io) are a mechanism by which a piece of software can, either during run-time or build-time, dynamically alter its behavior with what is effectively no more than an "if-else-then" implementation in the software. Feature toggles make it easy to separate a deployment from a release if we can "hide" new features behind such a toggle and enabling it only for the appropriate audiences. We can safely release new software without worrying about new functionality being used until the "feature is flipped on" for everyone. It's an imperfect pattern, yes, but it has stood its test against time by removing more problems than it causes.

The secret sauce is very simple: Separate the configuration (called the _feature_ or _flag_) from the code that will run that feature. In effect, we **defer something that is typically baked into the code to an external resource** which will respond with some relevant information on what the code should do. It would work something like this:

* `[CODE]` My context is that I am running for a logged-in beta user. I am now at a branching point in the code regarding the display of a new feature. I'm asking you (the feature toggle service) whether or not to display a panel with new, not generally available options.
* `[FEATURE TOGGLE SERVICE]` For this user type you should display the panel.
* `[CODE]` OK, thanks, I'll show the panel.&#x20;

## A simple example

Enough said, time for a coded example, taken from [a Gist I made some time back](https://gist.github.com/mikaelvesavuori/ddbcc14ff95a4f9bffd42f0e5e54b11c).

```javascript
/**
 * @description API that caters for current (production) requirements, as well as future (beta) data needs.
 * Uses a kind of primitive feature toggle to switch version, without needing to deploy and maintain two separate backends.
 */
function api(event) {
  const clientVersion = event?.headers["X-Client-Version"];

  // Return data structure expected of current version
  if (!clientVersion || parseFloat(clientVersion) < 1.1) return dataCurrent();

  // Return new data structure
  return dataBeta();
}

/**
 * @description This is the current production response.
 *
 * @version 1.0.0
 * @deprecated Will be deprecated after version 1.1
 */
const dataCurrent = () => ({
  response: "This is the current production response",
});

/**
 * @description This is the new beta response.
 *
 * @version 1.1.0
 */
const dataBeta = () => ({
  data: {
    text: "This is the new beta response",
  },
});

/**
 * @description Call API with both current and future application versions.
 */
const responseProd = api();
console.log("Production response:", responseProd);

const responseBeta = api({ headers: { "X-Client-Version": "1.1" } });
console.log("Beta response:", responseBeta);
```

It's a very basic piece of software but it still manages to make a rather big point that at least I am not seeing enough in production software in my own experience: We can use a basic mechanism (in this case, a header) to drive traffic dynamically rather than resort to heavy-handed infrastructural segregation or using network/DNS segregation.

`Client A` (v 1.0, the public production version) calls backend with:

```
GET www.domain.com/api
```

`Client A` expects:

```
{
  "response": "This is the current production response"
}
```

`Client B` (v 1.1, the limited beta version) calls the same backend with:

```
GET www.domain.com/api
```

This one expects to get back the following shape:

```
{
  "data": {
    "text": "This is the new beta response"
  }
}
```

While the technical artifact is always one and the same it is "intelligent" enough to dynamically respond to two different code paths. Using good software architecture and competent development practices, the actual coded implementation itself should be of robust quality, being decoupled and well-structured to allow for this behavior without cringe-worthy logical holes.

{% hint style="info" %}
Another useful pattern when dealing with multiple active code paths is [branching by abstraction](https://www.branchbyabstraction.com). Instead of using branches in your version control (Git) you do this in your code. A key benefit is that you can maintain continuous integration—which you effectively can't if you only sporadically merge back as with feature branches—and logically handle any unfinished code.
{% endhint %}

## Some concerns you will have to address

Take note that a few concerns will pop up as a consequence of using feature toggles:

* Will you buy or build a tool?
* Will you have a significant cost attached to using/building/running such a tool?
* Do you also want to make a cultural move and train your business colleagues to use such a tool to release features, or do you still let developers handle releases, but just with another tool?
* From a solution architecture side, how are you changing the characteristics of your software if you retrieve toggles for every call (latency, cost, usage quotas, added complexity...), or is it more appropriate to "bake in toggles" during build time? What are the trade-offs you want to make?
* How will you handle fallback states if you don't successfully retrieve a feature toggle?
* How will you ensure proper lifecycle management of feature toggles in code as well as in configuration? You won't want if/else statements from an old feature lingering about longer than necessary.

While it seems like we are inviting a lot of new problems into our house by using feature toggles, they do offer a powerful, easy-to-understand way of achieving a critical benefit that is well worth fighting a bit for.

{% hint style="info" %}
Feel free to also read more in-depth material at the [feature flags article on Martin Fowler's site](https://martinfowler.com/articles/feature-toggles.html) as well as on the LaunchDarkly-sponsored [FeatureFlags.io site](https://featureflags.io).&#x20;
{% endhint %}
