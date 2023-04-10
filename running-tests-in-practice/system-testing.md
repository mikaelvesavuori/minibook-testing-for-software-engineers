# System testing

{% hint style="info" %}
**Surface area**

Theoretically the entirety of a system

**Confidence level**

High

**Granularity**

Low-medium

**Pros**

* Cheaper and faster than the UI-based approach
* Possibility to slice and scale as needed

**Cons**

* Similar drawbacks to UI end-to-end testing
* Most likely the slowest test of any category
* Requires a fully deployed and functional system or set of systems
* Will create side effects
* Requires writing custom logic as there is no conventional or easy tool-based way to exercise a system (or set of systems) in the precise manner you will want
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Consider doing this only if you have these circumstances:

1. There is a significant "entry" component that exercises multiple "deep" or unknown backend systems so you know precisely that the required systems are in scope.
2. You have a very clear end state for success or failure, ideally without relying on privileged access to databases or similar for verification.
3. You are able to use a stable pre-production environment:
   1. Without contaminating environments with side effects (generated test data);
   2. Without requiring multiple teams to co-deploy and/or induce code freeze or environment freeze.
{% endhint %}

{% hint style="info" %}
The nomenclature regarding system vs end-to-end tests can be confusing. A meaningful distinction would be that, commonly, system testing is for the entirety of a single system, whereas end-to-end testing could encompass multiple systems. Also, it's at least implicit in many contexts that end-to-end tests are primarily driven through visual means, such as through the user interface.

However, I don't necessarily agree that it's a hard and fast rule that a system test could not interact with more than a single system.

Instead, the bigger problem is: Where is the ownership (and code!) located for a huge test (or set of tests) that is not restricted to a clean-cut, perfectly isolated service?
{% endhint %}

System testing seems to go into the bucket of tests that people hope will cover everything: "We need to ensure all of it works together!". While I personally see a lot more traction regarding UI end-to-end testing, this one can be useful to have some mental construct for.

We can immediately assess that this is a **functionally oriented** test type—we care about our particular use cases and that they work as expected. As opposed to other lightweight tests, this test will indeed use actual infrastructure. The test will most likely not happen in a production environment, but absolutely could if you want it to.

I’d recommend seeing the system test as a “fully functional”, API-driven backend-oriented way of testing expectations. Focus on raw functionality, sort of like an extended integration test. For example, in a system that has four components that are sequentially called and end with data in a database, you could call the main entry point and verify that data ended up correctly stored.

It's easy to hate a type of type that's as extreme as this one, but there are _some_ good sides:

* It enables a high degree of confidence.
* It will (hopefully) function exactly as expected.
* Having one or more system tests can help codify the _totality_ of a use case (in a bizarre sense improving technical documentation!).
* By automating this/these tests and crafting them well, you can indirectly improve your stance on disaster recovery and full-environment recovery.

## A partial example of a system test

In this example from a [microservices testing workshop](https://github.com/mikaelvesavuori/microservices-testing-workshop) I ran, we have an order system that we want to verify completely, from the external-facing API Gateway to fully formed orders residing in the order database.

The solution will be to use the exposed endpoint, calling it with fake data, and then verifying that the exact number of items exist and look as expected. Even in this extreme test, we do not need to check things like if the AWS libraries work if an event bus got a message, or if it will be raining tomorrow—we _know_ that it works because _any failure_ along the way will mean we are unable to correctly retrieve the data.

{% code title="__tests__/system/index.ts" lineNumbers="true" %}
```typescript
import { verifyData } from './verifyData';
import { createOptions, callService, createDummyOrder, sleep, generateTestId } from './utils';
import { getEndpointConfig } from './config';

/**
 * Environment
 * Update this to your values
 */
// API Gateway ID
const ID = 'abcde12345'; // {{UNIQUE_ID}}
// AWS region
const REGION = 'eu-north-1';
// API Gateway stage
const ENV = 'dev';

/**
 * Test configuration
 * Modify if you want, or just leave it be
 */
// ID for this specific test run
const TEST_ID = generateTestId();
// Time to wait until first verification
const WAIT_TIME = 5000;
// Time to wait until running next iteration in call-loop
const LOOP_WAIT_TIME = 150;
// How many test orders to produce
const TEST_COUNT = 25;
// Test can be an empty object, in which case defaults will be used
const TEST_CONFIG = {};

/**
 * @description Full system test, starting with a number of test orders and verify presence in order database at the end.
 */
async function SystemTestController(testCount = 1, customerData = {}) {
  console.log('Generated test ID is', TEST_ID, '\n');

  try {
    /**
     * Get endpoints for our environment
     */
    const { createOrderServiceEndpoint, getOrdersServiceEndpoint } = getEndpointConfig(
      ID,
      REGION,
      ENV
    );

    /**
     * Loop-call service
     */
    for (let i = 1; i <= testCount; i++) {
      const order = createDummyOrder({ ...customerData, testId: TEST_ID });
      const resp = await callService(createOrderServiceEndpoint, createOptions('POST', order));
      await sleep(LOOP_WAIT_TIME);
      console.log(i, order, resp, '\n');
    }

    /**
     * Wait for events to have settled a bit
     */
    await sleep(WAIT_TIME);

    /**
     * Get data
     */
    const dbData = await callService(
      getOrdersServiceEndpoint,
      createOptions('POST', {
        testId: TEST_ID
      })
    );

    console.log('Count of items is', dbData.Count);

    /**
     * Verify
     */
    verifyData(dbData, testCount, TEST_ID);
  } catch (error) {
    throw new Error(error);
  }
}

// Run tests
SystemTestController(TEST_COUNT, TEST_CONFIG);
```
{% endcode %}

Data is tagged as test data and the application is rigged to never read any of the generated data.

We use a [test data builder](http://www.natpryce.com/articles/000714.html) pattern, calling `createDummyOrder()` to create valid order data, and utilizing the [Faker library](https://fakerjs.dev) to set parts of our data randomly and dynamically.

```typescript
/**
 * @description Create (test) order
 */
export function createDummyOrder(customerData) {
  let {
    name,
    email,
    phone,
    street,
    city,
    customerType,
    market,
    products,
    totalPrice,
    orgNumber,
    testId
  } = customerData;

  const MARKET_US_QUOTA = 50; // US vs MX quota
  const CUSTOMER_TYPE_B2B_QUOTA = 20; // B2B vs B2C quota

  market = market
    ? market
    : (() => {
        const chance = Math.round(Math.random() * 100);
        if (chance <= MARKET_US_QUOTA) return 'US';
        return 'MX';
      })();

  customerType = customerType
    ? customerType
    : (() => {
        // Mexico is B2C-only
        if (market === 'MX') return 'B2C';

        const chance = Math.round(Math.random() * 100);
        if (chance <= CUSTOMER_TYPE_B2B_QUOTA) return 'B2B';
        return 'B2C';
      })();

  if (market === 'US') faker.locale = 'en_US';
  if (market === 'MX') faker.locale = 'es_MX';

  orgNumber = (() => {
    if (customerType === 'B2C') return 0;
    return orgNumber
      ? orgNumber
      : faker.random.number({
          min: 6,
          max: 20
        });
  })();

  const firstName = name ? name : faker.name.firstName();
  const lastName = name ? name : faker.name.lastName();
  email = email ? email : faker.internet.email();
  phone = phone ? phone : faker.phone.phoneNumber();
  street = street ? street : faker.address.streetAddress();
  city = city ? city : faker.address.city();
  products = products ? products : 'BB001,BA002';
  totalPrice = totalPrice ? totalPrice : parseInt(Math.round(Math.random() * 1500) + '00');

  const customer = {
    name: `${firstName} ${lastName}`,
    email,
    phone,
    street,
    city,
    customerType,
    market,
    products,
    totalPrice,
    orgNumber,
    testId
  };

  return JSON.parse(faker.fake(JSON.stringify(customer)));
}
```

As you see, the majority of the above code is simply the overall test controller function. For this particular system, it will create a configurable number of dummy orders, call the order service and pass in the data and cycle this loop for the number of times we need it to do the work.

{% hint style="info" %}
Dedicated functionality for verifying and creating data isn't part of the above snippet as these are entirely dependent on what exactly is required.

For more on this, you can check out the full code in the [associated GitHub repository](https://github.com/mikaelvesavuori/microservices-testing-workshop/tree/master/\_\_tests\_\_/system).
{% endhint %}

After having sent the data we will wait a short time to let the system settle and then call an endpoint that will retrieve our orders with the unique test ID. Our table structure is set up in a way in which items are possible to retrieve by this ID and by sharing it across multiple items for testing purposes we could theoretically do this test in a production environment without too much risk.

## In closing

System tests are like taking a tank to a gunfight. Yes, it's powerful, and not quite that different from integration tests, but it comes at the pretty steep cost of _touching everything_. As far as possible, gain the needed confidence by other means. But if you do it: Make it as slick and lean as you can!&#x20;
