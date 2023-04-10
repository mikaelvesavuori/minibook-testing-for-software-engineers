---
description: Putting some kind of definition on the table.
---

# What is a test?

Man. This question! It can only lead to something bad.

{% hint style="warning" %}
It should be clear that my goal is not to be authoritative, final, or encyclopedic in this book or in my definition of tests. However, so we have some common ground, I will lay down what I mean with them.
{% endhint %}

My main experience with building software is in the web development area.

{% hint style="info" %}
I have some experience with regulated industries as well and feel confident from my work in those areas that their _expected approaches_ are several years behind the curve. This helps neither the respective standards, the industries that are regulated, the products to be built, or ultimately, the customer or end-user. Of course, this also only makes things worse for the software engineer.
{% endhint %}

The following is the basic definition I would propose, being incomplete and all:

**A test verifies the actual performance of software against its expected functionality**. There exist different **testing types** that are each in their own ways capable of doing such verification. Such verifications apply a variety of levels of _knowledge of the software_ (code level, user level...) and expected _granularities_ (unit level, API level...) of the response to better determine whether or not the operation completed as expected.

{% hint style="info" %}
There are certainly more terms and concepts, such as test oracle, test harness, test suite, and many more. However, they are not _useful_ in common parlance.
{% endhint %}

The above definition _should_ adequately resolve most pragmatic concerns on the subject.

Also let's be clear that **tests are not technically hard nor should a good test be**.

Let's look at a unit test to clarify this point.

## A basic unit test

Our "[system under test](https://en.wikipedia.org/wiki/System\_under\_test)" (SUT) will be a function that adds two numbers and returns the result.

```javascript
function add(firstNumber, secondNumber) {
  return firstNumber + secondNumber;
}
```

And our test—for brevity written without any testing framework—is as simple as:

```javascript
const expected = 5;
const result = add(2, 3);

if (result === expected) console.log('Test passed!');
else console.error('Test failed!');
```

If you'd run this, you'll see that this will pass when given the numbers above.

But is this not over-trivializing testing? Yes and no.

* **No**, because, well, this is software development and things will undoubtedly grow to be more complex than the above. You'll also have to learn nitty-gritty details like how to deal with unreachable code lines, what to do about asynchronous failures, and such. Nothing that's impossible, but as always: The devil is in the details.
* **Yes**, because well-structured code (and code paths) and the **right type of test** using the **right boundary** will make testing marginally more difficult than the above example. A good test _is not_ a complex thing! More on that later.

## Is there a need for dedicated QA?

**No, I don't believe there is in most cases**, as long as you can work with relatively deterministic circumstances. I'd be less negative to dedicated QA in places where there is an extreme degree of emergent behavior and non-determinism, such as in the games industry and probably some very complicated parts of digital-physical products and manufacturing. Given that the majority of development does not happen in such places, I'd still propose "no" as the most sensible binary answer for most people.

As should be clear from the title of this book, the primary audience for this book are software engineers. In the spirit of Agile and actually well-working development strategies, **the quality assurance or testing role should be minimized and be an integrated concern in the day-to-day development and operations**. Experts are certainly always welcome, and definitely so when we are dealing with really tricky situations or when a team needs qualified coaching on conducting better testing. We also need to separate the roles of _subject matter experts_ to support us in better understanding the domain and how we implement it truthfully in the code. Given the choice of better subject matter experts that help the team write better, crisper, more testable code or simply having a tester whack out tests on a sidetrack to the regular development, then the better, crisper code (leading to easier testing, leading to testing being done at all, asking the dev team to write them) is always a better option.

## Checking vs testing?

This is a [red herring](https://en.wikipedia.org/wiki/Red\_herring) subject that seems to come and go.

[The "checking vs testing" issue](https://www.infoq.com/news/2009/12/testing-or-checking/) seems to have revolved around what we call _testing_ (more exactly, _automated testing_) actually being **semantically closer to **_**checking**_** than testing**. _Checking_ is said to be a rote task that a machine can do because we look at the known parts of a system. Testing is said to be something more complex and creative that only a dedicated expert (human) can do, and that testing fundamentally is about uncovering _unknown_ information about the system.

{% hint style="info" %}
The semantics should be similar enough to not really warrant this dialog, and I'm not really seeing a scenario in which we will be discussing _automated checking_ any time soon.
{% endhint %}

The real issue isn't about differentiating between non-creative labor and creative labor. The problem is that we also start creating a box into which we can put select people and roles. Hence we are potentially creating an ecosystem that is constrained to the testing community and not to the people who built the systems. There are some arguments why you might want someone else to inspect or fiddle with something you built, but we already have decades of talk about pair programming, pull requests, and mob programming to deal with that. Unless you are already running such practices it simply seems more reasonable to evolve towards ideals that have been proven to work, instead of setting up a testing gulag on the perimeters of your development team.

If you have something to win by pushing on the "human" card, then some will do it. :person\_shrugging:

{% hint style="info" %}
It seems to me the more useful and concrete outcrop of this led to the creation of the _exploratory testing_ approach which we will address later. That _actually_ has some (limited) value.
{% endhint %}

## Continuously testing your software

When we further extend the approach of having testing as part of our _definition of done_, then we've already started going down the path of _continuous testing_.&#x20;

> Many development teams now use a methodology known as continuous testing. It is part of a DevOps approach – where development and operations collaborate over the entire product life cycle. The aim is to accelerate software delivery while balancing cost, quality, and risk. With this testing technique, teams don’t need to wait for the software to be built before testing starts. They can run tests much earlier in the cycle to discover defects sooner when they are easier to fix.
>
> — [IBM](https://www.ibm.com/se-en/topics/software-testing)

This marks the natural progression of ensuring that the testing (partially or all, if possible) is done throughout the IDE development experience, through to pre-commit hooks, and then the CI environment itself running the full set of tests. Doing so creates a practically completely transparent view of the expectations put on your code and what it takes for it to go to production.

Given that tests are meaningful, truthful, and fast then we can start taking steps to even revamp how we do any manual verification whatsoever. And very soon, you might not need that, nor the complex array of environments, given that you've written more, better, faster, more truthful tests and run them all the time and continuously integrate your codebase several times per day.

That will be a true game changer for most people who might today be used to weird, breaking tests, many complex environments, manual verification, and other travesties of the trade. The best thing is: All of this is completely attainable.

{% hint style="info" %}
Some recommended reading:

* [Wikipedia: Software testing](https://en.wikipedia.org/wiki/Software\_testing)
* [Wikipedia: Software testing controversies](https://en.wikipedia.org/wiki/Software\_testing\_controversies)
* [Wikipedia: ISO/IEC 29119](https://en.wikipedia.org/wiki/ISO/IEC\_29119)
* [DevOps tech: Continuous testing](https://cloud.google.com/architecture/devops/devops-tech-test-automation)
* [IBM: What is software testing?](https://www.ibm.com/se-en/topics/software-testing)
{% endhint %}
