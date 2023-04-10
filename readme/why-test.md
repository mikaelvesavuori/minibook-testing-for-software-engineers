---
description: >-
  Go for “built-in quality” instead of raising money to buy an off-shore QA
  department.
---

# Why test?

> It is not enough for code to work.
>
> — Robert C. Martin

My background (whatever that means these days…) is not in testing. But as a software developer and architect, of course, the concern of testing and quantifying quality has been something that I’ve had to deal with for years. But what is quality? What do tests have to do with it?

[Software quality](https://en.wikipedia.org/wiki/Software\_quality) as an overarching concept is incredibly hard for most people to grasp. It tends to be an "I know it when I see it" type of thing. Fact: **Quality is in fact highly regulated in many industries**, basing itself on the bars set by standardization bodies such as the ISO. In most heavy industries, it would be unacceptable or perhaps even illegal to start meddling with the quality of the product.

{% hint style="info" %}
Without being right or wrong, we can use ISO's definition of software quality for now:

"**\[The] capability of a software product to conform to requirements**".

See the concept [system quality attributes](https://en.wikipedia.org/wiki/List\_of\_system\_quality\_attributes) for a related, extended scope of such requirements.
{% endhint %}

This problem, though, seems to grow even harder in software engineering, as it can feel obtuse and opaque for non-programmers, requiring specialized labor and the work product itself is immaterial and hard to judge. One part of our history should be clear, however, and that is that **we do have concepts and champions that clearly expound quality as an integral part of competent and serious software engineering**, examples being:

* **Robert C. Martin**'s [many well-known books](https://www.pearson.com/us/higher-education/series/Robert-C-Martin-Series/348084.html), such as Clean Code, emphasize the craftsmanship, ethics, and professionalism of the trade.
* Modern development approaches such as [Agile](http://agilemanifesto.org/principles.html), which take quality seriously.
* **ISO standards** such as [ISO 25010](https://en.wikipedia.org/wiki/ISO/IEC\_9126#Developments), which attempt to codify the criteria for quality.

When we build software, in other words, conduct **software delivery**, it is usually at the request of someone. Most teams will have a "[definition of done](https://www.agile-academy.com/en/scrum-master/what-is-the-definition-of-done-dod-in-agile/)" that includes some set of criteria determining whether or not a piece of work is ready to ship or not. This definition usually includes [functional](https://en.wikipedia.org/wiki/Functional\_requirement) and [non-functional/cross-functional](https://en.wikipedia.org/wiki/Non-functional\_requirement) requirements.

The **functional requirements** deal with the need itself: Does it do what we want it to do? The **non-functional requirements**—more and more we hear these called quality attributes because that sounds less "optional"—are instead about the _quality_ of that execution: Does it do it at the expected speed? Is it secure? Is it reliable? Is it maintainable? And so on.

Without some set of these questions answered in competent and well-constructed ways, there is simply no guarantee that we can _trust_ the provided solution. Here the spirit of Robert Martin hovers around us: "**It is not enough for code to work**". It may be immoral, illegal, impossible to maintain in a team, work poorly, or exhaust system resources at an unsustainable pace. If that's the case, someone would be hard-pressed to accept that the _code working_ was good enough.

In a world where more and more of our reality is transmogrified into code, it becomes imperative that those in charge of leading software delivery do so without sacrificing quality.

The greater question is of course: **How many, and which, quality attributes will you need to support**? We could for example use the [Consortium for Information & Software Quality](https://www.it-cisq.org)'s (CISQ) [primary selected characteristics](https://en.wikipedia.org/wiki/Software\_quality) to guide our work:

* Reliability
* Efficiency
* Security
* Maintainability

[All of these can be assessed](https://www.it-cisq.org/standards/code-quality-standards/) in relatively well-known and automated ways with a plethora of tools. For our concerns in this book, we will broadly keep these in mind for the rest of the material.

{% hint style="info" %}
Testing as presented here won't affect all of those attributes in any big ways.

My number one argument will be steadfast: We need to write deterministic, well-structured, good code with the _minimum amount of test scaffolding_ and with _the smallest number of tests to reach maximum confidence_ (and most likely, near-100% test coverage).

Our tests and test tooling should help us understand our reliability, efficiency, security, and maintainability.
{% endhint %}

## Why automate testing?

A venerable topic which obviously many articles and books have been written about by people far greater than myself.

Let me briefly give you my personal highlights:

### **Tests are fundamentally about ensuring stability**

With modern development practices like CI/CD, trunk-based development, and putting complex distributed systems in production multiple times per day, one of the great risks becomes whether one can _guarantee_ to some extent that **what is released is stable**. Without mincing words: If you have no test automation, you’re dead in the water.

You can certainly deploy and release fast _without tests_—I can attest to this practice since I did it for years before “growing up” and taking responsibility—but at some point, you’ll probably be faced with clients or users calling in about issues, rather than _you_ observing issues first.

The easy answer is: Test your things. You might still get novel new problems, but at least that dumb small thing you checked in for your testing is not out in the wild causing problems for everyone. Deterministic systems are fun to build, easy to test and are what we need to strive for. These conditions provide a very good foundation for stable, well-behaving software.

### We test because that's the way we set some level of guarantee on our delivery

How do you assess the completion of the work, and that it has the expected level of quality? Automated testing ensures that you can quickly and objectively verify these things. While it's not unheard of that you'd need manual acceptance tests, that is by extension more of a feature-oriented verification (which we can test and deploy at the time of completion and hide behind a [feature toggle](https://martinfowler.com/articles/feature-toggles.html)), and in some cases, it's no more than a psychological crutch.

Taken to the extreme, this is where something like [acceptance-test-driven development](https://en.wikipedia.org/wiki/Acceptance\_test-driven\_development) would shine. And no, you wouldn't do manual such tests!

### **Automation may replace people, but it will definitely replace the drudgery**

Back to the frequent releases: Even in some modern organizations today, I am seeing not small, but _significant_, arguments being made against CI/CD and related concepts. The crutch of manual verification is strong with many.

One of the key wins with automation is that the drudgery and churn around manual verification can disappear, leaving only—in the best case—business validation; which is frankly more about diplomacy and process rather than a technical concern. **No one wants war rooms every 6 weeks just because you will launch something**.

### **No tests = no modern dev practices**

I was recently surprised by how many of the modern development practices I thought were second nature actually reside on **testing as a foundational activity**. So, if my assessment is correct, that testing understood or conducted as often as it should, it’s fascinating how people want to pick the good nice bits (“CI/CD sounds awesome”) and leave out the harder bits (“yeah my code don’t need no testing”).

Frankly this invalidates any claims on modern practices. And when I say modern, I mean things that have been almost taken for granted for 20 years or so.

<figure><img src="https://cdn-images-1.medium.com/max/1600/1*pR4W0mDVEpyjENKzcbdkXA.jpeg" alt=""><figcaption><p>​Yeah. Sick burn. I felt it too, so it’s nothing personal.</p></figcaption></figure>

### **You will only get the results you test for**

Then, of course, let's not be naive... The fallacy of testing is that you might be tempted to think that full test coverage is a promise of perfect functionality. That _could be true_, but it’s not universally true. Good testing will often require several types of testing. And even then, you are still only in the space of known issues that you are testing for!

Tests are brilliant at verifying the "known knowns" of the problem space. You will need other tools, like observability, to catch the "unknown unknowns".

## In closing

Tests are a core part of the development practices most of us have worked with, or at least read about, for the last decades. Without it, we won't get most of the benefits of CI/CD, trunk-based development, and so on. As for application development in the cloud, and the aggressive timetables of many companies—who are suddenly on a global playing field—we've seen the DevOps movement really push far on how we think about infrastructure in a lifecycle similar to that of code. All of this is good, as we upskill millions of software engineers into a new reality. But there is a dark side to all that new shiny stuff...

**"Playing DevOps" in a hyperscaler while building modern cloud-native apps, without quality practices such as testing, is like riding a rocket ship going straight to Hell: No other option will continuously deliver you into the shit quite as fast**. You need to have some way of controlling things—_test automation is that thing_. ITIL and manual processes are dead and won't be making an appearance in the cloud-native world. If we don't want back, we will have to take on the (actually not that heavy) burden of testing our things.
