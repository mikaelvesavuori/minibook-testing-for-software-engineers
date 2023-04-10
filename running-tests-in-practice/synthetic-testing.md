# Synthetic testing

{% hint style="info" %}
**Surface area**

Facade of your deployed service

**Confidence level**

Medium

**Granularity**

Low

**Pros**

* Roughly the same benefits as smoke testing
* Relatively easy to automate today with decent tooling
* Given correct thresholds and alerts this will give the only (so far) solution that sees the solution performing as expected over a longer timespan than conventional testing

**Cons**

* Roughly the same drawbacks as smoke testing
* Not well-understood
* Not standardized or strong conventions in place
* You should design your system to accept synthetic input without creating adverse side effects unless you really do want them
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

If you are very interested in knowing the continued functionality of something, and if there is considerable reason to believe the functionality is on a "sliding plane" of working or not, then you should consider this type of testing. Also, if you don't have consistent traffic or need to fake some traffic, then this might be a good option for you.
{% endhint %}

Synthetic testing sounds really cool but it's, as always, not quite so exciting. It's no more than plain old smoke testing with some small but important differences.

Synthetic testing and smoke testing are somewhat similar in that they are typically very basic assertions of an actually exposed system. Both types of testing can easily be done on a machine that you control or from a third party that provides such testing as a service. They are equally poor at addressing debuggable aspects as their respective responses will be no more or less than status codes or similar. Their commonality extends to both using "real systems" so you should be able to trust that both types of tests give you a fair sense of confidence.

The relative problems around synthetic testing seem to me to be mainly conceptual, rather than technical.

Where a smoke test is done _at the time of testing_ it cannot guarantee the continued functionality of a system. Using a scheduled synthetic test we can actually continuously verify it! Once again, let's turn to [Microsoft's Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/synthetic-monitoring-tests/) for some wisdom on how things change when we add a time aspect to the testing pyramid model:

> However, as more organizations today provide highly-available (99.9+ SLA) products, they find that the nature of long-lived distributed applications, which typically rely on several hardware and software components, is to fail. Frequent releases (sometimes multiple times per day) of various components of the system can create further instability. This rapid rate of change to the production environment tends to make testing during CI/CD stages not hermetic and actually not representative of the end user experience and how the production system actually behaves.
>
> For such systems, the ambition of service engineering teams is to reduce to a minimum the time it takes to fix errors, or the [MTTR - Mean Time To Repair](https://en.wikipedia.org/wiki/Mean\_time\_to\_repair). It is a continuous effort, performed on the live/production system. Synthetic Monitors can be used to detect the following issues:
>
> * **Availability** - Is the system or specific region available.
> * **Transactions and customer journeys** - Known good requests should work, while known bad requests should error.
> * **Performance** - How fast are actions and is that performance maintained through high loads and through version releases.
> * **3rd Party components** - Cloud or software components used by the system may fail.
>
> — Microsoft: Code With Engineering Playbook, [_Synthetic Monitoring Tests_](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/synthetic-monitoring-tests/)

By probing our applications with what seems to be actual, human traffic we can do more than just see "it worked" and start seeing it in more realistic, elaborate circumstances, such as for complex user flows. This is especially interesting in case you have an application in which there is low or even no real traffic, or if you have bursty traffic and need to verify things work in a low-traffic period.

Failures still need to be caught in your observability tool and applications, as ever, need to be built to a standard that affords rich and concise observability, i.e. making it possible for engineers to understand what went wrong, when, where, and why.

{% hint style="success" %}
Now would be a good time to also remind ourselves of how testing and observability are not the same thing but need to co-exist if we are to deliver applications at any acceptable modern level. If the probe fails we should expect that our tooling to alert our team of this unexpected, negative fallout.
{% endhint %}

But, why probe applications that have steady traffic? The perhaps most enticing reason would be that it should be easier to detect a direct relation between a failing test (that you control) and the error that occurred, which may be logically harder to pinpoint from an actual user error. Your mileage may vary, of course. If you are not making troubleshooting _easier_ with these tests then you have more fundamental problems to address.

{% hint style="info" %}
Read more about synthetic testing at:

* [Google SRE Book: Testing for Reliability](https://sre.google/sre-book/testing-reliability/)
* [Azure DevOps: Shift right to test in production](https://learn.microsoft.com/en-us/devops/deliver/shift-right-test-production)
* [Martin Fowler: Synthetic Monitoring](https://martinfowler.com/bliki/SyntheticMonitoring.html)
{% endhint %}

If you are interested in setting up synthetic monitoring I would recommend a nice pay-as-you-go option such as [Checkly](https://www.checklyhq.com) or your cloud-native offerings such as [AWS CloudWatch Synthetics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch\_Synthetics\_Canaries.html). These both provide either basic checks ("pings") for basic system-level interactions or actual UI end-to-end testing for anything where user flows need to be tested.

{% hint style="info" %}
I was using AWS' synthetic canaries some time ago and [wrote an "improved" variant of how it does things](https://github.com/mikaelvesavuori/aws-canary-improved-webcrawler) as I hit limits on how efficiently it would crawl complex websites.

It may still offer some value to you if you get any such issues.&#x20;
{% endhint %}

## Comparing to RUM (real user monitoring)

This is a dead giveaway when you just weigh "real user" and "synthetic" against each other :robot:

But really, let Mozilla explain:&#x20;

> Synthetic is well suited for catching regressions during development life cycles, especially with [network throttling](https://developer.mozilla.org/en-US/docs/Glossary/Network\_throttling). It is fairly easy, inexpensive, and great for spot-checking performance during development as an effective way to measure the effect of code changes, but it doesn't reflect what real users are experiencing and provides only a narrow view of performance.
>
> RUM, on the other hand, provides real metrics from real users using the site or application. While this is more expensive and likely less convenient, it provides vital user experience data.
>
> — [Mozilla: Performance Monitoring: RUM vs synthetic monitoring](https://developer.mozilla.org/en-US/docs/Web/Performance/Rum-vs-Synthetic)

An example of a relatively lightweight RUM product could be [Cloudflare Web Analytics](https://developers.cloudflare.com/analytics/web-analytics/) or you could look at bigger commercial products like those in [Datadog](https://www.datadoghq.com/product/real-user-monitoring/), [New Relic](https://newrelic.com/platform/browser-monitoring), and [Akamai](https://www.akamai.com/products/mpulse-real-user-monitoring). Your exact choice will most likely depend heavily on what other network and/or monitoring tools you have in place.

## In closing

There are interesting use cases for this type of testing, but you should probably first ensure that your team has tightened the bolts of other tests before progressing to this type, also given its tad more academic value.
