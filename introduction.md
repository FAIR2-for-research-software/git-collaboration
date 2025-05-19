---
title: "Introduction"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- Who else is doing this course?
- What can you expect from this course?
- How do I use the command line?
- How can I edit files at the command line?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Find out something interesting about other participants.
- Understand the way in which you are expected to behave and interact with other participants.
- Have an overview of the content and material that will be covered.
- Basic command line navigation.
- Basic use of `nano` editor.
- Pair up with another participant to collaborate with during this workshop.

::::::::::::::::::::::::::::::::::::::::::::::::

[Git][git] is, in 2025, the most widely used version control system by far. It was developed by Linus Torvalds to manage
[Linux kernel][linux] development and since then has exploded. Websites such as [GitHub][gh] and [GitLab][gl], both
types of Forge[^1], facilitate asynchronous collaboration on common code bases and underpin many, many software projects
from enterprise grade tools such as the aforementioned [Linux kernel][linuxGithub], the increasingly popular
[Rust][rustGitHub] through to niche products such as [Snapcast][snapcast] or Android apps for tracking your exercise
such as [OpenTracks][openTracks].

Git and Forges are wonderful tools for
collaboration. However, because of the complexities of version controlling software in distributed, collaborative
environments the tool itself, Git, has become quite complex. There are many different tasks that one may wish to
undertake and often several different ways of achieving these.

It's relatively easy to get the _basics_ of working with [Git][git] on your own or with small groups to work
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

Once paired up please add details to the [Collaborative Notepad][collab_notepad] along with your GitHub usernames.

::::::::::::::::::::::::::::::::::::: callout

## Pair Programming

The aim of pairing up is _not_ to divide the tasks between people. There are a few exceptions but for most tasks you
should work with your partner to solve each of the challenges, but with one person at the "driving seat" making the
changes to the code as required.

You should discuss what you think the solution should be as you work through the challenge.

This is a software development technique known as [Pair Programming][pairprogramming] and by discussing the solutions
you will hopefully come away with a better understanding of the material.

::::::::::::::::::::::::::::::::::::::::::::::::

## Getting to Know Each Other

In order to break the ice and find out something about the other participants on this course, please think about a
situation BVC (**B**efore **V**ersion **C**ontrol) where you might have had a problem that Version Control would have
prevented. This might be deleting files by mistake or making changes to code that broke your programme and not being
unable to undo them. Alternatively you could describe a mistake you've made AVC (**A**fter  **V**ersion **C**ontrol)
where you perhaps committed to the wrong branch or deleted something.

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

Before the start of the course you should setup a new [collaborative pad][collab_notepad] where participants can answer
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

## Bash and Nano Basics

You will be using the [Bash][bash] shell to navigate and type commands and the [nano][nano] editor to edit files,
although you are free to edit your files in any programme you want such as VSCode, RStudio or Emacs.

### Bash

There are a few useful tips and tricks you can use to help you work at the command line more quickly.

#### Navigation

You can find out what directory you are in using `pwd` (`p`resent `w`orking `d`irectory).

```bash
pwd
/home/neil/work/git
```

You can use `cd` to (`c`hange `d`irectory).

**NB** The tilde (`~`) is short cut for your "home" directory, typically `/home/<username>` on GNU/Linux systems,
`/Users/<username>` on OSX and `/c/Users/<username>` on Window systems.

``` bash
cd ~/work
```

You can create directories with `mkdir` (`m`a`k`e `dir`ectory), the `-p` flag ensures the parent directories exist.

``` bash
mkdir ~/work/git
```

#### Tab Completion

You can start typing a command or directory path in Bash and use the `<Tab>` button to auto-complete the word you are
typing. If you have typed sufficient characters to give a unique command or path Bash will complete it for you. If there
are several options you will be presented with a list of the options, one of which will be highlighted. Hitting `<Tab>`
again will move onto the next possible completion. When you are at the completion you want simply hit `<Return>` and it
will be entered on your command line.

```bash
cd ~/te <Tab>
tests/  tmp/
```

#### Redirection

This course uses the redirection pipes `>` to "pipe" what is on the left of the symbol into the file give on the
right. A single `>` will over-write any contents in the file if it already exists. If you want to append something to a
file you can use `>>` instead.

#### History

Bash keeps a record of the commands you use in its `history`. You can view _all_ your history by simply typing
`history`.

```bash
history
...
cd work
mkdir git
git clone git@github.com:ns-rse/python-maths
```

You can recall previous commands using the `Ctrl + p` or the Up arrow, each press takes you to the `p`revious command in
the history. You can move forwards to the `n`ext command using `Ctrl + n` or the Down arrow. When you get to the command
you want simply hit `Return` to execute it.

This is really useful but what if there is a command you know you've used at some point in the past but it wasn't
recently? It could take a long time going back through the history one command at a time. Fortunately you can search
your history for a command using `Ctrl + r` to carry out a "reverse search" on the term you enter. Each subsequent press
of `Ctrl + r` will go the previous match.

::::::::::::::::::::::::::::::::::::: callout

## Readline Library

There are a lot of useful key chords that have evolved over the years which make navigating on the command line easier
and faster. They have been collected into the [Readline library][readline].

These are available not just in Bash but many other command line interfaces (CLI) and learning them will make you
quicker when working at the command line. A useful [cheatsheet][readlinecheat] is available.

::::::::::::::::::::::::::::::::::::::::::::::::

### Nano

[Nano][nano] is a basic terminal editor, you open files by calling `nano <path/to/filename>`, there is a useful [nano
cheatsheet][nano_cheat].

#### Navigating

You can jump to a specific line number with `Ctrl + /` and will be prompted for the line you want to go to, on hitting
`Return` the cursor will move there.

#### Finding text

If you want to search for some text you can do so with `Ctrl + F` and you are prompted to enter some text, on hitting
`Return` the cursor moves to the next instance of that text (if it is present!). If you hit `Ctrl + F` again you can
either enter new text to search for or you can hit `Return` to search for the previous text again.

#### Saving Files

When you are done editing use `Ctrl + O` to save the file you are editing, you are prompted for a file to save it to,
typically you don't need to change this, just hit `Enter`.

#### Exiting `nano`

When done editing you need to exit and return to the command line, you do this with `Ctrl + x`.

::::::::::::::::::::::::::::::::::::: callout

## Useful alias

You may want to set the following alias in your `~/.bashrc` file, it sets various options. You can then `source
~/.bashrc` to

```bash
echo "alias nano='nano --autoindent --linenumbers --tabstospaces --tabsize=4'" >> ~/.bashrc

```

These options will be used whenever you use `nano`. See more options with `nano --help`

::::::::::::::::::::::::::::::::::::::::::::::::

## Git Configuration

Git configuration comes in two forms, "global" and "local" and is courtesy of some simple text files. The global
configuration file lives in your home directory and on GNU/Linux and OSX systems is `~/.gitconfig` (on Windows it is
`C:\Users\<username>\.gitconfig`) and will have been setup when you first attempted to use Git and were prompted for
your name and email address.

Each repository that is under Git version control has a `.git/` directory where all of the configuration, hooks and
history live. Within this directory you will find a `.git/config` file which is the "local" configuration for that
repository. Configuration options defined locally over-ride global configuration options.

There are two ways of modifying either the global or local configuration, using the Command Line `git config <options>`
or by editing either the global (`~/.gitconfig`) or local (`git/config`) files.

### `git config`

The `git config` command has a host of options that you can view with the `--help` flag. The first required option says
what file should be modified and is typically either `global` or `local`. You can view the configuration with `git
config --list` and you can optionally restrict it to show either the `--global` or `--local` configuration.

``` bash
git config --list
git config --list --local
git config --list --global
```

Adding values requires a bit of understanding about the structure of the configuration file, a very simple example is
shown below.

``` bash
[user]
 email = a.n.other@sheffield.ac.uk
 name = A N Other
[core]
 editor = nano
 sshCommand = ssh -i ~/.ssh/id_ed25519 -F /dev/null
 attributesFile = $HOME/.gitattributes
 autocrlf = input
 excludesfile = ~/dotfiles/git/.gitignore
```

Sections are in square brackets with names, e.g. `[user]` or `[core]`. Fields then have key and value pairs e.g. the
`name` value is set to `A N Other` the `email` address is `a.n.other@sheffield.ac.uk` and the `editor` is set to `nano`
and so forth.

To modify values at the command line you need to know the section and the key you want to change, these are combined to
give the third argument `user.email` and you then provide the value you want it to be as the fourth argument. For
example to change the email address in the global configuration you would.

``` bash
git config --global user.email a.other@sheffield.ac.uk
git config --global user.name "Neil Shephard"
```

::::::::::::::::::::::::::::::::::::: callout

## Where are my configuration files?

You can always lookup the location of configuration options using the following command which shows the file in which each
configuration is set as the first column of output.

``` bash
git config --list  --show-scope --show-origin

```

::::::::::::::::::::::::::::::::::::::::::::::::

### Editing config files

You can also edit both the local (`.git/config`) and global (`~/.gitconfig`) files directly to set configuration options
and this can at times be much quicker.

For example if we wanted to configure Git so that the order in which branches are listed is by the most recent commit we
could add the following to our `~/.gitconfig` using `nano`, which will result in branches being listed in reverse
chronological order when you `git branch --list`.

``` bash
[branch]
    sort = -commiterdate
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1

Set globally the default editor to be `nano` (**Hint** this is nested under the `core` options).

:::::::::::::::::::::::: solution

## Solution 1 - Command Line

``` bash
git config --global core.editor nano
```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::: solution

## Solution 2 - Editing `~/.gitconfig`

You could alternatively edit the `~/.gitconfig` file directly and add the following lines

``` bash
[core]
    editor = nano
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

### Alias'

A very useful configuration option available is the ability to set [aliases][gitaliases] for Git. This means you can
create short cuts to complex commands. Aliases live under the `[alias]` section of the global (`.gitconfig`) or local
(`.git/config`) configuration files. They can be set at the command line with `git config --[global|local]
alias.<shortcut> <command>` .

If you wanted to save a few key strokes and set `sw` as an alias for `switch` globally you would.

``` bash
git config --global alias.sw switch
```

Or if you want to unstage files that are currently staged you can set an `unstage` alias using the following where the
command you wish to add is put in quotes so the shell doesn't think they are arguments to the command and treats them as
a string.

As with other configuration options you can also edit the configuration files directly to add the commands.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2 - Set a &nbsp; `git log` &nbsp; alias

`git log` shows the history of commits on the current branch, but its default is quite verbose. Fortunately there are a
_lot_ of options to modify the output adding colour, shortening dates and including a graph and we've been using a
version a fair bit already. You can see all the options in the manual ([`git log --help`][gitlog])

Rather than having to remember all the options and type them out each time you can set an alias that stores the options
you want and can be called instead.

For this exercise add the following set of log options to a global alias of your choice (the solution uses `logp` but
you are free to set it to whatever you want, e.g. `lp`)

``` bash
log --graph --pretty=format:"%C(yellow)%h\\ %C(green)%ad%Cred%d\\ %Creset%s%Cblue\\ [%cn]" --decorate --date=short
```

**NB** - Because there are double-quotes in this command you will if using the command line solution have to enclose all
of the above in single quotes.

:::::::::::::::::::::::: solution

## Solution 1 - Edit &nbsp; `~/.gitconfig`

You can set the alias `logp` to the above `git log` options by editing `~/.gitconfig` and adding the following

``` bash
[alias]
    logp = log --graph --pretty=format:"%C(yellow)%h\\ %C(green)%ad%Cred%d\\ %Creset%s%Cblue\\ [%cn]" --decorate --date=short
```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::: solution

## Solution 2 - Use &nbsp; `git config`

You could also set this alias at the command line

``` bash
git config --global alias.logp 'log --graph --pretty=format:"%C(yellow)%h\\ %C(green)%ad%Cred%d\\ %Creset%s%Cblue\\ [%cn]" --decorate --date=short'
```

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

### `.gitignore`

The [`.gitignore`][gitignore] file does exactly what you might expect it to, it contains lists of directories and files
that should be ignored by Git. To save having to write out the path to each and every file the format accepts
[patterns][gitignorepatterns]. This file, like many others uses `#` as a comment, to use a `#` in a file name you
therefore need to escape it with the `\` slash. A `*` matches anything but slashes and leading/trailing `**` match
all directories (leading) or everything within a directory (trailing). For more details refer to [Git Ignore
Patterns][gitignorepatterns].

A common set of files you may want to ignore is the `.DS_Store` directory that Mac OSX automatically generates in
most directories. Just as you can exclude files you can list directories so add that to the `.gitignore` in the
`python-maths` repository now. Navigate to the directory and open the file using [nano][nano] and add the following
line.

``` bash
.DS_Store
```

It is often sensible to ensure data files are not included in your repository. What these files might be depends on how
you are working, common formats are `.csv` for text files `.RData` for files from [R][r] and `.pkl` are the Python
pickles.

GitHub has a useful feature when you create a repository to include template `.gitignore` files for specific languages,
but if you missed out this step you can always use the [.gitignore generator][ignoregenerator] to generate files to be
ignored and copy and paste these in.

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Remember to switch to GitHub and go through the process of creating a new repository to show where the option to select
a template can be found.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

The `.gitignore` file is part of the repository and is itself version controlled, this means that its rules are applied
consistently across anyone who works on the project or a fork of it (since forks may end up making contributions
up-stream). You therefore have to remember to stage and commit changes to the file just as you would other files in the
repository.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 3

In your pairs exclude files with the extension, `.RData`, `.csv` and `.pkl` and the `.DS_Store` director from being
added to the `python-maths` project by adding the appropriate patterns to the `.gitignore` file on a new branch and
merge it into the `main` branch via a pull-request, assigning it to the other person for review.

**NB** Only one person needs to make the changes, but talk through how to solve the problem together.

:::::::::::::::::::::::: solution

## Solution

### Create a new branch

First create a new branch.

``` bash
git switch main
git pull
git switch -c ns-rse/ignore-csv-pkl
```

### Update the `.gitignore`

The following lines to `.gitignore` will ignore all files with the extensions `.csv` and `.pkl`. The wildcard symbol `*`
is required to ensure _any_ file, no matter what comes before the extension is ignored.

```output
.DS_Store
*.csv
*.pkl
*.RData
```

Stage and commit the changes to `.gitignore` and push to GitHub.

``` bash
git add .gitignore
git commit -m "chore: Ignoring .csv and .pkl files"
git push
```

Pull requests are created on GitHub.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Cloning Repositories

::::::::::::::::::::::::::::::::::::: challenge

## Choose Roles, Clone Repository and Install Package

In your pairs you now need to decide who is to take on each of the two roles. There isn't much between them in terms of
what you will be doing but one person needs to be the **repository owner** and one person needs to be a
**collaborator**.

Follow the instructions below under each section. If you have any questions please do not hesitate to ask.

:::::::::::::::::::::::: solution

## Clone the repository

Click on the _Code_ button at [Python Maths][pythonmaths] and then the _SSH_ tab. Copy the URL. If you want to clone the
work to `~/work/git/` then in a terminal run the following commands (be wary of copy and pasting them `<owners_id>`
needs replacing)

``` bash
cd ~/work/git
git clone git@github.com:<owners_id>/python-maths
cd python-maths
git fetch origin {divide,multiply,ns-rse/merge-conflict}
```

### Edit `.git/config`

Both users should now edit the `.git/config` file and modify line 7 where the `url` of the `origin` is defined and
replace `ns-rse` with the GitHub username of the person who will be the Repository Owner. For example if the repository
owner uses the `alice_and_bob` username on GitHub it should read.

``` bash
 [remote "origin"]
     url = git@github.com:alice_and_bob/python-maths.git
     fetch = +refs/heads/*:refs/remotes/origin/*
```

This edit changes the `origin` to be the empty repository you created under the _Repository Owner's_ account called
`python-maths` and pushes the cloned repository there.

### Force Push

**NB** This step is _just_ for the **Repository Owner**, the **Collaborator** should **not** perform this step.

The repository owner should now create an empty repository called `python-maths` using the [new repo][gh_newrepo], do
_not_ add a license or `.gitignore` to the repository, it should be **completely empty**.

The Repository Owner can push the cloned repository to their account with the following command, the `--force` is
optional and shouldn't be required unless you have inadvertently initialised the repository with additional files.

``` bash
git push --force
```

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Protect the Main Branch

**NB** This step is _just_ for the **Repository Owner**, the **Collaborator** should **not** perform this step.

On the `python-maths` repository you both now have access to, protect the `main` branch to require approvals.

1. _Settings > Branches > Add classic branch protection rule_
2. Enter `main` under _Branch name pattern_
3. Check the boxes _Require a pull request before merging_ and _Require approvals_
4. Prevent the repository owner from bypassing the rules by checking _Do not allow bypassing the above settings_.
5. Save the changes using the button at the bottom of the page.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Invite your Collaborator

**NB** This step is _just_ for the **Repository Owner**, the **Collaborator** should **not** perform this step.

The Repository owner should now invite their collaborator to work on the repository.

1. Navigate to _Settings > Collaborators_
2. Click on _Add People_ and enter the GitHub username of your collaborator.

The **Collaborator** should receive an email invitation to collaborate and should accept it.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Install the Package

**NB** Both the **Repository Owner**, the **Collaborator** should perform this step.

If you have not already done so activate the `git-collaboration` environment you created as described in the setup
instructions.

``` bash
conda activate git-collaboration
```

You can now install the package and its test dependencies in an editable format so that as you work on the package the
changes you make will instantly be available. Make sure you are in the `python-maths` directory (use `pwd` to show where
you are and `cd` to change directory).

``` bash
pwd
cd ~/work/git/python-maths
pip install -e .[tests,dev]
```

You can optionally check everything is installed and runs by running the tests via [pytest][pytest].

``` bash
pytest
========================================== test session starts ==========================================
platform linux -- Python 3.13.1, pytest-8.3.4, pluggy-1.5.0
Matplotlib: 3.10.0
Freetype: 2.6.1
rootdir: /home/neil/tmp/gitcollab-20250210/python-maths
configfile: pyproject.toml
testpaths: tests
plugins: cov-6.0.0, github-actions-annotate-failures-0.3.0, mpl-0.17.0
collected 25 items

tests/test_arithmetic.py .....................                                                                                                           [ 84%]
tests/test_trig.py ....                                                                                                                                  [100%]

---------- coverage: platform linux, python 3.13.1-final-0 -----------
Name                        Stmts   Miss  Cover
-----------------------------------------------
pythonmaths/arithmetic.py       8      0   100%
pythonmaths/trig.py             4      0   100%
-----------------------------------------------
TOTAL                          12      0   100%


========================================== 25 passed in 0.28s ===========================================
```

:::::::::::::::::::::::::::::::::

After completing these steps you should both have a copy of the `python-maths` repository on your local computer.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Update Metadata

If desired you can between you update the Metadata in `pyproject.toml`. It is important to have accurate Metadata in this
file because if you ever publish your package to [Python Package Index (PyPI)][pypi] it will be used.

To update the metadata create a branch and update lines 12 and 13 with your names and email addresses. Push the changes,
create a pull request and merge the changes.

::::::::::::::::::::::::::::::::::::::::::::::::

[^1]: The term "forge" refers to a web-based collaborative software platform.  [GitHub][gh] and [GitLab][gl] are perhaps
the most well known but there are many others including [BitBucket][bitbucket], [Codeberg][codeberg], and
[ForgeJo][forgejo] and [SourceHut][sourcehut].

[bash]: https://www.gnu.org/software/bash/
[bitbucket]: https://bitbucket.org/
[coc]: https://rse.shef.ac.uk/community/code_of_conduct
[codeberg]: https://codeberg.org/
[collab_notepad]: https://docs.google.com/document/d/1deRatN-J7RDLaEW2_rE1a01pH2INL2KermibFY9vqYk/edit?tab=t.0
[forgejo]: https://forgejo.org/
[git]: https://git-scm.com
[gitaliases]: https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases
[gitignore]: https://git-scm.com/docs/gitignore
[gitignorepatterns]: https://git-scm.com/docs/gitignore#_pattern_format
[gh]: https://github.com
[gh_newrepo]: https://github.com/new
[gl]: https://gitlab.com
[ignoregenerator]: https://www.toptal.com/developers/gitignore
[linux]: https://www.kernel.org
[linuxGithub]: https://github.com/torvalds/linux
[nano]: https://www.nano-editor.org/
[nano_cheat]: https://www.nano-editor.org/dist/latest/cheatsheet.html
[openTracks]: https://github.com/OpenTracksApp/OpenTracks
[pairprogramming]: https://en.wikipedia.org/wiki/Pair_programming
[pypi]: https://pypi.org/
[pythonMaths]: https://github.com/ns-rse/python-maths
[pytest]: https://docs.pytest.org/
[rustGithub]: https://github.com/rust-lang/rust
[r]: https://www.r-project.org/
[readline]: https://tiswww.cwru.edu/php/chet/readline/rltop.html
[readlinecheat]: https://readline.kablamo.org/emacs.html
[snapcast]: https://mjaggard.github.io/snapcast/
[sourcehut]: https://sourcehut.org/
[swCarpentryGit]: https://swcarpentry.github.io/git-novice/
[zeroHero]: https://srse-git-github-zero2hero.netlify.app
