---
description: Don't do all of the heavy lifting unless you need to.
---

# Offload some validation to the API level

{% hint style="info" %}
Read more about this in practice in the ["API schema validation" page on Better APIs](https://betterapis.mikaelvesavuori.se/stability/api-schema-validation).
{% endhint %}

If you are able to use a managed API solution such as AWS API Gateway, then you should be able to add request validation at the API level. This will act as a further guard against invalid POST requests.

Request validation happens on the **structural and pattern-based layers**. While this allows for a relatively rich validation manner, it is not something that works for logical validation or other types of checks that you'd need conventional programming for. Such checks are still firmly in the territory of application layer validation and testing.

If you already have an API schema, constructing the validator schemas (in the AWS case, this is in [JSON Schema](https://json-schema.org) format) is merely a question of copy-pasting a bit of that data into a separate document. For more on these details and how it actually works, see the above link.

## Testing validation

What does this mean for testing, then? You could for example **validate the validation logic** during your integration tests. You could create a number of test cases that ensure that expected inputs work, and that invalid inputs throw the correct error codes.

Does this replace any other testing? No, not really, but it does complement it. We are really just enforcing our solution with one additional layer that goes on top of the API. In case your system isn't even an API, well then none of this matters anyway!

What you do get is some guarantee that invalid input should not be leaking into your application layer. If you are already doing logical input validation you're still going to need that, but you can perhaps start shaving off any tedious structural validations you've set up.
