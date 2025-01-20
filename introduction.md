---
title: "Introduction"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- Who else is doing this course?
- What can you expect from this course?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Find out something interesting about other participants.
- Understand the way in which you are expected to behave and interact with other participants.
- Have an overview of the content and material that will be covered.
- Pair up with another participant to collaborate with during this workshop.

::::::::::::::::::::::::::::::::::::::::::::::::

[Git][git] is, in 2024, the most widely used version control system by far. It was developed by Linus Torvalds to manage
[Linux kernel][linux] development and since then has exploded. Websites such as [GitHub][gh] and [GitLab][gl]
make asynchronous collaboration on common code bases possible and underpin many, many software projects from enterprise
grade tools such as the aforementioned [Linux kernel][linuxGithub], the increasingly popular [Rust][rustGitHub] through
to niche products such as [Snapcast][snapcast] or Android apps for tracking your exercise such as
[OpenTracks][openTracks].

Git and Forges, online repositories for working with Git, such as [GitHub][gh], [GitLab][gl]
[SourceHut][sourcehut], [Codeberg][codeberg], and [ForgeJo][forgejo] and so forth are wonderful tools for
collaboration. However, because of the complexities of version controlling software in distributed, collaborative
environments the tool itself, Git, has become quite complex. There are many different tasks that one may wish to
undertake and often several different ways of achieving these.

Its relatively easy to get the _basics_ of working with [Git][git] on your own or with small groups to work
collaboratively on code development. If you aren't already familiar with these basics then this course isn't for you
(yet!) and you would benefit from an introductory course such as [Git, GitHub through GitKraken : From Zero to
Hero!][zeroHero] or the [Software Carpentry : Version Control with Git][swCarpentryGit]. This course aims to show you
some of the more involved ways to use Git in a collaborative environment.

Most of the ways in which collaboration can be eased is through a better understanding of how Git works and by
maintaining clean and focused commits which make the task of reviewing work easier for those you are collaborating with.

## Code of Conduct

To make clear what is expected, everyone participating in The Carpentries activities is required to abide by our
[Code of Conduct][coc]. Any form of behaviour to exclude, intimidate, or cause discomfort is a violation of the Code of
Conduct. In order to foster a positive and professional learning environment we encourage you to:

- Use welcoming and inclusive language
- Be respectful of different viewpoints and experiences
- Gracefully accept constructive criticism
- Focus on what is best for the community
- Show courtesy and respect towards other community members

If you believe someone is violating the Code of Conduct, we ask that you report it to The Carpentries Code of Conduct
Committee by completing [this form](https://goo.gl/forms/KoUfO53Za3apOuOK2).

## Icebreaker

::: challenge

## Collaboration

Since this course is all about collaboration we would like you now to pair up with another participant in order to
undertake the exercises contained in this course. This could be the person sitting next to you if this is an in-person
course or if the course is online one of the instructors will pair you up at random.

Once paired up please add details to the Etherpad along with your GitHub usernames.

::::::::::::::::::::::::::::::::::::: callout

The aim of pairing up is _not_ to divide the tasks between people. There are a few exceptions but for most tasks you
should work with your partner to solve each of the challenges, but with one person at the "driving seat" making the
changes to the code as required.

You should discuss what you think the solution should be as you work through the challenge.

This is software development technique known as [Pair Programming][pairprogramming] and by discussing the solutions you
will hopefully come away with a better understanding of the material.

::::::::::::::::::::::::::::::::::::::::::::::::

## Getting to Know Each Other

In order to break the ice and find out something about the other participants on this course, please think about a
situation BVC (**B**efore **V**ersion **C**ontrol) where you might have had a problem that Version Control would have
prevented. This might be deleting files by mistake or making changes to code that broke your programme and not being
unable to undo them.

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## In-Person

If the course is being run in person please describe the situation to the person or people sat next to you. Write your
answer in the collaborative pad under a heading with your name.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Online

If you are participating online please write down your names of pairs and provide an answer in the collaborative notepad.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Before the start of the course you should setup a new [collaborative pad][carpentryPad] where participants can answer
questions and collaborate.

If running the course online you should have a list of participants and have paired them off at random.

When explaining the challenge remember to let participants know that they can use these pages to work through the steps,
this is particularly important for those who are not overly familiar with Python.

Once people have completed the task ask for volunteers to describe their experiences BVC.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

If anyone has multiple GitHub accounts it is possible that permission may be denied which force pushing if the wrong SSH
key is used. It is simple to work around this by adding the following to the `.git/config` of the user and ensuring it
points to the correct private SSH key that is associated with the account they wish to use.

``` bash
[core]
    ...
    sshCommand = ssh -i ~/.ssh/id_ed25519 -F /dev/null
```

The important part is that it points to the correct SSH key, in the above this is `~/.ssh/id_ed25519` which will
need modifying to reflect the users key for the account they wish to use.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

## Cloning Repositories

::::::::::::::::::::::::::::::::::::: challenge

## Choose Roles, Clone Repository and

Introduce yourself to the person you have paired up with. You now need to decide who is to take on each of the two
roles. There isn't much between them in terms of what you will be doing but one person needs to be the **repository
owner** and one person needs to be a **collaborator**.

### Repository Owner

The Repository Owner should visit the [Python Maths][pythonMaths] repository on GitHub. To avoid the default base branch
being this repository we do _not_ use templates. Instead the Repository owner should follow these steps to get a copy of
the repository under their account.

1. Use the `Code` button of the [Python Maths][pythonMaths] to clone the repository locally (`git clone
   git@github.com:ns-rse/python-maths.git`).
2. Fetch additional branches with `git fetch origin {divide,multiply,ns-rse/merge-conflict}`.
3. On GitHub create an empty repository called `python-maths` using the [new repo][gh_newrepo], do _not_ add a license
   or `.gitignore` to the repository, it should be completely empty.
4. In the locally cloned `python-maths` directory open the `.git/config` file and edit the line 7 that reads
   `url = git@github.com:ns-rse/python-maths.git` and replace `ns-rse` with _your_ GitHub user name. E.g. if your GitHub
   username is `alice_and_bob` it should read `url = git@github.com:alice_and_bob/python-maths.git`. Save these changes.
5. Force push with `git push --force`.

This edit changes the `origin` to be the empty repository you created under _your_ account called `python-maths` and
pushes the cloned repository there.

Once you have completed this you need to invite your collaborator to work on the repository with you. Navigate to
_Settings > People_ and add invite you collaborator to the project.

### Collaborator

You should accept the invitation you have received to work on the Template the Repository Owner just sent you and clone
their version of the `python-maths` repository.

## Install `python-maths` under the Virtual Environment

Both individuals should now have local copies of the repository. After activating the `git-collaboration` Virtual
Environment you created during setup should install the package in editable mode within the environment along with the
`test` dependencies. If you are not familiar with working with Python follow the instructions in the Solutions below.

**NB** -  Once cloned you may have to explicitly fetch the `multiply` and `divide` branches, instructions are in the
solution.

:::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Clone the repository

Both the repository owner and collaborator should now clone the repository _from the repository owners copy **not** the
original template_.

Click on the _Code_ button and then the _SSH_ tab. Copy the URL. If you want to clone the work to `~/work/git/` then in
a terminal

``` bash
cd ~/work/git
git clone git@github.com:<owners_id>/python-maths
cd python-maths
```

### Repository Owners

Just the repository owner should now edit the `.git/config` and modify line 7 where the `url` of the origin is defined
replace `ns-rse` with their GitHub username. For example if the repository owner uses the `alice_and_bob` username on
GitHub it should read.

``` bash
 [remote "origin"]
     url = git@github.com:alice_and_bob/python-maths.git
     fetch = +refs/heads/*:refs/remotes/origin/*
```

Alternatively you can do this at the command line with...

``` bash
git remote set-url origin git@github.com:alice_and_bob/python-maths.git
```

The Repository Owner should create a new, empty, but public repository on GitHub called `python-maths`, there is no need
to include a license nor `.gitignore` file.

The Repository Owner can push the cloned repository to their account with, the `--force` is optional and shouldn't be
required unless you have inadvertently initialised the repository with additional files.

``` bash
git push --force
```

### Collaborator

Once the Repository Owner has cloned and pushed a copy of the repository to their account the Collaborator can clone
that. If the Repository Owner has username `alice_and_bob` then you can clone with the following command.

``` bash
git clone git@github.com:alice_and_bob/python-maths.git
```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::: solution

## Protect the Main Branch

On the `python-maths` repository you both now have access to protect the `main` branch to require approvals.

1. _Settings > Branches > Add branch protection rule_
2. Enter `main` under _Branch name pattern_
3. Check the box _Require a pull request before merging_
4. Prevent the repository owner from bypassing the rules by checking _Do not allow bypassing the above settings_.
5. Save the changes using the button at the bottom of the page.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Install the Package

If you have not already done so activate the `git-collaboration` environment you created as described in the setup
instructions.

``` bash
conda activate git-collaboration
```

You can now install the package and its test dependencies in an editable format so that as you work on the package the
changes you make will instantly be available. Make sure you are in the `python-maths` directory (use `pwd` to show where
you are and `cd` to change directory).

``` bash
pip install -e .[tests,dev]
```

You can optionally check everything is installed and runs by running the tests via [pytest][pytest]

``` bash
pytest
========================================== test session starts ==========================================
platform linux -- Python 3.11.8, pytest-8.1.1, pluggy-1.4.0
Matplotlib: 3.8.4
Freetype: 2.6.1
rootdir: /home/neil/work/teaching/git_collaboration/2024-04-19/python-maths
configfile: pyproject.toml
testpaths: tests
plugins: regtest-2.1.1, pylint-0.21.0, github-actions-annotate-failures-0.2.0, xdist-3.5.0, cov-5.0.0, anyio-4.3.0, mock-3.14.0, mpl-0.17.0
collected 26 items

tests/test_arithmetic.py ......................                                                   [ 84%]
tests/test_trig.py ....                                                                           [100%]

----------------------------------------- pytest-regtest report -----------------------------------------
total number of failed regression tests: 0

---------- coverage: platform linux, python 3.11.8-final-0 -----------
Name                        Stmts   Miss  Cover
-----------------------------------------------
pythonmaths/arithmetic.py       8      0   100%
pythonmaths/trig.py             4      0   100%
-----------------------------------------------
TOTAL                          12      0   100%

========================================== 26 passed in 0.28s ===========================================
```

:::::::::::::::::::::::::::::::::

After completing these steps you should both have a copy of the `python-maths` repository on your local computer.

::::::::::::::::::::::::::::::::::::: callout

If desired you can between you update the Metadata in `pyproject.toml` it is important to have accurate Metadata in this
file because if you ever publish your package to [Python Package Index (PyPI)][pypi] it will be used.

To update the metadata create a branch and update lines 12 and 13 with your names and email addresses. Push the changes,
create a pull request and merge the changes.

::::::::::::::::::::::::::::::::::::::::::::::::

[carpentryPad]: https://pad.carpentries.org/
[coc]: https://docs.carpentries.org/topic_folders/policies/code-of-conduct.html
[codeberg]: https://codeberg.org/
[forgejo]: https://forgejo.org/
[git]: https://git-scm.com
[gh]: https://github.com
[gh_newrepo]: https://github.com/new
[gl]: https://gitlab.com
[linux]: https://www.kernel.org
[linuxGithub]: https://github.com/torvalds/linux
[openTracks]: https://github.com/OpenTracksApp/OpenTracks
[pairprogramming]: https://en.wikipedia.org/wiki/Pair_programming
[pypi]: https://pypi.org/
[pythonMaths]: https://github.com/ns-rse/python-maths
[pytest]: https://docs.pytest.org/
[rustGithub]: https://github.com/rust-lang/rust
[snapcast]: https://mjaggard.github.io/snapcast/
[sourcehut]: https://sourcehut.org/
[swCarpentryGit]: https://swcarpentry.github.io/git-novice/
[zeroHero]: https://srse-git-github-zero2hero.netlify.app
