---
description: "The dread from beyond the context –\_a reality, not a horror film."
---

# Handle and mock side effects

Concerns around side effects, such as polluting a production database with test data or calling rate-limited and pay-by-use third-party services come up pretty swiftly in conversations around testing and test practices. All of these are valid concerns. The real question isn't "can we test this thing?", it's "how do we minimize unintended side effects?".

A _side effect_ is that something somewhere happened or changed. An **intended side effect** can be that a function should write a file to disk. An **unintended side effect** could be that calling a function also sends an event to a hardcoded event bus residing in a production environment; this event may not be part of a test's scope, but it is part of the deeper functionality of the system under test. In other words, it's "needed" for our functionality, but is not something our test cares about. This is solid and sound advice, given that [we don't want to test I/O](https://medium.com/@\_ericelliott/yes-i-am-clearly-saying-dont-unit-test-i-o-but-not-just-in-js-in-any-language-ca8ce5016942) and our [test scope should be limited to their correct boundaries](https://blog.cleancoder.com/uncle-bob/2017/05/05/TestDefinitions.html).

{% hint style="info" %}
Some further reading on the subject includes:

* [Wikipedia: Side effect (computer science)](https://en.wikipedia.org/wiki/Side\_effect\_\(computer\_science\))
* [What is a "side effect?"](https://softwareengineering.stackexchange.com/a/40314)
* [The Not-So-Scary Guide to Functional Programming](https://www.yld.io/blog/the-not-so-scary-guide-to-functional-programming/)
{% endhint %}

We will touch on a whole spectrum of ways to handle mocks and side effects:

* Letting the code be test environment aware
* Abstractions and dependency injection (Test doubles)
* Consumer side mocks
* Provider side mocks
* Ephemeral (temporary) environments
* Tagged data

## Temporary environments

A spin on the "test environment" idea would be to have a temporary environment and make sure your code is hooked up in a way that uses only infrastructure linked to that particular one. This way, when the tests are over, no data is retained and there is no leakage outside of the containment area. However, this will be slower, and depending on your level of determination, you should probably keep this environment only for your service.

While this sounds like an easy solution, you will have to model your code to use environment parity which might not be the way you have resolved relations, paths, and URLs before.

## Tagged data

This solution uses the regular production or shared environment to run in, but you tag data (messages, stored items, etc.) with some type of marker or information that your systems will know means it's test data and that it can be removed. Also, you should ensure such data is never read back by any other system.

This solution, again, sounds pretty easy to implement but will require _knowledge_ of this whole mechanism elsewhere, creating undue risk.

Now for the better options.

## Mocking

> In object-oriented programming, mock objects are simulated objects that mimic the behaviour of real objects in controlled ways \[...]
>
> — Wikipedia: [_Mock object_](https://en.wikipedia.org/wiki/Mock\_object)

This seems like a good idea (and is, to some extent), but you'll have to be careful with mocks. Mocking may create very real coupling issues and harm later refactoring. As Philippe Bourgau writes in [_Careless Mocking Considered Harmful_](https://philippe.bourgau.net/careless-mocking-considered-harmful/)_:_

> The test initialization code was getting longer and longer, as it included a lot of mock setup. This made the tests more complex and less readable. It also made them unreliable, as it was not rare for all my unit tests to pass while the system was not working. I was taking the habit of running my end to end test more and more often. I was also losing a lot of time maintaining the mock setup code in line with the real classes. Mocks also tricked me into the bad practice of keeping a 1 to 1 mapping between code and test files. That again increased my maintenance burden when moving code from one file to another.
>
> It reached a point where I could not take it anymore.

A nod to his title, we don't want to be careless.

Let's say you have built a pizza delivery service and you want to test the functionality for receiving an order. You have a dependency in your code on both some persistence infrastructure (a database) and messaging infrastructure (a pub/sub topic).

Are you now required to deploy this whole shebang to some test environment and then run integration tests on it to see if it works? No, you don't.

Given that the infrastructure is something built by someone else (for example your cloud hosting company or if it's some infra that another team manages) then **it is effectively out of scope for your tests**. But how do you tell your code this?

### Dependency injection

We can use dependency injection (to pass in for example our database/message bus instance into the higher-level object) and/or differentiate between [abstractions and concretions](https://khalilstemmler.com/blogs/typescript/abstract-class/) to enable a composable, classic way to handle this. Our controller or handler functionality could therefore be tested with a faked or stubbed version of our persistence infrastructure, and outward there would be no change. As far as possible, this is what I would recommend you do. You approximate the expected behavior and since the boundary of the test does not concern the database (etc.) then you are well within your remit to set the boundary here.

```typescript
interface Database {
  store(pizza: string): void;
}

class LocalDatabase implements Database {
  store(pizza: string) {
    console.log(`Storing order for a ${pizza}`)
  }
}

interface MessageBus {
   send(message: string): void;
}

class LocalMessageBus implements MessageBus {
  send(message: string) {
     console.log(`Sending message: ${message}`)
  }
}

class PizzaOrderService {
  private readonly  database: Database;
  private readonly messageBus: MessageBus;

  constructor(database: Database, messageBus: MessageBus) {
    this.database = database;
    this.messageBus = messageBus;
  }

  public order(pizza: string) {
    this.database.store(pizza);
    this.messageBus.send(`Order received for a ${pizza}`);
  }
}

const database = new LocalDatabase();
const messageBus = new LocalMessageBus();

// Injecting dependencies, service doesn't care if it's a mock or real
const pizzaOrderService = new PizzaOrderService(database, messageBus);

// Coded implementation isn't changed in any way
pizzaOrderService.order('Margherita');
```

That's a pretty conventional and completely frameworkless way to deal with that problem. This method could be dubbed the _consumer side mock_, since you had to take the time on your end to create this solution, despite the dependencies being built by someone else.

### Provider-side mocks

This isn't something I have any real experience with, but anecdotally I know of dependencies/services that do provide mocks _to you as a user_ that you can use. Regardless of the level of fantasy here, that's definitely an interesting idea but don't expect to see it used a lot.

### Being "test-aware" and leaking test details to code

What if you want to test some implementation logic on the code that wraps the infrastructure? One way I deal with this, and this isn't very orthodox, is to actually check whether this is running in a test or not.

```typescript
class RealMessageBus implements MessageBus {
  send(message: string) {
    // Do lots of things here that we actually want to test, then... (indicative example)
    if (process.env.NODE_ENV !== 'test') messageBusDependency.emitMessage(`The actual message that goes to a real piece of infrastructure`);
  }
}
```

While this specific solution is particular to Node, the general idea holds. I've found it very common, despite good clean code and separation of it, that we might want to test stuff on an implementation, except of course we don't want it to persist anything or start creating side effects.

You'll also, in the case of the database mock/fake, want to return some data back.

I've never had this do something it shouldn't but I also know that we are stretching the rules and principles a bit here. Given the method's effectiveness, the big drawback is that we leak some of the test structure into your code. If you can accept this trade-off, then it's a very easy and pretty safe way to really test _all of it_ minus the bad bits (side effects).

### Libraries when mocking is actually useful

It pains me to use a mocking library, but if you have something that actually works, it's worth using. For me, the clearest use of library-provided mocking is for networking and overall just getting a truthful sense of how a piece of third-party infrastructure works. I'm very hesitant to use it for more detailed work, rather it's mostly to ensure I leak less of the testing logic to my implementations.

A library that I've had good use of is [MSW](https://mswjs.io) with its network mocking capabilities (fetching data), so one can effectively test network interactions (responses/requests) in a very simple way because these can be more painful and boilerplate'y to mock.

While _it is a mocking library_, I've found [aws-sdk-mock](https://aws.amazon.com/blogs/developer/mocking-modular-aws-sdk-for-javascript-v3-in-unit-tests/) to be a reliable and powerful way to mock out AWS infrastructure.

You should be able to mock manually or automatically in most testing frameworks. If you use Jest, there is [`jest.mock()`](https://jestjs.io/docs/mock-functions).
