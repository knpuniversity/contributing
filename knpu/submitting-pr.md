# PR Details & Continuous Integration

The *first* thing to notice is the base branch. This is *super* important. Because
we created our branch off of master, this base branch must be master. If we
created the branch off of 3.4, then, of course, this should be 3.4.

One easy way to be sure you have things setup correctly is to check that you
only see the commits down here that you expect. If you mess up the base branch,
you'll probably see a bunch of extra commits and changes.

## Making Changes to your PR

The pull request description comes with a nice template to get us started. For
the branch, because this is a new feature, we *do* want the `master` branch.
This is not a bug fix and this *is* a new feature.

Oh, and if this is a new feature, apparently, we should update the CHANGELOG.
I totally forgot about that!

Go back to your editor. Then, inside whatever bundle or component you're working
on - so SecurityBundle for us - find the `CHANGELOG.md` file. If our new feature
is accepted, it will be in the next minor version, which is 4.2 right now. You can
already see a few new features listed there. Progress!

Let's add *our* new feature: describe what we introduced & why it's useful.

To add this, we *could* just make a second commit. And that would be *totally*
fine. In fact, if you're making significant changes to the pull request, making
new commits *is* a good idea: it will help people see how your pull request evolves
over time.

But, in this case, because the changes are so simple, run:

```terminal
git add -u
```

to add everything. Then:

```terminal
git commit --amend
```

That will add these changes to my previous commit to keep things clean. Push with:

```terminal
git push weaverryan target-path-trait --force
```

## The Pull Request Description

Perfect! Head back to the pull request. We did not introduce any backwards compatibility
breaks, we didn't deprecate any features and yes, the tests should pass. A lot
of these are just reminders to *think* about things. After we submit the PR, we'll
see for sure if the tests pass. For fixed tickets, there isn't *always* a fixed
ticket, but there is in this case: `#27835`.

Oh, and whenever you add a new feature, you need to create a documentation pull
request. I'll put TODO for now. But we *are* going to do this soon.

Finally, at the bottom, it's our job to put a short README so that other people
can understand how our feature works. Showing some code examples is best - but
because the code behind this is pretty simple, what I *really* want to do is
describe *why* this is needed.

And... we're ready! Create that pull request! Boom! Great work people! Will this
pull request be accepted? Who knows? But, we did good work, wrote tests and clearly
stated *why* we created this feature. We rock!

## Making fabbot Happy

Until... yep! We immediately see failures at the bottom! Two things happen when
you create a pull request. First, Symfony's continuous integration system
starts running the tests: Travis CI runs tests on Linux & AppVeyor runs them on
Windows. Click the details for Travis - it's pretty awesome.

It executes the tests on *multiple* versions of PHP and uses different flags to
use both the *newest* version of Symfony's dependencies and the oldest allowed
versions. We'll let this keep doing its thing.

The *second* thing that happens after creating a pull request is that you are visited
by the famous... fabbot! Fabbot automatically checks your pull request for coding
standards violations. Apparently we have two problems! But, here's the best part:
copy that curl statement, find your terminal, and paste! Run:

```terminal
git diff
```

Cool! This automatically fixed the two whitespace issues that violated Symfony's
coding standards. This is why I don't worry *too* much about coding standards until
now: fabbot will help us.

And because these changes aren't important, just like before, let's *amend* our
commit:

```terminal
git add -u
git commit --amend
```

Push that with:

```terminal
git push weaverryan target-path-trait --force
```

Ok, go back to the pull request. Refresh and... yea! Fabbot is happy!

## Caring for your PR After Submitting

So... what now? First, wait to see if the tests pass. If they don't, yea, you'll
need to see if our changes caused some unexpected bug. And second, wait for community
feedback! Sometimes, feedback can be direct and to the point. We're developers,
so we like to look at all the smallest technical details. Don't take this personally:
feedback is meant to be constructive - we're all on the same side. And, yea, you'll
almost *definitely* need to make at least *some* changes. Heck, I *rarely* make a
pull request that doesn't need *significant* changes after some feedback. It's awesome!
Usually, someone thinks of a *way* better implementation. When that happens,
you know the drill: make the changes, commit, push, and check fabbot and the tests
again.

Oh, and don't worry too much about doing the `git commit --amend` thing or rebasing.
It's *totally* ok to have multiple commits. And also, when someone merges your pull
request, they use a tool that makes it *really* easy to squash all of your commits
down into 1 commit, if they want to. That's not something you need to worry about.

Next: we haven't created our documentation pull request yet. Time to do that!
