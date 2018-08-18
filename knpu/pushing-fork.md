# Autowiring & Pusing to your Fork

There's still *one* small piece missing with the service config. To allow `TargetPathTrait`
to be *autowired*, like `FirewallMap` in the issue example, we need to create an alias
from that class to the service id - just like in the comment below.

## Enabling Autowiring

To do this, add `<service id="" />`, go copy the *class* name, and paste it here.
Then, `alias=""`, copy the service *id* this time, and paste again.

That's it! The `TargetPathHelper` will now be an autowireable service.

And... we're done! The *last* thing I'd recommend is to create a real project and
test our your new feature manually. Sure, our class has a test... but there is
*not* a test for our service config - we could have messed that up. Because we
already went through this process earlier when testing Colin's PR, I'll skip it.
But, saying you tested your code in a real app can definitely help push your
PR forward.

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
Actually, that's *great* - that sounds like too much responsibility to me.

Instead of pushing directly to Symfony, we need to fork the repository. Click "Fork"
and either create a new fork, or, if you already have a fork like me, click into
it. Here we are: `weaverryan/symfony`.

Next, click "Clone or download", copy the URL, the move back over to the terminal
to add this ass a new remote: `git remote add weaverryan` and then paste:

```terminal-silent
git remote add weaverryan git@github.com:weaverryan/symfony.git
```

Awesome! *Now* we can push. The branch we created is called `target-path-helper`.
Cool:

```terminal
git push weaverryan target-path-helper
```

Ok, back on GitHub, if you're locally, you *should* see a little yellow banner
about your new branch. This banner doesn't *always* show up, so if it doesn't,
you can refresh, find the `target-path-helper` branch and click "New pull request".

Next, let's fill this in & learn about Symfony's continuous integration system
and the famous fabbot.
