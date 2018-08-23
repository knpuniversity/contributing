# Bug Reproducer

Triaging issues and pull requests is seriously, the *best*. But, occasionally,
*you* might be the person who finds the bug! That happened to me just today, and
I want to report it!

To make the *best* possible bug report, we should create a "reproducer": a Symfony
project that shows the bug with as *little* code as possible. I don't have the error
I saw in front of me now, but it was pretty simple: I created a form class, tried
to use it in my controller, then boom! I got a *very* strange container cache error.

## Creating the Reproducer Project

The bug happened when I was playing with the `master` branch of Symfony. So, let's
create our new reproducer project based on that:

```terminal
composer create-project symfony/skeleton:dev-master container_bug_reproducer
```

When that finishes, move into the new directory. The *one* package I know I need
is `form`. Get it installed:

```terminal
composer require form
```

While we're waiting for this to finish, go back to PhpStorm, close a few files,
and go into the new directory. To reproduce my error, I know I need a new form
class. Right inside `src/`, create a new PHP class: `SomeFormType`. My creativity
today is *off* the charts. Make this extend the usual `AbstractType`. Normally,
we would *also* add the `buildForm()` method. But... I'm not even sure that's needed
to trigger the bug. So, in the spirit of making our reproducer as *small* and
focused as possible, let's skip it, until we *know* it's needed.

In the `Controller/` directory, create another new PHP class: 
`ContainerTestController` in the `App\Controller` namespace. Give it the usual
`public function test()`.

Because we need to create a form, extend `AbstractController` from `FrameworkBundle`
so we have that shortcut. Then, `$form = $this->createForm()` and pass
`SomeFormType::class`.

If I'm right, the error will happen *right* here. To test this, find `routes.yaml`,
uncomment the route and point the controller to our code.

We're ready! Go back to the terminal and start the built-in web server:

```terminal
php -S localhost:8000 -t public
```

Move back, find that browser tab and... refresh! Say hello to the super weird
error:

> Compile error: failed opening required file

... in some `var/cache/dev` directory. This is related to the container, and it's
very low-level code. It's *possible* I'm doing something wrong, but this *really*
looks like a bug.

To be sure, let's stop the web server, manually clear the cache directory with:

```terminal
rm -rf var/cache/*
```

restart the server and try it again. Same error. Let's take a screenshot: it's
*so* useful to be able to see the full stacktrace of an error.

## Opening the Bug Issue

Go back to the Symfony repository and click to open a new issue. Yep, this is a
"Bug Report". And, this is cool! We have a nice outline of all the info needed.
Affected versions: `master`. This *could* be a bug on a stable version, and that
*is* something we could check. But, since it would be such a critical and obvious
bug, it's probably just on `master`.

For description, let's describe what we're trying to do. I'll also upload my
error screenshot. Oh, and I forgot a title: I'll reference part of the error message
for this.

## Pushing the Reproducer to GitHub

Under "How to Reproduce", ah, *this* is where our issue will *shine*! Let's push
our test project up to GitHub so we can share it.

Move back to the terminal, stop the web server, then initialize a new Git repository
with:

```terminal
git init
git add .
git commit
```

and a message. Next, go to GitHub, click "New", type a name and click "Create Repository".
Copy the two lines near the bottom for an existing repository. Then, find your
terminal and... paste!

That's it! Refresh the page on GitHub: here is our simple reproducer app. Copy
its URL. Then, head back to the issue. Let's mention our reproducer app first and
how to trigger the error on it.

Then, to make life *even* easier, summarize what we did to get the error. As
extra credit, I'll even link *right* to the line that triggers it.

And... that's it! I don't have *any* possible solution: this error is way above
my pay grade. And, that's ok. Finish the message and... submit!

We're done! *This* is a bug report worth being proud of.

And, to prove it! I have an update! This issue was fixed less than *3* hours after
we posted this. That's amazing.

Next, let's jump into how we can contribute *code* to Symfony via a pull request.
