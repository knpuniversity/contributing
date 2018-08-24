# Recipes & Other Repositories

We've seen a *bunch* of ways to contribute: triaging issues & pull requests,
creating pull requests and contributing to the documentation.

But there's so much more! The Symfony ecosystem is a *lot* bigger than just
these two repositories. For example, go to [github.com/symfony](https://github.com/symfony).
Woh! There are *118* repositories under Symfony! Click on, for example, `dom-crawler`.

This repository is what's called a "subtree split". Cool name, right? The DomCrawler
component *actually* lives and is managed inside of the main `symfony/symfony` repository
that we've been working in. I'll show you: open that repository and navigate to
`src/Symfony`, `DomCrawler`.

*This* is the DomCrawler component. An automated process *splits* this directory
into its own repository so that people can use it independently. If you want to contribute
to DomCrawler, you'll do it in `symfony/symfony`. The sub-tree split is read-only.

How can you know if a repository is a sub-tree split? You'll notice that the "Issues"
tab has been disabled.

## Other Repositories

*Many* of those 118 repositories under Symfony *are* subtree splits. And so, you
contribute to them in the main repo. But a lot of them are *normal* repositories
that you *can* contribute to directly. For example, [recipes](https://github.com/symfony/recipes).

This is one of the *coolest* ways that you can help Symfony. If you install a
bundle or library and it doesn't have a good recipe, or doesn't have a recipe
at all, you should totally add or improve it! You can create a recipe here, or
on the less-stringent `recipes-contrib`.

We're going to improve a recipe in a minute. But, first, I want to point out a few
other repositories. Search for "flex". This is the Composer plugin that powers
the recipe system. Search for "maker". MakerBundle is all about code generation.
Try "encore": this is a library written in Node that helps make Webpack easy. And,
one more: panther a new library that allows you to functionally test your pages,
including the JavaScript on those pages.

And there are a *lot* more - like `MonologBundle`. The point is: *each* of these
needs help triaging issues and reviewing pull requests. And actually, because these
independent libraries get less traffic, you can make an even bigger difference in
a short amount of time.

## Improving a Recipe

Let's make one small contribution to the recipes. Go back to the main recipes
repository. One of the recipes is for the `twig/extensions` library: a standalone
PHP library. When you install that package, its recipe gives you a new
`config/packages/twig_extension.yaml` file. *These* are the four classes provided
by that library. After installing the library, you just need to uncomment the
ones that you want.

Let's make this even *more* obvious by adding a comment above to describe that.

To do that, go back to main recipes page and copy the clone URL. Hopefully, this
process is starting to feel boring... and repetitive. At your terminal, move back
into the `contributing/` directory and clone that:

```terminal-silent
git clone git@github.com:symfony/recipes.git
```

Then, move inside. To create a pull request, we will eventually need our own fork.
I already have a fork, so I'll skip straight to copying *my* URL, going back to
the terminal, and adding that remote: `git remote add weaverryan` and paste.

```terminal-silent
git remote add weaverryan git@github.com:weaverryan/recipes.git
```

Back in the editor, I'll close a few files. Then open
`twig/extensions/1.0/config/packages/twig_extensions.yaml`. Add the comment:

> Uncomment any lines below to activate that Twig extension

Brilliant! Let's commit this! The recipes repository is a bit unique: it *only*
has a `master` branch. So, we'll create our new branch from it:

```terminal
git checkout -b adding-twig-extensions-note
```

Then, add, commit

```terminal-silent
git add -u
git commit -m "Adding a small note about what to do in the twig_extensions.yaml file"
```

and...

```terminal
git push weaverryan adding-twig-extensions-note
```

Awesome! Go back to GitHub! Sweet! Here's the yellow bar: click "Compare & pull request".
Add a small note about *why* we think this is a good idea. And, make sure we don't
have any other "surprise" changes. Looks good. Hit "Create pull request".

## Testing a Recipe

This was a pretty simple change. But, when your changes are bigger, you'll
probably want to be able to *test* your recipe before it's merged: to see how it
works in the real world!

And... we can do that! Almost immediately after posting the PR, you'll hopefully
see a message: "Pull request passes validation". This means that our changes passed
a few rules that are described in this repository's README.

Another spot says "View deployment". Open that in a new tab. This is *really* cool.
The Flex server just "deployed" our recipe. And we can temporarily change our Flex
"endpoints" to *use* our new recipe... even though it's not merged yet!

Copy the `export` line, find a terminal and paste. We just set an environment
variable on this terminal tab only. To test the recipe, let's just move into
one of our projects, like `triage_issue_27901`.

Then run:

```terminal
composer require twig/extensions
```

When we run that, it gives us a warning that we're not using the normal Symfony
endpoint... which is perfect. And... it looks like it worked!

Go check it out: open `config/packages/twig_extensions.yaml`. We got it!
No surprise for *this* small tweak. But for bigger changes, this is *so* useful.

When you're done playing with things, be sure to unset that variable so that you
once again use the *real* Symfony endpoint.

## See you on GitHub!

Ok people, that's it! Oh, there is *so* much good stuff to work on in the Symfony
world. And we need the help! There's complex stuff, like working on the main `symfony/symfony`
repository. But there are also many, many other ways to contribute, like improving
the documentation, working on recipes or just finding that third party library or
bundle you love and helping to improve it or its docs. *You* are the person
that can make a difference by adding that feature or fixing that bug.

And if you have more questions on contributing, ask them down in the comments! We
would love to help answer them

Ok people, seeya on GitHub!
