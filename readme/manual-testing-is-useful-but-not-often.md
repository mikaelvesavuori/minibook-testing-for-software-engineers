---
description: >-
  Let's not beat it to death, because it has its moments too. Spare some pity
  for the poor sod.
---

# Manual testing is useful, but not often

Let's not kid anyone: **You will have a better product if you automate the boring and repetitive parts and definitely those parts that happen to involve boring and repetitive testing**. However, in reality, you will sometimes find yourself needing to do certain manual verification or "prodding-and-poking". Should you now look deep into the glass and become a brooding existentialist... a _manual tester_, is that what your parents wished for you? :cry:

No, you don't have to brood on this question. There **are some valid reasons** to _sometimes_ do these things manually. I'll try to address some parts which are totally fine to do manually without being contradictive to our end goal of automating tests.

## The first time you do something

In general, **automating anything is something you do for the long term**. Therefore it's not uncommon that when experimenting, designing, and trialing ideas, then you do this manually. At this stage, the code is not ready to be tested because that's not where you are in the lifecycle of whatever you are building.

## Collecting initial test data

Inevitably you will need to have _some_ amount of data available. If you are building a new service and expect it to integrate with some third-party system then you'll progress roughly through a sequence such as:

* Design a solution, or make a hypothesis
* Find out _which_ APIs to integrate with
* Ensure they are documented
* Find out what the expected inputs and outputs look like (i.e. data)
* Verify "toy" functionality, using the API for real (see the next point)
* Collect the test data (inputs, outputs)

Storing the data in your code base means you now have superpowers, making it possible to:

* Write the integration
* Mock the integration so you don't have to use the real deal during tests etc.
* Write the tests of the integration

Yes, it's a chore to get that data but that's nothing to have feelings about, given you win a lot for that tiny investment of manual effort.

## Basic checks and verifications

During development, it's convenient and entirely realistic to have an Insomnia [design document](https://docs.insomnia.rest/insomnia/design-documents) or Postman [request collection](https://learning.postman.com/docs/sending-requests/intro-to-collections/) with your endpoints and typical calls, ready to use at the click of a finger. So, there, I wrote it: Manual checks and verification are OK to some extent when it carries a meaningful impact.

{% hint style="warning" %}
Handle this like a _troubleshooting tool_ rather than as anything else. Whenever the case is something that you systematically have to do, it's time to look into automating whatever it is you are doing.
{% endhint %}

## Exploratory testing

Exploratory testing can be an interesting approach, but should not be done in an _unqualified_ _way_ or on _unqualified work_. If a system is strictly deterministic and you've written at least basic safeguards and checks, as well as using a module structure in which there are clear flows that cannot be misinterpreted or abused, then the need for exploratory testing rapidly diminishes.

Exploratory testing, at least for me, seems to be most useful when we truly know nothing (or very little) about the thing we are testing. For most engineers, this should never be the case; rather think of this as sending in a minesweeper to an uncharted war zone to understand what nasty surprises we are facing.&#x20;

### Exploratory testing might also be a ruse

{% hint style="danger" %}
I am opining this in a totally self-aware snarky tone.
{% endhint %}

**Your number 1 goal is to write deterministic code**. Without it, anything could happen! There is no meaningful test that can be written for such situations.

[Exploratory testing](https://www.atlassian.com/continuous-delivery/software-testing/exploratory-testing) is a well-meaning test strategy that has started to gain traction.

> Exploratory testing is an approach to [software testing](https://www.atlassian.com/continuous-delivery/software-testing) that is often described as simultaneous learning, test design, and execution. It focuses on discovery and relies on the guidance of the individual tester to uncover defects that are not easily covered in the scope of other tests.&#x20;
>
> — [Atlassian](https://www.atlassian.com/continuous-delivery/software-testing/exploratory-testing)

It aims to leverage _qualified manual labor_ to "explore" what a system can do, which to me at least is about trying to find holes in it. To me, it sounds like a strategy not dissimilar from the work done by a penetration tester whose aim is to uncover vulnerabilities in a system. The exploratory tester, however, will have a broader mindset.

{% hint style="info" %}
One of the more influential names in this area is [Maaret Pyhäjärvi](https://maaretp.com). Check her videos, texts, or social media if you are curious.
{% endhint %}

Don't get me wrong now, but part of your unspoken job as a software engineer is to _not_ have testers around. Just don't tell them that! Following agile principles and practices, there would literally exist no testers. See for example [Kent Beck's old rules of Extreme Programming](http://www.extremeprogramming.org/rules.html) (which is a sister/brother to most of the other Agile you may know):

> All code must have unit tests.
>
> All code must pass all unit tests before it can be released.
>
> When a bug is found tests are created.
>
> Acceptance tests are run often and the score is published.

I'm sure you'll already be well aware that old grumpy [Uncle Bob will also have written several books on this](https://www.goodreads.com/shelf/show/uncle-bob). Even a corporate moderate like Dan Radigan at Atlassian goes as far as stating that "[let's be clear: scripted manual testing is technical debt.](https://www.atlassian.com/agile/software-development/testing)" **There is no **_**manual**_** option. It's that easy.**

So if your number 1 goal is to write deterministic systems, then being "explorative" may very well be meaningful, either in:

* Very ill-performing systems.
* Unknown or undocumented systems.
* Very large (probably undocumented) systems.

To the extent that we need manual testing, I feel entirely confident in writing that such a role should come with abilities include coaching, enabling, being transformative, and being only transitive (disappearing after some short time)—i.e. more along the lines of quality engineer, quality coach, or similar notions. Atlassian also comments on this limited scope:

> An exploratory testing session should not exceed two hours and should have a clear scope to help testers focus on a specific area of the software. Once all testers have been briefed, various actions should be used to check how the system behaves.
>
> — [Atlassian](https://www.atlassian.com/continuous-delivery/software-testing/types-of-software-testing)

In summary, exploratory testing sounds like something that makes a lot of sense when we want "bang for the buck", being expensive, laborious, and qualitative in its delivery. We can't rely on it for our _continuous_ work.

## In closing

While this is in no way an authoritative account of all cases where manual testing makes sense, they should summarize some clear directions:

* Manual testing is unavoidable in many cases while you are writing the first bits with integrations.
* We need to uncover unknown parts of unknown, undocumented systems.
* As one part of our development work ("pushing-and-prodding").
