---
description: >-
  Focusing on the use and problem, rather than everything around it, makes
  development more effective.
---

# Design and think before coding

Looking back at [Verner Vogel's advice on APIs](https://thenewstack.io/werner-vogels-6-rules-for-good-api-design/), we read:

> Work Backwards from Customer Use Cases

As we can see, there are two important pieces here, the first being "working backwards" and the other being "customer use cases". Let's start with _working backward_:

> The Working Backwards product definition process is all about is **fleshing out the concept and achieving clarity of thought about what we will ultimately go off and build**. It typically has four steps \[...]
>
> Once we have gone through the process of creating the press release, faq, mockups, and user manuals, it is amazing how much clearer it is what you are planning to build. We'll have a suite of documents that we can use to explain the new product to other teams within Amazon. We know at that point that **the whole team has a shared vision** on what product we are going \[sic] the build.
>
> — [Werner Vogels](https://www.allthingsdistributed.com/2006/11/working\_backwards.html)

{% hint style="info" %}
Concerning "working backwards", to understand that specific part of the advice, see:

* [Working Backwards](https://www.allthingsdistributed.com/2006/11/working\_backwards.html) by Werner Vogels
* [Working backwards: The story behind the AWS Cloud Development Kit](https://aws.amazon.com/blogs/opensource/working-backwards-the-story-behind-the-aws-cloud-development-kit/) by AWS, as an example of this
{% endhint %}

This is a brilliant, cheap, and powerful way to get to the core of what you need to build instead of jump-starting with building something before we know what it should even be. If Amazon, notoriously good at producing new ideas and services, can do it, then why not at least try to learn something from this, flipping the script for a bit?

At the time of starting to write your code, you already have some idea what the API, or user's interface and needs towards your code, will be.

And what about code at this stage? Without rehashing all kinds of quotes and books, a way to write better code could include an understanding of code as being about layers, either being exposed or unknown. While such a concept is very tangible with classes (`private` and `public` methods) I mean this in a wider sense: Each layer is on its own as simple as possible and utilizes indirection to let other functions/classes/etc. take care of what they do best. By writing the simple code possible, locally, we compose, globally, a more sophisticated solution. Each part is easy to understand, well-written, and easy to test. If this sounds like something you learned early in your training, then I can only say, "yes" indeed. But why do we lose sight of the training we've taken and the things we've learned? There is a reason for such concepts and principles.

We need a way to connect the _product vision_, its _design_, and its _implementation_ in a flexible yet correctly aligned way. As this book is not on product vision or team exercises, we'll leave that part off the table. But when it comes to design and implementation, we definitely can say a few things about that! Let's now look at some actionable ways to _work from the back_.

## Write, make bullet points, doodle, or diagram your way to a design

Which comes first: Bad code or a bad solution? I strongly believe that most _really bad_ ideas can be fended off if we think about what we are doing first. One under-utilized capacity we have is to think in a concentrated and structured way. Many of us are too keen on jumping the gun, hovering over the keyboard, and smashing out a solution, but:

**Code is not important — your solution to a problem is. The code is a vehicle to deliver the solution — it's not necessarily the same vehicle that also solves the design itself (i.e. the solution).**

There are plenty of ways to do this:

* Write in plain text what you are trying to solve
* Tell someone in plain English (or whichever language you use) how you are trying to solve a problem; also known as [rubber duck debugging](https://en.wikipedia.org/wiki/Rubber\_duck\_debugging)
* Make bullet points of how you can solve the problem sequentially
* Make a doodle or diagram of some kind to express how the solution works
* Explain or write what the solution is supposed to solve and for whom

This works just as well with others, as with yourself. In such cases, consider appropriate tools such as whiteboards (or collaborative online whiteboards) or anything physical or digital that is collaborative in nature. Even something simple as co-designing a JSON blob can do wonders when problem-solving!

## **Use scratch files to remove complexity from your problem solving**

When it's time for at least some code to be written, I will sometimes do my coding in a scratch file—maybe not even in TypeScript, just regular old JavaScript—and continually run it, either manually or ideally with something like Jest which can watch files for edits (in effect being close to TDD, though not strictly TDD). When I mean scratch file, I simply mean a `.js` or `.ts` file that I will run outside the context of the code base. This will be removed later. You can also easily do this in a big pre-existing project if you want to isolate the logic in a crude but effective manner.

The scratch file is not more efficient per se, but gives an undeniable focus on just solving that specific part of the puzzle. This way of encapsulating the problem solving is, at least for me, a way to decomplexify the circumstances to the minimum required details. I won't _always_ do this, but it works especially well when you are in complex circumstances, and testing the specific feature you are building is not necessarily super easy.

Once we are done we can easily transport the code into the actual code base. At this point, you will already have actual tests, or at the very least, some test data you've had to construct.

## Write the "API" before the code

A divergent but related evolution on the above is thinking of the initial work as _designing the API_, or in other words, the public interface that will be used.

In the actual web API community there is something called literally the API-first approach, in which the API definition is written before any implementation. You could take inspiration from this and, given that you work with literal APIs, also write your specs in OpenAPI or AsyncAPI or whatever to design the actual API. There is no code needed for this, yet you take on many of the design challenges without encumbering yourself with needless implementation detail.

The good thing with this is that you could now mock the expected behavior very quickly and work "backward" from the intended final public state to filling in the missing implementation details needed.

Once you start to intuitively separate the outer shell (the API, what others see) from the inner workings you will get much closer to being "good at testing". Given that tests don't check internal implementation detail (which is invisible to the test; only the request and response are known) you are stuck with the fact that the API or outer surface has to be "good" – expressive enough for the user/caller and rich enough to pass in the required information for the processing to be made correctly.

This can be done either as full-on TDD, in which case you are effectively writing what the expected usage should be first anyway, or writing some of the (known) logic from a user's perspective first. Doing this is no harder than setting up (as above) a scratch file to toy around with, or writing "fake" docs in your `README.md`, an approach that is somewhat similar in spirit to the [Amazon 6-pager](https://writingcooperative.com/the-anatomy-of-an-amazon-6-pager-fc79f31a41c9) though not quite as long!
