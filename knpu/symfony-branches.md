# Symfony's Branching Strategy & Pull Requests

We've already helped push forward a pull request, solved an issue and even
reported a bug. Hello! We deserve cake!

And we deserve to move up one more level of difficulty: it's time to contribute *new*
code with a pull request. Let's look at an issue I found: [#27835](https://github.com/symfony/symfony/issues/27835).

## Understanding the Issue

This comes from the Security component. Let me give you some background: if you try
to access a protected page as an anonymous user - like `/admin` - Symfony stores
that URL to a special key in the session. Then, after you login, Symfony reads this
key and redirects the user back to that URL.

Occasionally, it's useful to *manually* set that session key to control where
the user goes after logging in. To help with that, Symfony has `TargetPathTrait`.
The problem is that, to *use* its `saveTargetPath()` method, you need something
called the "provider key"... which is actually just the "name" of your firewall.
You *could* hardcode it, but, that *really* shouldn't be necessary.

In a recent version of Symfony, a feature was added so that you can read
the firewall name by getting a `FirewallMap` object, calling `getFirewallConfig()`
and *then* calling `getName()`. Phew!

But, here's the problem: that `FirewallMap` service is *not* an autowireable service.
That makes it inconvenient to use. And, one of the core contributors gives a reason
behind why that service is not autowireable.

You *can* work around this. But, I had an idea: I'm not even sure if it's a good
idea, but let's try it. What if we created a new `TargetPathHelper` class that
allowed you to set this "target path", *without* needing the provider key.
Internally, *it* would use the `FirewallMap` to figure it out automatically.

The end user could use this new class without needing to worry about the firewall
name at all.

If you don't completely understand, that's ok. The important thing is the *process*
we're going to use to bring this new idea to life!

## Symfony's Branching Strategy

Go back to PhpStorm: let's change our project to look *only* at the `symfony/`
directory. Then, find the terminal that's in this directory. We're still on the
feature branch from `colinodell`. Start by making sure your copy of Symfony is up
to date by running:

```terminal
git fetch origin
```

And then create a new branch for our feature:

```terminal
git checkout -b target-path-helper origin/master
```

This is important: we just created a new branch based off of Symfony's `master`
branch. Why? Why not base the branch off of Symfony's 4.1 branch - that's the
currently-released version?

Let's talk about Symfony's branching system. It's... *kinda* simple. If you're
adding a feature, it should *always* be made to the `master` branch. Then,
it will be included in the next Symfony *minor* release: in this case Symfony
4.2. But if you're fixing a bug, you should fix that in the *oldest*, *supported*
branch where the bug exists. For example,: if you found a bug that was introduced
in Symfony 3.4, create your branch based off of `origin/3.4`.

But, if a bug was first found in version 3.2, you actually would *not*
base your branch off of Symfony's 3.2 branch. Why? Because Symfony 3.2 is no longer
supported. To help understand this, go to https://symfony.com/roadmap. At this
moment, only three versions of Symfony are supported: 2.8, 3.4 and 4.1.

So, if you found a bug in Symfony 3.2, you would fix it in 3.4, because that's the
oldest, *supported* version of Symfony that contains the bug. If you found a bug
in Symfony 2.7, you would fix it on the 2.8 branch.

But... if I fix a bug in 2.8... won't that bug still exist in 3.4 and 4.1? Ah,
a *very* good question. But... no! The core team routinely merges all *old* branches -
like 2.8 - up *into* the newer branches, like 3.4 and 4.1. If you fix a bug in 2.8,
it will also be merged & included in all newer versions. Booya!

Anyways, because this will be a new *feature*, our branch is based off of `origin/master`.
And *now*, we're ready to code. Let's do that next!
