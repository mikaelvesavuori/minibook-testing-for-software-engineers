---
description: >-
  Getting the right toolbox assembled doesn't need to be a pain if you follow a
  couple of house rules.
---

# Choose high-quality automatable tools

[Bad organizational structure has a clear relation to poor software quality](https://www.linkedin.com/pulse/what-causes-unreliable-software-abi-noda). As nice as it would have been, good tooling on its own won't save you from poor leadership, poor requirements, poor code, poor tests, or even a bad day at work. What they _will_ do, though, is make sure that whatever your conditions are they will be honored in a better way. In some sports, better tools mean it's easier to reach better results. Like other tools, a good test tool is reliable, easy to use, fast, unobtrusive, and provides valid and clear feedback.

## First: How can you test your software?

Conventional testing is, to be fair, easy enough that a mediocre developer can build a relatively good testing framework in a matter of a few days, given that the software is of a typical nature.

You can ask yourself: Is your software possible to reach with an API or other technical interface? Do you have source code access (for example, it's your own software)? Is it only possible to test your software through a user interface – why?

{% hint style="info" %}
Even in the case of something like SAP business software, it does actually provide APIs to test functionality. However, the low general testing competence among people working with such software sometimes sparks false notions of the software not being able to test in a more technically-accurate, automatable fashion. Resist heavily when someone says it is not possible to automate testing!
{% endhint %}

## Open source tooling or purchased?

Testing _isn't_ about being a unique snowflake, about any type of exceptionalism, or about esoteric tools. In fact, these aspects are what I believe is off-putting to regular developers, when it comes to starting to do testing. Testing needs to be idiot-proof. And for this requirement we have lots of frameworks, both of the classic variety (Jasmine, Mocha, Chai), the evolved school (Jest), minimalist options (Ava, the Node-native test runner), and when you bring in other testing types there are dozens of others names that whiz by. There is no lack of good tools in the open-source space. An additional pro of such tooling is that there is a community around the tool, its use, and best practices. Commercial tooling seldom has such factors in play for them.

Unless there is something exceptional going on, selling this type of tool at a premium is a fool's errand. Use open-source tools if at all possible.

## In-context tests

More often than not, tests need to run in the same context as the code. That is to say, tests are part of the codebase. Separating test code from application code, for example in their own repositories, will make it incredibly inconvenient to test the functionality. I am hard-pressed to see how you could do any kind of white box testing (using the actual source code) if you were to do it this way.

Always prefer code-adjacent tools, testing the source code rather than "on top of" the running system.

## **Don’t fall in love with your test tooling**

As much as you might come to love your shiny tools, remember that technology progresses, time moves on, and trends shift. I am still seeing a lot of testers use "ancient" tools that also happen to use languages and runtimes that cannot be supported by modern pipelines (don’t want/cannot mix Java 8 with Node 18, for example).

As far as possible, it’s good to work with tests that are longer-term or even universal. In that regard, a bash script running `curl` will stand the test of time better than using a legacy test tool running a deprecated runtime will. While I haven’t been in a situation like this, I do know that many businesses face significant problems when their tests become unmaintainable.
