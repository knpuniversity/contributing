# Coding a new Feature

We've just created a new branch based off of Symfony's `master` branch. And now,
we're ready to create our new `TargetPathHelper` class. But... where should it live?
It's related to Security... which means it could either live in the Security component,
or SecurityBundle.

## Components Versus Bundles

As a general rule, most code should live in a component so it's reusable even
outside of Symfony. But, sometimes, you'll write code that's *really* integrated
with Symfony. And that will live in the bundle. Don't over-think it too much:
it'll usually become pretty obvious if you put something in the wrong spot.

Press Shift+Shift and search for a file that is closely related to our new feature:
``TargetPathTrait``. Ok, this lives in the Security component. I'll double click
on the directory to move here. At first, it seems like ``TargetPathHelper`` should
live here. And that's where *I* would put it at first. I say "at first" because,
if you started coding, you'd notice a problem.

This class will *ultimately* use the `FirewallMap` class internally to do its
work. There are *two* `FirwallMap` classes: one lives in the Security component,
and the other lives in SecurityBundle. After digging a little bit, you'd find out
that *we* will need to use the one from SecurityBundle.

And here's why that's important: a class in a component *can* depend on classes
from other components. But, it can *never* depend on a class from a *bundle*.
Because our new ``TargetPathHelper`` depends on a class from SecurityBundle, it
*can't* live in the component: it must live in the bundle.

If you get this wrong, no big deal: someone will help you out on your pull request.

Go find SecurityBundle and look inside ``Security``. Hey! Here are the ``FirewallMap``
and ``FirewallConfig`` classes we'll be using! That *probably* means this directory
is a great spot: create the new PHP class: ``TargetPathHelper``. Add our first
public function, how about just ``savePath()`` with a string `$uri` argument.

Symfony 4.0 and above requires PHP 7.1, so you *should* use scalar type-hints
and return types. But, Symfony does *not* use the ``void`` return type.

## All About PHPDoc

Because this is a public function, we should add some PPH doc to describe it.
Add a clear, but short description above this. In general, Symfony does *not*
have a lot of PHPDoc: we don't want to maintain too much documentation inside
the code - there's a separate repository for documentation.

And, thanks to the `string` type-hint, the `@param` documentation is totally redundant
and should be removed... *unless* it's valuable to add more information about it.
I'll do that... even though it doesn't add a *lot* of extra context.

Also *every* PHP file in Symfony should have a copyright header on top. Go copy
that and paste it here. Don't worry too much about these details: it's easy to add
them later if you forget.

## Injecting the Services we Need

To make our job easier, use the `TargetPathTrait` on top of the class. Then all
*we* need to do is say `$this->saveTargetPath()`.

But... hmm... this needs 3 arguments: the session, provider key - which is the
firewall name - and the URI. We know that we can get the firewall name by using
the ``FirewallMap`` service. This means that we need the `Session` and the
`FirewallMap` to be injected as constructor arguments. Add `SessionInterface $session`
 and `FirewallMap` - the one from SecurityBundle - `$firewall`.
 
 I'll press Alt+Enter and select initialize fields to create those properties and
 set them. Make sure to remove the PHPDoc above each property: this is redundant
 thanks to the constructor type-hints.

To calculate the provider key, create a new private function: `getProviderKey`
that will return a string. For now, just put a TODO.

Back up in `setTargetPath()`, pass `$this->session`, `$this->getProviderKey()`
and the `$uri`.

Awesome! Look back at `getProviderKey()`. Did you notice? I'm not using PHP documentation?
That's for two reasons. First, this is a private function, and those are typically
*not* documented inside Symfony. And second, we already have the return type, so
there's no reason to duplicate that.

Finally, let's fill in this function! To get the firewall name, we use the FirewallMap,
call `getFirewallConfig()` on that and pass it the request. Let's do that here:
`$firewallConfig = $this->firewallMap->getFirewallConfig()`. But, hmm, now we need
the Request object... and we don't have that. No problem: add a third constructor
arg: `RequestStack $requestStack`. I'll hit alt+Enter again to create that property
and set it. Clean off the PHPDoc, then head back down.

Normally, when you use the RequestStack, you call its `getCurrentRequest()` method
to get the request. But, in this case, I'm going to use another method:
`$this->requestStack->getMasterRequest()`. I'm not 100% sure this is correct.
The whole topic of requests and sub-requests is pretty complex. But, essentially,
Symfony's security firewall only operates on the outer, "master" request. So,
to find the active firewall, that's what we should use. If I'm wrong, hopefully
someone will tell me on the pull request.

Next, if you look at the `getFirewallConfig()` method it's possible that this would
return null. Code for that: if `null === $firewalllConfig`. This is another Symfony
coding standard - backwards if statements known as Yoda conditionals! But, don't
worry too much about coding standards - Symfony has a magic way of fixing them
that we'll see later.

Inside, if there's no firewall config for some reason, throw a new `LogicException`
with as clear a message as possible. Why a LogicException? Well, it seems to make
sense - something went wrong logically. And... usually, the exact exception class
won't matter. But sometimes, there *will* be a specific exception class you should
throw. If you're not sure, don't worry: this is *another* spot where someone reviewing
your PR can help.

Finally, at the bottom, `$firewallConfig->getName()`.

And that should be it!

While we're here, let's add one more function: `getPath()` that will return a
string. Inside, return `$this->getTargetPath()` with `$this->session` and
`$this->getProviderKey()`. This time, I *will* add some PHPDoc. I don't need
`@return` - that's redundant - but I will add a description about what this method
does.

## Making the Class final

Hey, we're done! Yea, we still need to write a test & add some config to register
this as a service: we'll do that next. But, this class should work!

However... I'm going to make *one*, ahem, final change: add `final`.

Making this class final means that nobody is allowed to subclass it. Why are we
doing this? Because, in the future, if the class is `final`, it will be easier
to make changes to it without breaking backwards compatibility. Basically, if you
*allow* a class to be sub-classes, you have to be a bit more careful when making
certain changes. Making classes `final` is a good "default" for new Symfony classes.

Of course, if there is a legitimate use-case for some to sub-class this, then you
don't *need* to make it final. But, while we can easily *remove* `final` later,
we can't *add* `final` in the future, at least not without jumping through a few
extra hoops to avoid breaking backwards compatibility.

Ok, let's get to the config & test!
