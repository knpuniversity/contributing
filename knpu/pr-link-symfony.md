# Linking Symfony deps to your Local Copy

Here's the question now: how can we make our test app use the pull request code from
the `symfony/` directory? Check out the `vendor/symfony` directory in the app:
it's just a bunch of sub-directories, each containing code from Symfony's master
branch. But, what we *really* want is for this `validator` directory to instead be
a symbolic link to the correct path in our `symfony/` directory:
`src/Symfony/Component/Validator`.

We could do this by hand... but! Symfony has a cool script to do this automatically:
it's called `link`.

Go back to the terminal that holds the app and run:

```terminal
ls -la vendor/symfony
```

Yep! Just a bunch of lonely directories. Go back to the other tab and run
`./link` and point to our app: `../triage_pr_28069`:

```terminal-silent
./link ../triage_pr_28069/
```

Wow! Go *back* to your app and check the symfony directory again:

```terminal
ls -la vendor/symfony
```

Awesome! Every package that comes from the `symfony/symfony` repository is now a
symlink to our local copy! In other words, our app is *now* using Colin's code!

## Testing the PR Code!

It's time for us to write some code that tests the new validator! In `src/`,
create a new PHP class, um, how about: `ClassToValidate`. I'm feeling creative!

Inside add a new public property called `enteredNumber`. I'm trying to keep my
code as *simple* as possible: a public property is a nice shortcut.

Next, add the annotation: `@Assert\MultipleOf()` of 10. I'm also going to add
a *second* annotation that will eventually fail - `@Assert\Blank()` - just to make
sure everything is working ok.

To try this, in the `Controller/` directory, create a new class: `TestingController`.
Fill in the namespace: `App\Controller`. Because we have multiple apps in one
PhpStorm project, some of the magic we normally get isn't working.

Inside this, add `public function test()`. Ok: to test validation we'll need the
validator service *and* the object to validate. And an argument:
`ValidatorInterface $validator`. Then, `$myObject = new ClassToValidate()` and
set its `enteredNumber` to 10.

Oh, and change the `MultipleOf` to be 5: we'll test that 10 is a multiple of 5.

To validate, add `$errors = $validator->validate($myObject);`. Then, dump that
and die!

Finally, we need a route for this! We don't have annotations installed, so, to
keep things simple, add this in `routes.yaml`: uncomment the example, and change
the controller to `TestingController::test`.

Done! Back in the terminal, start the built-in PHP web server in the `public/`
directory:

```terminal
php -S localhost:8000 -t public
```

We're ready! Find your browser, go to http://localhost:8000 and... what?! No validation
errors! That's actually *not* good - the `@Blank` constraint should give us *one*
error.

The problem is that our setup is not quite complete: to use annotations with the
validator, you need to install the annotations library. Stop the web server and
run:

```terminal
composer require annotations
```

This installs `sensio/framework-extra-bundle`... we only *technically* need
to install `doctrine/annotations`. But, that's ok. Restart the built in web server:

```terminal-silent
php -S localhost:8000 -t public
```

Move over and, refresh. It works! We get the *one* expected error from `@Assert\Blank`,
but we do *not* get a second error: 10 *is* a multiple of 5. To make sure the failure
works, change this to 9, move over and... yes! There is the second error. That
error language looks really nice to me.

## Finishing the PR Review

Hey! The code works! This is great news! Let's go back to GitHub and tell the world!
I'll "Approve" this pull request. And, like everything, don't worry: this doesn't
mean that the PR is *definitely* perfect: just that you think it's ready. The *really*
important part is to add as much information about *why* you think it's ready or
not ready. In this case, we checked the code *and* we *actually* tested this in
a real project.

And... approve! Oh, but I *did* forget to check one thing: whether or not this
new feature has a documentation pull request or issue. But, of course, it does!
Not *every* pull request needs documentation - but, if you think it does, and
it's missing, gently poke the pull request author. Or, even better! Go create the
documentation pull request yourself! We'll do that later.

Oh, and fun fact! This feature was *merged* about 1 week after we did this review.
Go open source! After some good community feedback, it was renamed from
`MultipleOf` to `DivisibleBy`. A great change!

Next, let's triage an issue and try to close a bug.
