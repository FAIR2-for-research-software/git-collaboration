---
title: "Additional Topics"
teaching: 10
exercises: 2
---

Some additional topics that are useful but didn't fit into the time frame.

## `difftastic`

When undertaking Pull Requests on GitHub there is the ability to toggle between two [different views][githubdiff] of the
differences. The standard view shows the changes line-by-line and looks like the following where the deleted lines are
started with `-` signs and may well be in red and the added lines are started with `+` and may well be in green. Changes
within a line are reflected as a deletion _and_ addition.

``` bash
@@ -1861,12 +1862,18 @@ tree -afhD -L 2 main/

 Each branch can have a worktree added for it and then when you want to switch between them its is simply a case of
-`cd`ing into the worktree (/branch) you wish to work on. You use Git commands within the directory to apply them to that
-branch and Git keeps track of everything in the usual manner.
+`cd`ing into the worktree (/branch) you wish to work on. You use Git commands within the worktree directory to apply
+them to that branch and Git keeps track of everything in the usual manner.

-Lets create two worktree's, the `contributing` and `citation` we created above when working with branches.
+###
+Lets create two worktree's, the `contributing` and `citation` we created above when working with branches. If you didn't
+already follow along the above steps do so now.
```

Its a matter of personal preference but it can sometimes be easier to look at differences in the split view that
`difftastic` provides, the same changes above using the split view are shown below.

``` bash
1862                                                                            1863
1863 Each branch can have a worktree added for it and then when you want to swi 1864 Each branch can have a worktree added for it and then when you want to swi
.... tch between them its is simply a case of                                   .... tch between them its is simply a case of
1864 `cd`ing into the worktree (/branch) you wish to work on. You use Git comma 1865 `cd`ing into the worktree (/branch) you wish to work on. You use Git comma
.... nds within the directory to apply them to that                             .... nds within the worktree directory to apply
1865 branch and Git keeps track of everything in the usual manner.              1866 them to that branch and Git keeps track of everything in the usual manner.
1866                                                                            1867
....                                                                            1868 ###
1867 Lets create two worktree's, the `contributing` and `citation` we created a 1869 Lets create two worktree's, the `contributing` and `citation` we created a
.... bove when working with branches.                                           .... bove when working with branches. If you didn't
....                                                                            1870 already follow along the above
steps do so now.
```

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Show how to toggle the view on GitHub pull requests. Make sure to have an example that is already open in a tab of your
browser.

If you have `difftastic` already configured for Git make sure to disable if you are going to show the difference in the
terminal live.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1

Install [difftastic][difftastic] on your computer and configure Git globally to use it.

**Hint** There are instructions on the [website][difftastic_git].

:::::::::::::::::::::::: solution

## Update the `~/.gitconfig`

The [instructions](https://difftastic.wilfred.me.uk/git.html) show the configuration options you can add to
`~/.gitconfig` to setup an alias for `git dft` which uses `difftastic`. The following in your `.gitconfig` will set that
up.

```config
[diff]
        tool = difftastic

[difftool]
        prompt = false

[difftool "difftastic"]
        cmd = difft "$LOCAL" "$REMOTE"

[pager]
        difftool = true
# `git dft` is less to type than `git difftool`.
[alias]
        dft = difftool
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Finding bugs with `git bisect`

`git bisect` is one of the killer features of `git` that helps you find where bugs were introduced. Unfortunately it
requires that you be somewhat organised in your workflow as it works best when a number of small commits have been made
rather than one or two large commits with lots of changes. If you've followed the advice in this course and grouped your
changes into atomic commits you should be good to go.

### When to use?

If you've found that your tests are failing or if there is unexpected behaviour but don't know when the changes that
caused them were introduced you can leverage `git bisect` to search for the commit that changed the behaviour.

### What is bisecting?

The idea behind bisecting is that you have a "Good" commit and a "Bad" commit and any number of commits have been made
in between these two. `git bisect` will use a [bisect algorithm](https://en.wikipedia.org/wiki/Bisection_method) to
checkout a commit between the "Good" and "Bad" commit. You can then run your tests or check behaviour to see if the
problem still occurs, if it does then you mark the commit as "Bad", if it doesn't you mark it as "Good". Git then
iterates the splitting strategy on the "Bad" half of commits, quickly and efficiently narrowing down the offending
commit that introduced the problem and (often) saving you hours of trawling through a large amount of changes to
identify what caused the problem.

There is no worked example for this but it is a very powerful tool that is worth knowing about and so a broad overview
is given. If you need to use `git bisect` it is recommended that you read the [official
documentation][gitbisect]. A useful feature is being able to include a script which
automatically "runs" the tests or invocation that you wish to perform at each step so that after you have marked your
good and bad commits you use a script which runs your tests and reports whether they were good or bad with `git bisect
run <your_script> [aguments]`. A worked example of this can be found
[here](https://interrupt.memfault.com/blog/git-bisect#scripting-the-testing).

## Worktrees instead of Branches

Sometimes you will want to switch between branches that are all in development in the middle of work. If you've made
changes to files that you have not saved and committed Git will tell you that the changes made to your files
will be over-written if they differ from those on the branch you are switching to and it will refuse to switch branches.

This means either making a commit or as we've just seen stashing the work to come back to at a later date. Neither of
these are particularly problematic as you can `git pop` stashed work to restore it or `git commit --amend`, or `git
commit --fixup` and squash commits to maintain small atomic commits and avoid cluttering up the commit history with
commits such as "_Saving work to review another branch_" (more on this in the next episode!). But, perhaps
unsurprisingly, Git has another way of helping your workflow in this situation. Rather than having branches you can use
"_worktrees_".

Normally when you've `git clone`'d a repository all configuration files for working with the repository are saved to the
repository directory under `.git` _and_ all files in their current state on the `main` branch are also copied to the
repository directory. If we clone the [pytest-examples](https://github.com/ns-rse/pytest-examples) directory
we can look at its contents using `tree -afHD -L 2` (this limits the depth as we don't need to look deep inside the
`.git` or `mypy` directories which contain lots of files).

```bash
git clone git@github.com:ns-rse/pytest-examples.git
cd pytest-examples
tree -afhD -L 2
[4.0K Mar 11 07:26]  .
├── [ 52K Jan  5 11:26]  ./.coverage
├── [4.0K Mar 11 07:26]  ./.git
│   ├── [ 749 Jan  5 11:30]  ./.git/COMMIT_EDITMSG
│   ├── [ 394 Jan  5 11:28]  ./.git/COMMIT_EDITMSG~
│   ├── [ 479 Feb 17 14:08]  ./.git/config
│   ├── [ 556 Feb 17 14:06]  ./.git/config~
│   ├── [  73 Jan  1 13:24]  ./.git/description
│   ├── [ 222 Mar 11 07:26]  ./.git/FETCH_HEAD
│   ├── [  21 Mar 11 07:26]  ./.git/HEAD
│   ├── [4.0K Jan  1 13:27]  ./.git/hooks
│   ├── [1.3K Mar 11 07:26]  ./.git/index
│   ├── [4.0K Jan  1 13:24]  ./.git/info
│   ├── [4.0K Jan  1 13:24]  ./.git/logs
│   ├── [4.0K Mar 11 07:26]  ./.git/objects
│   ├── [  41 Mar 11 07:26]  ./.git/ORIG_HEAD
│   ├── [ 112 Jan  3 15:57]  ./.git/packed-refs
│   ├── [4.0K Jan  1 13:24]  ./.git/refs
│   └── [4.0K Jan  1 13:31]  ./.git/rr-cache
├── [4.0K Jan  2 11:52]  ./.github
│   └── [4.0K Jan  3 15:57]  ./.github/workflows
├── [3.0K Jan  2 12:06]  ./.gitignore
├── [1.0K Jan  1 13:24]  ./LICENSE
├── [ 293 Jan  2 12:06]  ./.markdownlint-cli2.yaml
├── [4.0K Jan  5 11:27]  ./.mypy_cache
│   ├── [ 12K Jan  5 11:28]  ./.mypy_cache/3.11
│   ├── [ 190 Jan  2 10:39]  ./.mypy_cache/CACHEDIR.TAG
│   └── [  34 Jan  2 10:39]  ./.mypy_cache/.gitignore
├── [1.7K Mar 11 07:26]  ./.pre-commit-config.yaml
├── [ 763 Jan  1 13:25]  ./.pre-commit-config.yaml~
├── [ 18K Jan  2 12:06]  ./.pylintrc
├── [4.8K Mar 11 07:26]  ./pyproject.toml
├── [4.7K Jan  1 17:36]  ./pyproject.toml~
├── [4.0K Jan  1 19:04]  ./.pytest_cache
│   ├── [ 191 Jan  1 19:04]  ./.pytest_cache/CACHEDIR.TAG
│   ├── [  37 Jan  1 19:04]  ./.pytest_cache/.gitignore
│   ├── [ 302 Jan  1 19:04]  ./.pytest_cache/README.md
│   └── [4.0K Jan  1 19:04]  ./.pytest_cache/v
├── [4.0K Mar 11 07:26]  ./pytest_examples
│   ├── [1.3K Mar 11 07:26]  ./pytest_examples/divide.py
│   ├── [ 179 Mar 11 07:26]  ./pytest_examples/__init__.py
│   ├── [4.0K Jan  5 11:18]  ./pytest_examples/__pycache__
│   ├── [ 491 Mar 11 07:26]  ./pytest_examples/shapes.py
│   └── [ 390 Jan  2 13:34]  ./pytest_examples/shapes.py~
├── [4.0K Jan  2 16:09]  ./pytest_examples.egg-info
│   ├── [   1 Jan  2 16:09]  ./pytest_examples.egg-info/dependency_links.txt
│   ├── [3.1K Jan  2 16:09]  ./pytest_examples.egg-info/PKG-INFO
│   ├── [ 481 Jan  2 16:09]  ./pytest_examples.egg-info/requires.txt
│   ├── [ 446 Jan  2 16:09]  ./pytest_examples.egg-info/SOURCES.txt
│   └── [  16 Jan  2 16:09]  ./pytest_examples.egg-info/top_level.txt
├── [ 602 Jan  3 15:57]  ./README.md
├── [   0 Jan  1 13:31]  ./README.md~
├── [4.0K Jan  1 13:30]  ./.ruff_cache
│   ├── [4.0K Jan  2 11:57]  ./.ruff_cache/0.1.8
│   ├── [  43 Jan  1 13:30]  ./.ruff_cache/CACHEDIR.TAG
│   └── [   1 Jan  1 13:30]  ./.ruff_cache/.gitignore
├── [4.0K Mar 11 07:26]  ./tests
│   ├── [ 681 Mar 11 07:26]  ./tests/conftest.py
│   ├── [  26 Jan  2 12:11]  ./tests/conftest.py~
│   ├── [4.0K Jan  5 11:26]  ./tests/__pycache__
│   ├── [1.7K Mar 11 07:26]  ./tests/test_divide.py
│   ├── [1.6K Mar 11 07:26]  ./tests/test_shapes.py
│   └── [   0 Jan  2 13:36]  ./tests/test_shapes.py~
└── [ 460 Jan  2 16:09]  ./_version.py

21 directories, 43 files
```

## The Worktree

Worktrees take a different approach to organising branches. They start with a `--bare` clone of the repository which
implies the `--no-checkout` flag and means that the files that would normally be found under the `<repository>/.git`
directory are copied but are instead placed in the top level of the directory rather than under `.git/`. No tracked
files are copied as they may conflict with these files. You have all the information Git has about the history of the
repository and the different commits and branches but none of the _actual_ files.

**NB** If you don't explicitly state a target directory to clone to it will be the repository name suffixed with `.git`,
i.e. in this example `pytest-examples.git`. I recommend sticking with the convention of using the same repository name
so will explicitly state it.

```bash
cd ..
mv pytest-examples pytest-examples-orig-clone
git clone --bare git@github.com:ns-rse/pytest-examples.git pytest-examples
cd pytest-examples
tree -afhD -L 2
[4.0K Mar 13 07:45]  .
├── [ 129 Mar 13 07:45]  ./config
├── [  73 Mar 13 07:45]  ./description
├── [  21 Mar 13 07:45]  ./HEAD
├── [4.0K Mar 13 07:45]  ./hooks
│   ├── [ 478 Mar 13 07:45]  ./hooks/applypatch-msg.sample
│   ├── [ 896 Mar 13 07:45]  ./hooks/commit-msg.sample
│   ├── [4.6K Mar 13 07:45]  ./hooks/fsmonitor-watchman.sample
│   ├── [ 189 Mar 13 07:45]  ./hooks/post-update.sample
│   ├── [ 424 Mar 13 07:45]  ./hooks/pre-applypatch.sample
│   ├── [1.6K Mar 13 07:45]  ./hooks/pre-commit.sample
│   ├── [ 416 Mar 13 07:45]  ./hooks/pre-merge-commit.sample
│   ├── [1.5K Mar 13 07:45]  ./hooks/prepare-commit-msg.sample
│   ├── [1.3K Mar 13 07:45]  ./hooks/pre-push.sample
│   ├── [4.8K Mar 13 07:45]  ./hooks/pre-rebase.sample
│   ├── [ 544 Mar 13 07:45]  ./hooks/pre-receive.sample
│   ├── [2.7K Mar 13 07:45]  ./hooks/push-to-checkout.sample
│   ├── [2.3K Mar 13 07:45]  ./hooks/sendemail-validate.sample
│   └── [3.6K Mar 13 07:45]  ./hooks/update.sample
├── [4.0K Mar 13 07:45]  ./info
│   └── [ 240 Mar 13 07:45]  ./info/exclude
├── [4.0K Mar 13 07:45]  ./objects
│   ├── [4.0K Mar 13 07:45]  ./objects/info
│   └── [4.0K Mar 13 07:45]  ./objects/pack
├── [ 249 Mar 13 07:45]  ./packed-refs
└── [4.0K Mar 13 07:45]  ./refs
    ├── [4.0K Mar 13 07:45]  ./refs/heads
    └── [4.0K Mar 13 07:45]  ./refs/tags

9 directories, 19 files
```

What use is that? Well from this point you can instead of using `git branch` use `git worktree add <branch_name>` and it
will create a _directory_ with the name of the branch which holds all the files in their current state on that branch.

```bash
git worktree add main
Preparing worktree (checking out 'main')
HEAD is now at 2f7c382 Merge pull request #6 from ns-rse/ns-rse/tidy-print
tree -afhD -L 2 main/
[4.0K Mar 13 08:13]  main
├── [  64 Mar 13 08:13]  main/.git
├── [4.0K Mar 13 08:13]  main/.github
│   └── [4.0K Mar 13 08:13]  main/.github/workflows
├── [3.0K Mar 13 08:13]  main/.gitignore
├── [1.0K Mar 13 08:13]  main/LICENSE
├── [ 293 Mar 13 08:13]  main/.markdownlint-cli2.yaml
├── [1.7K Mar 13 08:13]  main/.pre-commit-config.yaml
├── [ 18K Mar 13 08:13]  main/.pylintrc
├── [4.8K Mar 13 08:13]  main/pyproject.toml
├── [4.0K Mar 13 08:13]  main/pytest_examples
│   ├── [1.3K Mar 13 08:13]  main/pytest_examples/divide.py
│   ├── [ 179 Mar 13 08:13]  main/pytest_examples/__init__.py
│   └── [ 491 Mar 13 08:13]  main/pytest_examples/shapes.py
├── [ 602 Mar 13 08:13]  main/README.md
└── [4.0K Mar 13 08:13]  main/tests
    ├── [ 681 Mar 13 08:13]  main/tests/conftest.py
    ├── [1.7K Mar 13 08:13]  main/tests/test_divide.py
    └── [1.6K Mar 13 08:13]  main/tests/test_shapes.py

5 directories, 14 files
```

Each branch can have a worktree added for it and then when you want to switch between them its is simply a case of
`cd`ing into the worktree (/branch) you wish to work on. You use Git commands within the worktree directory to apply
them to that branch and Git keeps track of everything in the usual manner.

Lets create two worktree's, the `contributing` and `citation` we created above when working with branches. If you didn't

```bash
cd ../
mv pytest-examples pytest-examples-orig-clone
git clone --bare git@github.com:ns-rse/pytest-examples.git pytest-examples
cd pytest-examples
git worktree add contributing
git worktree add citation
```

You are now free to move between worktrees (/branches) and undertake work on each without having to `git stash` or `git
commit` work in progress. We can add the `CONTRIBUTING.md` to the `contributing` worktree then jump to the `citation`
worktree and add the `CITATION.cff`

```bash
cd contributing
echo "# Contributing\n\nContributions to this repository are welcome via Pull Requests." > CONTRIBUTING.md
cd ../citation
echo "cff-version: 1.2.0\ntitle: Pytest Examples\ntype: software" > CITATION.cff
```

Neither branches have had the changes committed so Git will not show any differences between them, but we can use `diff
-qr` to compare the directories.

```bash
diff -qr contributing citation
Only in citation: CITATION.cff
Only in contributing: CONTRIBUTING.md
Files contributing/.git and citation/.git differ
```

If we commit the changes to each we can `git diff` them.

```bash
cd contributing
git add CONTRIBUTING.md
git commit -m "Adding basic CONTRIBUTING.md"
cd ../citation
git add CITATION.cff
git commit -m "Adding basic CITATION.cff"
git diff citation contributing
CITATION.cff --- Text
1 cff-version: 1.2.0
2 title: Pytest Examples
3 type: software

CONTRIBUTING.md --- Text
1 # Contributing
2
3 Contributions to this repository are welcome via Pull Requests
```

**NB** The output of `git diff` may depend on the difftool that you have configured, I use and recommend the brilliant
[`difftastic`](https://difftastic.wilfred.me.uk/) which has easy [integration with
Git](https://difftastic.wilfred.me.uk/git.html).

### Listing Worktrees

Just as you can `git branch --list` you can `git worktree list`

```bash
git worktree list
/mnt/work/git/hub/ns-rse/pytest-examples               (bare)
/mnt/work/git/hub/ns-rse/pytest-examples/citation      19ff076 [citation]
/mnt/work/git/hub/ns-rse/pytest-examples/contributing  ad56b91 [contributing]
/mnt/work/git/hub/ns-rse/pytest-examples/main          2f7c382 [main]
```

### Moving Worktrees

You can move worktrees to different directories, these do _not_ even have to be within the bare repository that you
cloned as Git keeps track of these in the `worktrees/` directory which has a folder for each of the worktrees you create
and the file `gitdir` points to the location of that particular worktree.

```bash
cd pytest-examples   # Move to the bare repository
tree -afhD -L 2 worktrees
[4.0K Mar 13 09:27]  worktrees
├── [4.0K Mar 13 09:31]  worktrees/citation
│   ├── [  26 Mar 13 09:31]  worktrees/citation/COMMIT_EDITMSG
│   ├── [   6 Mar 13 09:27]  worktrees/citation/commondir
│   ├── [  55 Mar 13 09:27]  worktrees/citation/gitdir
│   ├── [  25 Mar 13 09:27]  worktrees/citation/HEAD
│   ├── [1.4K Mar 13 09:31]  worktrees/citation/index
│   ├── [4.0K Mar 13 09:27]  worktrees/citation/logs
│   ├── [   0 Mar 13 09:31]  worktrees/citation/MERGE_RR
│   ├── [  41 Mar 13 09:27]  worktrees/citation/ORIG_HEAD
│   └── [4.0K Mar 13 09:27]  worktrees/citation/refs
├── [4.0K Mar 13 09:30]  worktrees/contributing
│   ├── [  29 Mar 13 09:30]  worktrees/contributing/COMMIT_EDITMSG
│   ├── [   6 Mar 13 09:27]  worktrees/contributing/commondir
│   ├── [  59 Mar 13 09:27]  worktrees/contributing/gitdir
│   ├── [  29 Mar 13 09:27]  worktrees/contributing/HEAD
│   ├── [1.4K Mar 13 09:30]  worktrees/contributing/index
│   ├── [4.0K Mar 13 09:27]  worktrees/contributing/logs
│   ├── [   0 Mar 13 09:30]  worktrees/contributing/MERGE_RR
│   ├── [  41 Mar 13 09:27]  worktrees/contributing/ORIG_HEAD
│   └── [4.0K Mar 13 09:27]  worktrees/contributing/refs
└── [4.0K Mar 13 08:13]  worktrees/main
    ├── [   6 Mar 13 08:13]  worktrees/main/commondir
    ├── [  51 Mar 13 08:13]  worktrees/main/gitdir
    ├── [  21 Mar 13 08:13]  worktrees/main/HEAD
    ├── [1.3K Mar 13 08:13]  worktrees/main/index
    ├── [4.0K Mar 13 08:13]  worktrees/main/logs
    ├── [  41 Mar 13 08:13]  worktrees/main/ORIG_HEAD
    └── [4.0K Mar 13 08:13]  worktrees/main/refs

10 directories, 19 files
```

If we look at the `gitdir` file in each `worktree` sub-directory we see where they point to.

```bash
cat worktrees/*/gitdir
/mnt/work/git/hub/ns-rse/pytest-examples/citation/.git
/mnt/work/git/hub/ns-rse/pytest-examples/contributing/.git
/mnt/work/git/hub/ns-rse/pytest-examples/main/.git
```

These mirror the locations reported by `git worktree list`, albeit with `.git` appended.

If you want to move a worktree you can do so, here we move `citation` to `~/tmp`.

```bash
git worktree move citation ~/tmp
```

### Removing worktrees

It's simple to remove a worktree after the changes have been merged or it is no longer needed, make sure to "prune" the
tree after having done so.

```bash
git worktree remove citation
git worktree prune
git worktree list
/mnt/work/git/hub/ns-rse/pytest-examples               (bare)
/mnt/work/git/hub/ns-rse/pytest-examples/contributing  ad56b91 [contributing]
/mnt/work/git/hub/ns-rse/pytest-examples/main          2f7c382 [main]

```

## Not Breaking Things During Rebasing

As you rebase your branch you can make sure that you don't break any of your code by running tests at each step. This is
achieved using the `-x` switch which will execute the command that follows. The example below would run `pytest` at each
step of the `git rebase` and if tests fail you can fix them.

``` bash
git rebase -x "pytest" <reference>
```

## Constructive Reviewing

Working collaboratively invariably involves reviewing pull/merge requests made by others. This is not something you
should be afraid or anxious about undertaking as its a good opportunity to learn. Whether your work is being reviewed or
you are reviewing others reading other people's code is an excellent way of learning.

## Code Review Tutorial

[Code-Review.org](https://code-review.org/) is an online tutorial to help you learn and improve how to undertake code
reviews. It is an interactive self-paced learning resource that you can work through with the goals of...

- Becoming a better reviewer and consider your method of communication, constructive and actionable criticism.
- Be more comfortable having your code reviewed, share early and often.
- Use code review as a collaboration tool for sharing knowledge so that everyone understands what changes are being
  made.
- Read more code! You will be encouraged to read the source code of the software and tools you regularly use, its a
  great way of learning.
- Enable more open source contributions and reviews.

### Code Review Principles

There are a number of useful guides out there to help you improve how you undertake code review. Two that stand out are
listed below and it is recommended that you take the time to read through these.

- [Tidyteam code review principles](https://code-review.tidyverse.org/) (derived from [How to do a Code
  Review](https://google.github.io/eng-practices/review/reviewer/)).
- [pyOpenSci Software Peer Review Guidebook! — Software Peer Review
  Guide](https://www.pyopensci.org/software-peer-review/)

## Maintenance

Overtime the information about branches and commits can become bloated. We've seen how to delete branches already but
there are a few other simple steps we can take to help keep the repository clean.

[`git maintenance`][gitmaintenance] is a _really_ useful command that will "_Run tasks to optimize Git repository data,
speeding up other Git commands and reducing storage requirements for the repository._". The details of what this does
are beyond the scope of this tutorial (refer to the [help page][gitmaintenance] if interested). Providing you have setup
your GitHub account with SSH keys and they are available via something such as keychain locally then you can bring a
repository under `git maintenance` and forget about it.

``` bash
git mainetenance register
```

This adds entries to your global configuration (`~/.gitconfig`) to ensure the repository will have these tasks run at
the scheduled point (default is hourly).

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Be prepared to explain how SSH keys can be unlocked on login so that the passwords don't need entering every time you
try to use the SSH key.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

[difftastic]: https://difftastic.wilfred.me.uk/
[difftastic_git]: https://difftastic.wilfred.me.uk/git.html
[gitbisect]: https://git-scm.com/docs/git-bisect
[githubdiff]: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-comparing-branches-in-pull-requests#diff-view-options
[gitmaintenance]: https://git-scm.com/docs/git-maintenance
