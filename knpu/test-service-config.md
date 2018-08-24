# Writing Tests & Service Config

When you make a pull request to Symfony, you *almost* always need at least one
test. And... yea... we definitely need a test for our new `TargetPathHelper`.

But, before we start writing it... shouldn't we *first* figure out how to *run*
Symfony's tests? Great idea! And I'm happy to report that it's *quite* easy.

## Getting Symfony's Dependencies

Look in the `symfony/` directory. It has a `composer.json` file that describes
all of the libraries that Symfony *itself* needs in order to work *and* in order
to run its tests.

Move over to your terminal and run:

```terminal
composer update
```

There's one important difference between a reusable library like Symfony and a
normal application: Symfony does *not* have a `composer.lock` file! We commit
the `composer.json` file to Symfony, but we do *not* commit `composer.lock`. Why
not? Well, there's just no point. Individual apps that require Symfony will lock
Symfony at some version in *their* app. But, when we're working on Symfony itself,
we usually want the *latest* version of all of its dependencies.

So before you run your tests, make sure to run `composer update`. Running
`composer install` isn't good enough, because there could already be a `composer.lock`
file from an *earlier* time you ran `composer install`. Running update makes
sure you have the latest stuff for whatever branch of Symfony you're currently on.

Perfect! *Now* we *do* have a `composer.lock` file.

## Running Symfony's Test Suite

Ok, we're ready to run the tests! Do it with:

```terminal
./phpunit
```

Um... that's it! This is a wrapper around PHPUnit: it downloads some dependencies
to a different directory, then... starts running the tests! And... yea... there are
a lot of tests. I'm going to stop these by pressing Ctrl+C.

## Running only Some Tests

To be honest, I *never* run the full test suite locally. You just don't need to!
As you'll see in a few minutes, Symfony has a robust continuous integration setup:
when you make a pull request, Symfony's test suite is run automatically.

Thanks to that, locally, I usually just run the tests I'm working on. Let's test
everything in SecurityBundle:

```terminal
./phpunit src/Symfony/Bundle/SecurityBundle
```

This time... if you didn't fast forward like me... you'd see that these tests only
take a minute or two. There *are* a few "skipped" tests: that's probably not something
you need to worry about. Some tests require a special PHP extension or some other
service that your local computer might not have. So, those tests are skipped. No
big deal.

## Adding our Test

Now that the tests are running, it's time to add our own! I'll double-click to
get back into SecurityBundle. Because we want to test `TargetPathHelper`, the
test should live in `tests/Security`. Create a new PHP class called
`TargetPathHelperTest`. Make this extend the normal `TestCase` from PHPUnit. Then
add `public function testSavePath()`.

For the body of the test... yea... I'm going to cheat. This isn't a testing tutorial,
so I'll paste in some code I already prepared. Oh, and I need to auto-complete
a few things to get the missing `use` statements, like `FirewallMap` from SecurityBundle,
and a few other ones.

Our `TargetPathHelper` class doesn't really do much: it pushes most of the work
back to the methods from the trait. So, this test basically creates a bunch of mocks,
creates a `FirewallConfig` that returns a firewall name of, um, `firewall_name`,
and then we ultimately make sure that this special key is set on the session to the
URL we passed to `savePath()`.

If you're interested in understanding this test better, you can totally look into
it more. But, the beautiful part is that creating a unit test for Symfony is no
different than creating a unit test for an application: there's no framework code
here.

Let's go run this *one* test directly:

```terminal
./phpunit src/Symfony/Bundle/SecurityBundle/Tests/Security/TargetPathHelperTest.php
```

The *last* step is to register our new class as a service *and* enable it to be
autowired. Let's get to it!
