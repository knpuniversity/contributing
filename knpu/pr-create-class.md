# Coding a new Feature

We've just created a new branch based off of Symfony's `master` branch. And now,
we're ready to create the amazing new `TargetPathHelper` class. But... where should
it live? It's related to Security... which means it could live in the Security component
*or* SecurityBundle.

## Components Versus Bundles

As a general rule, most code should live in a component so that it's reusable even
outside of the framework. But, sometimes, you'll write code that's *really* integrated
with the framework. That code will live in the bundle. My best advice... don't
over-think it: it usually becomes pretty obvious if you put something in the wrong spot.

Press `Shift`+`Shift` and search for a file that's closely related to our new feature:
`TargetPathTrait`. Ok, this lives in the Security component. I'll double click
on the directory to move there. At first, it seems like `TargetPathHelper` should
live right here. And that's where *I* would put it at first. I say "at first" because,
if you started coding, you'd notice a problem.

What problem? This new class will *ultimately* use the `FirewallMap` class internally
to do its work. There are *two* `FirwallMap` classes: one lives in the Security component,
and the other lives in SecurityBundle. After digging a little bit, you'd find out
that *we* will need to use the one from SecurityBundle.

And here's why that's important: a class in a component *can* depend on classes
from other components. But, it can *never* depend on a class from a *bundle*.
Because our new `TargetPathHelper` needs a class from SecurityBundle, it *can't*
live in the component: it must live in the bundle.

If you get this wrong, no big deal: someone will help you out on your pull request.

Go find SecurityBundle and look inside `Security`. Hey! Here are the `FirewallMap`
and `FirewallConfig` classes we'll be using! That's a good sign! Create the new
PHP class: `TargetPathHelper`. Add our first public function, how about just
`savePath()` with a string `$uri` argument:

[[[ code('34dbbde0c1') ]]]

Symfony 4.0 and above requires PHP 7.1, so you *should* use scalar type-hints
and return types. But, Symfony does *not* use the `void` return type.

## All About PHPDoc

Because this is a public function, we should add some PHPDoc to describe it.
Add a clear, but short description above this:

[[[ code('f0d4656db8') ]]]

Actually, Symfony does *not* have a lot of PHPDoc... which might seem weird at first.
The reason is that we don't want to maintain too much documentation inside the code - we
use a separate repository for documentation.

Oh, and, thanks to the `string` type-hint, the `@param` documentation is totally
redundant and should be removed... *unless* there's some valuable *extra* info
that you want to say about it. I'll keep it and add some extra notes... even though
it doesn't add a *lot* of extra context:

[[[ code('13e2f22d73') ]]]

Also *every* PHP file in Symfony should have a copyright header on top. Grab that
from another file and paste it here:

[[[ code('d7eb34318a') ]]]

Don't worry too much about these details: it's easy to add them later if you forget.

## Injecting the Services we Need

To make life nicer, use `TargetPathTrait` on top of the class:

[[[ code('387d0553aa') ]]]

Then all *we* need to do is say `$this->saveTargetPath()`:

[[[ code('c883dd31df') ]]]

But... hmm... this needs 3 arguments: the session, provider key - which is the
firewall name - and the URI. We know that we can get the firewall name by using
the `FirewallMap` service. Let's add some constructor arguments: `SessionInterface $session`
and `FirewallMap` - the one from SecurityBundle - `$firewallMap`:

[[[ code('a9bd2fd086') ]]]
 
I'll press `Alt`+`Enter` and select initialize fields to create those properties and
set them:

[[[ code('71a494404e') ]]]

Make sure to remove the PHPDoc above each property: this is redundant thanks to the
constructor type-hints.

To calculate the provider key, create a new private function: `getProviderKey()`
that will return a string. For now, just put a TODO:

[[[ code('5f24803465') ]]]

Back up in `setTargetPath()`, pass `$this->session`, `$this->getProviderKey()`
and the `$uri`:

[[[ code('5689c48bc0') ]]]

Awesome! Look back at `getProviderKey()`:

[[[ code('d0a8432df1') ]]]

I didn't add any PHPDoc to this function, but that's *not* because I'm lazy. Or...
not entirely because I'm lazy. Really, it's for two reasons. First, this is a private
function, and those are typically *not* documented inside Symfony. And second,
we already have the return type - no reason to duplicate it!

Lets finish this function. To get the firewall name, we need to use the FirewallMap,
call `getFirewallConfig()` and pass it the request. Ok:
`$firewallConfig = $this->firewallMap->getFirewallConfig()`. But, hmm... we don't
have the Request object! No problem: add a third constructor arg:
`RequestStack $requestStack`. I'll hit `Alt`+`Enter` again to create that property
and set it. Clean off the PHPDoc, then head back down:

[[[ code('5205685e4a') ]]]

Normally, when you use RequestStack, you call its `getCurrentRequest()` method
to get the request. But, in this case, I'm going to use another method:
`$this->requestStack->getMasterRequest()`:

[[[ code('c9bc0ea72e') ]]]

I'm not 100% sure that this is correct. The whole topic of requests and sub-requests
is pretty complex. But, basically, Symfony's security firewall only operates on the outer,
"master" request. So, to find the active firewall, that's what we should use. If I'm wrong,
hopefully someone will tell me on the pull request.

Next, if you look at the `getFirewallConfig()` method, it's possible that this will
return `null`. Code for that: if `null === $firewallConfig`:

[[[ code('0d29e53a47') ]]]

This is *another* Symfony coding convention: we use Yoda conditionals!

> Mysterious, Symfony's coding conventions are. Herh herh herh.

But hey! If the force isn't strong with you today, don't worry: Symfony has a magic
way of fixing coding convention problems that I'll show you later.

If there is no firewall config for some reason, throw a new `LogicException` with
as clear a message as possible:

[[[ code('cce89d4786') ]]]

Why a LogicException? Well, it seems to make sense - something went wrong... logically.
And usually, the exact exception class won't matter. If it *does* matter, someone
will tell you when reviewing your PR.

Finally, at the bottom, `return $firewallConfig->getName()`:

[[[ code('1641edc2e9') ]]]

That should be it!

While we're here, let's add one more function: `getPath()` that will return a
string. Inside, return `$this->getTargetPath()` with `$this->session` and
`$this->getProviderKey()`:

[[[ code('706c8b1b75') ]]]

This time, I *will* add some PHPDoc. I don't need `@return` - that's redundant - but
I will add a description about what this method does:

[[[ code('4089a1b0f7') ]]]

## Making the Class final

And... we're done! Yea, we still need to write a test & add some config to register
this as a service: we'll do that next. But, this class should work!

However... I *am* going to make *one*, ahem, final change: add `final`:

[[[ code('e6f6698af2') ]]]

Making this class final means that nobody is allowed to subclass it. Why are we
doing this? Because, in the future, if the class is `final`, it will be easier
to make changes to it without breaking backwards compatibility. Basically, if you
*allow* a class to be sub-classed, you have to be a bit more careful when making
certain changes. Making classes `final` is a good "default" for new Symfony classes.

Of course, if there is a legitimate use-case for some to sub-class this, then you
don't *need* to make it final. But, while we can easily *remove* `final` later,
we can't *add* `final` in the future, at least not without jumping through a few
extra hoops to avoid breaking backwards compatibility.

Ok, let's add some service config & a test!
