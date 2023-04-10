---
description: Don't settle for less than anyone should expect today.
---

# Use modern technical practices

> "I have nothing against the professionalisation of programming; it would be a good thing. But the fact remains that the great majority of programmers are not engineers. We are tradesmen, a bit like plumbers or domestic electricians. There's nothing to be ashamed of in being a skilled tradesman; for a good part of my career, a plumber earned more than I did."
>
> — [User "denton-scratch" on YCombinator](https://news.ycombinator.com/item?id=33961785)

We've seen (depending on your specific area) languages come and go, and trends blossom and die. Whether you are a "skilled tradesman" or professionalized software engineer, have one foot in conventions, standards, wisdom, and the core foundations of software engineering and the other foot in the flow of time and the novel ideas and tools that come along to stay informed.

For that second part, the following are a few of the things I've picked up that have certainly made my deliveries better in terms of quality.

{% hint style="info" %}
[Google's DevOps site is a fantastic resource that I highly recommend you look through.](https://cloud.google.com/architecture/devops)
{% endhint %}

## Work in a DevOps model

Few things are as powerful in human psychology as making someone feel responsible for something. One of the key learnings in DevOps is that it's very little about technology, and very much about creating accountability in the team to "own it" from start to finish throughout all of the software's phases, which of course also includes testing. Given the tools to act on that ownership, few will look back nostalgically to their old-school "throw it over the wall" dev teams.

:white\_check\_mark: **Testing benefits:** Faster feedback, increased ownership.

## Own your CI/CD pipe

We've already discussed this, but it's a complete game changer if you aren't yet familiar and comfortable with having the power of taking your code from laptop to customer. Of course, this is a cornerstone technical feature of the above DevOps aspect too. Push as often as you have code that deserves being pushed and run your tests on every commit.

:white\_check\_mark: **Testing benefits:** Continuous testing.

## Configure your code with infrastructure-as-code (IAC) tools

It's a very old complaint among developers to rant about environment issues, misconfigured infrastructure, or environment disparity. Using an IAC tool like Terraform, AWS CDK, or Serverless Framework, you get the possibility to define (in code) what the cloud infrastructure should be and you can safely leave it to the tool to converge those states for you. You end up with a whole lot fewer of those complaints when you start using IAC.

:white\_check\_mark: **Testing benefits:** Less brittle and flaky infrastructure, greater clarity into system state and configuration.

## **Migrate to Typescript if you are using JavaScript**

If you, like me, develop primarily in the JS/Node stack I can’t really stress enough the quality-of-life improvements that Typescript adds, not to mention the impact it makes to overall quality and ensuring “dumb bugs” are more or less instantly vanquished. It may feel like a bump in the road at first, but you should know that TypeScript offers the possibility to gradually move into it, even with pre-existing codebases. Many JavaScript/Node developers can be a bit shaken by all the new things coming in — interfaces, types, generics, class orientation, and more — but again, this can be taken in gradually. For me, it took me back also 10 years to when I was writing a lot more of that kind of code, and it offered me much clearer traction on applying [design patterns](https://softwareengineering.stackexchange.com/questions/124109/is-there-a-canonical-book-on-design-patterns) ([see here for examples](https://refactoring.guru/design-patterns/typescript)) than regular JavaScript gave me.

{% hint style="info" %}
The one book to get on TypeScript is Dan Vandenkam's [_Effective TypeScript: 62 Specific Ways to Improve Your TypeScript_](https://effectivetypescript.com). Do it; get it now.
{% endhint %}

:white\_check\_mark: **Testing benefits:** Greatly increased language capabilities to write better code, reduces some needs in validation and dumb checking.

## Refactoring

The one skill that marks the kings and queens of software engineers is being good at refactoring, the systematic improvement of your code. A common unspoken belief seems to be that code _is good_ (or not) instantly and at the point of being written. In fact, code _evolves_ as we pass the code over from its crude first implementation to an increasingly better, more readable, more maintainable, less error-prone state. Therefore **we need to accept that code grows better and the application art/science of doing so is refactoring**.

You don't ask for time to refactor (at least unless it's critical and something that will set you back significantly in time and effort). The point of refactoring is that we evolve code from one state to another, better one. **Passing over code and seeing that this could be improved and light-handedly improving it, say in under a few minutes, is not a task to be ticked off the list – it's part of the continuous positive evolution of your codebase**. This works well with the ubiquitously-mentioned boy scout rule ("always leave the code better than you found it").

However, for significant changes, refactoring in a stricter and more precise sense still matters – it's just that you may need more tools from the toolbox!

{% hint style="info" %}
One of the better online sources on this would be [Refactoring.guru](https://refactoring.guru) and the undisputed book is [_Refactoring_ by Martin Fowler](https://refactoring.com).
{% endhint %}

A brilliant thing about your tests is that, given you have tests, they should not fail after having refactored your code.

:white\_check\_mark: **Testing benefits:** Continuous refactoring leads to "better" code, often making testing easier.

## Use a well-considered software architecture

Using a conventional and well-working architecture such as any of the [hexagonal types](https://en.wikipedia.org/wiki/Hexagonal\_architecture\_\(software\)) (I propose the [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) derivation) will put your code in a more rigid and understandable place. Hexagonal architecture is also good at decoupling relevant hierarchies of the code and is a surefire way of improving the logical separation, especially if you are a more junior developer.

**Understandable and well-decoupled code is testable code**. More on this later in this chapter.

:white\_check\_mark: **Testing benefits:** Better understanding of the code base and the way code interacts, may lead to better code and therefore easier and better tests.

## Be literate with the cloud, including microservices and serverless

More and more of our technical solutions are cloud-based or cloud-native, and it's getting a lot more common to see microservices architectures being used, as well as the steady growth of serverless technologies.

Both of these concepts are major and respectively disrupt a lot of old truths and assumptions on how we build, run, and test software. Being adept at these styles and technologies is something that does not come from prior experience with virtual machines and classic architectures — you will have to take the time to be proficient in them. Also, respect that they change the testing game a bit (though not always in the expected ways). In our context, we'll assume both microservices and serverless so I'll do my part in training you.

:white\_check\_mark: **Testing benefits:** Certain types of tests get more important than others, which may lead to focusing on lower-level tests (e.g. unit tests) over more complicated types. Also means greater separation between "your code" and infrastructural stuff from the cloud host – don't test third-party code.

## **Consider TDD**

Maybe you are surprised this is at the bottom?

Personally, I have never tried hard enough to make [Test-Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html) (TDD) “my” game, and I think most folks are in unison that as long as there is the required degree of testing done _before_ actually committing your code, then everyone wins. I am personally happy to write tests and then do any minor mods to my code, _after_ writing the first complete-ish bit of code.

**The main point is that there is, at a minimum, always sufficient testing and that it happens close in time to the writing of the code.**

:white\_check\_mark: **Testing benefits:** You always have tests, code is structured to provide the absolute essence of the need/use case.
