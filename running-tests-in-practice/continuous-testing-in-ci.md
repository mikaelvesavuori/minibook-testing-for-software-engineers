---
description: >-
  The crowning jewel - doing all of that work (and more) with complete
  automation.
---

# Continuous testing in CI

This is the last page before we move into some concrete testing scenarios. Here is where we will see how to push what you've learned about practical testing into the CI context and get ready to industrialize your work.&#x20;

Google is great at development and I'm not going to be a stranger to a painfully long quote from [their own description](https://cloud.google.com/architecture/devops/devops-tech-test-automation) of [continuous testing](https://en.wikipedia.org/wiki/Continuous\_testing):

> The key to building quality into software is getting fast feedback on the impact of changes throughout the software delivery lifecycle. Traditionally, teams relied on manual testing and code inspection to verify systems' correctness. These inspections and tests typically occurred in a separate phase after "dev complete." This approach has the following drawbacks:
>
> * Manual regression testing is time-consuming to execute and expensive to perform \[...]
> * Manual tests and inspections are not reliable \[...]
> * Once software is "dev complete", developers have to wait a long time to get feedback on their changes \[...]
> * Long feedback cycles also make it harder for developers to learn how to build quality code, and under schedule pressure development teams can sometimes treat quality as "somebody else's problem".
> * When developers aren't responsible for testing their own code it's hard for them to learn how to write testable code.
> * For systems that evolve over time, keeping test documentation up to date requires considerable effort.
>
> Instead, teams should:
>
> * Perform all types of testing continuously throughout the software delivery lifecycle.
> * Create and curate fast, reliable suites of automated tests which are run as part of your [continuous delivery pipelines](https://continuousdelivery.com/implementing/patterns/#the-deployment-pipeline).
>
> Not only does this help teams build (and learn how to build) high quality software faster, DORA's research shows that it also drives improved software stability, reduced team burnout, and lower deployment pain.
>
> — [Google](https://cloud.google.com/architecture/devops/devops-tech-test-automation)

It's always a bit iffy to do long quotes, but I definitely believe that the above encapsulates a lot of the reasons why you want to do proper Continuous Delivery and not the traditional, ill-performing delivery models.

{% hint style="info" %}
Consider also reading:

* [Continuous Testing - Continuous Delivery](https://continuousdelivery.com/foundations/test-automation/)
* [Martin Fowler - Software Delivery Guide](https://martinfowler.com/delivery.html)
* Jez Humble & David Farley: [_Continuous Delivery: Reliable Software Releases Through Build, Test, and Deployment Automation_](https://www.goodreads.com/book/show/8686650-continuous-delivery)
{% endhint %}

With newer conventions like [GitFlow](https://nvie.com/posts/a-successful-git-branching-model/) and [GitHub Flow](https://docs.github.com/en/get-started/quickstart/github-flow), substantial deviations were made against the classical notion of Continuous Integration in which the trunk is always, continuously, integrated and ready to be deployed.

{% hint style="info" %}
See for example [Dave Farley's video providing some context on why GitFlow (and GitHub Flow) should be avoided](https://www.youtube.com/watch?v=JOr4QeIjyW4).
{% endhint %}

I won't provide too much bias henceforth, but we will keep our example as lean as we can as I can't provide all answers for all integration models.

## Scripting a CI pipeline to enable continuous testing

In the following example, we will use [GitHub Actions](https://github.com/features/actions)[ ](https://github.com/features/actions)to create a single pipeline that will take our code from commit to being deployed to production. Our theoretical application will be some sort of API.

The steps will be:

* Baseline activities
  * Install dependencies
  * Compile
  * Package
  * Test
  * Check for credentials leaks with [Gitleaks](https://github.com/marketplace/actions/gitleaks)
  * Check for license compliance with [license-compliance](https://github.com/marketplace/actions/license-compliance)
  * Run a vulnerability check with [Trivy](https://github.com/marketplace/actions/aqua-security-trivy)
  * Run a configuration check with [Checkov](https://github.com/marketplace/actions/checkov-github-action)
* Deploy a testing stack
* Test the stack
* Deploy the production stack

My tip is to always write in plain text—as above—what you want a pipeline to do before committing any work on it.

GitHub Actions is a powerful and easy way to do CI/CD. You can set up your jobs pretty much just as you need them and you don't have to care about hardware as GitHub will provide all of that for you. What you need to do is to provide a description of your desired workflow—this is what we'll work on now.

You can have multiple workflows, meaning you can (for example) conveniently support a dedicated workflow for pull requests, something for main branch commits, and something for hotfixes. Again, here we will consider a single, monolithic pipeline called `main.yml`.

The script we will look at is nothing magical, which is a good thing. Remember that when you are running a workflow on a remote machine it is a "stateless" activity so things like dependencies you might have locally will typically not exist. It also won't persist state or carry over across jobs (unless you [upload](https://github.com/marketplace/actions/upload-a-build-artifact)/[download](https://github.com/marketplace/actions/download-a-build-artifact) artifacts).

Enough said, time to check out the example.

{% code title=".github/workflows/main.yml" lineNumbers="true" %}
```yaml
on: [push]

jobs:
  #########
  # BUILD #
  #########

  build:
    runs-on: ubuntu-latest
    name: Build, package, and test
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Lint
        run: |
          npx eslint './src/**/*.ts' --quiet --fix
          npx prettier ./src --check
      - name: Compile
        run: npx tsc
      - name: Package
        run: npx sls package
      - name: Test
        run: npm test

  gitleaks:
    needs: [build]
    runs-on: ubuntu-latest
    name: Gitleaks
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      - uses: gitleaks/gitleaks-action@v2

  licenses:
    needs: [build]
    runs-on: ubuntu-latest
    name: License compliance
    steps:
      - uses: actions/checkout@v3
      - name: License compliance check
        uses: mikaelvesavuori/license-compliance-action@v1.0.2

  trivy:
    needs: [build]
    runs-on: ubuntu-latest
    name: Trivy vulnerability scanner
    steps:
      - uses: actions/checkout@v3
      - uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          format: 'sarif'
          output: 'trivy-results.sarif'
          severity: 'CRITICAL'

  checkov:
    needs: [build]
    name: Checkov
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Checkov
        id: checkov
        uses: bridgecrewio/checkov-action@master
        with:
          directory: src/
          quiet: true

  ###########
  # RELEASE #
  ###########

  deploy-test:
    needs: [build, gitleaks, licenses, trivy, checkov] 
    runs-on: ubuntu-latest
    name: Deploy to test
    steps:
      - uses: actions/checkout@v3
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN_TEST }}
          aws-region: eu-north-1
      - name: Deploy to test
        run: npm run deploy:test

  live-tests:
    needs: [deploy-test]
    name: Run live tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Run live tests
        run: |
          npm run deploy:test
          npm run test:smoke
          npm run test:integration
          npm run test:load

  deploy-prod:
    needs: [live-tests]
    runs-on: ubuntu-latest
    name: Deploy to production
    steps:
      - uses: actions/checkout@v3
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v1
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN_PROD }}
          aws-region: eu-north-1
      - name: Deploy to production
        run: npm run deploy
```
{% endcode %}

The most crucial concepts to understand now are:

* `on`: What can trigger this workflow? In this case, it's a "push" to any branch.
* `jobs`: Sets of actions that can be run. The jobs align well with our previous list of desired activities.
* `steps`: Defines the individual things happening, such as running a CLI command. Most jobs use ready-made Actions (see below) but a few use regular Bash/shell commands mapping to scripts defined in our assumed `package.json` file of this project.
* `uses`: Allows the use and reuse of, for example, Marketplace-provided Actions. This constitutes the majority of activities.
* `secrets`: Makes it possible to use concealed values that we want to use but not be able to read back in clear text. Great for stuff like passwords, credentials, or other sensitive values.

{% hint style="info" %}
To learn more about the specifics of GitHub Actions, turn to [Understanding GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions).

If you are interested in more about how the AWS credentials section works, [read this practical article by my colleague Elias Brange](https://www.eliasbrange.dev/posts/secure-aws-deploys-from-github-actions-with-oidc/).
{% endhint %}

Our script as per above would cover most of the essentials when it comes to quality-checking an API. Provided that our testing practices were half-decent we can now feel entirely confident in deploying directly into production without any further manual intervention.

## Need for speed: Pushing CI times

When we talk about CI/CD there is a widely known heuristic that we want testing and deployment to finish in no more than X number of minutes. You'll easily find numbers ranging from maybe 5 to 15 minutes. The exact number is not important—what matters is that you do what you can to ensure that you get the fastest possible feedback from testing your system.

Technical activities you can do include:

* **Add more/faster hardware**. However, this rarely increases performance in a meaningful way!
* **Migrate to faster deployment mechanisms**. If you are in the AWS world, you will know that CloudFormation can be a very slow experience. Migrating to other models, such as Cloudflare Workers might be a reasonable option for certain use cases; Changing could take you from minutes of deployment time to a few seconds. Of course, this is an extreme option, but keep it in mind when you are assessing architectures from a holistic, lifecycle perspective.
* **Don't wait for teardowns**. If you are using tools like Terraform, you can opt to not wait for the completion of removal/destruction tasks. This will save you quite some time if you are currently awaiting the results of such tasks in your pipeline.
* **Do less**. You can always choose to do less. Sometimes that makes sense. You can also look at a more elaborate deployment strategy, though I personally fear that could drive you from aiming for a simple and clean strategy which is a _better_ goal.
* **Deploy to fewer stages**. Related to the "slow deployment mechanisms" point, it makes sense to deploy to the minimum number of stages needed. Naturally, if it takes 2 minutes to deploy to a given stage, it's a matter of `<stage count> * 120 seconds`—yet another good argument for a [single environment setup](https://conferences.oreilly.com/velocity/vl-ca-2018/public/schedule/detail/66987.html).
* **Remove long-running and/or slow tests**. This is the thing _you absolutely should do_, and a great driver for deleting and/or migrating tests from the system/UI level to unit tests. You should expect at least 1000 unit tests per minute to run. You'd be happy to have 10 end-to-end tests run in that space.&#x20;

## Breaking down a big script into smaller ones

When a pipeline becomes unwieldy or you want to support many teams composing their own pipelines from smaller pieces you can turn to [reusable pipelines](https://docs.github.com/en/actions/using-workflows/reusing-workflows#example-reusable-workflow).

Using these reusable pipelines we can start disassembling our script and provide, as here, the overall build step as a single component.

{% code title="build.reusable.yml" lineNumbers="true" %}
```yaml
name: 'Build, package, and test an application'

on:
  workflow_call:

jobs:
  build:
    runs-on: ubuntu-latest
    name: Build, package, and test
    steps:
      - uses: actions/checkout@v3
      - name: Install dependencies
        run: npm ci
      - name: Lint
        run: |
          npx eslint './src/**/*.ts' --quiet --fix
          npx prettier ./src --check
      - name: Compile
        run: npx tsc
      - name: Package
        run: npx sls package
      - name: Test
        run: npm test
```
{% endcode %}

You'll need to make it available somehow. Let's assume you put these pipelines in a repository with such bits and bobs. Now, to use the above workflow from your calling (orchestrating) workflow, you can simply do:

{% code title="pipeline.yml" lineNumbers="true" %}
```yaml
name: 'Call reusable workflow'

on: push

jobs:
  build:
    uses: YOUR_USER/REPO_WITH_REUSABLE_WORKFLOWS/.github/workflows/build.resusable.yml@main
```
{% endcode %}

It's a nifty way to scale your CI/CD operations and to decouple your own work for that matter if you have a whole lot of repositories.

To summarize, without fully committing to a competent CI pipeline you won't be able to take advantage of all the benefits that modern software engineering and testing can provide you. I've heard horror stories of teams that attempt to set arbitrary limits of 80/20 test automation and use CI tooling that should have been considered useless even 10 years ago.

If you take anything from this book, then that should be how critical continuous integration (and testing) is for doing software engineering well.
