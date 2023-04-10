---
description: Centering on use cases in your application helps focus on the actual needs.
---

# Lean towards use-case driven APIs

By directing attention first to the _need_—and designing, exposing, and mocking a solution for it—we are able to create a realistic product rapidly and fill in the detail as needed to the extent we require later on. **This should be an iterative rather than an incremental phase, as we continuously move toward a more evolved state**. In incremental development we add work like bricks to a wall – the wall is never complete and functional without all of the bricks, making change sometimes hard to account for. In the iterative mindset, however, we begin with a strip of tape (the most minimal substitute for the wall) and gradually learn and progress until we have the desired qualities. Maybe we didn't need a brick wall, but a plaster wall – learning this with the incremental approach always results in waste and is seldom a good thing; in the iterative approach, it's the opposite. That's a lot closer to the spirit of Lean and Agile, and also much more fruitful as an approach for software engineering.

## A system as a set of use cases

We use the word "_use case_" but it's perhaps time to understand it a bit more_. Use-cases_ can certainly be generally understood as broadly meaning "something the system does", but in the context of software engineering and Agile in particular, the term is attributed to [Ivar Jacobsen](https://en.wikipedia.org/wiki/Ivar\_Jacobson) who minted the term with a specific intended meaning.

> "For instance," says Jacobson, "**a system with a dozen use cases is a reasonably good piece of software**. But when you scale it down – or zoom in as we say – you’ll get maybe several hundred user stories, maybe even thousands of user stories. The problem with all these user stories is that, to understand the bigger picture, you’ll end up drowning in the details, and user stories are usually not maintained after you have developed the system. Whereas **with use cases, the big picture is more easily available to help teams understand how a system will be used, and the value it will provide to its users and other stakeholders**."
>
> Agile is touted as being a means to respond to market feedback, user feedback, and customer feedback in the business setting. When asked how use cases support organizations becoming more Agile and responsive, Jacobson shared that, "with use cases in Agile development, you don’t throw away everything when you go from release to release – you keep most of it. And the value in having this persisting use case picture is that you can gradually grow it and add more use case slices" as you receive feedback from customers and the market.
>
> "**Every use case slice is a number of user stories that bring value in some way to the customer. But value is generated only if the product you’re delivering is actually embraced and used by your intended audience, so it is much better to focus on delivering usable value than on long lists of the functions or features your product will offer**."
>
> — [Use Case 2.0 and its Role in Agile Software Development](https://www.ivarjacobson.com/publications/blog/use-case-20-and-its-role-agile-software-development)

Consequently, accounting for your system as a set of use cases makes it easy to reason about what your system does in plain language.&#x20;

A software architecture approach that takes use cases as a literal layer is [Robert C. Martin's Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html). I've used it and evolved my own use of it for some years and feel that it provides many tangible benefits to my work as a software engineer. I highly encourage trying to use it as a disciplined approach.&#x20;

## Using Clean Architecture as our foundation

{% hint style="info" %}
This section is lifted from [my book on DDD and its "Modules" section](https://ddd.mikaelvesavuori.se/tactical-ddd/modules). Consider reading that as well, since there is quite some overlap.
{% endhint %}

The "Clean Architecture" is a relatively well-known variant of the onion/hexagonal/ports-and-adapters school of architecture.

Many have tried and many have failed when it comes to setting up a folder structure for DDD. For my part, I've found that Robert C. Martin's "clean architecture" is a better (and simpler!) elaboration of where so many developers have tried to find a way. It's not magic, just a very nice mapping (and [blog article](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html), and [book](https://www.goodreads.com/en/book/show/18043011-clean-architecture) for that matter!).

<figure><img src="../.gitbook/assets/CleanArchitecture.jpg" alt=""><figcaption><p>From Robert C. Martin's blog. "<a href="https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html">The Clean Architecture</a>", 10 August 2012.</p></figcaption></figure>

I find it the most immediately effective and neat variant of these, as it:

* Introduces very little in terms of novel concepts;
* Is almost directly compatible with how DDD envisions structure in the software realm;
* Powerfully exploits the _dependency rule_ for well-working and testable software.

Robert Martin writes about the _dependency rule_ like this:

> The concentric circles represent different areas of software. In general, the further in you go, the higher level the software becomes. The outer circles are mechanisms. The inner circles are policies.
>
> The overriding rule that makes this architecture work is _The Dependency Rule_. This rule says that _source code dependencies_ can only point _inwards_. Nothing in an inner circle can know anything at all about something in an outer circle. In particular, the name of something declared in an outer circle must not be mentioned by the code in the inner circle. That includes functions, and classes. variables, or any other named software entity.
>
> By the same token, data formats used in an outer circle should not be used by an inner circle, especially if those formats are generated by a framework in an outer circle. We don’t want anything in an outer circle to impact the inner circles.
>
> — [Robert C. Martin: The Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

The intention with all of these ideas for how to structure an application is all well-meaning, but I've also seen and reflected on how a higher level of "layers" or "circles" can complicate things quite quickly.

Let's at least look at the levels and some examples of what would go into each, respectively.

* **Entities**: "Business objects of the application"
* **Use cases**: "Use cases orchestrate the flow of data to and from the Entities, and direct those Entities to use their enterprise wide business rules to achieve the goals of the use case"
* **Interface adapters**: "A set of adapters that convert data from the format most convenient for the use cases and Entities, to the format most convenient for some external agency such as the Database or the Web"
* **Frameworks and Drivers**: "Where all the details go. The Web is a detail. The database is a detail. We keep these things on the outside where they can do little harm"

Ultimately: **The farther in something is, the less likely it is to change. Any inner layers must not depend on the outer layers.**

## In closing

Using the Clean Architecture is one way you could use a disciplined and relatively well-known approach to segmenting your code, helping you write better-structured code that will most likely be easier to test.
