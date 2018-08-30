# Services, Autowiring & Pushing To GitHub

We *now* have a fully-functional new class *with* a test! But, we have *not* registered
this class as a service yet. Which means... the user would still need to do that
manually. That's a bummer!

## Adding the Service Config

Inside SecurityBundle, look at `DependencyInjection` and open `SecurityExtension.php`.
This class loads several XML files that provide all of the services for this bundle.
Inside the `Resources/config/` directory, open `security.xml`. Around line 136...
yep! You'll see the services that our *new* service depends on - like `FirewallMap`
and `FirewallConfig`.

To register our new `TargetPathHelper` as a service, we could include some XML
config in *any* of these XML files: it doesn't *technically* matter. But, which
file makes the most sense? Well, 1 minute ago, I wasn't sure. But now that I see all
of these related services, I think we've *already* found the right
place. If we're wrong, someone will tell us when we create the PR.

Add a new service tag. For the id, how about, `security.target_path_helper`. I'm
trying to follow the existing naming conventions in this file. 

For the class, it's `Symfony`, well, let's cheat: copy the namespace from the class
above, paste, then `TargetPathHelper`. Inside, our service will need 3 arguments:
the session, firewall map & request stack.

Add  `<argument type="service" id="session" />`. Next, `<argument type="service" id=""`
The id for the firewall map is up here: `security.firewall.map`.
Finally, `<argument type="service" id="request_stack" />`.

Done! Our new class is now registered as a service!

But... there's still *one* small thing missing with this service. To allow `TargetPathHelper`
to be *autowired*, like `FirewallMap` in the issue example, we need to create an
*alias* from that class to the service id - just like in the comment below.

## Enabling Autowiring

To do this, add `<service id="" />`, go copy the *class* name, and paste it here.
Then, `alias=""`, copy the service *id* this time, and paste again.

That's it! The `TargetPathHelper` will now be an autowireable service.

And... we're done! The *last* thing I'd recommend is to create a real project and
test your new feature manually. Sure, our *class* has a test... but there is
*not* a test for our service config: if we have a typo on the class name, we wouldn't
know!

However, because we already went through the process earlier when testing Colin's
PR, I'll skip it. But, saying you tested your code in a real app can definitely
help push your PR forward.

Hey! We're done with all the hard work! Let's push our code to GitHub!

## Pushing your Fork

Head over to the terminal that's in the `symfony/` directory and run:

```terminal
git status
```

No surprises! Add everything and then commit with a nice message that briefly
describes what we're doing:

```terminal-silent
git add .
git commit -m "Adding a new TargetPathHelper class and service"
```

Cool!

```terminal-silent
git remote
```

Right now, we have two remotes: `colinodell` & `origin`, which is the main
`symfony/symfony`. But, of course, we don't have access to push directly to `origin`.
Actually, that's *great* - that sounds like *way* too much responsibility to me.

Instead of pushing directly to Symfony, we need to fork the repository. Click "Fork"
and either create a new fork, or, if you already have a fork like me, click into
it. Here we are: `weaverryan/symfony`.

Next, click "Clone or download", copy the URL, then move back over to the terminal
to add this as a new remote: `git remote add weaverryan` and then paste:

```terminal-silent
git remote add weaverryan git@github.com:weaverryan/symfony.git
```

Awesome! *Now* we can push. The branch we created is called `target-path-helper`.
So:

```terminal
git push weaverryan target-path-helper
```

Back to GitHub! If you're lucky, you'll see a little yellow banner about your new
branch. This banner doesn't *always* show up, so if it doesn't, you can refresh,
find the `target-path-helper` branch and click "New pull request".

Next, let's fill this in & learn about Symfony's continuous integration system
*and* the famous... fabbot!
