---
description: >-
  It bears mentioning that nothing is untestable, but very bad code will be very
  good at feeling untestable.
---

# Can something be untestable?

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.
>
> — Martin Fowler

Why is it that sometimes we build something, but it tends to be hard to actually prove that it works, other than when we use the thing "for real"?

Software engineering is, like Uncle Bob writes, neither regulated nor a profession in the strict sense of the word. Any software engineer, they'll have found their own "truths" and hard learnings. The **work becomes more akin to a craft than to a well-informed profession** which you require a sanctioned license to perform.

Unfortunately, that also means that there is—compared to other industries—limited common understanding, common frameworks, common standards, etc. I can hear you from the other side of the screen, "hell no, there are countless things like SOLID, TOGAF, IEEE, WCAG, W3C...!". And you are right. Yet, any time you go into a new project with new teammates you'll have to decide on _so_ many distracting things: Which flavor of Agile? Not Agile? Linting standards? Which frameworks? What structure? What language? MVC, 3-tier, n-tier, microservices? Cloud-native components or Docker image in a PaaS filled to the brim with open source? What kinds of testing? Nah, just kidding. No one will mention testing.

And that last one thing is the one single, simple reason that forms the core of why this small book came about at all: I see so many developers either not testing at all; thinking it's useless; opining that testers should do the work; reptile reflexes arguing against learning something new; testing being seen as selective _nice-to-have_; being satisfied with having _any_ testing at all...

The most foundational aspect that we should adhere to as professionals, and for the remainder of this book, is that **we have to write clean, testable code. You should also write tests in direct temporal relation to writing the code. Not necessarily TDD, but in the same rough window of time. Don't leave your code untested.**

Testing is, at least in my thinking, the crowning jewel to prove that you have actually delivered clean, deterministic, and predictable code. **The enemy of good, testable code is unpredictability (indeterminacy).** Tests just validate that your code is not in such a sorry state. It gives you a way to add safeguards to something that does not come with safeguards out of the box.

## The potential role of TDD

Test Driven Development (TDD) is one way, but not the only way, to reach testable code. One of the key reasons that TDD is effective is because the tight loop between coding and testing ensures that there will be no good way for you to _not_ have both the code and test when you move on to other things. Writing code after the fact is something that a hardcore TDDer would scoff at, for good reason: There is no guarantee that your ethics, team/manager, or backlog will allow for the test to be written as a separate component when you have working code. I think that this is a true and clear-sighted observation of how this works out, in reality.

However, there is nothing that is "absolute" about this stance. I tend to write tests when there is a rough sketch up and (maybe!) running. I use the test as a way to actually, well, test and run the code, rather than doing this manually by running and debugging the code. Using a test framework makes it extremely convenient to actually verify the functionality and I would never go back to a more primitive state of this.

## Good code is the way to testability

You will want to write code that behaves in a deterministic fashion, meaning you can always trust it to be in an expected manner given the same input.

Well-structured _clean_ code will be easy to test. That's just the truth of the matter.

{% hint style="info" %}
There are many good resources for software engineering. Some common recommendations that I have read and feel happy to also recommend include:

* [Robert C. Martin: _Clean Code: A Handbook of Agile Software Craftsmanship_](https://www.goodreads.com/book/show/3735293-clean-code) (2007)
* [Robert C. Martin: _The Clean Coder: A Code of Conduct for Professional Programmers_](https://www.goodreads.com/book/show/10284614-the-clean-coder) (2011)
* [Robert C. Martin: _Clean Craftsmanship: Disciplines, Standards, and Ethics_](https://www.goodreads.com/book/show/56631473-clean-craftsmanship) (2021)
* [Robert C. Martin: _Clean Agile: Back to Basics_](https://www.goodreads.com/book/show/45280021-clean-agile) (2019)
* [Erich Gamma, Ralph Johnson, John Vlissides, Richard Helm: _Design Patterns: Elements of Reusable Object-Oriented Software_](https://www.goodreads.com/book/show/85009.Design\_Patterns) (1994)
* [Martin Fowler, Kent Beck, Don Roberts: _Refactoring: Improving the Design of Existing Code_](https://www.goodreads.com/book/show/44936.Refactoring) (1999)
* Language-specific relevant literature, such as [Effective TypeScript](https://www.goodreads.com/book/show/48570456-effective-typescript?from\_search=true\&from\_srp=true\&qid=NdMnpnRmQx\&rank=1)
* And of course: Understand core software engineering concepts like [SOLID](https://blog.bitsrc.io/solid-principles-in-typescript-153e6923ffdb), know how to work with [object-oriented programming](https://betterprogramming.pub/understand-object-oriented-programming-with-typescript-c4ff8afa40d) and have a grasp of [modern software and cloud architectures](https://learn.microsoft.com/en-us/azure/architecture/guide/).

Anyone who knows me knows that I am heavily indebted to [Domain Driven Design](https://www.thoughtworks.com/insights/blog/evolutionary-architecture/domain-driven-design-in-10-minutes-part-one) as well. You should absolutely look into books on that topic for more on the level of patterns, strategies, module thinking, etc.
{% endhint %}
