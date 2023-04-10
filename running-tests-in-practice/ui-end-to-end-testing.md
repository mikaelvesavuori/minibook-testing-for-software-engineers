# UI end-to-end testing

{% hint style="info" %}
**Surface area**

Theoretically the entirety of a system, but most critically its UI layer

**Confidence level**

Medium-high

**Granularity**

Minimal

**Pros**

* Gives a user-oriented and functional understanding of whether or not flows and functionality work as intended
* Tooling is getting better so there are some tools today that are easy to use and work well

**Cons**

* Cumbersome to write, many steps needed to reproduce a user flow
* Will use actual hardware and software so there will be side effects
* Notoriously brittle tests
* Encourage incorrect test boundaries (i.e. testing things that change naturally over time)
* Expensive
* Long test times
* If something breaks or fails, there is effectively no good way to granularly understand _why_ it broke
* Depends on all or most of a system to be deployed and functional
* Many older tools are particularly brittle and poor in DX
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Consider doing this only if you have these circumstances:

1. There is a significant UI component that exercises multiple "deep" or unknown backend systems that you do not control.
2. You have a very clear end state for success or failure, ideally without relying on privileged access to databases or other infrastructure for verification.
3. Your team has more experience with UI-based testing than programmatic testing.
4. You are able to use a stable environment:
   1. Without contaminating environments with side effects (generated test data);
   2. Without requiring multiple teams to co-deploy and/or induce code freeze or environment freeze.
{% endhint %}

End-to-end testing ("E2E") is the customer-facing , often graphical, part of the flow: In other words, just as a user will experience it. Focus therefore on user flow. That means setting up _automated_ browser testing.

If you just came from the previous page about system testing, I explained that UI end-to-end testing and system testing share in common that they use the full scope and real infrastructure to assess functionality. Where system testing leaned toward using the extremes of the system landscape—such as the API as an entrypoint and the database as the final output—E2E will do much the same but through the same means as the user has to their disposal: That is, through the user interface.

While frontend-centric testing has not been given a lot of place in this book, it's smart to keep in mind that the same overall model and guidance is valid here too:

* **Logic in your app should use unit tests**. It's not uncommon to see front-end developers bake logic into hooks and other "harder to test" parts of their apps. As always, appropriate tooling here includes [Jest](https://jestjs.io), [Ava](https://github.com/avajs/ava), [React Testing Library](https://testing-library.com/docs/react-testing-library/intro/) and even the [Node 18+ native test runner](https://codewithhugo.com/node-test-runner/). Really: The dumber => the faster => the better, when it comes to this category.
* **Use** [**snapshot testing**](https://blog.bitsrc.io/whats-snapshot-testing-and-can-we-use-it-for-frontend-f5a441cae27a) **or** [**visual testing**](https://www.chromatic.com/) **for the visual components**. Of course, _not_ testing the visuals would never work in the front-end world! This is well-worth it, especially if you have a [good review process in place](https://medium.com/user-experience-design-1/continuous-design-and-how-to-enable-it-79863eeca0f7).
* **Keep E2E tests, integration tests etc. at a minimum, i.e. as per the "top of the pyramid"**. If you've already ensured that the logic is correct, that your visuals aren't broken, and the required testing is done in the API or back-end layer, why would you need to do more? If you have a compelling answer, then by all means add a dash of these here, but don't do it unless you know exactly what you are hoping to uncover.

## E2E and its bad reputation

So why exactly is E2E problematic in terms of testing?

Firstly, it comes down to the key premise that a test should use the simplest form of verification possible without sacrificing confidence. I've spent quite some time in this book arguing how even quite complex phenomena can be tested accurately with "simple" testing types like unit tests. Testing closer to the code also gives you a better understanding of its failure modes.

Secondly, E2E tests are (in theory) no more than scripted manual user interactions. In fact, usually, the E2E testing tool will fire up a browser and quite literally reproduce the steps you've given it. Even if this is a computer doing the dirty work, you have all of the same waiting for the process to start, for things to load, for things to get ready, for performing interactions, and so on. Also, it's not uncommon that tests become brittle as UIs can change a lot (_high volatility_), even without the functional flow or requirements changing at all. Tests that are ill-devised will break on these otherwise unproblematic changes.

On the flip side, when done well and selectively, they are a very close approximation of the actual, real user experience. Also, using modern tools like Codecept make this form of testing more stable than before and we can even run it "headless" to make it faster and less dependent on the visual elements (as there is no human in the loop, anyway!).

Importantly, from our test models, we need to recall and respect the highly selective approach to these tests, as well as not simply copy-pasting tests that are already confidently covered in better, easier, and cheaper tests.

Does it ever make sense to _start_ with E2E tests? Well, given a situation in which there is a heavy—and maybe even troublesome—separation between a front-end team and the API or back-end team—and especially if the overall solution has no (or very low) test coverage—it may be an efficient and somewhat easy move for the front-end team to set up E2E testing. This could cover the key flows if the API is not documented or easily testable, and if there is no other surface area that can have deep, informed testing; Unit testing and any of the "cheaper" and more exact tests require you to have direct access to it. Without access, and certainly, if the code is in someone else's responsibility and/or domain, then you might need these other mechanisms of ensuring confidence. In this situation, **we could think of end-to-end-testing being a transitional form of activity, rather than the ultimate end goal of any testing**.

## Using Codecept to do UI testing

Traditionally [Selenium](https://www.selenium.dev/) has been a big name, but a lot has happened in recent times (once again), so consider [Codecept](https://codecept.io/), [Playwright](https://playwright.dev), [Cypress](https://www.cypress.io), or other newer tooling. For our own example, it'll be using Codecept.

The following example is from my [microservices testing workshop](https://github.com/mikaelvesavuori/microservices-testing-workshop/blob/master/\_\_tests\_\_/e2e/frontend\_test.js) on GitHub.

We have a feature in the system which is called "Order items". The first scenario we will use is named "Order single item as US B2C customer". Give the following a read and see if you can figure out what's going on!

{% code lineNumbers="true" %}
```javascript
Feature("Order items");

Scenario("Order single item as US B2C customer", ({ I }) => {
  I.amOnPage("http://127.0.0.1:8000");

  I.seeElement('//button[contains(., "300 $")]');
  I.click("300 $");
  I.see("1", "#cart-itemcount");
  I.click("#cartsymbol");

  I.fillField("#input-name", "Anders Andersson");
  I.fillField("#input-street", "Thisway 123");
  I.fillField("#input-city", "Göteborg");
  I.fillField("#input-email", "someone@nowhere.xyz");
  I.fillField("#input-phone", "123123123123");

  I.click("Submit order");
  I.see("Thank you for your order!");
  I.seeInCurrentUrl("/success");
});
```
{% endcode %}

As you see, the format is very close to what you would expect of [behavior-driven development (BDD)](https://en.wikipedia.org/wiki/Behavior-driven\_development). That's definitely a nice thing as it makes for more concise and readable code. In this example, you've probably understood that we are acting on a locally running copy (telltale sign in the URL, `127.0.0`.`1:8000`) and that our script is performing a sequence of activities:

* Finding a button element with a value, `300 $`
* Clicking the button
* Seeing the status of the cart inventory update
* Clicking the cart symbol
* _(we get moved to a second page)_
* Fill in several fields
* Click to submit the order
* Verifying the finished state by checking the confirmation message and final URL which has now changed

Once the script is running, the browser (headless or regular) will receive the programmed inputs and the test will break if something is not possible to do, or if the assertion is not met.&#x20;

## Good or bad demonstration?

Well, you might ask, is this a good or a bad test? I would argue that this one is serviceable but not more than that. We still have a number of highly volatile assertions—such as the exact contents of texts and the exact classes or IDs of fields—and we could fail on incredibly minor changes. Also, since modern frameworks and bundlers often have auto-generation of CSS class names, they might not even be easily accessible or stable over time. While this can be solved as we did here, using IDs (which don't get replaced), I think it should be even clearer now for you how E2E tests dramatically decrease the stability of the underlying test compared to most of the other test types we've seen, already in a basic demonstration such as this one.

As far as I am concerned, the biggest thing we might have wanted to do in order to make the test better is to remove any assertions regarding exact text contents, as these are always more volatile than IDs.

## Remaining scenarios

Next up, we'll add the two remaining scenarios we have in the application.

```javascript
// Continuing on the first example
Scenario("Order single item as US B2B customer", ({ I }) => {
  I.amOnPage("http://127.0.0.1:8000");

  I.seeElement('//button[contains(., "150 $")]');
  I.click("150 $");
  I.see("1", "#cart-itemcount");
  I.click("#cartsymbol");

  I.selectOption("#customer-type", "B2B");
  I.fillField("#input-orgnumber", "123123123");

  I.fillField("#input-name", "Anders Andersson");
  I.fillField("#input-street", "Thisway 123");
  I.fillField("#input-city", "Göteborg");
  I.fillField("#input-email", "someone@nowhere.xyz");
  I.fillField("#input-phone", "123123123123");

  I.click("Submit order");
  I.see("Thank you for your order!");
  I.seeInCurrentUrl("/success");
});

Scenario("Order single item as MX B2C customer", ({ I }) => {
  I.amOnPage("http://127.0.0.1:8000");

  I.seeElement('//button[contains(., "150 $")]');
  I.click("180 $");
  I.see("1", "#cart-itemcount");
  I.click("#cartsymbol");

  I.selectOption("#country", "MX");

  I.fillField("#input-name", "Anders Andersson");
  I.fillField("#input-street", "Thisway 123");
  I.fillField("#input-city", "Göteborg");
  I.fillField("#input-email", "someone@nowhere.xyz");
  I.fillField("#input-phone", "123123123123");

  I.click("Submit order");
  I.see("Thank you for your order!");
  I.seeInCurrentUrl("/success");
});
```

{% hint style="info" %}
It's possible all of these three scenarios could somehow be more DRY and programmatic, as the majority of their steps are similar. At least they are pretty easy to follow.
{% endhint %}

Nevertheless, we can see the neatness that a well-functioning E2E test can exert, as well as providing a very good sense of the software doing what it should.

## In closing

Given adequate DRYness, precise use of tests, selection of a good tool, and asserting stable (low-volatility) behaviors, then end-to-end testing can definitely work as an OK transitional test type, at least until we can push toward lower-level testing. Don't stop with this!
