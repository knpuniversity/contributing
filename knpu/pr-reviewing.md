# Testing the Code from a PR

It looks like Colin did a *great* job with this new feature. But, we can give a
merger *much* more confidence by actually testing it in a real project!

## Creating a new Test App

In PhpStorm, I've already created an empty `contributing` directory. And, I already
have a terminal open to this same place. To test the PR, let's literally create a
brand-new Symfony app:
```terminal
composer create-project symfony/skeleton
```

I'm using `symfony/skeleton` instead of the larger `symfony/website-skeleton` to keep
things as small and focused as possible. Grab the `dev-master` version of the skeleton:

```terminal-silent
composer create-project symfony/skeleton:dev-master
```

Why? Colin's PR is against Symfony's `master` branch. So, to test it, I want to
create an app that's based on that *same* version of Symfony.

Finally, put this into a new directory called `triage_pr_28069`:

```terminal-silent
composer create-project symfony/skeleton:dev-master triage_pr_28069
```

When that finishes, move over and... yea! Here's the new app. Check out its
`composer.json` file:

[[[ code('416afc2568') ]]]

It's using version 4.2 of Symfony, which is the next, unreleased, version of Symfony
at this moment. In other words, this code is from Symfony's master branch. We also
have `minimum-stability` set to `dev`:

[[[ code('0b283784e3') ]]]

Which means that Composer will try to install new, unreleased version of libraries.

Look back at the PR: all of the changes were to the `Validator` component. Ok,
let's get that installed: find your terminal, move into the directory and run:

```terminal
composer require validator
```

This will install the `dev-master` version of `symfony/validator`. In other words,
it will get the code from Symfony's `master` branch. But... hmm... that's not quite
what we want: we somehow need to get the code from *Colin's* branch. How can we do
that? Oh, it's *super* cool.

## Getting the Code from the Pull Request

Go to your terminal and open a new tab. Go back up to the `contributing` directory.
I'm going to clone the *entire* Symfony project into a new directory here. To do
that, go back to your browser, move to the repository's homepage, click "Clone or
download" and copy the URL.

Move back over, `git clone` and paste:

```terminal-silent
git clone git@github.com:symfony/symfony.git
```

When that finishes, we now have a `symfony` directory right next to our app. To
get Colin's branch, we have a few options. Move into the new `symfony` directory:

```terminal-silent
cd symfony/
```

And type:

```terminal
git remote
```

and

```terminal
git remote show origin
```

Let's add a *second* remote for Colin's *fork*. Copy the Symfony URL, then run
`git remote add` and paste. Copy Colin's username - `colinodell`, move back,
call the new remote `colinodell`, and change the username part of the URL:

```terminal-silent
git remote add colinodell git@github.com:colinodell/symfony.git
```

Nice! Grab his branches with:

```terminal
git fetch colinodell
```

Yep! There's the branch: `feature/multiple-of-validator` - this is the one used
for the PR. To check out to that code, create a new branch:

```terminal
git checkout -b feature/multiple-of-validator colinodell/feature/multiple-of-validator
```

Sweet! To prove we've got the right code, go back to PhpStorm, press `Shift`+`Shift`,
and search for the new file. There it is!

We *now* have a test app *and* the new code in our `symfony` directory. But, they're
not connected yet! Let's do that next.
