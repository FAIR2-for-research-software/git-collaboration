---
title: "Diverging Branches"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- What are branches?
- How do we use branches in git effectively?
- How can I check out other people's branches whilst working on my own?
- How do I keep my development branch up-to-date with `main`?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- How branches can be used to fix bugs or develop features in isolation.
- Switching branches, stashing and restoring.
- How to keep a development branch up-to-date.
- Differences between and when to use merge and rebase.
- Git worktrees instead of branches.
- Tracking multiple origins.

::::::::::::::::::::::::::::::::::::::::::::::::

## Diverging Branches

As you and your collaborator(s) work on your repository you may find that changes others have made get merged into the
`main` before you have finished your work. This has in fact just happened, the work to add a Zero Division exception
has been merged via a Pull Request, but the work to address the Square Root function hasn't and is in effect behind the
`main` branch. The following is a representation of the current state, albeit from a single developer.

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_diverging_branches-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101"
       commit id: "1-51816d6"
       commit id: "2-6769ff2 (base)"
       branch "ns-rse/1-zero-division"
       branch "ns-rse/2-square-root"
       checkout "ns-rse/1-zero-division"
       commit id: "3-8c52dce"
       checkout "ns-rse/2-square-root"
       commit id: "4-8ec389a"
       checkout "ns-rse/1-zero-division"
       commit id: "5-2315fa0"
       checkout "ns-rse/2-square-root"
       commit id: "6-93e787c"
       checkout main
       merge "ns-rse/1-zero-division" id: "7-bc43901" tag: "Merge zero division"
       commit id: "HEAD"

-->

<!-- markdownlint-disable-next-line MD013 -->
![Diverging branches in `python-maths`: `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`.](https://mermaid.ink/img/pako:eNqtklFv2yAUhf8KQoq8SSYDbDD4bVqr9WF72tvkF4JxglqbDOOpreX_PkiaKpXadNJmP9j38N1zD9KdoXatgTVcrWY72FCDOQs705usBtlGjSbLQba14atX-12WTr0LKpgvru9t-KY25i6qwU9maQZweuL_slodhVPz87E-tALb1qCBGJUV7QgmDXwdIIgRQXjL3wIo4hWXXUfBh5T34xm38WrQu8gMI_Kj-UTQo_EOtfa3Ha0bLpAUjb8m5Q3yzoXzyTujb90U_sbzRcoCCc1oq81FszfHnluVSBhdCKn-Ry6GaEFYp_C_5-JIFqYSlX7Nqld2eFZ747fmQtQnwwptdFnItBsgqG2Svh86Ew7eu9nN9eerBsIcxmlxeht3fE5kAw_73cAEtcrfJoMlctO-jYt93drgPKzTSudQTcH9eBj0qT4yV1ZtvepP4l4NP52LZafuxmMN6xnewxqVAq8lq-KLZcmpECyHD7DmeM0xZfFqRMr4kUsOHw8WdI1piQvBSMEKLmW1_AHrjxWc?type=png){alt-text="Diverging branches in `python-maths` `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`."}

In this example the `main` branch now includes the commits `3-8c52dce` and `5-2315fa0` from the `ns-rse/1-zero-division`
branch as well as the commit `7-bc43901` which was made when the `ns-rse/1-zero-division` branch was merged in. The
`ns-rse/2-square-root` branch does _not_ contain these commits.

Sometimes this won't be a problem, the two features/issues are completely independent and have not made changes to the
same file and it would be possible to merge the branches into `main` without any merge conflicts because neither have
modified the same files in the same location.

That will not always be the case though, sometimes merge conflicts might arise if the second branch is changing some of the
same files as the first branch. Another scenario might be that whilst work was being done on adding a new feature branch,
a critical bug was fixed that the new feature depends on and the changes now in `main` need incorporating in the feature
branch. That is in fact the situation we have with our `ns-rse/2-square-root` branch, it conflicts with `main` because
of the changes we merged from the `ns-rse/1-zero-division` branch.

There are two approaches to solving this: merging (`git merge`)  and rebasing (`git rebase`).

### Merging

<!-- markdownlint-disable-next-line MD013 -->
![**Before** - diverged branches in `python-maths`: `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`.](https://mermaid.ink/img/pako:eNqtklFv2yAUhf8KQoq8SSYDbDD4bVqr9WF72tvkF4JxglqbDOOpreX_PkiaKpXadNJmP9j38N1zD9KdoXatgTVcrWY72FCDOQs705usBtlGjSbLQba14atX-12WTr0LKpgvru9t-KY25i6qwU9maQZweuL_slodhVPz87E-tALb1qCBGJUV7QgmDXwdIIgRQXjL3wIo4hWXXUfBh5T34xm38WrQu8gMI_Kj-UTQo_EOtfa3Ha0bLpAUjb8m5Q3yzoXzyTujb90U_sbzRcoCCc1oq81FszfHnluVSBhdCKn-Ry6GaEFYp_C_5-JIFqYSlX7Nqld2eFZ747fmQtQnwwptdFnItBsgqG2Svh86Ew7eu9nN9eerBsIcxmlxeht3fE5kAw_73cAEtcrfJoMlctO-jYt93drgPKzTSudQTcH9eBj0qT4yV1ZtvepP4l4NP52LZafuxmMN6xnewxqVAq8lq-KLZcmpECyHD7DmeM0xZfFqRMr4kUsOHw8WdI1piQvBSMEKLmW1_AHrjxWc?type=png){alt-text="**Before** - diverged branches in `python-maths` `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`."}

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_merging-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101"
       commit id: "1-51816d6"
       commit id: "2-6769ff2 (base)"
       branch "ns-rse/1-zero-division"
       branch "ns-rse/2-square-root"
       checkout "ns-rse/1-zero-division"
       commit id: "3-8c52dce"
       checkout "ns-rse/2-square-root"
       commit id: "4-8ec389a"
       checkout "ns-rse/1-zero-division"
       commit id: "5-2315fa0"
       checkout "ns-rse/2-square-root"
       commit id: "6-93e787c"
       checkout main
       merge "ns-rse/1-zero-division" id: "7-bc43901" tag: "v0.1.1"
       commit id: "HEAD"
       checkout "ns-rse/2-square-root"
       merge "main" id: "8-a80cef8" tag: "Merge main"
       commit id: "9-cb839a0"
       commit id: "10-1ae54c3"
       checkout "main"
       merge "ns-rse/2-square-root" id: "11-a8c9932" tag: "v0.1.2"

-->

<!-- markdownlint-disable-next-line MD013 -->
![**After** - `main`, which has already had the work in `ns-rse/1-zero-division` merged into it, is merged into `ns-rse/2-square-root`. Development is completed on `ns-rse/2-square-root` and the feature merged into `main`.](https://mermaid.ink/img/pako:eNqtk8GOmzAQhl8FWYpoJUxtA8bmVnVX7aE99VZxMcYk1i44NWbVXcS715CyTaIkqtpywuN_vvk9mhmBNLUCBdhsRt1pVwRj6HaqVWERhJXoVRgF4Va7j1bsd-F8a40TTn0wbavdZ1GpRx91dlBT2QXr5_-nzeYQWJNfr-WSGui6CEqAYJqTBiNcgssCDDPMMK3pNQGBNKe8aUjwZvb79khXWdHJndd0PbS9eofhi7IG1vpJ99p0N5QE9t8HYRW0xrjjyjslH8zg_oR54jKBTGakluom7GrZY1QKmZIJ4-J_-MogSXDWCPTvvijkicpZLi-hWqG712ir7FbdsPoLmMNKpgmfZyNwYjuHnlCM46uz8un-_d1fvWM1tLhcyzMoGJKqYb_Lf1l0B9VlCxzKiiX8tJ8n84wgFipLZXLZ6Rn8rFVnD1iZ2FuVnCfkrFNkJoEIeIrn1n7Nx5lcgmXFSzALa2EfZtnkdcO-9rt9X2tnLCjmrY6AGJz5-tzJ9XzQ3GmxtaIFRSMeex_di-6bMSdnUIzgBygwyWOcEoSTDHHMEU8i8AwKwmjMckwYyThllLIpAi8LAcUMYR9KEfU5Wcbx9BO0WV5-?type=png){alt-text="**After** - `main`, which has already had the work in `ns-rse/1-zero-division` merged into it, is merged into `ns-rse/2-square-root`. Development is completed on `ns-rse/2-square-root` and the feature merged into `main`."}

The syntax of `git merge` is

``` bash
git merge <OPTIONS> <ref>
```

Where `<ref>` is one of a commit, branch name or tag (both of which are references to commits). There is an option for
how the merge is made known as `fast-forward`. Fast-forward is the default action unless annotated tags are being merged
that are in the incorrect hierarchy and it updates the branch pointer (`HEAD`) to match the merged branch.

Typically, as in this example, the `main` branch contains work from someone else's branch and we want to incorporate
those changes in the other branch.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Remember to take the time to show the contents of the files and how they "disappear" when switching branches, in
particular after having added `README.md` to `branch1` and switching back to `main`.

Also use `git log` alias (or other form of `git log` that shows branches) to show the changes and explain the point at
which each of the branches is at with reference to the `*` indicating commits, the branch names and where they sit and
the date/time stamps.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

#### 1. Make a new repository

``` bash
cd ~/work/git/hub/ns-rse
mkdir git-merge-test
cd git-merge-test
git init --initial-branch=main
git commit --allow-empty -m "Initial commit"
```

#### 2. Create `branch1`, add a `README.md` and commit it

``` bash
git switch -c branch1
echo "# Just a test" > README.md
git add README.md
git commit -m "docs: Adding README.md"
git log --pretty="%h %ad (%cr) %x09 %an : %s"
```

#### 3. Switch back to `main`

Check the contents of `README.md` (there is no such file as the it exists on `branch1`).

``` bash
git switch main
cat README.md   # Note that `README.md` does not currently exist on this branch
git log --pretty="%h %ad (%cr) %x09 %an : %s"
```

#### 4. Create `branch2`, add a `LICENSE` and commit it

``` bash
git switch -c branch2
echo "YOU CAN DO WHAT YOU WANT WITH THIS CODE" > LICENSE
git add LICENSE
git commit -m "Adding a LICENSE"
git log --pretty="%h %ad (%cr) %x09 %an : %s"
```

#### 5. Merge `branch1` into `main`

Switch back to `main` and merge `branch1` (this is equivalent to merging a Pull Request). The file `README.md` now
exists on the `main` branch.

``` bash
git switch main
git merge branch1
cat README.md
git log --pretty="%h %ad (%cr) %x09 %an : %s"
```

#### 6. Merge `main`, which now contains `README.md`, into `branch2`

Switch to `branch2` which has now diverged as it contains changes of its own _and_ `main` contains the changes made on
`branch1`.  We want to merge the changes on `main` and "fast-forward" if possible.

``` bash
git switch branch2
git merge --ff main # Merge changes merged into main from branch1 into branch2
git log --pretty="%h %ad (%cr) %x09 %an : %s"

*   d914fee - (HEAD -> branch2) Merge branch 'main' into branch2 (2024-03-01 12:02:08 +0000) <Neil
|\
| * 7817070 - (main, branch1) Adding a README.md (2024-03-01 11:57:35 +0000) <Neil Shephard>
* | a14a643 - Adding a LICENSE (2024-03-01 12:00:39 +0000) <Neil Shephard>
|/
* 1bd6bb8 - Initial commit (2024-03-01 11:57:06 +0000) <Neil Shephard>
```

#### 7. Merge `branch2` into `main`

We now have the changes from `branch1` included in `branch2` by virtue of having merged `main`. If we switch back to
`main` we can merge the changes from `branch2`.

``` bash
git switch main
git merge branch2
git log --pretty="%h %ad (%cr) %x09 %an : %s"
*   d914fee - (HEAD -> main, branch2) Merge branch 'main' into branch2 (2024-03-01 12:02:08 +0000) <Neil Shephard>
|\
| * 7817070 - (branch1) Adding a README.md (2024-03-01 11:57:35 +0000) <Neil Shephard>
* | a14a643 - Adding a LICENSE (2024-03-01 12:00:39 +0000) <Neil Shephard>
|/
* 1bd6bb8 - Initial commit (2024-03-01 11:57:06 +0000) <Neil Shephard>
```

#### 8. Delete `branch1` and `branch2`

As we're done with `branch1` and `branch2` we can delete them.

``` bash
# Delete the two branches
git branch -d branch{1,2}
git log --pretty="%h %ad (%cr) %x09 %an : %s"
*   d914fee - (HEAD -> main) Merge branch 'main' into branch2 (2024-03-01 12:02:08 +0000) <Neil Shephard>
|\
| * 7817070 - Adding a README.md (2024-03-01 11:57:35 +0000) <Neil Shephard>
* | a14a643 - Adding a LICENSE (2024-03-01 12:00:39 +0000) <Neil Shephard>
|/
* 1bd6bb8 - Initial commit (2024-03-01 11:57:06 +0000) <Neil Shephard>
```

Having used `git merge` we couldn't perform a simple fast-forward because the history of `main` now contained changes
that were made on `branch1` and so a separate commit (`d914fee`) was made to merge the `main` branch into `branch2`
(commits are denoted by `*` and so you can see the commits were made on separate branches). We can see from the graph
that `README.md` was added from a separate `branch1` and `LICENSE` was added from `branch2`, although after deleting the
branches they are no longer shown by name in the `git log --graph` output.

### Rebasing

Rebasing moves the point at which the branch diverged from its original position to another, in this case the `HEAD` of
the `main` branch. You are changing the `base` commit, hence the name `git rebase`.

<!-- markdownlint-disable-next-line MD013 -->
![**Before** - diverged branches in `python-maths`: `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`.](https://mermaid.ink/img/pako:eNqtklFv2yAUhf8KQoq8SSYDbDD4bVqr9WF72tvkF4JxglqbDOOpreX_PkiaKpXadNJmP9j38N1zD9KdoXatgTVcrWY72FCDOQs705usBtlGjSbLQba14atX-12WTr0LKpgvru9t-KY25i6qwU9maQZweuL_slodhVPz87E-tALb1qCBGJUV7QgmDXwdIIgRQXjL3wIo4hWXXUfBh5T34xm38WrQu8gMI_Kj-UTQo_EOtfa3Ha0bLpAUjb8m5Q3yzoXzyTujb90U_sbzRcoCCc1oq81FszfHnluVSBhdCKn-Ry6GaEFYp_C_5-JIFqYSlX7Nqld2eFZ747fmQtQnwwptdFnItBsgqG2Svh86Ew7eu9nN9eerBsIcxmlxeht3fE5kAw_73cAEtcrfJoMlctO-jYt93drgPKzTSudQTcH9eBj0qT4yV1ZtvepP4l4NP52LZafuxmMN6xnewxqVAq8lq-KLZcmpECyHD7DmeM0xZfFqRMr4kUsOHw8WdI1piQvBSMEKLmW1_AHrjxWc?type=png){alt-text="**Before** - diverged branches in `python-maths` `ns-rse/2-square-root` is now behind the `main` branch which has incorporated the changes from `ns-rse/1-zero-division`."}

<!-- Source for Mermaid diagram :
     https://gist.github.com/ns-rse/08fb86b003a26f7855281eeea88566d0#file-git_graph_branching_rebase-js
-->
<!--
%%{init: {'theme': 'base', 'gitGraph': {'rotateCommitLabel': true}
         }
}%%
    gitGraph
       commit id: "0-472f101"
       commit id: "1-51816d6"
       commit id: "2-6769ff2 (base)"
       branch "ns-rse/1-zero-division"
       checkout "ns-rse/1-zero-division"
       commit id: "3-8c52dce"
       checkout "ns-rse/1-zero-division"
       commit id: "5-2315fa0"
       checkout main
       merge "ns-rse/1-zero-division" id: "7-bc43901" tag: "v0.1.1"
       commit tag: "Rebase"
       branch "ns-rse/2-square-root"
       checkout "ns-rse/2-square-root"
       commit id: "4-8ec389a"
       checkout "ns-rse/2-square-root"
       commit id: "6-93e787c"
       commit id: "7-cb839a0"
       commit id: "8-1ae54c3"
       checkout "main"
       merge "ns-rse/2-square-root" id: "9-ba93020" tag: "v0.1.2"

-->

<!-- markdownlint-disable-next-line MD013 -->
![**After** - rebase to bring the diverged branch up-to-date with `main` which includes `ns-rse/1-zero-division`. Two more commits are made and `ns-rse/2-square-root` is then merged into `main`.](https://mermaid.ink/img/pako:eNqtk02PmzAQhv8KshTRSjH1Bxiba1v10lN7q7gYYxJrF5was-ou4r_XziYrdpVEK7Wc8DvvPB7PaGagbKtBBTab2QzGV8mc-r3udVolaSNHnW6TdGf8NycP-zRGnfXS68-2743_Lht9H1TvJr3UQ3L-wv-y2TwL5-SXsDqmJqatkhogmJekwwjX4LIBwwJzzFp2zUAgK5noOpJ8iPV-XPkaJwe1D55hhG7UnzB80s7C1jyY0dhhTdxrdWcn_y7v-nYKuSpIq_T_gBWQUFx0El2C9dIML2qv3U7f4J-AJWxUTkXsbuLlLkoPKMPZhW6fwj90bOKNHhI4_p6k09BZ628--qpz_eQccq0oF_LfUQwKqkteqmuGEqqGU_G6vWsDh1jqIlf0cjHHCYBrM3hT4wkpYCMFRQS9mQCJoHoAWxAwAdyGFZwjugbH9atBtLbS3UXjEnzToQ1797U13jpQxY3bAjl5-_NxUOfzs-eLkTsne1B18n4M6kEOv6x9dQbVDP6ACpMywzlBmBZIYIEE3YJHUBHOMl5iwkkhGGeML1vwdCSgjCMcpByxkFMUAi9_ARUBQVQ?type=png){alt-text="**After** - rebase to bring the diverged branch up-to-date with `main` which includes `ns-rse/1-zero-division`. Two more commits are made and `ns-rse/2-square-root` is then merged into `main`."}

`git rebase` takes a different approach to bringing branches up-to-date and in effect moves the point at which a branch
diverged from `main` rather than merging the changes in.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Again remember to take the time to show the contents of the files and how they "disappear" when switching branches, in
particular after having added `README.md` to `branch1` and switching back to `main`.

Also use `git log` alias (or other form of `git log` that shows branches) to show the changes and explain the point at
which each of the branches is at with reference to the `*` which denote commits, the branch names and where they sit and
the date/time stamps.

It can be useful at the end to open a second terminal and show the history of the two `git-merge-test`  and
`git-rebase-test` repositories to show how they differ in terms of branches.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

#### 1. Make a new repository

``` bash
cd ~/work/git/hub/ns-rse
mkdir git-rebase-test
cd git-rebase-test
git init --initial-branch=main
git commit --allow-empty -m "Initial commit"
```

#### 2. Create `branch1`, add a `README.md` and commit it

``` bash
git switch -c branch1
echo "# Just a test" > README.md
git add README.md
git commit -m "docs: Adding README.md"
```

#### 3. Switch back to `main`

As before `README.md` does not exist on the `main` branch.

``` bash
git switch main
cat README.md   # Note that `README.md` does not currently exist on this branch
```

#### 4. Create `branch2`, add a `LICENSE` and commit it

``` bash
git switch -c branch2
echo "YOU CAN DO WHAT YOU WANT WITH THIS CODE" > LICENSE
git add LICENSE
git commit -m "docs: Adding a LICENSE"
```

#### 5. Merge `branch1` into `main` (equivalent to making a Pull Request)

Switch back to `main` and merge `branch1` (this is equivalent to merging a Pull Request). The file `README.md` now
exists on the `main` branch.

``` bash
git switch main
git merge branch1  # Merge branch1 into main, equivalent to a Pull Request
cat README.md
```

#### 6. Rebase `branch2` onto `main` so it includes the `README.md` and the point of divergence is updated

Switch to `branch2` which has now diverged as it contains changes of its own _and_ `main` contains the changes made on
`branch1`.  We want to rebase `branch2` onto `main` so that it appears as if `branch2` forked _after_ the changes from
`branch1` were merged.

``` bash
git switch branch2
git rebase main # Rebase branch2 onto main
git  --pretty="%h %ad (%cr) %x09 %an : %s"

* 12f5202 - (HEAD -> branch2) Adding a LICENSE (2024-03-01 12:19:12 +0000) <Neil Shephard>
* 4e8e933 - (main, branch1) Adding README.md (2024-03-01 12:18:37 +0000) <Neil Shephard>
* 2459609 - Initial commit (2024-03-01 12:18:37 +0000) <Neil Shephard>

```

#### 7. Merge `branch2` into `main`

We now have the changes from `branch1` included in `branch2` by virtue of having rebased onto `main` _after_ the changes
in `branch1` were merged in. If we switch back to `main` we can merge the changes from `branch2`.

``` bash
git switch main
git merge branch2
git  --pretty="%h %ad (%cr) %x09 %an : %s"

* 12f5202 - (HEAD -> main, branch2) docs: Adding a LICENSE (2024-03-01 12:19:12 +0000) <Neil Shephard>
* 4e8e933 - (branch1) docs: Adding README.md (2024-03-01 12:18:37 +0000) <Neil Shephard>
* 2459609 - Initial commit (2024-03-01 12:18:37 +0000) <Neil Shephard>
```

#### 8. Delete `branch1` and `branch2`

As we're done with `branch1` and `branch2` we can delete them.

``` bash
git branch -d branch{1,2}
git  --pretty="%h %ad (%cr) %x09 %an : %s"

* 12f5202 - (HEAD -> main) Adding a LICENSE (2024-03-01 12:19:12 +0000) <Neil Shephard>
* 4e8e933 - Adding README.md (2024-03-01 12:18:37 +0000) <Neil Shephard>
* 2459609 - Initial commit (2024-03-01 12:18:37 +0000) <Neil Shephard>
```

As you can see the history of the `main` branch is now linear.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1: Diverging Branches

In your pairs bring the `square-root` branch up-to-date and incorporate the changes that have been merged into
`main` from the `zero-division` branch and then create a Pull Request to merge the updated `square-root` changes into
`main` on GitHub, review it and merge it.

The person who has been working on the `square-root` issue/branch will be at the helm for this, but work together to
come up with a solution. You can use either of the two strategies `git merge` or `git rebase` to do this.

<!-- markdownlint-disable-next-line MD013 -->
![Diverged branches](https://mermaid.ink/img/pako:eNqtklFv2yAUhf8KQoq8SSYDbDD4bVqr9WF72tvkF4JxglqbDOOpreX_PkiaKpXadNJmP9j38N1zD9KdoXatgTVcrWY72FCDOQs705usBtlGjSbLQba14atX-12WTr0LKpgvru9t-KY25i6qwU9maQZweuL_slodhVPz87E-tALb1qCBGJUV7QgmDXwdIIgRQXjL3wIo4hWXXUfBh5T34xm38WrQu8gMI_Kj-UTQo_EOtfa3Ha0bLpAUjb8m5Q3yzoXzyTujb90U_sbzRcoCCc1oq81FszfHnluVSBhdCKn-Ry6GaEFYp_C_5-JIFqYSlX7Nqld2eFZ747fmQtQnwwptdFnItBsgqG2Svh86Ew7eu9nN9eerBsIcxmlxeht3fE5kAw_73cAEtcrfJoMlctO-jYt93drgPKzTSudQTcH9eBj0qT4yV1ZtvepP4l4NP52LZafuxmMN6xnewxqVAq8lq-KLZcmpECyHD7DmeM0xZfFqRMr4kUsOHw8WdI1piQvBSMEKLmW1_AHrjxWc?type=png){alt-text="Diverged branches"}
<!-- markdownlint-disable-next-line MD013 -->
![Merge](https://mermaid.ink/img/pako:eNqtk8GOmzAQhl8FWYpoJUxtA8bmVnVX7aE99VZxMcYk1i44NWbVXcS715CyTaIkqtpywuN_vvk9mhmBNLUCBdhsRt1pVwRj6HaqVWERhJXoVRgF4Va7j1bsd-F8a40TTn0wbavdZ1GpRx91dlBT2QXr5_-nzeYQWJNfr-WSGui6CEqAYJqTBiNcgssCDDPMMK3pNQGBNKe8aUjwZvb79khXWdHJndd0PbS9eofhi7IG1vpJ99p0N5QE9t8HYRW0xrjjyjslH8zg_oR54jKBTGakluom7GrZY1QKmZIJ4-J_-MogSXDWCPTvvijkicpZLi-hWqG712ir7FbdsPoLmMNKpgmfZyNwYjuHnlCM46uz8un-_d1fvWM1tLhcyzMoGJKqYb_Lf1l0B9VlCxzKiiX8tJ8n84wgFipLZXLZ6Rn8rFVnD1iZ2FuVnCfkrFNkJoEIeIrn1n7Nx5lcgmXFSzALa2EfZtnkdcO-9rt9X2tnLCjmrY6AGJz5-tzJ9XzQ3GmxtaIFRSMeex_di-6bMSdnUIzgBygwyWOcEoSTDHHMEU8i8AwKwmjMckwYyThllLIpAi8LAcUMYR9KEfU5Wcbx9BO0WV5-?type=png){alt-text="Merge"}
<!-- markdownlint-disable-next-line MD013 -->
![Rebase](https://mermaid.ink/img/pako:eNqtk02PmzAQhv8KshTRSjH1Bxiba1v10lN7q7gYYxJrF5was-ou4r_XziYrdpVEK7Wc8DvvPB7PaGagbKtBBTab2QzGV8mc-r3udVolaSNHnW6TdGf8NycP-zRGnfXS68-2743_Lht9H1TvJr3UQ3L-wv-y2TwL5-SXsDqmJqatkhogmJekwwjX4LIBwwJzzFp2zUAgK5noOpJ8iPV-XPkaJwe1D55hhG7UnzB80s7C1jyY0dhhTdxrdWcn_y7v-nYKuSpIq_T_gBWQUFx0El2C9dIML2qv3U7f4J-AJWxUTkXsbuLlLkoPKMPZhW6fwj90bOKNHhI4_p6k09BZ628--qpz_eQccq0oF_LfUQwKqkteqmuGEqqGU_G6vWsDh1jqIlf0cjHHCYBrM3hT4wkpYCMFRQS9mQCJoHoAWxAwAdyGFZwjugbH9atBtLbS3UXjEnzToQ1797U13jpQxY3bAjl5-_NxUOfzs-eLkTsne1B18n4M6kEOv6x9dQbVDP6ACpMywzlBmBZIYIEE3YJHUBHOMl5iwkkhGGeML1vwdCSgjCMcpByxkFMUAi9_ARUBQVQ?type=png){alt-text="Rebase"}

:::::::::::::::::::::::: solution

## Solution : `git merge`

The first thing to do is make sure `main` is up-to-date and has the changes that have been merged from the
`zero-division` branch locally.

``` bash
cd ~/work/git/hub/ns-rse/python-maths
git switch main
git pull
```

Then you can switch branches to the `square-root` branch and merge the main branch in.

``` bash
git switch ns-rse/square-root
git merge main
```

You should have received a message about conflicts in the `tests/test_arithmetic.py` file.

``` bash
git merge main
Auto-merging pythonmaths/arithmetic.py
Auto-merging tests/test_arithmetic.py
CONFLICT (content): Merge conflict in tests/test_arithmetic.py
Recorded preimage for 'tests/test_arithmetic.py'
Automatic merge failed; fix conflicts and then commit the result.
```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::: solution

## Solution : `git rebase`

The first thing to do is make sure `main` is up-to-date and has the changes that have been merged from the
`zero-division` branch locally.

``` bash
cd ~/work/git/hub/ns-rse/python-maths
git switch main
git pull
```

Then you can switch branches to the `square-root` branch and merge and rebase onto main.

``` bash
git switch ns-rse/square-root
git rebase main
```

You should have received a message about conflicts in the `tests/test_arithmetic.py` file.

``` bash
git rebase main
Auto-merging pythonmaths/arithmetic.py
Auto-merging tests/test_arithmetic.py
CONFLICT (content): Merge conflict in tests/test_arithmetic.py
error: could not apply bb4294a... feature: adds square root function
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
hint: Disable this message with "git config set advice.mergeConflict false"
Recorded preimage for 'tests/test_arithmetic.py'
Could not apply bb4294a... feature: adds square root function
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

### Oh no I've got a `merge conflict`

Both the `git merge` and `git rebase` strategies in the examples we went through worked because none of the changes that
were made touched the same files. In the Challenge though there was a conflict because both commits had added code to
the end of `tests/test_arithmetic.py` and Git didn't know which should be kept. This situation is known as "conflict"
and resolving it requires manual intervention.

If you have undertaken the [Git & GitHub Through GitKraken - From Zero to Hero!][zerohero] course you will have
encountered merge conflicts when working through the "_Python Calculator_" exercise and have some idea of how to resolve
them. We will however now go through resolving the issue when updating diverged branches.

#### 1. Create a new repository

``` bash
cd ~/work/git/hub/ns-rse
mkdir git-rebase-test-conflict
cd git-rebase-test-conflict
git init --initial-branch=main
git commit --allow-empty -m "Initial commit"
```

#### 2. Create `branch1` and add a `README.md`

Again we add a `README.md` but this time we make two commits to it, adding an extra line.

``` bash
git switch -c branch1
echo "# Just a test\n\n" > README.md
git add README.md
git commit -m "docs: Adding README.md"
echo "Lets add another line in a separate commit" >> README.md
git add README.md
git commit -m "docs: Ooops, missed a line from the README.md"
```

#### 3. Switch back to `main`

Again `README.md` doesn't exist on this branch yet.

``` bash
git switch main
cat README.md
```

#### 4. Create `branch2` and add a `README.md`

We now set ourselves up for a conflict by creating a `README.md` on `branch2`, knowing full well that such a file
already exists on `branch1`. We put different text into it.

``` bash
git switch -c branch2
echo "# Just a test\n\nBut we're creating a merge conflict\n" > README.md
git add README.md
git commit -m "This repo needs a README.md"
cat README.md
```

#### 5. Merge `branch1` into `main`

Merge `branch1` into `main`. The `README.md` has the text from `branch1`. As we are done with this branch we can delete
it now.

``` bash
# Switch to main and merge branch1
git switch main
git merge branch1
cat README.md
git branch -d branch1
```

#### 6. Switch to `branch2` and add another line to `README.md`

Switch back to `branch2` and add another line to `README.md`, stage and commit it. The history now shows that we have
two commits on this branch after the "Initial commit".

``` bash
# Switch to branch2 add more to `README.md` and rebase
git switch branch2
echo "Lets add another commit to make things messier" >> README.md
git add README.md
git commit -m "Bulking out README.md with more information"
git  --pretty="%h %ad (%cr) %x09 %an : %s"

* bce21bd - (HEAD -> branch2) Bulking out README.md with more information (2024-03-01 13:26:01 +0000) <Neil Shephard>
* 29b2e32 - This repo needs a README.md (2024-03-01 13:23:16 +0000) <Neil Shephard>
* 57e68aa - Initial commit (2024-03-01 13:20:14 +0000) <Neil Shephard>

```

#### 6. Rebase `branch2` onto `main`

We now want to update `branch2` by rebasing onto `main` so that we have the new changes from `main` (i.e. those merged
from `branch1`). In this instance though we _know_ both `branch1` and `branch2` have modified the file `README.md` and
so we expect to get a conflict and sure enough we do.

``` bash
git rebase main

Auto-merging README.md
CONFLICT (add/add): Merge conflict in README.md
error: could not apply fcfe2db... This repo needs a README.md
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
Recorded preimage for 'README.md'
Could not apply fcfe2db... This repo needs a README.md
```

Oh dear we have, as expected, encountered the dreaded "merge conflict" as both `branch1` and `branch2` made changes to
`README.md`. Lets take a look at what the file now looks like.

``` bash
cat README.md
<<<<<<< HEAD
# Just a test

Lets add another line in a separate commit
=======
# Just a test

But we're creating a merge conlict

>>>>>>> 29b2e32 (This repo needs a README.md)
```

Here `HEAD` refers to the branch that is being merged in (`main`) which contains the changes we made on `branch1` and
merged into `main`. The text that this refers to  is delimited by `<<<<<<<` and `=======` and is
`# Just a test` and `Lets add another line in a separate commit`. The commit (`fcfe2db`) on `branch2` which added _two_
lines (although technically its 4 since we also included blank lines) then follows and is delimited by `=======` and
`>>>>>>>` and includes the message.

We are given some useful information as to what we could do and there are three options.

1. `Resolve all conflicts manually, mark them as resolved with "git add/rm <conflicted files>", then run "git rebase
  --continue".`
2. `You can instead skip this commit: run "git rebase --skip".`
3. `To abort and get back to the state before "git rebase", run "git rebase --abort".`

These are really useful messages telling us how we can proceed. In this instance we want to take option 1, so we should
open the `README.md` and edit it to leave it in the state we want the file to be in.

#### 7. Resolve the conflict

You can use the `nano` editor to open the file with `nano README.md`. Edit it to look like this:

``` bash
# Just a test

Lets add another line in a separate commit

But we're creating a merge conflict
```

You can use `Ctrl+k` to remove a whole line at once. Save the file and return to the command prompt (in `nano` this is
`Ctrl+O` then `Ctrl+X`).

::::::::::::::::::::::::::::::::::::: callout

## Nano

[`nano`][nano] is a simple text editor found on most GNU/Linux and OSX systems that is quick and easy to use. A useful
bookmark to help whilst developing the muscle memory for the commands is the [nano shortcuts
cheatsheet](https://www.nano-editor.org/dist/latest/cheatsheet.html).

It is possible that your system may use a different editor than `nano` by default, e.g. `vim`. It does not matter which
text editor you use to edit and save the files and if you are comfortable using this then that is not a problem.

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- markdownlint-disable-next-line MD001 -->
#### 8. Add the conflicted file and continue with rebase

You can now continue with the advice and add the conflicted files back to Git and continue with the rebase.

``` bash
git add README.md
git rebase --continue

Recorded resolution for 'README.md'.
[detached HEAD d041adb] This repo needs a README.md
 1 file changed, 4 insertions(+)
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
error: could not apply 84a1592... Bulking out README.md with more information
hint: Resolve all conflicts manually, mark them as resolved with
hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
hint: You can instead skip this commit: run "git rebase --skip".
hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
error: could not parse conflict hunks in 'README.md'
Could not apply 84a1592... Bulking out README.md with more information
```

Hang on, we just resolved the merge conflict why are we being told there is another? Well the _first_ conflict with
commit `fcfe2db` was resolved and we are told as much in the line `Recorded resolution for 'README.md'`, however there
is now a conflict between that and commit `84a1592`. We get the same advice so lets take a look at the state of
`README.md`

``` bash
# Just a test

Lets add another line in a separate commit

But we're creating a merge conflict

<<<<<<< HEAD
>>>>>>> fcfe2db (This repo needs a README.md)
=======
Lets add another commit to make things messier
>>>>>>> 84a1592 (Bulking out README.md with more information)
```

Here we can see it's the second line that we added to `README.md` under `branch2` that read
`Lets add another commit to make things messier` that is causing the problem. It's not in the `main` branch on which we
are rebasing so Git doesn't know whether it should be and we have to manually resolve this. Edit the file so that it
looks like the following.

``` bash
# Just a test

Lets add another line in a separate commit

But we're creating a merge conflict

Lets add another commit to make things messier
```

#### 9. Add the conflicted file and continue with the second stage of the rebase

Then add the conflicted file and continue with the rebase.

``` bash
git add README.md
git rebase --continue
[detached HEAD 0ccfe91] Bulking out README.md with more information
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/branch2.
```

We are told that the rebase has been successful and `branch2` now contains all commits from `main` (which includes those
merged from `branch1`). If we look at the contents of `README.md` it contains all of the lines we added to both branches
as that is how we chose to resolve the conflicts manually.

``` bash
cat README.md
# Just a test


Lets add another line in a separate commit

But we're creating a merge conflict

Lets add another commit to make things messier
```

The history/graph is linear now and shows that `branch2` is two commits ahead of `main`.

``` bash
git  --pretty="%h %ad (%cr) %x09 %an : %s"
* 0ccfe91 - (HEAD -> branch2) Bulking out README.md with more information (2024-03-01 14:00:57 +0000) <Neil Shephard>
* d041adb - This repo needs a README.md (2024-03-01 13:59:31 +0000) <Neil Shephard>
* 64905e8 - (main) Ooops, missed a line from the README.md (2024-03-01 13:56:35 +0000) <Neil Shephard>
* e68485d - Adding README.md (2024-03-01 13:55:50 +0000) <Neil Shephard>
* dec5385 - Initial commit (2024-03-01 13:55:50 +0000) <Neil Shephard>
```

::::::::::::::::::::::::::::::::::::: callout

You may be wondering why when performing `git rebase` it mentions `git merge`. This is because a rebase will
sequentially merge all commits from the branch you are rebasing onto, in this case `main`, into the `HEAD` of your
current checked out branch (`branch2`).

::::::::::::::::::::::::::::::::::::::::::::::::

### Repeating yourself

You only had to resolve two merge conflicts here, if the history you are merging has a lot of commits you may end up solving
the same merge conflict repeatedly and wonder why you keep on getting asked to resolve the same conflict. There is a way
to avoid this though.

::::::::::::::::::::::::::::::::::::: callout

## Avoid divergence if possible

Bringing a diverged branch up-to-date can get _very_ messy and confusing if there is a large amount of divergence. The
best strategy to avoid this complication is two fold.

1. Break work down into small chunks and regularly merge them into `main`.
2. If this can not be avoided or lots of others are making changes you should `git merge` or `git rebase` your feature
   branch onto `main`  frequently.

::::::::::::::::::::::::::::::::::::::::::::::::

You may encounter this situation and find that you are repeatedly resolving the same conflict as you want the finer
grained control over `git rebase` and one option is to `git rebase --abort` and use `git merge` instead as you only have
to resolve the conflicts once, although there may be a lot of them. One disadvantage of this is it makes it look like
the commits stem from you and so many people prefer the rebase strategy.

Help is at hand though if you find you are repeatedly being asked to resolve the same conflict as you progress through a
rebase in the form of [rerere][rerere] which stands for "**re**use **re**corded **re**solution" and causes Git to
remember how it has resolved merge conflicts at a given point and the next time it is encountered it will use the
solution from the first instance.

You can enable this in your global configuration, which is covered in greater detail in the next episode, with the
following.

``` bash
git config --global rerere.enabled true
```

If you only wish to use this strategy on some repositories you can apply it to your local configuration from within the
working directory.

``` bash
git config --local rerere.enabled true
```

You can of course enable globally and disable locally as local configuration variables take precedence over global.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2: Diverging Branches

You didn't complete the merge/rebase in the previous challenge because of the merge conflict. In your pairs do so now.

If we look at the conflict in `tests/test_arithmetic.py` we can see at the bottom the sections that are
conflicting. They are de-marked by `<<<<<<<`, `======-` and `>>>>>>>` and the `HEAD` and `<commit-hash>` show which
branches they originate from.

The exact solution will depend on the strategy you have chosen to bring the branch up-to-date.

:::::::::::::::::::::::: solution

## Solution 1 : `git merge`

``` bash
cat tests/test/arithmetic.py
<<<<<<< HEAD


@pytest.mark.parametrize(
    ("x", "target"),
    [
        pytest.param(4, 2, id="square root of 4"),
        pytest.param(9, 3.0, id="square root of 9"),
        pytest.param(25, 5.0, id="square root of 25"),
        pytest.param(2, 1.4142135623730951, id="square root of 2"),
    ],
)
def test_square_root(x: int | float, target: int | float) -> None:
    """Test the square_root() function."""
    pytest.approx(arithmetic.square_root(x), target)
||||||| 80b95c0
=======


def test_divide_zero_division_exception() -> None:
    """Test that a ZeroDivisionError is raised by the divide() function."""
    with pytest.raises(ZeroDivisionError):
        arithmetic.divide(2, 0)
>>>>>>> main
```

We know that we want _both_ of these changes so we can edit the file and remove the delimiters to leave it looking like
the following.

Then you can switch branches to the `square-root` branch and merge the main branch in.

``` bash


@pytest.mark.parametrize(
    ("x", "target"),
    [
        pytest.param(4, 2, id="square root of 4"),
        pytest.param(9, 3.0, id="square root of 9"),
        pytest.param(25, 5.0, id="square root of 25"),
        pytest.param(2, 1.4142135623730951, id="square root of 2"),
    ],
)
def test_square_root(x: int | float, target: int | float) -> None:
    """Test the square_root() function."""
    pytest.approx(arithmetic.square_root(x), target)


def test_divide_zero_division_exception() -> None:
    """Test that a ZeroDivisionError is raised by the divide() function."""
    with pytest.raises(ZeroDivisionError):
        arithmetic.divide(2, 0)
```

If you check the status you see that you are in the middle of a merge and that `tests/test_arithmetic.py` has been
modified by `both`.

``` bash
git status
On branch ns-rse/2-square-root
Your branch is up to date with 'origin/ns-rse/2-square-root'.

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Changes to be committed:
    modified:   pythonmaths/arithmetic.py

Unmerged paths:
  (use "git add <file>..." to mark resolution)
    both modified:   tests/test_arithmetic.py
```

You now need to stage your changes and commit them.

``` bash
git add -u
git commit
git push
```

You can now create a pull request, assign it for review and merge it.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2 : `git rebase`

``` bash
cat tests/test/arithmetic.py
<<<<<<< HEAD


@pytest.mark.parametrize(
    ("x", "target"),
    [
        pytest.param(4, 2, id="square root of 4"),
        pytest.param(9, 3.0, id="square root of 9"),
        pytest.param(25, 5.0, id="square root of 25"),
        pytest.param(2, 1.4142135623730951, id="square root of 2"),
    ],
)
def test_square_root(x: int | float, target: int | float) -> None:
    """Test the square_root() function."""
    pytest.approx(arithmetic.square_root(x), target)
||||||| 80b95c0
=======


def test_divide_zero_division_exception() -> None:
    """Test that a ZeroDivisionError is raised by the divide() function."""
    with pytest.raises(ZeroDivisionError):
        arithmetic.divide(2, 0)
>>>>>>> main
```

We know that we want _both_ of these changes so we can edit the file and remove the delimiters to leave it looking like
the following.

Then you can switch branches to the `square-root` branch and merge the main branch in.

``` bash


@pytest.mark.parametrize(
    ("x", "target"),
    [
        pytest.param(4, 2, id="square root of 4"),
        pytest.param(9, 3.0, id="square root of 9"),
        pytest.param(25, 5.0, id="square root of 25"),
        pytest.param(2, 1.4142135623730951, id="square root of 2"),
    ],
)
def test_square_root(x: int | float, target: int | float) -> None:
    """Test the square_root() function."""
    pytest.approx(arithmetic.square_root(x), target)


def test_divide_zero_division_exception() -> None:
    """Test that a ZeroDivisionError is raised by the divide() function."""
    with pytest.raises(ZeroDivisionError):
        arithmetic.divide(2, 0)
```

If you check the status you see that you are in the middle of a merge and that `tests/test_arithmetic.py` has been
modified by `both`.

``` bash
git status
On branch ns-rse/2-square-root
Your branch is up to date with 'origin/ns-rse/2-square-root'.

You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Changes to be committed:
    modified:   pythonmaths/arithmetic.py

Unmerged paths:
  (use "git add <file>..." to mark resolution)
    both modified:   tests/test_arithmetic.py
```

You now need to stage your changes and commit them.

``` bash
git add -u
git rebase --continue
git push
```

You can now create a pull request, assign it for review and merge it.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Merge or Rebase

Arguments rage online between experienced users as to whether you should `git merge` or `git rebase` it can often be a
matter of preference and you should agree within your team which strategy to use and stick with it.

However it is worth noting that if you `git merge` your changes from the `main` branch into your feature branch when you
come to merge your feature branch into `main` via a Pull Request then the `git diff` will show all changes for commits that
have been merged into `main` since your feature branch was made and not just the changes you have made in your feature
branch (i.e. the commits that have already been merged into `main` also appear in your pull request). This can make
reviewing pull requests considerably harder and is a good case for using `git rebase` to keep your feature branches
up-to-date when you know they have diverged.
::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints

- Branches can become outdated as work progresses
- Branches can be brought up-to-date with either `git merge` or `git rebase`.

::::::::::::::::::::::::::::::::::::::::::::::::

## Links

- [The Case for Pull Rebase](https://megakemp.com/2019/03/20/the-case-for-pull-rebase/)
- [Dealing with diverged git branches](https://jvns.ca/blog/2024/02/01/dealing-with-diverged-git-branches/)
- [git rebase: what can go wrong?](https://jvns.ca/blog/2023/11/06/rebasing-what-can-go-wrong-/)
- [Getting solid at Git rebase
  vs. merge](https://medium.com/@porteneuve/getting-solid-at-git-rebase-vs-merge-4fa1a48c53aa)

[nano]: https://www.nano-editor.org/
[rerere]: https://git-scm.com/book/en/v2/Git-Tools-Rerere
[zerohero]: https://srse-git-github-zero2hero.netlify.app/
