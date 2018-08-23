# Triaging a Bug Issue

We just reviewed our first pull request! So let's see what other trouble we can
get into! One of the *most* important thing you can do is to triage *issues*.

If an issue is for a feature request, then it's probably a discussion about whether
or not it's a good idea and the best implementation. Helping those discussions is
great. But... click on the "bug" label. *These* are where you can *really* help.

Symfony is pretty stable & complex. So, if there *is* a bug, it's usually pretty
complicated or involves some edge-case situation. These can take a lot of time to
understand and replicate. The core team *really* needs help from the community to
verify the bug, ask for more information from the user and, ultimately, to create a
"reproducer": a tiny app that clearly shows that bug in action.

## Triaging an Issue

Let's triage an issue I found a few days ago: [#27901](https://github.com/symfony/symfony/issues/27901).
Ok, the user says that he got an error when trying to serialize a Doctrine
QueryBuilder with the web profiler: something about not being able to serialize
a PDO instance.

The web profiler works by collecting a *bunch* of information about the request
and serializing it to a cache file. It looks like something failed during that
process. This is actually a pretty nice bug report because he lists the steps
to reproduce: install the web profiler and then call `execUpdate` on Doctrine's
lower-level `Connection` object, passing it a QueryBuilder. He even suggests a
solution!

Ok, so, how can we help? First: see if we can reproduce & understand the issue.
Let's create another small project for this. Notice that this is an issue that's
reported on the stable version of Symfony: 4.1. So, we should create a new app based
on that same version - not `dev-master` like before.

Press Ctrl+C to stop the server, move back up to the top `contributing` directory
and run:

```terminal
composer create-project symfony/skeleton triage_issue_27901
```

Because we're not specifying a version, it will use the current stable version: 4.1.

When this finishes, move into the directory. To replicate this bug, we will
at *least* need to install the stuff he's using: Doctrine and the web profiler.
Back at the terminal, just install Doctrine for now so we can write some code:

```terminal
composer require orm
```

And... done! Close the old directory and open this new one. Then, go straight to
create a new controller class: how about `Issue27901Controller`. Give it a
`public function test()`.

Ok: check back on the issue. He's using the Doctrine Connection object - a lower-level
object I don't use too often. To see out how to get it, find your terminal and
run:

```terminal
php bin/console debug:autowiring
```

and scroll up. Yep! It looks like we can type-hint a `Connection` class to get
the service we need. Do that: `Connection $connection`.

Next, he calls `execUpdate()` and passes it a `QueryBuilder` argument. You may
already be familiar with the QueryBuilder from Doctrine. Well, in this case,
because we're using the lower-level `Connection` class from the Doctrine dbal
library, the `QueryBuilder` is *also* a lower-level class from that library.

These are the types of little details that can make triaging a bug tough! But,
it's also part of the fun: you'll need to *really* dig into the code to find out
what's going on.

Create the QueryBuilder with `$connection->createQueryBuilder()`. I won't even
do anything with it yet: we're still investigating. Next, he calls `execUpdate()`.
Oh, but that doesn't exist! I bet he meant `execteUpdate()` - pass that `$qb`.

Great! At this point, I would normally install the web profiler, create some database
entities and use a real query in the controller to see if we can replicate the
error. But, before we do that, I noticed something: the first argument looks like
it's supposed to be a string! Hold Command or Ctrl and click to open the
`executeUpdate()` method.

Yep! The first argument should be a string! But, the user is passing a `QueryBuilder`
object! In other words, I don't think this is a bug! The *only* reason the user's query
actually works is that, if you open the `QueryBuilder` class, it has a `__toString()`
method. Doctrine is probably accidentally converting this object to a string and
using that SQL.

This is why his possible solution is to, inside a related class, convert the
`sql` - which is a `QueryBuilder` in his case - into a string. That *would* fix
things, but I don't think this is really a bug.

But even still, it's *awesome* that the user opened this issue. In a lot of cases,
*even* if there is no bug, we can use the mistake to improve things, like with better
error messages.

## Replying to the Issue

So, let's reply! And give as *many* specific reasons why we think this might not
be a bug: in this case, that the first argument expects a string.

As extra credit, I'll link to this exact code. Go to the `doctrine/dbal` repository.
Then, press the letter "t" to open this search screen. I *live* by this shortcut.
Look for `Connection.php` and open it. Search for `executeUpdate()` and... click to
select that line: this updates the URL to point here.

*Then* - here's another trick - press the "y" key. This changes the URL from `master`
to the actual commit sha. This helps make sure that this link - to line 1068 - will
*forever* point to the line we want - even if someone makes changes on the `master`
branch and moves this line.

I'll paste the link and add a few more details. I *really* try to be as friendly
as possible: this is our chance to help make Symfony a warm & welcoming community.
Even if this is *not* a valid issue, it's great the user took their time to help
report it.

And... boom! You probably won't have the power to close the issue, but this should
make it easy for someone else to do that. Achievement unlocked!

This bug turned out to *not* be a bug. So, let's hunt for a bug that really *is*
a bug. And learn how to create and share a "reproducer" project... which is seriously
*almost* as valuable as actually fixing the bug.
