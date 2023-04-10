---
description: Considering a less technical testing model that I believe is more useful.
---

# Confidence-based testing

<figure><img src="../.gitbook/assets/testing_meme2jpg" alt=""><figcaption><p>If I remove 20 random lines of code, will you start sweating if I push it to production?</p></figcaption></figure>

> The only test coverage goal that makes any sense is 100%. It’s an asymptotic goal. You’ll likely never get there. But you should never stop trying.
>
> — [Robert C. Martin](https://twitter.com/unclebobmartin/status/1205922909350293505)

**The actual, real value that you want to get from your testing is confidence**. So regardless if you love or hate to write tests, confidence is the currency you should expect to be remunerated with. Nothing matters quite as much.

Solid quality engineering combined with rigorous, good tests ensure that you can, among many other things:

- Commit and deploy without a pull request
- Deploy before leaving on a Friday
- Be a human being, not always remembering all the details that may break
- More easily onboard newcomers (say, new hires or open-source contributors) to a project
- Clearly understand the functional requirements of your software

You gain confidence that unexpected behaviors and regressions are caught and don't slip out. You gain confidence in the code doing what you actually expect it to do. You have a relative guarantee that no (known) surprises should show up and stop you.

Confidence that is unverified, unverifiable, and unquantifiable borders on ignorance. You need some kind of proof and determinism to go with that sweet feeling. Sometimes it's easy enough that you know that a couple of quick cURL calls show the code is working. Maybe the thing you build can't fail in any spectacular way. Then that's enough. But for most of us, there's no valid way to get there without testing, because we build things that are non-trivial. Non-trivial things tend to have more failure modes than success modes.

Again, confidence is verifiable. Tests are not strictly equal to confidence; you can have untested code that you trust. But if I would build something with any degree of failure potential, then I would want it to promise the same reliability that any consumer product does—something as close as I can get to 100%. Something that works in the expected ways 100% of the time I can feel truly confident about. Because to be frank, it will still be able for it to fail. But likely not in the expected ways.

The opposite of confidence is of course **distrust**. If you distrust your code, you can never be quite sure that it works. If it works or doesn't, you never really know. Your code may break for people, set an end to a revenue stream for your company, stop you from distributing your band's new record, or worse, actively hurt someone who cannot get the medical attention they seek. The fallout of your problem space is certainly peculiar to your own circumstances. It may not even be that big of an issue!

However, without confidence in our code, and without being plain ignorant, we get anxious and worried, stressed and confused, instead of just fixing the problems, becoming proud of our achievements, and trusting the work we have done.

Confidence is for new coders _and for_ seasoned software engineers. But many lose it along the way, which is sad to see. A confidence-based model is, in my opinion, the only realistic (and pragmatic) model that makes sense in the long run.
