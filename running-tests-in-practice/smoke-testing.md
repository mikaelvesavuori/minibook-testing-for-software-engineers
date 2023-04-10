# Smoke testing

{% hint style="info" %}
**Surface area**

Facade of your deployed service

**Confidence level**

Medium

**Granularity**

Low

**Pros**

* Cheap and simple
* Very close to "the real deal"

**Cons**

* Side effects from using an actual system
* Errors aren't necessarily directly debuggable
{% endhint %}

{% hint style="success" %}
**When to do this type of testing?**

Always, as needed.
{% endhint %}

[Wikipedia](https://en.wikipedia.org/wiki/Smoke\_testing\_\(software\)) explains smoke testing as

> preliminary testing to reveal simple failures severe enough to, for example, reject a prospective software release.

[Microsoft's Engineering Playbook](https://microsoft.github.io/code-with-engineering-playbook/automated-testing/smoke-testing/) expands on this:

> Smoke tests **cover only the most critical application path, and should not be used to actually test the application's behavior, keeping execution time and complexity to minimum**. The tests can be formed of a subset of the application's integration or e2e tests, and they cover as much of the functionality with as little depth as required.
>
> The golden rule of a good smoke test is that it saves time on validating that the application is acceptable to a stage where better, more thorough testing will begin. \[...]
>
> **Smoke testing is a low-effort, high-impact step** to ship more reliable software. It should be considered **amongst the first stages to implement** when planning continuously integrated and delivered systems.

Smoke tests are a rough type of test that assesses whether or not something is working as expected. Therefore, smoke testing is really nothing more than a thin slice of integration tests. _Smokes_ are ideally short-running and technically simple.

{% hint style="info" %}
I would therefore recommend that smoke tests are broad, functionally-oriented tests rather than (pointing to the Wikipedia article) on any _unit size_ in scale—prefer other, faster, and simpler tests for such cases.
{% endhint %}

Personally, I've not worked with any large number of smoke tests. The way I've approached them, as compared to integration/API tests, is that smoke tests are very simple and make only basic assertions whereas integration/API tests can be more granular if needed. The technology itself is in both cases the simplest possible that makes sense. For _smokes_ the simplest would be something like `cUrl` because the nature of the assertion is decidedly primitive.

Here's an example of a callable Bash (or shell) script testing an online API:

```bash
#!/bin/bash
set -e

# RUN SMOKE TEST
# Expects input parameter with an URL
# Checks for status code 200

URL=$1
STATUS_RESPONSE=$(curl -s -o /dev/null -w "%{http_code}" $URL)

if [ "$STATUS_RESPONSE" == 200 ]
then
  echo Worked just fine && exit 0
else
  echo Yep that broke something && exit 1
fi
```

Calling it would look something like:

```bash
bash smoketest.sh https://www.acmecorp.xyz
```

Remind yourself of the fact that these only attempt to give you a very broad sense of whether or not something worked or not!

## You still need to handle side effects

The problem with using any "real" system is always that you will get side effects so any advice on implementing your system in a way that intelligently takes care not to propagate test runs to production systems is applicable here too.
