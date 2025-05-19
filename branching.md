---
title: "Branching"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- What are branches?
- Why use branches?
- How can I switch between branches and commits?
- Comparing commits on a branch

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- What branches are and why they are used
- Switching branches and commits
- Comparing branches/commits
- Stashing commits

::::::::::::::::::::::::::::::::::::::::::::::::

## Branches

Branches are key to working with version control as they allow the development of new features or fixing of bugs without
touching the current working version of code. New features and bug fixes are then merged into the `main` branch to
update the code base, but what is a branch?

The word suggests an analogy with trees where branches are parts of a tree that extend from the "main" trunk or recursively
from parent "branches". An intuitive model of this is shown in the figure below.

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_branches-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101"
       commit id: "1-51816d6"
       commit id: "2-6769ff2 (base)"
       branch "branch"
       commit id: "3-8c52dce"
       checkout main
       commit id: "4-8ec389a"
       checkout "branch"
       commit id: "5-2315fa0 (HEAD)"
       checkout main
       commit id: "6-93e787c (HEAD)"
-->

<!-- markdownlint-disable-next-line MD013 -->
![Basic GitHub Branches](https://mermaid.ink/img/pako:eNqVkTtrwzAUhf-KuWDcgh30sPVa29KlW7fiRZHkWKS2gitDU-P_XjshJUNKqab7-M65gjOBCdaBgjSdfO-jSqYstq5zmUqyrf5wWZ5kOx-fB31os3U7hKijewhd5-OL3rr3ZRqH0c11n1zeUs9peh5cxD9rc5Im3qqkBlSUnDQY4RpuA7iosMDMst8AUjDOZNOQ5G797_0Vtx10b9qFORe_OdBCmIpY466B1pl9GGPSad_flpWFcIYKqW_J_rpZFYTiqtHofzdZIanjgpsaIIfODQtql_SmFa_hlFwNK2n1sF-954UbD3aJ7Mn6GAZQa1g56DGG12NvLv2ZefR6N-juMjzo_i2E6xbUBJ-gWLXBkmAqCUKIlLLM4QiKSLmhAqFKcsIk54LOOXydDPCmZJRhxEqJkRCC8fkbTTe3dw?type=png){alt="Basic GitHub Branches with the `main` branch showing five commits and a `branch` forking off at the third commit with two commits of its own"}

The `branch` has two commits on it and stems from the parent `main` at a point referred to as `base`. A branch is _not_
just the two commits that appear to exist on it (i.e. `3-8c52dce` and `5-2315fa0`) rather it is the full commit history
of that lineage including the commits in the "parent". That means the `branch` consists of the commits `0-472f101`,
`1-98f9a30` and `2-6769ff2` as well as `3-8c52dce` and `5-2315fa0`.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Take the time to make sure everyone understands what the graphic represents, explaining that each tag is a commit and
that the `branch` forks at a given point but doesn't have a commit associated with it.

The history of _both_ the `main` and the `branch` contain all points from the origin but

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

In a repository that is version controlled you will typically be checked out on the `HEAD` of a named branch. The `HEAD`
means the most recent commit in the history of that branch which on the `branch` is commit `5-2315fa0` whilst on `main`
the `HEAD` is `6-93e787c`.

You can change branches by using `git switch <branchname>`.

We can improve the output of `git log` using the following options.

```bash
git log --graph --pretty="%h %ad (%cr) %x09 %an : %s"
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1: What is the first and last commit on branch &nbsp; `divide`?

Using the `python-maths` repository you have cloned look up the first and last commit of the `divide` branch.

What are the commit hashes, commit messages, date/time and committers' names?

:::::::::::::::::::::::: solution

## Solution

```bash
git switch divide
git log --graph --pretty="%h %ad (%cr) %x09 %an : %s"
* 6353fb4 - (HEAD -> divide, origin/divide) bug: Fix tpyo in divide function (2024-03-26 10:28:36 +0000) <Neil Shephard>
* 7485e56 - chore: Fix merge conflict (2024-03-26 10:28:11 +0000) <Neil Shephard>
* adfef4d - feat: Divide branch (2024-03-25 15:55:15 +0000) <Neil Shephard>
* 400896a - Divide branch (2024-03-25 15:55:15 +0000) <Neil Shephard>
* c1f64b0 - Setting up the repository for git-collaboration (2024-02-02 15:48:50 +0000) <Neil Shephard>
*   fa76751 - (origin/main, main) Merge pull request #6 from RSE-Sheffield/ns-rse/5-setup-clean-up (2023-10-19 22:46:14 +0100) <Neil Shephard>
|\
| * c8f0697 - 5 | Removing comment from setup.cfg (2022-10-04 11:12:23 +0100) <Neil Shephard>
* |   aff8153 - Merge pull request #7 from RSE-Sheffield/subtract-mistake (2023-01-20 10:07:58 +0000) <bobturneruk>
|\ \
| |/
|/|
| * a45a8dd - introduce mistake in subtract issue (2023-01-20 09:50:03 +0000) <Robert (Bob) Turner>
| * 604a397 - introduce delibarate mistake (2022-12-21 10:29:34 +0000) <Robert (Bob) Turner>
|/
*   f06c0ab - Merge pull request #4 from RSE-Sheffield/simplify_deliberate_errors (2022-06-07 14:58:27 +0100) <David Wilby>
|\
| * f55c0d2 - remove missing colon and no newline deliberate errors (2022-05-06 11:50:24 +0100) <David Wilby>
|/
* 5c9ae75 - correct python testing instruction (2021-05-18 16:15:23 +0300) <Anna Krystalli>
* 86d7633 - add correct details to each issue (2021-05-18 16:01:50 +0300) <Anna Krystalli>
* a58d6e7 - add all github issue templates (2021-05-17 13:43:57 +0300) <Anna Krystalli>
* 9429ab4 - complete subtract issue template (2021-05-14 15:53:25 +0300) <Anna Krystalli>
* bb560b0 - simplify function (2021-05-14 15:53:01 +0300) <Anna Krystalli>
*   325d038 - Merge pull request #1 from RSE-Sheffield/tests_changes (2021-05-14 14:40:36 +0300) <Anna Krystalli>
|\
| * 608ad59 - Restructure so tests pass (2021-05-14 12:24:23 +0100) <Will Furnass>
|/
* 8584b0f - correct pull request branch spec (2021-05-14 12:45:21 +0300) <Anna Krystalli>
* cdc9ea3 - correct push branch specification (2021-05-14 12:40:01 +0300) <Anna Krystalli>
* c01ff62 - add instructions to README (2021-05-14 12:38:29 +0300) <Anna Krystalli>
* 585287a - add test and CI (2021-05-14 12:38:09 +0300) <Anna Krystalli>
* 3f4d54b - rename python_package folder (2021-05-14 12:37:48 +0300) <Anna Krystalli>
* 4b1707b - use requirements.txt instead of env.yml (2021-05-14 10:04:02 +0100) <davidwilby>
* 2556966 - remove build specs from conda env (2021-05-14 10:01:28 +0100) <davidwilby>
* b50e658 - move env.yml to right place.. (2021-05-14 09:54:59 +0100) <davidwilby>
*   0d2f520 - Merge branch 'main' of github.com:RSE-Sheffield/python-calculator into main (2021-05-14 09:53:44 +0100) <davidwilby>
|\
| * b1179a7 - add package name folder (2021-05-14 11:33:06 +0300) <Anna Krystalli>
* | c883789 - add conda environment yaml (2021-05-14 09:53:06 +0100) <davidwilby>
|/
* fdb8716 - draft commit (2021-05-14 11:23:42 +0300) <Anna Krystalli>
* 328e61b - Add subtraction issue template (2021-05-13 12:23:42 +0300) <Anna Krystalli>
* 31a4a93 - Initial commit (2021-05-13 12:14:08 +0300) <Anna Krystalli>
```

From the `git log` graph we see the first and last commits were:

| Commit | Hash    | Message                          | Date/time           | Committer      |
|:-------|:--------|:---------------------------------|:--------------------|:---------------|
| First  | 31a4a93 | Initial commit                   | 2021-05-13 12:14:08 | Anna Krystalli |
| Last   | 6353fb4 | bug: Fix tpyo in divide function | 2024-03-26 10:28:36 | Neil Shephard  |

:::::::::::::::::::::::::::::::::

## Challenge 2: What commit did the &nbsp; `multiply` &nbsp; branch diverge from &nbsp; `main` &nbsp;?

Again using the `python-maths` repository switch to the `multiply` branch. Use `git log` to find the commit at which
`multiply` diverged from `main`. How many commits have been made on the `main` branch?

**Hint** You will need to find `HEAD -> multiply, origin/multiply` and follow the line backwards.

:::::::::::::::::::::::: solution

## Solution

```bash
git switch main
git log --graph --pretty="%h %ad (%cr) %x09 %an : %s"
*   9a267a0 - (origin/main, origin/HEAD, main) Merge pull request #7 from slackline/ns-rse/6-square-root-warning (2025-02-10 14:22:58 +0000) <slackline>
...
* 3f6494f - chore: tidying up prior to trial run (2024-05-02 14:26:33 +0100) <Neil Shephard>
* ba7a5da - bug: ISSUE_TEMPLATES > ISSUE_TEMPLATES (2024-04-24 14:49:45 +0100) <Neil Shephard>
*   ae5402f - Merge pull request #3 from ns-rse/multiply (2024-04-23 23:14:14 +0100) <Neil Shephard>
|\
| * b702501 - (HEAD -> multiply, origin/multiply) bug: multiply instead of add arguments (2024-03-26 10:33:37 +0000) <Neil Shephard>
| * 11e36a3 - feat: Adding multiply function and tests (2024-03-26 10:32:42 +0000) <Neil Shephard>
* |   12a159c - Merge pull request #2 from ns-rse/divide (2024-04-23 23:13:13 +0100) <Neil Shephard>
|\ \
| * | 6353fb4 - (origin/divide, divide) bug: Fix tpyo in divide function (2024-03-26 10:28:36 +0000) <Neil Shephard>
| * | 7485e56 - chore: Fix merge conflict (2024-03-26 10:28:11 +0000) <Neil Shephard>
| * | adfef4d - feat: Divide branch (2024-03-25 15:55:15 +0000) <Neil Shephard>
| * | 400896a - Divide branch (2024-03-25 15:55:15 +0000) <Neil Shephard>
| |/
* |   f8dcc8d - Merge pull request #1 from ns-rse/ns-rse/initial-setup (2024-03-15 18:04:02 +0000) <Neil Shephard>
|\ \
| * | a4b1bb4 - Min Python version >= 3.10 to align with workflow (2024-03-15 18:01:01 +0000) <Neil Shephard>
| * | 4f5daa2 - Quoting Python versions and adding os matrix to workflow (2024-03-15 17:59:05 +0000) <Neil Shephard>
| * | 74e909b - Issue template for amend and fixup of square_root function (2024-03-08 13:01:42 +0000) <Neil Shephard>
| * | bffc993 - Issue template for amend/fixup on zero-division branch (2024-03-07 17:28:49 +0000) <Neil Shephard>
| * | 44c70e6 - Tidying .pre-commit-config.yaml| (2024-02-02 15:48:50 +0000) <Neil Shephard>
| |/
| * c1f64b0 - Setting up the repository for git-collaboration (2024-02-02 15:48:50 +0000) <Neil Shephard>
|/
*   fa76751 - Merge pull request #6 from RSE-Sheffield/ns-rse/5-setup-clean-up (2023-10-19 22:46:14 +0100) <Neil Shephard>
|\
| * c8f0697 - 5 | Removing comment from setup.cfg (2022-10-04 11:12:23 +0100) <Neil Shephard>
* |   aff8153 - Merge pull request #7 from RSE-Sheffield/subtract-mistake (2023-01-20 10:07:58 +0000) <bobturneruk>
|\ \
| |/
|/|
| * a45a8dd - introduce mistake in subtract issue (2023-01-20 09:50:03 +0000) <Robert (Bob) Turner>
| * 604a397 - introduce delibarate mistake (2022-12-21 10:29:34 +0000) <Robert (Bob) Turner>
|/
*   f06c0ab - Merge pull request #4 from RSE-Sheffield/simplify_deliberate_errors (2022-06-07 14:58:27 +0100) <David Wilby>
|\
...
```

This is a little more challenging to interpret but reading the output carefully we have an indicator of where the
`multiply` branch is where it reads `(HEAD -> multiply, origin/multiply)`. We can trace that line back to the furthest
left branch (which is `main` and `origin/main`).

We can see that the `multiply` branch diverged from the `fa76751` commit on `main` and that three commits
have been made on the `multiply` branch.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

## Working with Branches

The `git switch` command is the common method for working with branches. It allows you to create and switch between
branches along with a few other tasks.

To list the branches that are available you can just type `git branch` or optionally include the `--list` option. In the
`python-maths` repository you  have cloned you should see a number of branches listed. The branch you are currently
checked out on is listed first with an asterisk (`*`) at the start and they are listed alphabetically. Later we will
change the default order to be more informative.

``` bash
git branch

* main
divide
```

::::::::::::::::::::::::::::::::::::: callout

## `switch` v's `checkout`

`git switch` was introduced in [Git
v2.23.0](https://github.blog/2019-08-16-highlights-from-git-2-23/#experimental-alternatives-for-git-checkout) along with
`git restore` to provide two separate commands for the functionality that was originally available in `git checkout`.
The main reason was to separate the functionality of `git checkout` which could "switch" branches, including creating
branches using the `--branch`/`-b` flag, and change ("restore") individual files with `git checkout [treeish] --
<filename>` (more on this later).

Splitting this functionality means that `git switch` is solely for `switch`ing branches whilst `git restore` is solely
concerned with `restore`ing files but is destructive and we will cover later the `git revert` command as an alternative.

`git checkout` has not been deprecated and is still available and many people still use it as old habits die hard.

::::::::::::::::::::::::::::::::::::::::::::::::

### Creating Branches

You can create a new branch using `git switch -c <new_branch>`. By default it will use the branch you currently have
checked out as a basis for the new branch. If you wish to use a different branch as a basis you can do so by including
its name before the name of the new branch.

::::::::::::::::::::::::::::::::::::: callout

## Starting off up-to-date

Most of the time when creating branches you should do so from the `main` branch. It is therefore important to make sure
your local copy of the `main` branch is up-to-date. Before creating a branch you should switch to the `main` branch and
ensure it is up-to-date.

``` bash
git switch main
git pull
```

This means you can omit the explicit statement of which branch you wish to use as the basis for the new branch,
typically `main`, when creating it as you will be already be checked out on that branch when `git pull`.

::::::::::::::::::::::::::::::::::::::::::::::::

To create a new branch called `ns-rse/test` you can use the following.

``` bash
git switch main
git pull
git switch -c main ns-rse/test
```

Git will use the current `HEAD` of the `main` branch as a basis for creating the `ns-rse/test` branch.

### Naming Branches

Branch names can not include spaces, you should use underscores or dashes instead. You can include some special
characters too but I would avoid using `#` as this is the character used by most shells to indicate a comment and you
would therefore have to _always_ double quote the branch name at the command line.

A useful convention when creating branches is to include some meta data about who owns the branch and what it is for and
to construct the branch name from your GitHub/GitLab username followed by a `/`. Because you will typically be
working on a particular issue then including the issue number followed by a short few words which describe the work or
issue can make it easy for others (including your future self) to find out more information about the branch and why the
work has been undertaken. For example GitHub user `ns-rse` working on issue 1 to fix typehints might create a branch called
`ns-rse/1-fix-typehints` from main.

This structure is informative as it provides other people you collaborate with or who look at the repository an
indication of who created the branch, what issue they are working on and a very short indication of what it is concerned
about. With this information it is very easy to look up the relevant Issue.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 3: Assign Issues, Create Branches and Complete the Tasks

In the `python-maths` repository you have cloned and setup on GitHub there are issue templates.

In your pairs assign the `01 Add zero division exception and test` to one person and the `02 Add a square root function
and test` to the other person.

Work through the tasks adding the necessary code, saving, staging and committing your changes then pushing to `origin`
(GitHub). **The issues contain all the code you need and it can be copy and pasted into files.**

**NB** Please note the comment about holding back on merging the Square Root work, there will be conflicts
that need resolving.

Assign the person who worked on the Square root function to review the Zero Division exception and if everything looks
good merge the pull request.

:::::::::::::::::::::::: solution

## Solution - 01 Zero Division Exception

``` bash
git switch main
git pull
git switch -c ns-rse/1-zero-divide-exception
# MAKE EDITS AS DESCRIBED IN THE GITHUB ISSUE
git add -u
git commit -m "Add Zero division exception and test"
git push
```

You should then create a pull request, assign it to your collaborator who can review and approve.
:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution - 02 Square root function

``` bash
git switch main
git pull
git switch -c ns-rse/2-square-root
# MAKE EDITS AS DESCRIBED IN THE GITHUB ISSUE
git add -u
git commit -m "Adds square root function"
git push
```

You should **not** have created a pull request, if you have you may find that there are conflicts which need
resolving. This is by design and we will resolve them later.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

### Deleting branches

Branches are typically short lived as they are created to address small focused pieces of work such as fixing a bug or
implementing a new feature before being merged into the `main` branch. Over time you will accrue a number of redundant,
out-dated branches and it is therefore good practice to delete unwanted branches after they have been merged.

You can not delete a branch you currently have checked out so you must first checkout an alternative branch. Typically
this would be the `main` branch after your Pull Request has been merged and the changes you were working on have been
incorporated. You should `git pull` the `main` branch after merging changes so your local copy is aware of any recent
merges from branches you are about to delete.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 4: Delete a branch

Create a throw away branch from `main` and then delete it (hint see `git branch --help`). You can create a branch with
your username and `throwaway` (e.g. `ns-rse/throwaway`) with the following.

``` bash
git checkout main
git pull
git switch -c ns-rse/throwaway
```

Pretending the branch you just created has been merged into the `main` branch via a Pull Request delete the now
redundant branch (in this example `ns-rse/throwaway`).

:::::::::::::::::::::::: solution

## Solution 1

You can use the `-d` or `--delete` flag to delete a branch.

``` bash
git switch main
git branch -d ns-rse/throwaway
```

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2

You can use the shortcut `git switch -` to switch to the last branch you were on (this is a shortcut that is common to
the Bash shell when navigating directories too, `cd -` will change directory to the previous directory you were in).

``` bash
git switch -
git branch -d ns-rse/throwaway
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Force branch deletion

You were able to delete the branch you created because you hadn't made any changes to it. If you have made changes on a
branch and they have not been merged into `main` then Git will warn you of this and refuse to delete the branch. This
can be over-ridden with the `--force` flag or the shorthand `-D` which is the same as `--delete --force`.

``` bash
git switch -c ns-rse/throwaway
touch test_file
git add test_file
git commit -m "Adding test_file"
git switch -
git branch -d ns-rse/throwaway
error: the branch 'ns-rse/throwaway' is not fully merged
hint: If you are sure you want to delete it, run 'git branch -D ns-rse/throwaway'
hint: Disable this message with "git config advice.forceDeleteBranch false"
git -D ns-rse/0-divide
```

Be _very_ careful when forcing deletion of branches, if you have not pushed your changes to the remote `origin` then you
_will_lose them.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 5 : Automatically delete branches on GitHub

In your pairs navigate to the _Settings_ page and enable the _Automatically delete head branches_ option.

**Hint** - Search [GitHub documentation][gh-help] for how to automatically delete branches.

:::::::::::::::::::::::: solution

## Solution 1

This option is on the _Settings > General_ page under the _Pull Requests_ section where you can select  "_Automatically
delete head branches_".

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

## Time Travelling - Losing your `HEAD`

A branch is a history of commits and you can use `git log` to see the commit history (and customise the output so it can
be easier to read), but what if you wanted to look at the state of the branch at a previous point in time? Well because
Git has kept track of everything you can do that and the command to do so is `git checkout` which takes a "reference" as
an argument. So far you have been using branch names as references but commit hashes are also references and so can be
used to checkout the state of the repository in the past.

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_diverging_checkout-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101"
       commit id: "1-51816d6"
       commit id: "2-6769ff2"
       commit id: "3-8c52dce"
       commit id: "4-8ec389a"
       commit id: "5-2315fa0"
       commit id: "6-93e787c"
       commit id: "7-bc43901"
       commit id: "8-a80cef8" tag: "HEAD"

-->

<!-- markdownlint-disable-next-line MD013 -->
![A linear Git History on the `main` branch showing the position of `HEAD`.](https://mermaid.ink/img/pako:eNp1kT1rwzAQhv-KOTBerCDJ1oe1lSa0Q7duxYsiyYlobAdHgabG_71ygguFWpPuuecOjncE01sHCtJ09J0PKhmzcHSty1SS7fXFZXmSHXx4GfT5mM3doQ86uOe-bX1403t3ijQMVzfVXbK8-J_S9AGW4d-2uY8m3qqkBoxKQRuCSQ3_CwQxIgm3fE2giAteNQ1dEwokDaPWuDWhRNKZQlZ6TWCIFoQ1Gq8JHFWFE1KYNUGgvSmLav1MibTExjWyhiTow4xed0_bRYccWje02tsY1TizGu4x1TCrVg-fszpF73q2MZ-d9aEfQM3J5KCvoX-_dWapH87W68OgW1CNPl0iPevuo-__1KBG-AJFRblhFDMuZSk445TlcItYlhuBaYEJpixex6ccvu8b8EYyIbGsGClwbFA6_QAk7q7t?type=png){alt="A linear Git History on the `main` branch showing the position of `HEAD`."}

Here we have a simple linear history and the `HEAD` of branch is on commit `8-a80cef8` If you want to checkout commit
`4-8ec389a` then you would `git checkout 4-8ec389a` and you will see the following useful and informative warning
message.

``` bash
git checkout 4-8ec389a
Note: switching to 4-8ec389a'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 4-8ec389a complete subtract issue template
```

Have you lost your head because it is now `detached`? No, `HEAD` is just a special reference that points to a specific
commit (tags are the same) and it is a short hand way of referring to a commit, what has happened is that Git has moved
the commit `HEAD` points to from `8-a40cef8` to `4-8ec389a`. If you make changes to this branch they will be lost when
you switch back to the `8-a40cef8` commit and you are told you can do this with `git switch -`. If you want to make
changes and save them you are advised to create a new branch to do so.

::::::::::::::::::::::::::::::::::::: callout

## Work in Progress

If you try to `git checkout` whilst you have unstaged changes you are likely to hit an error about changes being
overwritten.

``` bash
error: Your local changes to the following files would be overwritten by checkout:
 README.md
Please commit your changes or stash them before you switch branches.
Aborting
```

This is to be expected, here the `README.md` file has changed but those changes have not been staged and committed and
if we checkout another commit or branch the state of the `README.md` will be reverted. Without committing or stashing
the changes as advised Git would not be able to revert back to the changes you have made.

We're going to cover switching branches whilst working later on.
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 6: Checkout old commits

- Look at the history of the `python-maths` repository and find out who the author of commit `585287a` was (there are
  several ways of doing this).
- Checkout this commit and look at the contents of the file `tests/test_add.py` (you can use `cat tests/test_add.py`).
- Switch back to `HEAD`, has anything changed in the `tests/test_add.py` file?

:::::::::::::::::::::::: solution

## Solution 1

You could query the logs for the specific commit and it will show you the history from there.

``` bash
git log 585287a

commit 585287aec5d94b10d03ae8fd35ad0423b2c1b0db (HEAD)
Author: Anna Krystalli <annakrystalli@googlemail.com>
Date:   2021-05-14 12:38:09 +0300

    add test and CI

```

Alternatively you can checkout the specific hash and use `git log` to look at who made the commit.

```bash
git checkout 585287a
git log

commit 585287aec5d94b10d03ae8fd35ad0423b2c1b0db (HEAD)
Author: Anna Krystalli <annakrystalli@googlemail.com>
Date:   2021-05-14 12:38:09 +0300

    add test and CI

```

We can see it was made by `Anna Krystalli <annakrystalli@googlemail.com>` almost four years ago on `2021-05-14 12:38:09
+0300`.

```bash
cat tests/test_add.py

import src.python_calculator.add as add


def test_add():
    assert add.add(1, 3) == 4

git switch -
cat tests/test_add.py

cat: tests/test_add.py: No such file or directory
```

The file `tests/test_add.py` has an import statement and defines the `test_add()` function which checks if the
`add.add()` function returns the value of 4 when given the numbers 1 and 3.

The `tests/test_add.py` file no longer exists on the `HEAD` of the `main` branch!

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Checkout Anything

You are not restricted to switch to commits on the same branch you are currently on. You can checkout any commit in
the history as long as you know the commit hash.

::::::::::::::::::::::::::::::::::::::::::::::::

## Comparing References

This is quite a convoluted way of comparing branches though and in this instance the difference is quite simple the file
no longer exists, but imagine you wanted to compare a file between branches or commits _without_ having to switch
branches and try and hold in your head what the file looked like on one branch whilst you look at the other. That would
probably be very challenging.

Fortunately Git can help you here with `git diff`. This takes one or two arguments, which are commits or references that
you want to compare. If only one argument is given it compares the currently checked out commit to the supplied
commit/reference.

Thus to compare the `HEAD` of the `divide` branch you would

```bash
git switch ns-rse/1-zero-divide-exception
git diff 585287a
# Equivalent to...
git diff HEAD 585287a
```

If you want to limit this to a specific file use the `-- <file1> <file2>` option.

```bash
git switch main
git diff 585287a -- tests/test_add.py
```

## Ooops! I Did It Again

Nothing to do with Britney Spears but you are at some stage likely to commit changes to the wrong branch. This can
easily happen when starting to work on an issue without first creating a new branch to contain the work and you commit
the changes to either the `main` branch, which is often protected so you won't be able to push your changes, or the last
branch you were working on.

### `git reset`

One way to solve this with Git is to `git reset` the branch to which you have just mistakenly made the commit. This
removes reference to the changes from the Git history but leaves the changes to the files in place and they appear as
`unstaged` files. It is ideal if you have only _one_ commit you wish to undo.

#### Relative Refs

Normally you are working on the `HEAD` of a branch which is the most recent commit that has been made along with any
staged, but uncommitted changes. Git has a simple way of referring to previous commits relative to `HEAD` using the `~`
(tilde) character and counting backwards.

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_relative_refs-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101" tag: "~8"
       commit id: "1-51816d6" tag: "~7"
       commit id: "2-6769ff2" tag: "~6"
       commit id: "3-8c52dce" tag: "~5"
       commit id: "4-8ec389a" tag: "~4"
       commit id: "5-2315fa0" tag: "~3"
       commit id: "6-93e787c" tag: "~2"
       commit id: "7-bc43901" tag: "~1"
       commit id: "8-a80cef8" tag: "HEAD"

-->
<!-- markdownlint-disable-next-line MD013 -->
![Relative Refs on a Git Branch](https://mermaid.ink/img/pako:eNp10k1rgzAYB_CvIg8UL6bkxbzobaxlO-y22_CSxtiGVS02hXXiPvtii5uDmVPyf34JJE96MG1pIYfVqneN83nUx_5gaxvnUbzTZxsnUbx3_qnTp0M8VrvWa28f27p2_kXv7DGkvrvYoWiiaYT5sFrdg2nzT9nctkauzKMCMEolrQgmBURe78foSxXwPyaIE0VEKWZYLmGKhBRZVdEZFkuYIWU4LY2dYb6EU6SsYSrTM5wuYY4oI7zSeIbZEhYoY1YqaWaYLmGJdiZl2Z-nI0tYIa2wsZX6xc_bh83EIYHadrV2ZfgK_ZgVcPsGBYy01N37SIfgLqcy9H9bOt92kI-dT0BffPt6bcy0vpuN0_tO15BX-ngO6Uk3b237Zw15Dx-QU5muOcVcKJVKwQXlCVxDrNK1xJRhgikPNxVDAp-3E_BacamwyjhhOBQoHb4BB-fKEQ?type=png){alt="Relative references on the `main` branch with 9 commits showing the commit hash and the reference relative to the `HEAD`"}

If you want to undo the _last_ commit then you can do this using `git reset HEAD~1`.

``` bash
touch test_file
git add test_file
git commit -m "Adding test_file"
git reset HEAD~1
```

::::::::::::::::::::::::::::::::::::: callout

## `reset` options

There are three options to `git reset` that influence how the changes in commits are handled, these are `--soft`,
`--mixed` (the default) and `--hard`.

For a detailed exposition of `git reset` see the excellent [Atlassian | Git reset][atlassian_git_reset] article.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 7: Commits on the wrong branch

- Switch to the `main` branch of the `pytest-maths` repository.
- Create a new file using `echo "# This is TODO list" > TODO.md`
- Stage and commit the file to the `main` branch of your repository. **NB** to do this you will have to disable the
  `pre-commit` checks with the `-n` flag.

Ooops you've just committed to the `main` branch which is protected so you can't push your changes. Now move the commit
to a new branch so you can push them.

- Reset the change.
- Create a new branch called `<github_user>/todo`.
- Stage and commit the file to `<github_user>/todo`.

:::::::::::::::::::::::: solution

## Solution 1

You can `git reset --mixed` to `HEAD~1`, i.e. the previous commit, which removes the `CONTRIBUTING.md` file from the
commit history, leaving it unstaged, then create a new branch and add it to that.

``` bash
git switch main
echo "# This is a TODO list" > TODO.md
git add TODO.md
git commit -n -m "docs: Adding a todo file"
git reset --mixed HEAD~1
git switch -c ns-rse/todo
git add CONTRIBUTING.md
git commit -m "docs: Adding a todo file"
```

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2

Alternatively you can checkout the previous commit _before_ you added the file by mistake, create the
`<github_user>/contributing` branch,  and `git cherrypick` the commit from `main` which contains the `CONTRIBUTING.md`
file and _then_ remove the commit from main.

``` bash
git switch main
echo "# This is a TODO list" > TODO.md
git add TODO.md
git commit -n -m "docs: Adding a todo file"
git log              # Note the commit of the mistaken hash
git checkout HEAD~1    # Checkout the previous commit on the main branch
git switch -c ns-rse/todo
git cherrypick <hash>
git switch main
git reset --hard HEAD~1
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- :::::::::::::::::::::::: solution -->

<!-- ## Solution 3 -->

<!-- A third similar option is checkout the previous commit _before_ you added the file by mistake, create the -->
<!-- `<github_user>/contributing` branch, and copy the `CONTRIBUTING.md` file from the `HEAD` of `main` using -->
<!--  `git restore` and _then_ remove the commit from main. -->

<!-- ``` bash -->
<!-- # TODO get commit hash of last commit -->
<!-- git checkout HEAD~1 -->
<!-- git switch -c ns-rse/contributing -->
<!-- git restore -s main -- CONTRIBUTING.md # Copy the file from HEAD of main branch or -->
<!-- git add CONTRIBUTING.md -->
<!-- git commit -m "Adding contributing guideline template" -->
<!-- git switch -   # Switch back to main -->
<!-- git -->
<!-- # TODO : Complete solution and add output once sample repository is in place -->
<!-- ``` -->

<!-- **NB** You could also copy the file using the older `git checkout main -- CONTRIBUTING.md`. -->

<!-- ::::::::::::::::::::::::::::::::: -->

<!-- You then have to decide how to add the changes to a branch. If they are brand new then you can create a new  -->
<!-- branch and add them. If however they were meant to be added to an existing branch you face a slight problem  -->
<!-- as if you try to switch branches you will be told that this would over-write the changes to the files you  -->
<!-- have just modified and unstaged and you don't want to lose your work. -->

<!-- The solution here is to use `git stash` to temporarily store the unstaged changes, switch branches to the  -->
<!-- target branch they should be on, and you can then un-stash them (known as `pop`ing) onto the correct branch. -->

::::::::::::::::::::::::::::::::::::: callout

## `undo` &nbsp; alias

You can set an alias to undo the last commit with

``` bash
git config --global alias.undo 'reset HEAD~1'
```

This adds the following line to the `alias` section of your `~/.gitconfig`.

``` bash
[alias]
    ...
    undo = reset HEAD~1
```

::::::::::::::::::::::::::::::::::::::::::::::::

### `git revert`

`git reset` is destructive, you can lose work using it and it is advisable _not_ to use it when you have more than one
commit you wish to undo as you lose the intermediary work between commits as you are restored to the commit you reset
to. Fortunately Git has the `revert` option which is a non-destructive approach to undoing changes in your Git
history. Instead it takes a specified commit and inverts the changes, i.e. goes back to the previous state and rather
than discarding the changes it makes a new "revert" commit to record the inversion and this new "revert" commit becomes
the `HEAD` of the branch. `git revert` _has_ to have a reference in order to work, whether that is absolute (i.e. a
hash) or relative.

::::::::::::::::::::::::::::::::::::: callout

## reset v's revert

The differences between `reset` and `revert` is that one (`reset`) is destructive and loses changes the other (`revert`)
undoes the changes and makes a new commit recording these changes.

Be _very_ careful when using `reset`, if you have not pushed your changes to the remote `origin` then you will lose
them.

::::::::::::::::::::::::::::::::::::::::::::::::

## Switching Branches during Work in Progress

Sometimes you will be doing some work and a colleague will ask you to review a pull request or help them with a problem
they have on their branch. When performing pull request reviews it can be quite common to run tests to check everything
passes if you don't have Continuous Integration doing this automatically for you (we will come to that in another
episode).

But there is a challenge, in order to switch branches you have to stage and commit all changes to tracked files.

``` bash
git switch -c branch2
echo "Please feel free to contribute to this repository" >> CONTRIBUTING.md
git add CONTRIBUTING.md
git commit -m "Adding CONTRIBUTING.md"
echo "\nPlease don't break my repository though!" >> CONTRIBUTING.md
git switch main

error: Your local changes to the following files would be overwritten by checkout:
 CONTRIBUTING.md
Please commit your changes or stash them before you switch branches.
Aborting
```

Whilst you could commit your changes and subsequently `git commit --amend` (more on this in the next episode) there is
another option.

### `git stash`

`git stash` allows you to save your current changes in a temporary location and then reverts to the last commit
(`HEAD`). This allows you to move about`git switch` to other branches and undertake work. There are lots of options to
`git stash` but the basics are pretty straight-forward. You start by `git stash push` (the `push` is actually optional)
and you can include a `--message` that explains what the stash contains, you are told if this has worked and on what
branch the stash was made and can then switch branches, pull down changes, create a new branch and do something
different.

#### 1. Stash `CONTRIBUTING.md`

``` bash
git stash --message "WIP : CONTRIBUTING.md"
Saved working directory and index state On branch2: CONTRIBUTION.md WIP
```

#### 2. Switch to `main` and create `branch3`

``` bash
git switch main
# If this weren't a dummy example you might git pull
git switch -c branch3
```

Undertake the work that is required on `branch3`.

#### 3. Return to `branch2`

When you have finished this other work you can return to `branch2` and `pop` the stash back. To see what stashes there
are you can use `git stash list`

``` bash
git stash list
stash@{0}: On branch2: CONTRIBUTION.md WIP
```

#### 4. `pop` the last stash

When you are ready to restore the work you can do so using `git stash pop` which by default will restore the _last_
stash.

``` bash
git stash pop
On branch branch2
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
 modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (13c8c6fb23f9fcdd884b4528356db37527c9b3e4)
```

The changes to `CONTRIBUTING.md` and the corresponding entry are removed from the stash list.

### Multiple Stashes

Over time though you may collect multiple stashes.

#### 1. Make two stashes

We stash `CONTRIBUTING.md`, the last message is reused by default, then we add `ANOTHER.md` and stash it with a
different message.

``` bash
git switch -c example-stashes
git stash push --message "WIP CONTRIBUTING.md file"
echo "Yet another file" > ANOTHER.md
git add ANOTHER.md
git stash push --message "WIP ANOTHER.md file"

stash@{0}: On branch2: WIP ANOTHER.md file
stash@{1}: WIP on branch2: a8b6f5f WIP CONTRIBUTING.md
```

#### 2. Pop the `CONTRIBUTING.md` stash

There are now two stashes each with different names.

You may not want to restore the work stashed with the commit message `WIP ANOTHER.md file` but rather restore the
earlier `WIP CONTRIBUTING.md file` work first. You can do this by referring to the number associated with the stash that
is within the curly braces. For the `WIP CONTRIBUTING.md file` stash this is `1`.

``` bash
git stash list

git stash pop 1

On branch branch2
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
 modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{1} (dd538beb8f14590f720e9b9f677ba7381240bd92)
```

Only the `CONTRIBUTING.md` file has been restored and not the `ANOTHER.md` and there is only one stash left.

``` bash
git stash list

stash@{0}: On example-stashes: WIP ANOTHER.md file

ls -lha CONTRIBUTING.md ANOTHER.md
ANOTHER.md: No such file or directory (os error 2).

.rw-r--r-- neil neil 0 B Thu Feb 13 13:36:29 2025  CONTRIBUTING.md
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 7: Stashing

Working in your pairs on the `python-maths` repository...

1. Create a `<github-username>/todo` branch.
2. Create a `TODO.md` with `echo "# TODO Tasks." > TODO.md`
3. Do _not_ add and commit, instead `git stash push -m "Adding a TODO file"` your changes
4. Switch to the `main` branch and create a `<github-username>/citation` branch.
5. Add a basic `CITATION.cff` with `echo "cff-version: 1.2.0\ntitle: Python \ntype: software" > CITATION.cff`
6. Add and commit this file.
7. Unstash the `TODO.md` file on the `citation` branch.
8. Stage and commit the changes. Do **NOT** create a pull request or merge these changes.
9. Delete the branches locally (try and avoid any messages telling you there are unmerged changes, see `git branch
   --help` on how to force deletion of unmerged branches).

:::::::::::::::::::::::: solution

## Solution : stashing

Lets create the `<github-username>/todo` branch

```bash
git switch main
git pull
git switch -c <github-username>/todo
echo "# This is a ToDo List\n\n1. Task1\n2.Task2." > TODO.md
```

If we want to switch branches without making a commit but save our work in progress we stash the work and switch to
`main` and _before_ creating a new branch (`<github-username>/citation`) for adding a `CITATION.cff` file.

```bash
git stash -m "WIP : adding TODO.md"
git switch main
git switch -c <github-username>/citation
echo "cff-version: 1.2.0\ntitle: Python Maths Package\ntype: software" > CITATION.cff
git add CITATION.cff
git commit -m "doc: Adding a CITATION.cff"
```

We now unstash the `WIP : adding TODO.md` stash to this branch and commit the changes, amending the commit and push to
GitHub.

```bash
git pop
git add TODO.md
git commit -m "doc: Adding a TODO.md"
git push
```

You can now make a pull request to merge these changes, assign it and have it reviewed and merged.

``` bash
git switch main
git pull
git branch -D {<github-username>/citation, <github-username>/todo}
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Popping around and applying

You can `git stash apply` to `pop` a stash but leave it in the stash list. This might be useful if you want to apply the
stashed changes to several branches you are working on.

There are a lot of useful things `git stash` can be used for. Refer to the help pages (`git stash --help`) for more
information as well as the Further Resources.
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## References - a revelation

Whilst we have focused on consolidating our understanding of branches in this introductory episode there have been hints
as to the _true_ nature of branches in Git, have you worked out what this is yet?

Internally Git does not have branches at all! Branches are merely a reference to a series of commits and each commit in
a "branch" references the commit prior to it. In fact everything in Git that allows us to look at the
different states of the repository and move between them is a reference, whether that is a named branch, or a tag which
is a relative reference. They all point to a commit.

This was a revelation that came to me as I wrote the material for this Episode and thought it worth sharing.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints

- Branches and how they relate to each other are fundamental to collaborating using Git.
- The history of a branch is a series of commits and extends all the way back to the very first commit and _not_ the
  point at which it forked from its parents.
- Branches can be easily created, merged and deleted.
- Commits all have references and Git can move you between these references using `git commit` or compare them using
  `git diff`.

::::::::::::::::::::::::::::::::::::::::::::::::

## Links

- [git branches: intuition & reality](https://jvns.ca/blog/2023/11/23/branches-intuition-reality/)

[atlassian_git_reset]: https://www.atlassian.com/git/tutorials/undoing-changes/git-reset
[gh-help]: https://docs.github.com/en
