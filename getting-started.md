# 🤓 Getting started

You can deepen your experience by cloning, running, and learning from several reference projects (mentioned in `Project resources`).

{% hint style="info" %}
It's of course completely optional to actually run any of the projects. I will be using lots of examples from these projects, but nothing beats learning from hands-on experience.&#x20;

Regardless I think you'll gain something even if you decide to not run the projects.
{% endhint %}

Depending on the specific project you will have some variability in the exact details. Common factors among all of the reference projects are that:

* They are written in TypeScript
* They are using AWS' cloud infrastructure components, such as Lambda, EventBridge, DynamoDB, and more
* They are thoroughly tested using several types of tests
* The applications are composed using a microservices pattern

While they are all relatively small, most of them are not trivial or "dumbed-down". The displayed practices and solutions should easily carry across bigger, similarly well-structured codebases.

## Pre-requisites

Common prerequisites for the projects include having:

* [Node](https://nodejs.org/en/download/) 18+ installed (or a more recent version)
* [Git](https://git-scm.com/downloads) installed
* Optional: [Serverless Framework](https://www.serverless.com) installed (version 3 or later)
* [An Amazon Web Services (AWS) account](https://aws.amazon.com/free/)
* [AWS credentials available through your environment](https://www.serverless.com/framework/docs/providers/aws/guide/credentials/)
* Some familiarity with programming (Javascript/Typescript/Node) and command-line usage will be very helpful
* A modern IDE like [Visual Studio Code](https://code.visualstudio.com) installed
  * Consider useful VS Code plugins for [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) and [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
* For calling APIs manually, you might want to use a tool like [Insomnia Designer](https://insomnia.rest)

The projects should contain all other dependencies locally, so you shouldn't need anything else.
