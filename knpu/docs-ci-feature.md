# All about the Docs: CI & Format

After waiting about a minute, oh! You'll notice that the continuous integration for
our documentation pull request failed? What does that even mean? Are there tests
for the docs?

Let's learn a few important things about the docs. First, you probably noticed
that all the files use a `.rst` extension. That's called Restructured text. It's a
lot like markdown... on steroids. It has, for example, a special syntax for linking
from one page to another - that's this `:doc:` stuff.

Behind the scenes, a build process turns all of this into HTML. But, if we have a
link to a document that doesn't exist, that build will fail!

Click "Details" to open Travis CI. The continuous integration system does exactly
that: it runs the build to make sure all the basic stuff is okay: syntax, links
and a few other things.

And... yep! We have an error: apparently `security.rst` line 1269 contains a reference
to a non-existent document `security/target_path`. That's the page we removed!
Instead of printing a broken link, we know we need to remove it!

Move back over, find the `security.rst` file and scroll down to line 1269. Ah.
This `toctree` thing is another feature of RST - it helps build the table of
contents. Remove the `security/target_path` line.

To make sure there aren't any *other* references, find your terminal and search:

```terminal
git grep security/target_path
```

Only one other spot - `redirection_map`. That's an internal tool to help us manage
old URLs: not something we need to worry about. Let's commit:

```terminal
git add -u
git commit --amend
```

I'm using amend because this isn't an important change worth making a second commit.
Push with:

```terminal
git push weaverryan remove-outdated-note --force
```

Hopefully the build will work this time.

## Branching for the new Feature

Ok: back to our original task: we need to write documentation for our new feature.
That means we need to create a documentation PR against the `master` branch. Go
back to to the terminal and create a new branch:

```terminal
git checkout -b target-path-helper origin/master
```

## Writing in RST

Awesome! Move back and open the `form_login.rst` file again. Scroll all the way
down to the bottom.

If you're not comfortable writing documentation *or* if you're a non-native English
speaker, you might think that writing docs isn't for you. That's totally not true!
The *really* important thing about writing docs is creating good code examples.
Pay less attention to writing words and more attention to writing *code* that shows
how your feature is used. When you submit your PR, the docs team can help reword &
improve the little details. The hard work is writing the code.

I'll start with a quick sentence, then right into the code block. I'll paste in
an example I already created.

And, yeah, this green background is super annoying: I don't normally use PhpStorm
for documentation. Anyways, a few important things about the format. First, any
technical term - like a class name - should be surrounded by two ticks. Second,
when you want to add a PHP code block, finish the previous sentence with two colons
and indent the code. And third, when you're inside the code, put as *much* context
as possible. For  example, I've added a note to say that this code is from a controller.
You should also be sure to include any `use` statements needed for the new code.
Well, we don't include `use` statements for *everything*. For example, I didn't
include the `use` statement for the `Request` because people probably know what that
is and it's not directly relevant to what we're doing. But, I *did* add the `use`
statement for the class we're talking about: `TargetPathHelper`.

Finally, we recommend *avoiding* big paragraphs of explanatory text. That's why
we just included once sentence then code. If you want to explain a bit more, try
adding comments *into* the code instead. We've found that people tend to read the
code, but skip the paragraphs completely. Use that to your advantage!

And... that's it! Sure, there are a lot of other little format details. But,
the docs have *plenty* of examples of how to do just about anything.

Oh, but because this is a new feature, I'll add one more thing. Right above the
new text, add a special `versionadded:: 4.2` tag. If our feature is merged, it will
be included in Symfony 4.2 - the next Symfony version. This will add a new note
highlighting this fact.

This syntax is also special to RST. You can make tips and notes the same way.

Ok - let's move over, add this file, and commit:

```terminal-silent
git add -u
git commit -m "Documenting the new TargetPathHelper"
```

And... push:

```terminal
git push weaverryan target-path-helper
```

Move back over to GitHub. Hey! The tests passed on our other pull request! Sweet!
And, just like always, if you don't see the yellow bar here, go back to your fork,
select the new branch and hit "New pull request".

This time, our pull request *should* be against the master branch. I'll prefix
the title with `[WCM]` - that means "Waiting for Code Merge" - a little flag to
help us know this is for a still-unmerged new feature.

For the body, saying see `symfony/symfony#28181` should be enough. Create that
pull request!

Hey! You're now a docs expert! So, I hope to see a *bunch* of docs PR's from you.
Do it!
