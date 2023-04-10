---
description: >-
  Don't be a prisoner to your fantasies of how software development should be
  today what it was like decades ago.
---

# The testing-industrial complex and how it denies competent practices

<figure><img src="../.gitbook/assets/undraw_Vault_re_s4my.png" alt=""><figcaption><p>Agile consultants stowing money, having helped your company only superficially change. You got 10-minute stand-ups and QA consultants but got no continuous delivery or test automation.</p></figcaption></figure>

As stated in the introduction, my own experiences, and seemingly those of many others, seem to indicate that testing is often not carried out as an integrated development activity in any meaningful capacity. Sometimes it doesn't even happen at all! More often, though, we may have some degree of manual testing and verification combined with a degree of test automation, and teams feel caught in a swamp: How do we evolve from this situation?

## Knee-deep in it

My experiences stem from running my own businesses, creating and maintaining open source projects, working at a consultancy, an agency/studio, and working directly for the digital department of a manufacturing company. The range of clients I've had is broad and covers many sectors, maturity grades, and sizes. I am only one person, but again, when I talk with others and listen to their experiences they echo similar sentiments to what I've seen myself.

If I were to somehow express the situation that many of us have to deal with these would be some of the details in such a picture:

* There is often no expressed, unambiguous _definition of done_ (DOD).
* The DOD is not necessarily aligned with the business stakeholders, i.e. misalignment between the development and "making money" sides.
* The software development life cycle (SDLC) is either regulated in minute detail (typical for big enterprises) or non-existent (typical for agency contexts and low-tech-maturity orgs).
* Quality, a concept that should easily also encompass testing, is _said_ to be important but lacking clear conceptions of "what does good look like" (i.e. an expressed DOD with non-functional criteria) there is no accountability on either side of development or business or elsewhere.
* Most organizations are not adapting wholeheartedly to productive, modern software delivery models.
* The inspection model even Deming found deplorable is still in strong favor.&#x20;

And for the poor individual software engineer, we find that:

* The job market for software engineers and developers is in very high demand but is chronically undermanned; even senior positions are filled by juniors or potentially completely miscalculated hires at a rate that is actively damaging companies and their output.
* For better or worse, you can be entirely self-taught or have completed a post-doctoral thesis on compilers but still have similar job-related skills or expectations.
* There is no single or universal career path in software engineering. Adding to the previous point, consequently, it's easy to get stuck not using all of your potential.
* Testing and possibly most other quality-enhancing areas are probably one of the lesser discussed parts of the science and craft.
* Combining poor skills with unmoving orgs, we end up retaining much of the manual toil despite decades of attempts to remove it.

My theory is that given the incredibly fluid nature of skills and standards in software engineering, combined with the majority of organizations (that are paying for software engineers) not _being software organizations_, has resulted in an untenable situation where it's become a guessing game around whether or not one is "up to standards" and even what such standards mean. Because process is often emphasized over practice in typical orgs, it's more important what the diagrams say than how we actually produce good, tested software in practice. Modern engineering practices require (among other things):

* Frequent cross-functional investment from other stakeholders in an organization,
* Relative autonomy in the performance of the engineering work itself,
* The capability for the org to adapt to incremental continuous delivery,
* A very high degree of automation to minimize toil and lead times,
* Consequently a forced adaptation of human tasks to machine tasks, most importantly perhaps in the testing area.

The historical precedent and reason for this is, of course, Agile and DevOps, which accelerated how we can package an approach to do those things. Let's discuss Agile a bit – the more controversial of these two.

## Hijacking Agile

At this point in history (I'm writing this in 2023), on paper for many, the _only_ model of software engineering has been Agile. At least that's what we might say.

{% hint style="success" %}
The classic book on hardcore Agile is Kent Beck and Cynthia Andres's [_Extreme Programming Explained: Embrace Change_](https://www.goodreads.com/en/book/show/67833.Extreme\_Programming\_Explained). I can see this having been "[_The Satanic Verses_](https://en.wikipedia.org/wiki/The\_Satanic\_Verses)" of its time in corporate environments. It still carries a punch.

For a refreshing, more recent read on Agile, check out Robert Martin's [_Clean Agile: Back to Basics_](https://www.goodreads.com/en/book/show/45280021-clean-agile). As opposed to many opinionators in Agile, Martin _was there_ when the initial Agile principles were written. You'll find his book equally amusing, cleansing, and frustrating that we are where (many of us) are.
{% endhint %}

But looking at the recent past it becomes clear that **Agile became adapted, rather than adopted** by many companies. And with that move came Scrum and its Scrum Masters with many a pact signed with the project management devils; SAFe that watered down Agile into a model that is so hard to grok that you need to certify even to talk about it; and the rise of cargo cult Agile, where (for some reason) the rituals like standup meetings are seemingly more important than the [technical practices](https://en.wikipedia.org/wiki/Extreme\_programming\_practices), including continuous integration and test-driven development.

Suffice it to say that Agile never really panned out to what we would typically mean by the concept.

At least two industries weathered blood and money during this adaptation and went to work: Agile coaches and QA. How better to support an organization that "wants to do Agile" rather than "be Agile"? Millions of hours were billed, and are still being billed, as an effect of that. Don't get me wrong, there are competing and newer ideas that are not "Agile" — but they are "agile". The problem is that foundational philosophical concerns of this (and related) movements were never embraced truthfully by many organizations, only parts of the management-leaning practices and relatively inconsequential rituals. So while orgs may be crying to the bank about their agile coaches and testers, they prop up the defense against something worse: A critique of the entire way they deliver software in the first place.

{% hint style="info" %}
See my article [Software Delivery That Makes Sense](https://medium.com/@mikaelvesavuori/software-delivery-that-makes-sense-e6c4ae520b20) for more on this.
{% endhint %}

If your true goal is to get to continuous delivery/deployment and otherwise use the Agile technical practices then it becomes clear that there is no clear place for the traditional QA/tester role, as they will undoubtedly add latency and friction to the mix, rather than help resolve it. Even handing over code to be tested by someone else will be less effective as soon as an engineer has sufficient skills to use the tooling to write and perform the test. Unless your explicit goal is to either not test at all, or you have no use for testing skills, then you'll gain momentum already in the very short term by writing the tests yourself. Plus, you get the significant benefit of using tests as a development tool and letting the tests expose how well-constructed your code actually is.

I don't hate testers. They should just turn into becoming full-fledged engineers as that's the role they are already role-playing.

Agile testing is therefore, conceptually, something we should see as testing _within the Agile context_ rather than "this is a poor sod trying to write tests for someone else; it happens to be a pseudo-Agile team". Getting to being agile/Agile is not about micro-managing or setting up specializations, it's about reducing waste; handoffs are waste if you can do the work in the first place.

If on the other hand, your heartbeat is driven by billable hours to keep organizations not moving forward, alas, efficiency is not really part of that calculation. But then you are part of the problem, not the solution. :man\_shrugging:

{% hint style="info" %}
For more on Agile testing, see:

* Kent Beck: [_Test Driven Development: By Example_](https://www.goodreads.com/book/show/387190.Test\_Driven\_Development)
* Lisa Crispin & Janet Gregory: [_Agile Testing: A Practical Guide for Testers and Agile Teams_](https://www.goodreads.com/book/show/5341009-agile-testing)
* Steve Freeman & Nat Pryce: [_Growing Object-Oriented Software, Guided by Tests_](https://www.goodreads.com/en/book/show/4268826-growing-object-oriented-software-guided-by-tests)
{% endhint %}
