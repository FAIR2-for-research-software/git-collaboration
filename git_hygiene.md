---
title: "Git Hygiene"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- How do I configure Git globally and locally?
- How do we keep our repository and history clean?
- What are atomic commits?
- How do I avoid `Fixing typo` commits?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Command line configuration of Git.
- Manually editing Git configuration files.
- Use `.gitignore` to avoid adding unnecessary files.
- Understand the concept of Atomic commits.
- Ammending and fixing commits.
- Squashing commits.
- Staging hunks with `--patch`.

::::::::::::::::::::::::::::::::::::::::::::::::

## Atomic Commits

The idea of atomic commits is that they are small self-contained commits focused on one issue, all the changes are
typically in a small subset of files, e.g. only a particular module and its associated test file. You may have
learnt to make lots of small commits frequently and so you're history may look like.

``` bash
git log --oneline
  0d2f520 Correct spelling
  325d038 Document function xyz
  86d7633 Add docstring to function xyz
  a58d6e7 Fix function xyz to pass tests
  9429ab4 Add test for function xyz
  bb560b0 Add function xyz
```

Here six commits have been made for adding the `xyz` function, writing tests that pass, adding docstrings to the
function and correcting some spelling mistakes. But all of these pertain to one issue/task, namely adding the `xyz`
function. As the work is self-contained and we've not added to any other files they could be a single commit.

Git has a few functions to help here and we'll go through those in turn.

We'll use the `python-maths` repository as an example and will make a new branch called
`<github-user>/amend-fixup-tutorial` to add a `CONTRIBUTING.md` file to.

``` bash
cd pytest-maths
git switch -c ns-rse/amend-fixup-tutorial
  Switched to a new branch 'ns-rse/amend-fixup-tutorial'
```

We already added a basic `CONTRIBUTING.md` to the repository in the previous episode, we will add to that.

``` bash
echo "\nPlease make a fork of this repository, make your changes and open a Pull Request." >> CONTRIBUTING.md
git add -u
git commit -m "docs: Ask for PRs via fork in CONTRIBUTING.md"
```

``` bash
git logp
  01191a2 (HEAD -> ns-rse/amend-fixup-tutorial) docs: Ask for PRs via fork in CONTRIBUTING.md
```

### Making Amends

Sometimes you will have made a commit and then realise that you want to add more to it or perhaps you forgot to run your
test suite and find that on running it your tests fail so you needed to make a correction. In this example we want to be
more explicit about how to report bugs.

``` bash
echo "\nBug reports are also welcome please create an [issue](https://github.com/<user-name>/python-maths/issues).\n" >> CONTRIBUTING.md
```

We could add a new commit for this.

``` bash
git add -u
git commit -m "docs: Add link directing to GH issues for reporting bugs"
```

``` bash
git logp
9f0655b (HEAD -> ns-rse/amend-fixup-tutorial) docs: Add link directing to GH issues for reporting bugs
```

...and there is nothing wrong with that. However, Git history can get long and complicated when there are lots of small
commits, because these two changes to `CONTRIBUTING.md` are essentially the same piece of work and had we been thinking
clearly we would have written about making forks in the first place and made a single commit.

Fortunately Git can help here as there is the `git commit --amend` option which adds the staged changes to the last
commit and allows you to edit the last commit message (if nothing is currently staged then you will be prompted to edit
the last commit message). We can undo the last commit using `git reset HEAD~1` as we saw in the branching episode and
instead amend the first commit that added the `CONTRIBUTING.md`

``` bash
git reset HEAD~1
git add -u
git commit --amend
```

``` bash
git logp
  4fda15f (HEAD -> amend-fixup-tutorial) Adding CONTRIBUTING.md
cat CONTRIBUTING.md
# Contributing

Contributions via pull requests are welcome.

Please make a fork of this repository, make your changes and open a Pull Request.
```

We now have one commit which contains the new `CONTRIBUTING.md` file with all the changes we wished to have in the file
in the first place and our Git history is slightly more compact.

### `git commit --fixup`

Amending commits is great providing the commit you want to change is the last commit you made (i.e. `HEAD`). But
sometimes you might wish to correct a commit further back in your history and `git commit --amend` is of no use
here. Git has a solution though in the form of `git commit --fixup` command which allows you to mark a commit as being a
"fix up" of an older commit. These can then be autosquashed via an interactive Git rebase.

Let's add a few empty commits to our `amend-fixup-tutorial` branch to so we can do this.

``` bash
git commit --allow-empty -m "Empty commit for demonstration purposes"
git commit --allow-empty -m "Another empty commit for demonstration purposes"
```

```bash
git logp
  8061221 (HEAD -> ns-rse/amend-fixup-tutorial) Another empty commit for demonstration purposes
  65587ce Empty commit for demonstration purposes
  4fda15f Adding CONTRIBUTING.md
  35aa48c Previous commit before adding CONTRIBUTING.md
```

And let's expand our `CONTRIBUTING.md` file further.

``` bash
echo "\nPlease note this repository uses [pre-commit](https://pre-commit.com) to lint the Python code and Markdown files." >> CONTRIBUTING.md
```

We want to merge this commit with the first one we made in this tutorial using `git commit --fixup`. To do this we need
to know the hash (`4fda15f` see output from above `git logp`). You then use `git commit --fixup <hash>` to commit your
changes as a "fixup" of the earlier commit.

``` bash
git add -u
git commit --fixup 4fda15f
```

We see the commit we have just made starts with `fixup!` and is then followed by the commit message that it is fixing,
but it hasn't yet been combined into that commit.

```bash
git logp
  97711a4 (HEAD -> ns-rse/amend-fixup-tutorial) fixup! Adding CONTRIBUTING.md
  8061221 Another empty commit for demonstration purposes
  65587ce Empty commit for demonstration purposes
  4fda15f Adding CONTRIBUTING.md
  35aa48c Previous commit before adding CONTRIBUTING.md
```

The final step is to perform the automatic squashing via an "interactive rebase". You need to supply the hash of the
commit _before_ the one you are fixing up, in the above example `35aa48c` (check the output of `git logp` if you haven't
made a note of this). This can be done explicitly but, as covered in the earlier episodes, an alternative and convenient
way of referring to this previous commit is by using the `~1` appended to the commit you have chosen to fixup so you
would use.

``` bash
# Relative to the fixup
git rebase -i --autosquash 4fda15f~1
# Absolute commit
git rebase -i --autosquash 35aa48c
```

This will open the default editor and because the `--autosquash` option has been used it should have marked the
commits that need combining with `fixup`. All you have to do is save the file and exit and we can check the history and
look at the contents of the file.

**NB** If you find that the necessary commit _isn't_ already marked then you are likely to have mistakenly supplied the
wrong hash (most probably the hash of the commit your wish to fixup rather than the commit before it).

```bash
git logp
  0fda21e (HEAD -> amend-fixup-tutorial) Another empty commit for demonstration purposes
  65587ce Empty commit for demonstration purposes
  4fda15f Adding CONTRIBUTING.md
  35aa48c Previous commit before adding CONTRIBUTING.md

cat CONTRIBUTING.md
  # Contributing

  Contributions via pull requests are welcome.

  Please make a fork of this repository, make your changes and open a Pull Request.

  Please note this repository uses [pre-commit](https://pre-commit.com) to lint the Python code and Markdown files.
```

And you're all done! If you were doing this for real on a repository you would now `git push` or continue your work. As
this was just an example we can switch branches back to `main` and force deletion of the branch we created.

``` bash
git switch main
git branch -D amend-fixup-tutorial
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 4

In your pairs there are two issue templates in the `python-math` repository that you are using.

- _03 Zero Division Amend and Fixup_
- _04 Square Root Amend and Fixup_

Create and assign one of these each and work through the stages. The tasks build on material already covered
e.g. creating and switching branches and conventions for naming branches and rebasing. Solutions to each step are
provided but try not to use them instead you can use your `history` to check what commands you have used.

:::::::::::::::::::::::: solution

## Main branch with improved docstrings

The instructions should have guided you through.

On the `main` branch of your `python-maths` repository the `divide` function in `pythonmaths/arithmetic.py` should look
like the following with four examples.

```python
def divide(x: int | float, y: int | float) -> float:
    """
    Divide x by y.

    Parameters
    ----------
    x : int | float
        Numerator for division.
    y : int | float
        Denominator for division.

    Returns
    -------
    float
        The result of dividing `x` by `y`.

    Examples
    --------
    >>> from python_math import arithmetic
    >>> arithmetic.divide(10, 2)
        5.0
    >>> arithmetic.divide(5, 2)
        2.5
    >>> arithmetic.divide(3, 0)
        You can not divide by 0, please choose another value for 'y'.
    >>> arithmetic.divide(1, 0.1)
        10
    """
    return x / y

```

The `square_root` function should look like the following.

``` python
def square_root(x):
    """Return the square root of a number.

    Parameters
    ==========
    x : int | float
        The number for which you wish to find the square root.

    Returns
    =======
    float
        The square root of x.

    Examples
    ========
    >>> from python_math import arithmetic
    >>> arithmetic.square_root(4)
        2.0
    >>> arithmetic.square_root(169)
        13.0
     >>> arithmetic.square_root(-2)
         WARNING : you have supplied a negative number, the square root is complex.
         (8.659560562354934e-17+1.4142135623730951j)
    """
    if x < 0:
        print("WARNING : you have supplied a negative number, the square root is complex.")
    return (x) ** (1 / 2)

```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

### `git absorb`

Rather than having to look up commit hashes or work out how many commits back you need to go to pass as an argument to
`--fixup` you can instead use the [git-absorb][gitabsorb] extension that works out what commits changes to each file
being fixed up need rebasing and with the `--and-rebase` flag it will automatically perform the squashing rebase.

The steps involved then become much shorter with.

```bash
git add -u
git absorb --and-rebase
```

By default `git absorb` will search the last 10 commits but this can be configured at runtime using the `--base` flag to
specify the last commit to check or by adapting the configuration file.

::::::::::::::::::::::::::::::::::::::::::::::::

### Squashing commits

If you don't want to use [git-absorb][gitabsorb] and you forgot to use `git commit --fixup` you can still combine
commits before making pull requests using an interactive rebase `git rebase -i`.  We've already touched on `git rebase`
in the context of keeping branches up-to-date but its a very flexible and powerful component of Git and it also allows
you to "squash" commits on the same branch.

We will now make a few commits to our branch and then squash them via an interactive rebase. This helps keep commits
that you will merge into `main` atomic since even if you've been using `--amend` or `--fixup` to sequentially update
commits you may still have several commits on a branch which can be combined into a single informative commit that is
ready for merging into the `main` branch.

Returning to the `python-maths` repository we will make a series of empty commits on a new branch and then undertake an
interactive rebase to squash them.

``` bash
git switch -c test-rebase
git commit --allow-empty -m "Commit 1"
git commit --allow-empty -m "Commit 2"
git commit --allow-empty -m "Commit 3"
git commit --allow-empty -m "Commit 4"
git commit --allow-empty -m "Commit 5"
git logp
```

To squash these commits we need to know the hash or relative reference to the first commit we wish to interact with
which the `git log` command does (if you set the `gl` alias earlier you can use that)

``` bash
git logp
c33ab51 (HEAD -> test-rebase) Commit 5
f7bb1c9 Commit 4
d47d914 Commit 3
e859738 Commit 2
c437414 Commit 1
2f7c382 (origin/main) Merge pull request #6 from ns-rse/ns-rse/tidy-print
a1101c7 [pre-commit.ci] Fixing issues with pre-commit
```

The hash of the first commit we want to squash is `c437414` or `HEAD~5`) but you need to include it. We start a rebase
with `git rebase -i c437414` or `git rebase -i HEAD~5` which will open our default editor.

``` bash
pick c437414 Commit 1 # empty
pick e859738 Commit 2 # empty
pick d47d914 Commit 3 # empty
pick f7bb1c9 Commit 4 # empty
pick c33ab51 Commit 5 # empty

# Rebase c437414..c33ab51 onto c437414 (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup [-C | -c] <commit> = like "squash" but keep only the previous
#                    commit's log message, unless -C is used, in which case
#                    keep only this commit's message; -c is same as -C but
#                    opens the editor
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
# l, label <label> = label current HEAD with a name
# t, reset <label> = reset HEAD to a label
# m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
#         create a merge commit using the original merge commit's
#         message (or the oneline, if no original merge commit was
#         specified); use -c <commit> to reword the commit message
# u, update-ref <ref> = track a placeholder for the <ref> to be updated
#                       to this position in the new commits. The <ref> is
#                       updated at the end of the rebase
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
```

The instructions here are really useful and tell us how to edit the rebase. The first line tells us that we are rebasing
the range of commits `onto c437414`. Subsequently there is a list of commands, by default `pick` is in place for each of
the commits, but we are shown the available options and simply need to replace each of the `pick` with `s` or `squash`
and we want to apply it to commits two through to 5.

You can do this manually by editing the file or you can use your editors find and replace functionality which in `nano`
is `Ctrl + \` and you will be prompted for the string you want to find (`pick`) and what you want to replace it with
`squash` and then asked if you want to change the first instance or all. We can safely change all as it doesn't matter
if the instances in the comments section are replaced. The first four rows of the file should now read like the
following.

``` bash
pick   c437414 Commit 1 # empty
squash e859738 Commit 2 # empty
squash d47d914 Commit 3 # empty
squash f7bb1c9 Commit 4 # empty
squash c33ab51 Commit 5 # empty
```

Save this file and exit (in `nano` use `Ctrl + o` then `Ctrl + x`), the editor will exit return you to the prompt and
then in the blink of an eye open the editor again with a different message. This is now your opportunity to edit the
commit message for the single commit that will remain in the tree, as the notes show. Any lines starting with a `#` are
comments and will be ignored but this is very useful as it saves you having to re-write all the text across the commits
and you can instead edit them.

``` bash
# This is a combination of 5 commits.
# This is the 1st commit message:

Commit 1

# This is the commit message #2:

Commit 2

# This is the commit message #3:

Commit 3

# This is the commit message #4:

Commit 4

# This is the commit message #5:

Commit 5

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Fri Mar 8 14:39:47 2024 +0000
#
# interactive rebase in progress; onto 2f7c382
# Last commands done (5 commands done):
#    squash f7bb1c9 Commit 4 # empty
#    squash c33ab51 Commit 5 # empty
# No commands remaining.
# You are currently rebasing branch 'main' on '2f7c382'.
#
# No changes
```

Edit the file to read how you want it to, here I've gone with the following to make it clearly

``` bash
Squash of empty commits 1-5

This is an example of how to squash commits and combines the original commits...

+ Commit 1
+ Commit 2
+ Commit 3
+ Commit 4
+ Commit 5
```

When done save and exit (in `nano` use `Ctrl + O` then `Ctrl + X`). You should be informed the rebase was successful and
if you look at the plain `git log` your commit message will be there at the top in all its glory.

``` bash
git rebase -i 2f7c382
[detached HEAD 2a0c155] Squash of empty commits 1-5
 Date: Fri Mar 8 14:39:47 2024 +0000
Successfully rebased and updated refs/heads/main.

git log

commit 2a0c1551039f8fd43af74656a6150e71254c6669 (HEAD -> main)
Author: Neil Shephard <n.shephard@sheffield.ac.uk>
Date:   2024-03-08 14:39:47 +0000

    Squash of empty commits 1-5

    This is an example of how to squash commits and combines the original commits...

    + Commit 1
    + Commit 2
    + Commit 3
    + Commit 4
    + Commit 5

commit 2f7c3826b310269b06dd86cca930bdd767ad9fbf (origin/main)
Merge: feee987 a1101c7
Author: Neil Shephard <n.shephard@sheffield.ac.uk>
Date:   2024-03-07 16:07:06 +0000

    Merge pull request #6 from ns-rse/ns-rse/tidy-print
```

::::::::::::::::::::::::::::::::::::: callout

## Commits don't have to be adjacent

When squashing commits they do not have to be contiguous, you can pick and choose any combination. Commits that are
prefixed with `pick` will remain in the Git history.

::::::::::::::::::::::::::::::::::::::::::::::::

### Re-writing History - With Great Power

...comes great scope for messing things up!

The `--amend`, `--fixup` and `rebase -i` commands we have worked through are powerful tools, in effect they are
re-writing the Git history that is shown in the `git log`. You may have noticed that the commit hashes change when using
these commands.

If you have pushed your work to GitHub and then use any of these commands to change the history of your branch locally
the two will differ and Git will complain and tell you that you need to `git pull` first. If you know you want to push
the changes you can force them to be pushed using `git push --force-with-lease`, however you should be _very_ careful
doing so in some situations.

::::::::::::::::::::::::::::::::::::: callout

## Use force cautiously

If anyone else has `git pull` the branch or if the changes have been merged into `main` (or another branch) using these
commands then `git push --force` will cause a lot of headaches so make sure no one else is working on your branches and
don't force push to branches that have already been merged.

`--force-with-lease` offers some protection against the problems that can arise and `--force-if-includes` help catch if
you haven't `git pull` any changes that may be on the `origin`.

The following resources are highly recommended reading on this topic.

- [-force considered harmful; understanding git's -force-with-lease - Atlassian Developer
  Blog](https://blog.developer.atlassian.com/force-with-lease/)
- [Git: Force push safely with --force-with-lease and --force-if-includes - Adam
  Johnson](https://adamj.eu/tech/2023/10/31/git-force-push-safely/)

::::::::::::::::::::::::::::::::::::::::::::::::

## Keep things tidy

We've covered some ways of grouping commits together and/or squashing a series of commits that have been made into
logical groups, but what if a piece of work necessitated making changes to a large number of files simultaneously? You
have completed the work and all your tests are passing so the work is ready to commit but rather than making one large
commit with all the changes you would like to group the changes into a number of smaller commits, grouped by the
module/class that has been changed.

Fortunately this is possible using `git add --patch` (or just `-p` flag for short) which allows you to choose which
hunks of a file you wish to include in a commit. We can work through this in an example with our `python-maths` package.

### Make some changes to `pythonmaths/arithmetic.py`

Lets improve the docstrings for the `add()` and `multiply()` function by editing `pythonmaths/arithmetic.py`. We'll make
sure `main` is up-to-date and make a new branch.

```bash
git switch main
git pull
git switch -c ns-rse/test-patch
```

We then cange the first line of the `add()` function to read....

```python
    """
    Return the sum of two numbers.
```

...and update the first line of the `multiply()` function to read...

```python
Return the product of x and y.
```

Stage your changes, but include the `--patch` option, you should now be presented with the following screen asking if
you want to "Stage this hunk" (a "hunk" is a section of code). There are a number of possible options and you can use
the `?` option to give you help on what each does.

``` bash
git add --patch -u
diff --git a/pythonmaths/arithmetic.py b/pythonmaths/arithmetic.py
index 5fbabe8..6e7f4fd 100644
--- a/pythonmaths/arithmetic.py
+++ b/pythonmaths/arithmetic.py
@@ -3,7 +3,7 @@

 def add(x: int | float, y: int | float) -> float:
     """
-    Add two numbers together.
+    Return the sum of two numbers.

     Parameters
     ----------
(1/2) Stage this hunk [y,n,q,a,d,j,J,g,/,e,p,?]? ?
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
g - select a hunk to go to
/ - search for a hunk matching the given regex
e - manually edit the current hunk
p - print the current hunk, 'P' to use the pager
? - print help
(1/2) Stage this hunk [y,n,q,a,d,j,J,g,/,e,p,?]?
```

For this example we are going to skip this first hunk and add the second so we select `n`. We are then shown the second
hunk and asked the same question. In this case we want to include it so we answer `y`.

```bash
@@ -66,7 +66,7 @@ def divide(x: int | float, y: int | float) -> float:

 def multiply(x: int | float, y: int | float) -> float:
     """
-    Multiply x by y.
+    Return the product of x and y.

     Parameters
     ----------
(2/2) Stage this hunk [y,n,q,a,d,K,g,/,e,p,?]? y
```

If we check the current status we see that there are both staged and unstaged changes to the `pythonmaths/arithmetic.py`
file.

```bash
git status
On branch ns-rse/test-patch
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
    modified:   pythonmaths/arithmetic.py

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
    modified:   pythonmaths/arithmetic.py
```

We can now commit the changes to the `multiply()` function keeping changes atomic and focused on specific
functions.

``` bash
git commit -m "docs: Improve docstring of multiply() function."
```

Since we know the only outstanding change is to the docstring of the `add()` function (we can check with `git diff` to
reassure us), we can stage that hunk and commit it separately.

``` bash
git add --patch -u

diff --git a/pythonmaths/arithmetic.py b/pythonmaths/arithmetic.py
index 0546271..6e7f4fd 100644
--- a/pythonmaths/arithmetic.py
+++ b/pythonmaths/arithmetic.py
@@ -3,7 +3,7 @@

 def add(x: int | float, y: int | float) -> float:
     """
-    Add two numbers together.
+    Return the sum of two numbers.

     Parameters
     ----------
(1/1) Stage this hunk [y,n,q,a,d,e,p,?]? y


git commit -m "doc: improve docstring for add() function"

[ns-rse/test-patch 88a3d9c] doc: improve docstring for add() function
 1 file changed, 2 insertions(+), 2 deletions(-)
```

By staging hunks into groups it allows us to make commits that are atomic, focused and related on one specific function
or change, and in turn our history will become easier to understand and follow (particularly if we have errors and need
to use `git bisect` to find where they have been introduced, see [additional
topics](additional_topics.md#finding-bugs-with-git-bisect)).

## Conventional Commits

You may have noticed in many of the commit messages used so far a keyword is used to start the commit followed by a
colon. This is an example of [Conventional Commits][concommit] which are a standardised way of writing commit messages
that, as with the branch naming convention suggested earlier, include metadata about what the commit relates to.

There are keywords to start your commit message with that are self-explanatory

- `fix:`
- `feat:` - short for _future_
- `build:`
- `chore:`
- `ci:`
- `docs:`
- `style:`
- `refactor:`
- `perf:` - short for _performance_
- `test:`

If changes relate to a specific component or "scope" of a repository that can be included in parentheses afterwards. For
example the Zero Division issue in `python-maths` relates to the `artihmatic` module so might be started with
`fix(arithmetic)`.

You don't have to use [Conventional Commits][concommit] but do try and use informative titles and add more detail if
needs be to your commit messages. You don't want your history to look like this...

![[XKCD Git Commit](https://xkcd.com/1296/)](https://imgs.xkcd.com/comics/git_commit_2x.png)

Commit messages should explain _why_ you have made changes, the code itself shows _what_ has changed.

::::::::::::::::::::::::::::::::::::: keypoints

- Global configuration is via `.gitconfig`
- Local configuration is via `.git/config` and takes precedence over Global.
- Configuration can be done at the command line or by editing files.
- Ignore files using `.gitignore`.
- Make commits atomic, i.e. small and focused using `git commit --amend` and `git commit --fixup`, better still make
  life easier using `git absorb`.
- `git rebase --interactive` can be used to squash commits.
- Use `git add --patch` to group changes into atomic commits.
- Use [Conventional commits][concommit] and write informative messages.
- Keeping the commit history atomic and clean makes it easier to understand what work has been undertaken.

::::::::::::::::::::::::::::::::::::::::::::::::

## Links

- [Atomic commits will help you git legit. – Pauline Vos](https://www.pauline-vos.nl/atomic-commits/) the [video of her
  talk](https://www.youtube.com/watch?v=_e5oq4JT4_8) is well worth watching.
- [How to Write a Git Commit Message](https://cbea.ms/git-commit/)
- [Why you need small, informative Git commits](https://masalmon.eu/2024/06/03/small-commits/)
- [Hack your way to a good Git history · Maëlle's R Blog](https://masalmon.eu/2024/06/11/rewrite-git-history/)
- [So You Think You Know Git](https://www.youtube.com/watch?v=aolI_Rz0ZqY) an excellent talk by Scot Chacon, one of the
founders of GitHub and co-author of [Pro Git][progit] book on useful tips for using Git.
- [So You Think You Know Git Part 2](https://www.youtube.com/watch?v=Md44rcw13k4) follow-on from previous video.
- [Atlassian | Advanced Git Tutorials][advanced]

[advanced]: https://www.atlassian.com/git/tutorials/advanced-overview
[concommit]: https://www.conventionalcommits.org/en/v1.0.0/
[gitabsorb]: https://github.com/tummychow/git-absorb
[progit]: https://git-scm.com/book/en/v2
