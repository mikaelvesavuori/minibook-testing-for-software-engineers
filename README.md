---
description: Learn everything you wanted to know about testing but didn't dare to ask.
cover: .gitbook/assets/abstract.jpg
coverY: 0
---

# 👋 Introduction

> Inspection does not improve the quality, nor guarantee quality. Inspection is too late. The quality, good or bad, is already in the product. As Harold F. Dodge said, “You can not inspect quality into a product.”
>
> — [W. Edwards Deming](https://deming.org/quotes/10157/)

In a modern technical environment, testing, development, DevOps, and business merge—for better or worse. With 20+ years of Agile practices behind us, helping us stay true to a better ideal of how we _should_ work, I will in this book present a developer-driven approach to testing, focusing on automation and quality instead of tedious and expensive manual labor.

This book discusses some of the core concepts around testing and what "testability" means, and also practically demonstrates a number of testing types and patterns that should be helpful in testing, especially if you are building distributed microservice architectures.

Overall, this will be a friendly, highly opinionated, and impassioned rollercoaster ride through a subject that's commonly misunderstood and underappreciated. At least that's what I've seen! Throughout, I will liberally point to other materials, as my aim is to shape an attitude and approach with hands-on examples than to be a compendium tome of all there is to know on testing. I'm not that smart, and no one has that kind of patience anyway. There's too much great material written that I can't compete with all of it—but I hope this will be the content that drives hordes of developers to _actually become interested in testing_.

## What will you learn?

This book will get you in the trenches of writing testable code, understand modern test practices and how to pursue them, as well as demonstrate several testing methods applied to common microservice use cases with public front-end applications and serverless back-ends. So the next time the trap door is sprung underneath your feet—"caught you now, developer-who-hates-testing!"—you will have at least a grounded understanding of what to do and that testing doesn't have to be the pain some make it out to be.

The testing types we will discuss and I will demonstrate to you are:

* Static code analysis (linting etc.)
* Unit testing
* Smoke testing
* API (integration) testing
* Synthetic testing
* Load testing
* System testing
* UI end-to-end ("E2E") testing
* Contract testing

...and of course, we'll take a spin doing continuous testing during your CI stage.

{% hint style="info" %}
Throughout the book, I will often use the term **API (Application Programming Interface)**. You should understand the term in a broad sense, not just as a term for _web APIs_, but rather for any system that has a surface that can be accessed by someone or something else.
{% endhint %}

## Why write this book?

The weightiest reason for writing this book—or compiling it, rather, as it also contains some of my previous work—is that **I find testing to be woefully lacking in the professional contexts I've been in** for the duration of my career. In fact, testing is something I myself got enthused about simply _because_ of the price paid in abject failure—sometimes by myself, sometimes by my teams, and sometimes by my employers lacking good ways to conduct testing in the first place.

As such, I wouldn't be surprised if you have been in similar situations or situations where the business side overcompensates for poor quality with an insistence on manual testing efforts. It's a classic example of good intentions causing more pain.

The second reason is to clarify how testable code is good code—and the same goes the other way around. I will spend quite some time considering what good code, and tests, look like. Additionally, to test something, you should _know_ what you are testing. Therefore, we will also touch on a few techniques and conventions to add relevant documentation of various kinds to our code base.

## Audience

I am writing this for several intended audiences:

* **The frustrated but curious software developer**: You are out there, somewhere, building your services, and maybe you feel frustrated about things breaking for no good reason. Or you have a manual testing flow that you _just know_ could be removed. Or you just want to ace this software development thing, and testing happens to be your weakest skill.
* **Colleagues**: Because we talk a lot about these things and since nothing beats actually showing what we mean.
* **Myself**: As a way to learn more and hone my didactic, communicative, and technical skills.

## The project source material

I've tried to adapt this to something that hopefully is enlightening to more senior developers, but accessible enough for a tester who may have had less programming time. You should be able to tag along by following the instructions given throughout the book. You don't need to understand the details of the provided code.

The majority of examples and scenarios will use code bases from multiple applications I've written in the recent past:

* [https://github.com/mikaelvesavuori/get-a-room-ddd-example](https://github.com/mikaelvesavuori/get-a-room-ddd-example)
* [https://github.com/mikaelvesavuori/testable-systems-starter](https://github.com/mikaelvesavuori/testable-systems-starter)
* [https://github.com/mikaelvesavuori/microservices-testing-workshop](https://github.com/mikaelvesavuori/microservices-testing-workshop)
* [https://github.com/mikaelvesavuori/acmecorp-microservices-webshop](https://github.com/mikaelvesavuori/acmecorp-microservices-webshop)
* [https://github.com/mikaelvesavuori/better-apis-workshop](https://github.com/mikaelvesavuori/better-apis-workshop)

{% hint style="info" %}
**Notes before starting**

Since my main language of choice is TypeScript and modern Javascript/Node, there will be a slant towards that in tooling and any code examples, though I’m pretty sure most of the concepts should suit your language just as well.
{% endhint %}

Generally speaking, these applications follow modern cloud-native conventions, using serverless services like AWS Lambda, API Gateway, DynamoDB, and EventBridge. The applications will be detailed a bit more later in the book.

## How the book is structured

The very first sections are meta-materials around the book, such as an extended introduction to the subject; a declaration of the book being open source; that I'd love sponsorship if you enjoy this or my other books and materials; and where to find the project resources. In `Getting started` you'll be able to read a bit more in-depth on how to set up and understand the related projects.

An absolute prerequisite of doing testing at all is building testable systems. This is what the section `Building testable systems` addresses. I've already written quite a bit on that topic in (for example) [Better APIs](https://betterapis.mikaelvesavuori.se) so we'll try to address the most test-adjacent concerns this time around.

The real deal, in terms of testing, begins with `Towards modern testing practices` — this is where we'll start getting to the point about practical testing and the questions you may have wondered but didn't dare ask. This section addresses the critical theoretical knowledge you need (as far as I am concerned, at least!).

The following, similarly large section is named `Running tests in practice` and covers just what you'd expect. I will cover the majority of "reasonable" test types with commentary, comparisons, recommendations on when to use the type of test or when to avoid it, and also show-and-tell examples using the respective methods. Each page in that section should give you enough to proceed with your own testing, and certainly so when armed with the conceptual knowledge of the previous sections.

Continuing to `Example testing scenarios`, in which I will guide you through how I have attacked three different scenarios in a modern cloud landscape, from front-end-driven testing to complex event-driven microservice scenarios.

Finally, I will wrap things up in the chapter `In summary` and condense the most crucial points I hope I've made.

Finally, for the nerds and deep divers out there, I've prepared the `References and resources` to contain enough material to keep you reading 'til your eyeballs drop out of your skull.
