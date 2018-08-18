# Uh oh: Documentation Bug

The *last* TODO for our PR is to create a documentation PR. And, honestly, because
we're talking about contributing, in my opinion, making changes to the documentation
is probably the easiest and most effective way to contribute to Symfony! There are
*tons* of great ways to help the docs, even if you're *not* documenting a new feature.

For example, imagine you're reading the docs - like the forms page. Then, you find
something that's inaccurate or confusing. Well, just go back to the top, click
"Edit this page", and you'll be inside an editor on GitHub where you can make
some changes and create a pull request.

I've worked on the documentation for years. And the *best* way to improve it is
to get feedback from real people who are trying to use it. Taking a few minutes
to reword a paragraph could save someone else *hours*. That's pretty cool.

## Cloning the Docs

Go back to the homepage of the Symfony docs. Copy the clone URL: let's clone this
down onto our machine. At your terminal, move back into the main contributing directory
and run `git clone` and paste.

```terminal-silent
git clone git@github.com:symfony/symfony-docs.git
```

I'll also go to PhpStorm and move us back into this main `contributing/` directory
so we can see all of the test projects, symfony itself and the new `symfony-docs`
folder.

## Hunting down a Bug

Ok: we want to document our new `TargetPathHelper`. Great! Except... where should
this live? This can be harder than you think: it's not always obvious *where*
some docs should live. If you're not sure, don't worry: just choose some place
that makes sense to you. If there's a better place, someone will tell you when
reviewing your PR.

Head back to your terminal and move into `symfony-docs`. Because this feature
builds off of the `TargetPathTrait`, let's see where *that's* documented:

```terminal
git grep TargetPathTrait
```

Ok, apparently that's covered in some `form_login.rst` page. Go find that in
PhpStorm: `security/form_login.rst`. Look *all* the way down at the bottom. Yep,
here is where it talks about `TargetPathTrait`. Let's add a few more details below
this about our new class.

But wait... when I first opened this document, I noticed something interesting on
top. It describes how this "target path" feature works in general. Then, there's
a note below: sometimes redirecting to the originally requested page can cause
problems, like if a background AJAX request *appears* to be the last visited page,
causing the user to be redirected there.

That makes sense... except, it's not true! Nope, this note is out of date: Symfony
*no* longer has this problem. I think I just found a documentation bug!

Let's make sure: go to [github.com/symfony/symfony](https://github.com/symfony/symfony).
Then click "t" to open the "file search" and look for a class called `ExceptionListener`
from the `Security/` component. *This* is the class that's responsible for setting
the target path. It happens all the way down at the bottom in `setTargetPath()`. If
you go to a page like `/admin` as an anonymous user, *right* before you're redirected
to the login page, this `setTargetPath()` method is called.

And, cool! This uses the method from `TargetPathTrait`, just like we did. But, check
this out, it *checks* to see if the request is an AJAX request - that's the
`isXmlHttpRequest()` method. If it *is* an AJAX request, it does *not* set the
URL into the session. Yea! The documentation is wrong!

## Finding the Correct Bug Branch

The question *now* is: how *old* is this bug? How long ago was this changed in
Symfony and what versions of the *docs* do we need to change? Head back to the
[Symfony Roadmap](https://symfony.com/roadmap). The three maintained branches
are 2.8, 3.4 and 4.1. Remember: when fixing a bug, you should fix it in the
*oldest* maintained branch where the bug exists.

To figure out when the change was made to Symfony, let's `git blame` this file.
Scroll back down to the bottom. Hmm, so this line was last modified two years
ago. And if you look at that commit, its changes do *not* include the AJAX part
of this line. Yep, the change we're looking for is *more* than two years old.
*And* it was first included in Symfony 2.7!

In other words, the AJAX fix has existed since Symfony 2.7, if not earlier. But,
because Symfony 2.7 is no longer maintained, we'll fix this on the 2.8 branch of
the documentation. Then, after our pull request is submitted, our changes will
be merged *up* into all of the newer branches automatically.

## Fixing a Docs Bug

Awesome! Find your terminal and create the new branch:

```terminal
git checkout -b remove-outdated-note origin/2.8
```

Move back to the document. Yep, that bad note *did* exist even back then. And,
woh! It links to a *whole* other document that describes how to work around this
problem. We can delete all of this!

Delete the note first. Then delete that other file:

```terminal
git rm security/target_path.rst
```

And... we're ready!

```terminal
git status
git add -u
git commit
```

And describe why we're deleting all this stuff.

## Creating the Pull Request

Ok, the code is ready! Head back to GitHub and fork the repository if you haven't
already. Then, copy your git URL and add your remote: `git remote add weaverryan`
and paste. Now, push!

```terminal
git push weaverryan remove-outdated-note
```

Move back and... if you're lucky, you'll see a yellow bar. We *are* lucky this
time! Click "Compare and pull request".

Oh, but hmm: why are there two extra commits by other people? Because we need to
change our base branch back to 2.8.

Much better. In the description, we want to make this as easy as possible to merge.
So, let's describe *why* we're removing this and that we checked the code to be
sure.

Ok... submit! Next, the docs *also* have a continuous integration system. I want
to talk about that, then write our own new documentation and learn a bit about
the docs format.
