---
description: >-
  Do yourself a favor and delegate all usage questions to your API schema and
  docs.
---

# Provide an API schema and examples

Having an API schema is the bare minimum acceptance criteria for a system to be used by others. It's common that _not having it_ leads to a lot of questions coming from other teams and stakeholders, filling up your time helping them, instead of building and improving on your solution. While it sounds altruistic to produce a schema and docs "for others", in fact, you win a lot by doing so:

* Other people know what your thing is, and how it works
* It moves the design surface into a less technical place, meaning you can work on the API easier and more nimbly&#x20;
* It's easier to onboard new team members

The simplest you can go, with basic systems, is to just write down endpoints and some example usage in your `README.md` file. While not a schema per se, it's a good start.

An example of this from my project [https://maxslaofmy.systems](https://maxslaofmy.systems) is below:

{% code title="Request" %}
```bash
POST https://maxslaofmy.systems/api

[
  {
    "name": "amazon-api-gateway"
  },
  {
    "name": "aws-lambda"
  },
  {
    "name": "custom-my-heroku-backend",
    "sla": 97,
    "description": "Just some backend I built on Heroku"
  },
  {
    "name": "amazon-dynamodb"
  }
]
```
{% endcode %}

{% code title="Response" %}
```bash
Status 200

96.893
```
{% endcode %}

Already this, together with other documentation, gives end-users a useful way to start interacting with your API.

As soon as the API starts becoming complex or it needs to have a portable definition—for example for [CI-based linting and validation](https://github.com/stoplightio/spectral), [document generation](https://www.asyncapi.com/tools/generator), or other machine-readable contexts—then following a standardized convention is something you're absolutely going to want to do. The two major formats today (for the web context, at least) are OpenAPI and AsyncAPI.

{% hint style="info" %}
Read a short introduction to [doing this in practice with AsyncAPI in my DDD book](https://ddd.mikaelvesavuori.se/groundwork/api-schema).

Learn more about writing schemas at:

* [SwaggerHub OpenAPI 3 tutorial](https://support.smartbear.com/swaggerhub/docs/tutorials/openapi-3-tutorial.html)
* [AsyncAPI tutorial](https://www.asyncapi.com/docs/tutorials/getting-started)

For more on technical documentation writing, two great resources are:

* [Diátaxis: A systematic framework for technical documentation authoring](https://diataxis.fr)
* [Principles of technical documentation](https://www.innoq.com/en/articles/2022/01/principles-of-technical-documentation/)
{% endhint %}

The minimum you should aim at providing is:

* Technical documentation with relevant headings; [see an example template here](https://gist.github.com/mikaelvesavuori/0c19ef8f9e2c7b903fac9c52aee58a1f)
* API schema and/or API usage examples (requests, responses)
* That the above is located somewhere other teams and stakeholders have easy access to the material
