# 🤖 Towards modern testing practices

> To me, legacy code is simply code without tests.
>
> — Michael Feathers, [_Working Effectively With Legacy Code_](https://www.goodreads.com/book/show/44919.Working\_Effectively\_with\_Legacy\_Code)

While the previous section was devoted to delivering competent software engineering, this section proposes directions to thinking and practically conducting our testing.

The approach we should follow is called **continuous testing**, something which Google describes in the following way:

> **The key to building quality into software is getting fast feedback on the impact of changes throughout the software delivery lifecycle**. Traditionally, teams relied on manual testing and code inspection to verify systems' correctness. These inspections and tests typically occurred in a separate phase after "dev complete." This approach has the following drawbacks \[...]
>
> Instead, teams should:
>
> * Perform all types of testing continuously throughout the software delivery lifecycle.
> * Create and curate fast, reliable suites of automated tests which are run as part of your continuous delivery pipelines.
>
> Not only does this help teams build (and learn how to build) high quality software faster, [DORA](https://www.devops-research.com/research.html)'s research shows that it also drives improved software stability, reduced team burnout, and lower deployment pain.
>
> — [DevOps tech: Continuous testing](https://cloud.google.com/architecture/devops/devops-tech-test-automation)

In this section, we will address many of the concerns and theoretical parts relating to testing in a modern technical environment and how to actively move towards working with them. Among some of the topics, you'll get to know:

* What types of testing exist
* Some of the testing models and how they compare to each other
* What goes into writing a good test
* Why conventional wisdom on testing environments and test data is wrong
* Why testing serverless isn't that different, if at all
