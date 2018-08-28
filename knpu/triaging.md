# Organization & Triaging

What could be better than eating ice cream at the beach? Only one thing I can think of:
eating ice cream at the beach... wait for it... *while* contributing to Symfony!

## The Massive Power of Contributors

Seriously, I am *super*, *duper*, double-duper, excited about this tutorial! If you're
like me, you probably use Symfony almost every day. And that means, we're taking
advantage of *countless* hours of volunteer work from *thousands* of people! Contributing
to Symfony is a great way to give *back* and become part of that amazing effort.

But, I also have a few other motivations. Like, if you want to *truly* become an
expert on one part of Symfony, there is *no* better way than reviewing a pull
request or fixing a bug. Seriously.

Or, have you ever been annoyed by part of Symfony and wanted to improve it? How
about this: have you ever been confused, *finally* figured something out, and then
realized a *small* change to the documentation could have saved you hours of
frustration?

*These* are the things that get me excited to contribute back to Symfony! How cool
is it that *you* could save other people *hundreds* of hours by improving the
documentation with extra information that the core team didn't realize was missing?
Symfony is *truly* community-driven. There's actually no official roadmap: if you
want to add something, do it!

## The Organization of Symfony

Excited? There's just one small problem: contributing... ain't easy! At least,
not at first. Symfony is a *huge* and complex project. But, you will *not* regret
learning how to give back. It's fun and will make you an even better developer.

Let's jump in! The main repository for Symfony lives at
https://github.com/symfony/symfony. This holds *almost* all of the Symfony libraries.
There are a *few* others that live in other places - but we'll talk about those
later.

And, woh! 749 issues and 181 pull requests! That's, ah, a *lot*! And this leads
me to the *first*, *most* important and *least* celebrated way of contributing:
triaging! Here's the truth: there are too many issues and too many pull requests
for the Symfony core team to be able to reply & review everything.

The first way to contribute is to "triage": find an issue and help push it forward.
If it's a feature idea, you can give your feedback or offer alternate solutions.
If it's a bug, see if you can replicate that bug. We're going to do this.

You can also triage pull requests: find one, review its code, give your opinion
on whether or not you think it's a good idea, and even test it in a real project
to make sure it works. We'll do that too!

Oh, and I recommend focusing on *newer* issues and pull requests, at least at first.
If a PR or issue is old, it's probably pretty complicated.

## Your Opinion is Respected

If reviewing code or giving your opinion in a big repository like Symfony sounds
scary, don't worry! Symfony is a friendly place: everyone has the same goal: to
help move the project forward. Sure, it *is* possible that you'll say something
that's not completely correct. I do that all the time! I think I'm kinda famous
for it! It's really no big deal. Honestly, the time that you took to review that
pull request or issue has a *high* value. And if you say something that isn't totally
right, someone else will nicely correct you, you'll learn something, and the whole
project will move forward. Be nice, don't be afraid to be wrong, and use any feedback
as a way to learn more.

## Reviewing a Pull Request

So, let's start contributing! Let's triage a pull request that I found: - it's number
[28069](https://github.com/symfony/symfony/pull/28069). This PR is from my friend
Colin, who's proposing a new `MultipleOf` validation constraint that checks whether
a submitted value is divisible by another number.

I like this idea, but this PR hasn't gotten *any* attention yet. This is a perfect
opportunity for us to help push it forward!

First, let's review the code. As a new contributor, you might not really know
what to look for when reviewing. No problem: just see if the code makes sense and
look for potential bugs or other issues. You don't have to be perfect: every little
bit helps.

To create a validator, you need two classes. The first represents the annotation:
`MultipleOf`. The second - `MultipleOfValidator` - is the class that actually does
the validation work.

The annotation has an option message:

> The value should be a multiple of {{ compared_value }}

That's a pretty good message. In the validator, Colin uses ``fmod`` to compare the
values, which means the user can compare decimals - like 1 is a multiple of 5.
Yea, this all looks pretty good to me!

The second thing to look for is if the PR has a test: *most* features need some.
And, no surprise, Colin did a great job here too: he's testing valid and invalid
comparisons. This test uses a special base class to hook this all together.

So... I have *no* comments to add to this pull request! And even *that* is valuable!
We'll be able to post that we reviewed the code and it looks good to us. But, there
*is* still one important question: does this... *actually* work? It's one thing to
look at the code, but it helps *so* much if someone in the community says:

> Hey! I actually *tried* this in a real project and it works great!

Let's *be* that wonderful person next!
