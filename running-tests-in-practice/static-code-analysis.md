# Static code analysis

{% hint style="info" %}
states**Surface area**

Code

**Confidence level**

High, in the local code scope

**Granularity**

Very high precision but does not say much about the "big picture"

**Pros**

* Cheap (typically)
* Fast
* Mostly unobtrusive
* Relatively uncontroversial
* Helps streamline and enforce conventions

**Cons**

* Does not test logic, just the line-by-line syntax
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Always.
{% endhint %}

Static code analysis is a total game-changer for writing better, more readable code. It is a wide category of tools—some are commercial and run only within third-party products (such as [DeepSource](https://deepsource.io) or [CodeScene](https://codescene.com)) and others are IDE-centric and open-source, like [Checkov](https://www.checkov.io). Most commercial tools will bundle open-source packages into a more comprehensive bundle.

{% hint style="info" %}
Note that the commercial model does not preclude something running (or not) in an IDE!
{% endhint %}

The really big use case for a static code analysis tool is to offload menial, tedious work. Candidate activities for this category include:

* "Linting" code, finding problematic patterns such as security issues.
* Standardizing code formatting, such as spacing, tabs, line wrapping...
* Checking files for misconfiguration or problems.

By having configuration files in the repository your tooling will pick up the rules from them and apply them, meaning that in a team the same rules will apply to everyone. For the question of when to run them, I recommend:

* Apply the tools on each save; this works well with [ESLint](https://eslint.org) and [Prettier](https://prettier.io), for example. This allows you to receive instant feedback as you work.
* Run the tools (and any other testing) in a pre-commit hook, for example with [husky](https://github.com/typicode/husky), so that you won't miss anything before committing the code.
* Run the tools as part of your CI/CD pipeline as part of your baseline tests.

{% hint style="info" %}
See [here for an example](https://github.com/mikaelvesavuori/mikrolog/blob/main/.husky/pre-commit) of what a husky pre-commit file might look like.
{% endhint %}

## Using ESLint and Prettier in the IDE

Probably the most common tooling for a TypeScript developer will be ESLint and Prettier. I will show you how I typically set them up for a project.

ESLint is a "pluggable linting utility" that offers rich functionality to detect issues and fix problems with your code in a deeply configurable way.

Prettier is, on the other hand, an "opinionated code formatter". There is a good article by its team called [Why Prettier?](https://prettier.io/docs/en/why-prettier.html) that does a good job of explaining the pains of code style guides and why Prettier is a good and painless way to get most of the benefits without the brutal work that it takes to get to such style guides. The most significant thing about Prettier is that it also reformats any code (on save) so you literally never have to go in and manually edit things to follow the conventions.

### Installing and configuring

We will first install the required packages as developer dependencies. I will assume you already have TypeScript installed as a developer dependency. Run:

```bash
npm install prettier eslint eslint-config-prettier eslint-plugin-prettier @typescript-eslint/eslint-plugin @typescript-eslint/parser --save-dev
```

{% hint style="info" %}
Read more about the other packages here:

* [eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)
* [eslint-plugin-prettier](https://github.com/prettier/eslint-plugin-prettier)
* [@typescript-eslint/eslint-plugin](https://www.npmjs.com/package/@typescript-eslint/eslint-plugin)
* [@typescript-eslint/parser](https://www.npmjs.com/package/@typescript-eslint/parser)
{% endhint %}

It's not a very lean list, I'll give you that, but it's a bit of a headache to always get these tools to work together as expected without some extra plumbing.

Now to create a configuration file, `.eslintrc.json` in the root of your project:

{% code title=".eslintrc" %}
```json
{
  "plugins": ["@typescript-eslint"],
  "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": 2022,
    "sourceType": "module"
  },
  "rules": {
    "@typescript-eslint/no-explicit-any": ["off"],
    "@typescript-eslint/ban-ts-comment": ["warn"],
    "complexity": ["warn", { "max": 9 }],
    "no-async-promise-executor": ["off"],
    "no-prototype-builtins": ["off"],
    "no-useless-escape": ["off"]
  },
  "env": {
    "node": true,
    "es2022": true
  },
  "ignorePatterns": ["node_modules/**"]
}
```
{% endcode %}

Here we are using `@typescript-eslint` as a plugin so we can use ESLint with TypeScript. We set the parser to a modern ECMA version and extend the ruleset of `prettier` itself and the recommended subsets of two of our plugins. This all gives us a stable foundation to work with.

Next, we set a few rules of our own: We disallow warnings for the `any` type (so we _can_ use it when/if needed) and we turn down the `ban-ts-comment` rule to only being a warning (as we can make good use of that in negative tests when we want to test dumb inputs). Then we set a cyclomatic complexity threshold at 9 so we get warnings for anything that might look like a mess.

Finally, we set the environment to understand that we are in a backend/Node context.

Those are the basics of ESLint in TS!

The same story goes for the Prettier config—but a lot easier—at `.prettierrc`:

{% code title=".prettierrc" %}
```json
{
  "useTabs": false,
  "printWidth": 100,
  "tabWidth": 2,
  "singleQuote": true,
  "trailingComma": "none"
}
```
{% endcode %}

This should all be quite apparent in how we want Prettier to work its magic.

{% hint style="warning" %}
You may need to restart your editor for changes to be picked up.
{% endhint %}

### Demoing some of the basic functionality

With the tooling in place, you can now try things like:

```typescript
const message = "Hello world!";
```

Which upon saving now magically turns into:

```typescript
const message = 'Hello world!';
```

This is Prettier in action.

Or how about very long, ugly lines like:

```typescript
function willBeBrokenIntoShorterLines(param1: string, param2: string, param3: string, param4: string, param5: string, param6: string) {
  console.log(param1, param2, param3, param4, param5, param6);
}
```

Which now become broken up into more readable, shorter lines:

```typescript
function willBeBrokenIntoShorterLines(
  param1: string,
  param2: string,
  param3: string,
  param4: string,
  param5: string,
  param6: string
) {
  console.log(param1, param2, param3, param4, param5, param6);
}
```

Let's look at the cyclomatic complexity check provided by ESLint.

```typescript
function overlyComplex() {
  if (1 > 2) console.log('');
  if (1 > 3) console.log('');

  if (2 > 1) console.log('');
  if (2 > 3) console.log('');

  if (3 > 1) console.log('');
  if (3 > 2) console.log('');

  if (3 > 1 && 2 < 3) console.log('');
  if (2 > 1 && 1 < 3) console.log('');
}
```

This will now trigger a warning: `Function 'overlyComplex' has a complexity of 11. Maximum allowed is 9.` You might want to set a different limit than 9, and you might want to use actual errors rather than warnings. I've used the value 9 myself after seeing that CodeScene has gone with that specific number, and those are some pretty smart people.

The tools displayed here are boring, which is a good thing. They make your life, and those reading and working on your code, a little better and that goes a _long_ way.

## As a tool during CI

As mentioned previously, you can typically run this category of tools in CI as well.

However, some _are only available to run as a CI integration_ and aren't available as an offline/IDE tool. This may be for a variety of reasons, including that the analysis stage may not be something you can do in a few seconds. As an example of this, it is typical that dynamic code analysis may even take hours to complete.

A central benefit of a tool external to the IDE is to get a consolidated surface to see code quality metrics, statistics, states, and trends. This makes even more sense when you have a bigger engineering organization.

Some examples of commercially provided tools include (in alphabetic order):

* [Codacy](https://www.codacy.com)
* [CodeScene](https://codescene.com)
* [Codiga](https://www.codiga.io)
* [DeepSource](https://deepsource.io/gh/mikaelvesavuori)
* [Embold](https://embold.io)
* [SonarCloud](https://www.sonarsource.com/products/sonarcloud/)

{% hint style="info" %}
The tools in the above list all offer generous free (or open-source-focused) plans, so go ahead and try a few of them.

Some of them are possible to run in the IDE or with local analysis as well.
{% endhint %}

There are certainly _a lot_ more tools, and because code quality is a very wide umbrella you will find all kinds of varieties of tools that for example may focus more on delivery and issue tracking ([LinearB](https://linearb.io)) or security ([Veracode](https://www.veracode.com)). The above, however, does represent more developer-centric products with a clear focus on the actual code-level details.

Integrating a tool, like those mentioned above, is usually not a very taxing thing if they provide ready-to-use integrations for your version control system (GitHub, BitBucket, GitLab...). After integrating, my recommendation would be to run the testing tool on any commits or pull requests as early as possible.

There's no reason to go into more detail on that here as it will depend entirely on the tool and version control system. The important thing is to actually run the tools if you have them!
