# Writing Tests & Service Config

When you make a pull request to Symfony, you *almost* always need at least one
test. And... yea... we definitely need a test for our new `TargetPathHelper`.

But, before we start writing it... shouldn't we figure out how to *run* Symfony's
test suite first? Yep! And I'm *super* happy to report that it's *quite* easy.

## Getting Symfony's Dependencies

Look in the `symfony/` directory. It has a `composer.json` file that describes
all of the libraries that Symfony *itself* needs in order to work and in order
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
we usually want the *latest* version of all of our dependencies.

So before you run your tests, make sure to run `composer update`. Running
`composer install` isn't good enough, because there could already be a `composer.lock`
file from an *earlier* time you ran `composer install`. Running install makes
sure you're up to date.

Perfect - *now* we *do* have a `composer.lock` file.

## Running Symfony's Test Suite

Ok, we're ready to run the tests! Do it with:

```terminal
./phpunit
```

That's it. This is a wrapper around PHPUnit: it downloads some dependencies to
a different directory, then... starts running the tests! Andy... yea... there are
a lot of tests. I'm going to stop these by pressing Ctrl+C.

## Running only Some Tests

To be honest, I *never* run the full test suite locally. You just don't need to!
As you'll see in a few minutes, Symfony has a robust continuous integration setup:
when you make a pull request, Symfony's test suite is executed automatically.

Thanks to that, locally, I usually just run the tests I'm working on. Let's run
everything from SecurityBundle:

```terminal
./phpunit src/Symfony/Bundle/SecurityBundle
```

This time... if you didn't fast forward like me... you'd see that these tests only
take a minute or two. There *are* a few "skipped" tests: that's probably no something
you need to worry about. Some tests require a special PHP extension or other service
that your local computer might not have. So, those tests are skipped. No big deal.

## Adding our Test

Now that the tests are running, it's time to add our test! I'll double-click to
get back into the SecurityBundle. Because we want to test `TargetPathHelper`, the
test should live in `tests/Security`. Create a new PHP class called
`TargetPathHelperTest`. Make this extend the normal `TestCase` from PHPUnit. Then
add `public function testSavePath()`.

For the body of the test... yea... I'm going to cheat. This isn't a testing tutorial,
so I'll paste in some code I already prepared. Oh, and I need to auto-complete
a few things to get the missing `use` statements, like `FirewallMap` from SecurityBundle,
and a few other ones.

Our `TargetPathHelper` class doesn't really do much: it pushes most of the work
back to the methods from the trait. So, this test basically creates a bunh of mocks,
creates a `FirewallConfig` that returns a firewall name of `firewall_name`, and
then we ultimately make sure that this speicla key is set on the session to the
URL we passed to `savePath()`.

If you're interested in understanding this test better, you can totally look into
it further. But, the beautiful part is that creating a unit test for Symfony is no
different than creating a unit test for an application: there's no framework code
here.

Let's go run this one test directly:

```terminal
./phpunit src/Symfony/Bundle/SecurityBundle/Tests/Security/TargetPathHelperTest.php
```

## Adding the Service Config

Hey! It works! We *now* have a fully-functional new class *with* a test! But, because
we have not registered this class as a service yet, the user would still need to
do this manually. And that's a bummer.

Inside SecurityBundle, look at `DependencyInjection` and open `SecurityExtension.php`.
This class loads several XML files that provide all of the services for this bundle.
Inside the `Resources/config/` directory, open `security.xml`. Around line 136,
you'll start to see the services that our *new* service depends on - like the
FirewallMap and the FirewallConfig.

To register our new `TargetPathHelper` as a service, we could include some XML
config in *any* of these XML files - it doesn't technically matter. But, which
file makes most sense? Well, 1 minute ago, I wasn't sure. But now that I see all
of these related services in `services.xml`, I think we've already found the right
place. But, like everything else, if we're wrong, someone will tell us when we
create the PR;

Add a new service tag. For the id, how about, `security.target_path_helper`. I'm
trying to follow the existing naming conventions in this file. 

For the class, it's `Symfony`, well, let's cheat: copy the namespace from the class
above, paste, then `TargetPathHelper`. Inside, our service will need 3 arguments:
the session, firwall map & request stack.

Add  `<argument type="service" id="session" />`. Next, `<argument type="service" id=""`
and now we need the firewall map. Its id is up here: `security.firewall.map`.
Finally, `<argument type="service" d="request_stack">`.

Done! Our new class is now registered as a service!

## Enabling Autowiring

But, there's still *one* small piece missing. To allow the `TargetPathTrait` to
be autowired, like `FirewallMap` in the issue example, we need to create an alias
from that class to the service id - just like in the comment below.

To do this, add `<service id="" />`, go copy the *class* name, and paste it here.
Then, `alias=""`, copy the service *id* this time, and paste again.

That's it! The `TargetPathHelper` will now be an autowireable service.

And... we're done! The *last* thing I'd recommend is to create a real project and
test our your new feature manually. Sure, our class has a test... but there is
*not* a test for our service config - we could have messed that up. Because we
already went through this process earlier when testing Colin's PR, I'll skip it.
But, saying you tested your code in a real app can definitely help push your
PR forward.

Now, let's submit the PR!
