---
title: "Hooks"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- What the hell are hooks?
- How can hooks improve my development workflow?
- What is `pre-commit` and how does it relate to the `pre-commit` hook?
- What `pre-commit` hooks are available?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Understand what Git hooks are.
- Know what the different types of hooks are and where they are stored.
- Understand how `pre-commit` framework is configured and runs.
- Add new hooks and repos to `pre-commit`.
- How to keep `pre-commit` tidy.

::::::::::::::::::::::::::::::::::::::::::::::::

## What are hooks?

Hooks are actions, typically one or more scripts, that are run in response to a particular event. Git has a number of
stages at which hooks can be run and events such as `commit`, `push`, `pull` all have hooks that can run `pre` (before)
or `post` (after) the action and these are _really_ useful for helping automate your workflow as they can capture
problems with linting and tests much earlier in the development cycle than for example Continuous Integration failing
after pull requests have been made.

In a Git repository hooks live in the `.git/hooks` directory and are short [Bash][bash] scripts that are executed at the
relevant stage. We can list the contents of this directory with `ls -lha .git/hooks` and you will see there are a number
of executable files with names that indicate at what stage they are run but all have the `.sample` extension which means
they are _not_ executed in response to any of the actions.

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Make sure the audience understands what the `commit`, `push` and `pull` events are and they they are actions for git to
make on the repository at different stages in the Git workflow.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

``` output
❱ cd ~/work/git/hub/ns-rse
❱ mkdir test
❱ cd test
❱ git init
❱ ls -lha .git/hooks
drwxr-xr-x neil neil 4.0 KB Fri Feb 23 10:40:42 2024 .
drwxr-xr-x neil neil 4.0 KB Fri Feb 23 10:40:46 2024 ..
.rwxr-xr-x neil neil 478 B  Fri Feb 23 10:40:42 2024 applypatch-msg.sample
.rwxr-xr-x neil neil 896 B  Fri Feb 23 10:40:42 2024 commit-msg.sample
.rwxr-xr-x neil neil 4.6 KB Fri Feb 23 10:40:42 2024 fsmonitor-watchman.sample
.rwxr-xr-x neil neil 189 B  Fri Feb 23 10:40:42 2024 post-update.sample
.rwxr-xr-x neil neil 424 B  Fri Feb 23 10:40:42 2024 pre-applypatch.sample
.rwxr-xr-x neil neil 1.6 KB Fri Feb 23 10:40:42 2024 pre-commit.sample
.rwxr-xr-x neil neil 416 B  Fri Feb 23 10:40:42 2024 pre-merge-commit.sample
.rwxr-xr-x neil neil 1.3 KB Fri Feb 23 10:40:42 2024 pre-push.sample
.rwxr-xr-x neil neil 4.8 KB Fri Feb 23 10:40:42 2024 pre-rebase.sample
.rwxr-xr-x neil neil 544 B  Fri Feb 23 10:40:42 2024 pre-receive.sample
.rwxr-xr-x neil neil 1.5 KB Fri Feb 23 10:40:42 2024 prepare-commit-msg.sample
.rwxr-xr-x neil neil 2.7 KB Fri Feb 23 10:40:42 2024 push-to-checkout.sample
.rwxr-xr-x neil neil 2.3 KB Fri Feb 23 10:40:42 2024 sendemail-validate.sample
.rwxr-xr-x neil neil 3.6 KB Fri Feb 23 10:40:42 2024 update.sample

```

If you create a repository on [GitHub][gh], [GitLab][gl] or another forge when you clone it locally these samples are
created on your system. They are _not_ part of the repository itself as files under the `.git` directory are not under
version control by default.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1: Checking out and enable sample hooks

Lets take a look at the hooks in the [`python-maths`][pm] repository you have cloned for this course.

1. What does `.git/hooks/pre-push.sample` do?
2. Enable the `.git/hooks/pre-push` using the `.git/hooks/pre-push.sample`.
3. Test the enabled hook by creating a new branch and making an empty commit that will trigger the hook (**hint** it is
   case-sensitive). Remove the branch after you are satisfied the hook works.

:::::::::::::::::::::::: solution

## Solution 1: What does `.git/hooks/pre-push.sample` do?

Git will have populated the `.git/hooks` directory automatically when you cloned the [`python-maths`][pm].

1. Change directory to the cloned `python-maths` directory.
2. Look at the file `.git/hooks/pre-push.sample`.

``` bash
❱ cd ~/path/to/cloned/repository/python-maths
❱ cat .git/hooks/pre-push.sample
#!/bin/sh

# An example hook script to verify what is about to be pushed.  Called by "git
# push" after it has checked the remote status, but before anything has been
# pushed.  If this script exits with a non-zero status nothing will be pushed.
#
# This hook is called with the following parameters:
#
# $1 -- Name of the remote to which the push is being done
# $2 -- URL to which the push is being done
#
# If pushing without using a named remote those arguments will be equal.
#
# Information about the commits which are being pushed is supplied as lines to
# the standard input in the form:
#
#   <local ref> <local oid> <remote ref> <remote oid>
#
# This sample shows how to prevent push of commits where the log message starts
# with "WIP" (work in progress).

remote="$1"
url="$2"

zero=$(git hash-object --stdin </dev/null | tr '[0-9a-f]' '0')

while read local_ref local_oid remote_ref remote_oid
do
    if test "$local_oid" = "$zero"
    then
        # Handle delete
        :
    else
        if test "$remote_oid" = "$zero"
        then
            # New branch, examine all commits
            range="$local_oid"
        else
            # Update to existing branch, examine new commits
            range="$remote_oid..$local_oid"
        fi

        # Check for WIP commit
        commit=$(git rev-list -n 1 --grep '^WIP' "$range")
        if test -n "$commit"
        then
            echo >&2 "Found WIP commit in $local_ref, not pushing"
            exit 1
        fi
    fi
done

exit 0
```

When enabled this hook will "_prevent push of commits where the log message starts with "WIP" (work in progress)_"

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2: Enable the `pre-push` hook and test it

This sounds like a good idea as it, notionally, prevents people from pushing work that is in progress...if they are in
the habit of starting commit messages with "WIP"!

1. Enable the hook.
2. Create a new branch `<github-user>/test-hook` to test the hook on.
3. Make an empty commit with a message that starts with `WIP` e.g. `git commit --allow-empty "WIP - testing the
   pre-push commit"`. Was the commit pushed?
4. Delete the branch you created.

``` bash
❱ cd python-maths
❱ cp .git/hooks/pre-push.sample .git/hooks/pre-push
```

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 3: Test the hook

We can test the hook by making a throw-away branch and adding an empty commit that starts with `WIP` and then trying to
`git push` the commit. After it fails we can force delete this test branch.

``` bash
❱ git switch -c ns-rse/test-hook
❱ git commit --allow-empty -m "WIP - testing the pre-push hook"
❱ git push
Found WIP commit in refs/heads/ns-rse/test-hook, not pushing
error: failed to push some refs to 'github.com:slackline/python-maths.git'
❱ git switch main
❱ git branch -D ns-rse/test-hook
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: callout

## Push and Pull

You may have encountered the [non-fast-forward
error](https://docs.github.com/en/get-started/using-git/dealing-with-non-fast-forward-errors) when attempting to push
your changes to a remote. As the message shows this is because there are changes to the remote branch that are not in
the local branch and you are advised to `git pull` before attempting to `git push` again.

``` bash
❱ git push origin main
> To https://github.com/USERNAME/REPOSITORY.git
>  ! [rejected]        main -> main (non-fast-forward)
> error: failed to push some refs to 'https://github.com/USERNAME/REPOSITORY.git'
> To prevent you from losing history, non-fast-forward updates were rejected
> Merge the remote changes (e.g. 'git pull') before pushing again.  See the
> 'Note about fast-forwards' section of 'git push --help' for details.
```

A simple addition you can add to the `.git/hooks/pre-push` script is to have it `git pull` before attempting to make a
`git push` which retrieves details, but does not pull them, of changes that have been made to the branch on `origin`.

``` bash
#!/bin/sh
#
# A hook script to pull before pushing

exec git pull
```

::::::::::::::::::::::::::::::::::::::::::::::::

## Pre-Commit

Pre-commit hooks that run before commits are made are _really_ useful to the extent that they require special discussion
and will be the focus of the remainder of this episode. Why are they so useful? It's because they shorten the feedback
loop of changes that need to be made when checking and linting code.

It may seem mundane and unnecessary to apply such standards to your code, particularly if it is just exploratory code
development, but over time if you employ these tools the way in which you write code will change so that it becomes
natural to write code that is formatted and linted.

Should you then decide that code is ready to be used beyond exploratory stage it will not need refactoring in order to
get it in shape. In essence this encourages adoption of good coding practices from the outset, taking
responsibility/ownership of the code you write so that it is to the highest standards it can be. In the long run t is
better to form good habits than bad ones and hooks help you do so.

There is a framework for `pre-commit` hooks called, unsurprisingly, [pre-commit][pc] that makes it incredibly
easy to add (and configure) some really useful `pre-commit` hooks to your workflow.

::::::::::::::::::::::::::::::::::::: callout

## Pre-commit

From here on whenever `pre-commit` is mentioned it refers to the Python package [pre-commit][pc] and _not_ the
hook that resides at `.git/hooks/pre-commit`, although we will look at that file.

::::::::::::::::::::::::::::::::::::::::::::::::

### Why are Pre-Commit hooks so important?

You may be wondering why running hooks prior to commits is so important. The short answer, as we've already hear,  is
that it reduces the feedback loop and speeds up the pace of development. The long answer is that it only really becomes
apparent after using them so we're going to have a go at installing and enabling some `pre-commit` hooks on our code
base, making some changes and committing them.

### Installation

[pre-commit][pc] is written in [Python][python] but hooks are available that lint, check and test many languages
other than Python. Many Linux systems have [pre-commit][pc] in their package management systems so if you are using
Linux or OSX you can install these at the system level.

However, for this course the setup instructions asked you to install [Miniconda][miniconda3] and we can install
[`pre-commit`][pc] in a Conda environment to leverage it. The steps to do so are

1. Create a Conda environment called `python-maths` with `conda create -n python-maths python=3.11`
2. Activate the newly created `python-maths` environment.
3. Install `pre-commit` in the `python-maths` repository.

``` bash
❱ conda create -n python-maths python=3.11 pre-commit
Retrieving notices: ...working... done
Collecting package metadata (current_repodata.json): done
Solving environment: done

## Package Plan ##

  environment location: /home/neil/miniconda3/envs/python-maths

  added / updated specs:
    - pre-commit
    - python=3.11


The following packages will be downloaded:

    package                    |            build
    ---------------------------|-----------------
    cffi-1.16.0                |  py311h5eee18b_1         313 KB
    distlib-0.3.8              |  py311h06a4308_0         456 KB
    openssl-3.0.13             |       h7f8727e_2         5.2 MB
    platformdirs-3.10.0        |  py311h06a4308_0          37 KB
    virtualenv-20.26.1         |  py311h06a4308_0         3.5 MB
    ------------------------------------------------------------
                                           Total:         9.5 MB

The following NEW packages will be INSTALLED:

  _libgcc_mutex      pkgs/main/linux-64::_libgcc_mutex-0.1-main
  _openmp_mutex      pkgs/main/linux-64::_openmp_mutex-5.1-1_gnu
  bzip2              pkgs/main/linux-64::bzip2-1.0.8-h5eee18b_6
  ca-certificates    pkgs/main/linux-64::ca-certificates-2024.3.11-h06a4308_0
  cffi               pkgs/main/linux-64::cffi-1.16.0-py311h5eee18b_1
  cfgv               pkgs/main/linux-64::cfgv-3.4.0-py311h06a4308_0
  distlib            pkgs/main/linux-64::distlib-0.3.8-py311h06a4308_0
  filelock           pkgs/main/linux-64::filelock-3.13.1-py311h06a4308_0
  identify           pkgs/main/linux-64::identify-2.5.5-py311h06a4308_0
  ld_impl_linux-64   pkgs/main/linux-64::ld_impl_linux-64-2.38-h1181459_1
  libffi             pkgs/main/linux-64::libffi-3.4.4-h6a678d5_1
  libgcc-ng          pkgs/main/linux-64::libgcc-ng-11.2.0-h1234567_1
  libgomp            pkgs/main/linux-64::libgomp-11.2.0-h1234567_1
  libstdcxx-ng       pkgs/main/linux-64::libstdcxx-ng-11.2.0-h1234567_1
  libuuid            pkgs/main/linux-64::libuuid-1.41.5-h5eee18b_0
  ncurses            pkgs/main/linux-64::ncurses-6.4-h6a678d5_0
  nodeenv            pkgs/main/linux-64::nodeenv-1.7.0-py311h06a4308_0
  openssl            pkgs/main/linux-64::openssl-3.0.13-h7f8727e_2
  pip                pkgs/main/linux-64::pip-24.0-py311h06a4308_0
  platformdirs       pkgs/main/linux-64::platformdirs-3.10.0-py311h06a4308_0
  pre-commit         pkgs/main/linux-64::pre-commit-3.4.0-py311h06a4308_1
  pycparser          pkgs/main/noarch::pycparser-2.21-pyhd3eb1b0_0
  python             pkgs/main/linux-64::python-3.11.9-h955ad1f_0
  pyyaml             pkgs/main/linux-64::pyyaml-6.0.1-py311h5eee18b_0
  readline           pkgs/main/linux-64::readline-8.2-h5eee18b_0
  setuptools         pkgs/main/linux-64::setuptools-69.5.1-py311h06a4308_0
  sqlite             pkgs/main/linux-64::sqlite-3.45.3-h5eee18b_0
  tk                 pkgs/main/linux-64::tk-8.6.14-h39e8969_0
  tzdata             pkgs/main/noarch::tzdata-2024a-h04d1e81_0
  ukkonen            pkgs/main/linux-64::ukkonen-1.0.1-py311hdb19cb5_0
  virtualenv         pkgs/main/linux-64::virtualenv-20.26.1-py311h06a4308_0
  wheel              pkgs/main/linux-64::wheel-0.43.0-py311h06a4308_0
  xz                 pkgs/main/linux-64::xz-5.4.6-h5eee18b_1
  yaml               pkgs/main/linux-64::yaml-0.2.5-h7b6447c_0
  zlib               pkgs/main/linux-64::zlib-1.2.13-h5eee18b_1


Proceed ([y]/n)?

...

❱ conda activate python-maths
(python-maths) ❱ pre-commit install
pre-commit installed at .git/hooks/pre-commit
```

::::::::::::::::::::::::::::::::::::: callout

## Install Pre-commit globally

Examples of installing [pre-commit][pc] at the system level for different Linux systems or OSX. Note you will
need to have `root` access to install packages on your Linux system.

``` bash
# Arch Linux
❱ pacman -Syu pre-commit
# Gentoo
❱ emerge -av pre-commit
# Debin/Ubuntu
❱ apt-get install pre-commit
# OSX Homebrew
❱ brew install pre-commit
```

The advantage of this is that you will be able to `pre-commit install` in any repository without first having to
activate a virtual environment.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2 - Checking out the installed `pre-commit` hook

We have just installed `pre-commit` locally in the `python-maths` repository lets see what it has done.

- What will the message say if `pre-commit` can not be found by the `pre-commit` hook? (**Hint** - remember where hooks
  are installed and look for the line that starts with `echo`)

:::::::::::::::::::::::: solution

## Solution

We can look at the `.git/hooks/pre-commit` file that we were told was installed.

``` bash
❱ cat .git/hooks/pre-commit
#!/usr/bin/env bash
# File generated by pre-commit: https://pre-commit.com
# ID: 138fd403232d2ddd5efb44317e38bf03

# start templated
INSTALL_PYTHON=/home/neil/miniconda3/envs/python-maths/bin/python
ARGS=(hook-impl --config=.pre-commit-config.yaml --hook-type=pre-commit)
# end templated

HERE="$(cd "$(dirname "$0")" && pwd)"
ARGS+=(--hook-dir "$HERE" -- "$@")

if [ -x "$INSTALL_PYTHON" ]; then
    exec "$INSTALL_PYTHON" -mpre_commit "${ARGS[@]}"
elif command -v pre-commit > /dev/null; then
    exec pre-commit "${ARGS[@]}"
else
    echo '`pre-commit` not found.  Did you forget to activate your virtualenv?' 1>&2
    exit 1
fi
```

We see that near the end a message is `echo` that prints what follows to the terminal so if we get to that point the
sentence  "_`pre-commit` not found. Did you forget to activate your virtualenv?_" will be printed.
:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Configuring `pre-commit`

`pre-commit` needs configuring and this is done via the `.pre-commit-config.yaml` file that lives at the root
(top-level) of your repository. The `python-maths` repository already includes such a file so you will have a copy in
your local clone.

``` bash
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0 # Use the ref you want to point at
    hooks:
      - id: check-case-conflict
      - id: check-docstring-first
      - id: check-merge-conflict
      - id: check-toml
      - id: check-yaml
      - id: debug-statements
      - id: end-of-file-fixer
        types: [python]
      - id: fix-byte-order-marker
      - id: name-tests-test
        args: ["--pytest-test-first"]
      - id: no-commit-to-branch # Protects main/master by default
      - id: requirements-txt-fixer
      - id: trailing-whitespace
        types: [python, yaml, markdown]

  - repo: https://github.com/DavidAnson/markdownlint-cli2
    rev: v0.11.0
    hooks:
      - id: markdownlint-cli2
        args: []

  - repo: https://github.com/asottile/pyupgrade
    rev: v3.15.0
    hooks:
      - id: pyupgrade
        args: [--py38-plus]

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.8.0
    hooks:
      - id: mypy

  - repo: https://github.com/astral-sh/ruff-pre-commit
    # Ruff version.
    rev: v0.4.2
    hooks:
      - id: ruff
        args: [--fix, --exit-non-zero-on-fix, --show-fixes]

  - repo: https://github.com/psf/black-pre-commit-mirror
    rev: 23.12.1
    hooks:
      - id: black
        types: [python]
        additional_dependencies: ["click==8.0.4"]
        args: ["--extend-exclude", "topostats/plotting.py"]
      - id: black-jupyter

  - repo: https://github.com/adamchainz/blacken-docs
    rev: 1.16.0
    hooks:
      - id: blacken-docs
        additional_dependencies:
          - black==22.12.0

  - repo: https://github.com/codespell-project/codespell
    rev: v2.2.6
    hooks:
      - id: codespell

  - repo: https://github.com/pre-commit/mirrors-prettier
    rev: v4.0.0-alpha.8
    hooks:
      - id: prettier

  - repo: https://github.com/numpy/numpydoc
    rev: v1.6.0
    hooks:
      - id: numpydoc-validation
        exclude: |
          (?x)(
              tests/|
              docs/
          )

  - repo: local
    hooks:
      - id: pylint
        args: ["--rcfile=.pylintrc"]
        name: Pylint
        entry: python -m pylint
        language: system
        files: \.py$

```

This [YAML][yaml] file might look quite complex and intimidating if you are not familiar with the format so we'll go
through it in sections.

### `repos:`

The top-level section `repos:` defines a list of the repositories that are included and each of these is a specific
`pre-commit` hook that will be used and run when commits are made. In YAML list entries start with a dash (`-`).

### `- repo: https://github.com/<USER_OR_ORG>/<REPOSITORY>`

Each `repo` is then defined, the first line states where the repository is hosted and these are typically, although not
always on [GitHub][gh]. The first one is for `pre-commit-hooks` that come from the developers of `pre-commit`
itself. Other configured repositories are

- `markdownlint-cli2`
- `pyupgrade`
- `mypy`
- `ruff`
- `black`
- `black-jupyter`
- `blacken-docs`
- `codespell`
- `prettier`
- `local` - which runs `pylint` locally.

### `rev:`

The next line indicates the revision of the hook repository that you wish to use. These are typically `git tags` that
have been applied to releases of the hook. In this example the revision is `4.5.0` for the `pre-commit-hooks`.

### `hooks:`

There then follows another entry called `hooks:` which defines a list of `- id:` and each of these is the name of a
particular hook that will be run. There are hooks enabled for the following and they are fairly explanatory but the
[hooks][pc-hooks] page often has a one-line explanation of what the hooks enable.

- `check-case-conflict`
- `check-docstring-first`
- `check-merge-conflict`
- `check-toml`
- `check-yaml`
- `debug-statements`
- `end-of-file-fixer`
- `fix-byte-order-marker`
- `name-tests-test`
- `no-commit-to-branch`
- `requirements-txt-fixer`
- `trailing-whitespace`

Some of the hooks have additional arguments (`args:`) which are arguments that are passed to that particular hook or
types (`types`) which restrict the type of files the hook should run on.

::::::::::::::::::::::::::::::::::::: callout

## Comments in YAML files

You can add comments to YAML file by pre-fixing them with a `#`. These may be at the start of a line or can be added to
the end of a line and the text that follows will be treated as a comment and ignored when parsing the file.

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Check that attendees are familiar with `grep` and searching files for strings. If people are unfamiliar explain clearly
what each solution is doing in terms of the string being searched for, the target file (`.pre-commit-config.yaml`) the
before (`-B`) and after (`-A`) flags and how the pipe (`|`) command is used to chain expressions together.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Understanding `.pre-commit-config.yaml`

Now that we've gone through the structure of how a `pre-commit` repository is defined and configured lets look at some
of the others that are defined.

- What version of the `numpydoc` repo is configured?
- What hook(s) is/are enabled from the `black-pre-commit-mirror` repo?
- What arguments are listed for the `ruff` hook?

:::::::::::::::::::::::: solution

## Solution 1 : What version of the `numpydoc` repo is configured

Using [grep][grep] to search for the `numpydoc` string in the `.pre-commit-config.yaml` we can hone in on the `repo` and
its associated `rev`.

``` bash
❱ grep -A1 numpydoc .pre-commit-config.yaml  | grep -B1 rev
  - repo: https://github.com/numpy/numpydoc
    rev: v1.6.0
```

We see that it is `v1.6.0` that is currently configured for `numpydoc`.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 2 : What hook(s) is/are enabled from the `black-pre-commit-mirror` repo?

Searching for the `black-pre-commit-mirror` in the configuration and then looking for the `id` shows us what hooks are
configured for this `repi`.

``` bash
❱ grep -A10 "black-pre-commit-mirror" .pre-commit-config.yaml | grep "id:"
      - id: black
      - id: black-jupyter
```

The `black` and `black-jupyter` hooks are enabled. These will apply [black][black] formatting to Python files and
Jupyter Notebooks.

:::::::::::::::::::::::::::::::::

:::::::::::::::::::::::: solution

## Solution 3 : What arguments are listed for the `ruff` hook?

Finally searching for `ruff` in `.pre-commit-config.yaml` and then looking for the `args` field we can find out what
arguments are passed to the [ruff][ruff] linter.

``` bash
❱ grep -A5 ruff .pre-commit-config.yaml | grep "args:"
        args: [--fix, --exit-non-zero-on-fix, --show-fixes]
```

The `--fix`, `--exit-non-zero-on-fix` and `--show-fixes` options are enabled.

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Installing `pre-commit` hooks

The `.git/hooks/pre-commit` is a Bash script that runs `pre-commit` but where do the hooks come from? There is a hint in
the configuration file where each `- repo:` is defined which points to a Git repository which contains the code and
environment to run the hook.

These need downloading and initialising before they will run on your local system and that is achieved using `pre-commit
install-hooks`. We will now install the hooks.

```bash
❱ pre-commit install-hooks
```

The repos that are defined need installing, this is done once and sets up some virtual environments which are reused
across Git repositories that have `pre-commit` installed. If the `ref:` is changed or updated then it will require
downloading a new environment.

## Running `pre-commit`

Whilst configured as a hook to run before commits `pre-commit` you can run all hooks or a specific hook at any time
against the whole repository

``` bash
❱ pre-commit run --all-files                # Run all hooks on all files
❱ pre-commit run <hook-to-run> --all-files  # Run a specific hook on all files
```

...or on individual files, in this case `pyproject.toml` and `README.md`

``` bash
❱ pre-commit run --files pyproject.toml README.md
```

If there are problems identified with any of the files `pre-commit` will report them and you will have to fix them and
include the changes, staging before committing them (remember not to commit to the wrong branch such as `main`).

## Adding Hooks

Which hooks you use will depend largely on the language you are using but there are hundreds of hooks available and
these can be browsed at the [website][pc-hooks]. The `python-maths` repository has a number of
[pre-commit-hooks][pc-hooks-repo] enabled but lets add some more.

Looking at the [pre-commit-hooks repo][pc-hooks-repo] we can see there are a few hooks that we could enable. We will
create a new branch to make these changes on and add the `detect-private-keys`, and prevent files larger than 800kb from
being added using the `check-added-large-files` hook.

``` bash
❱ cd python-maths
❱ git switch main
❱ git pull
❱ git switch -c ns-rse/add-pre-commit-hooks
```

Add the following `- id:` to the `hooks:` section defined under the first `- repo:`.

``` yaml
      - id: check-added-large-files
        args: ["--maxkb=800"]
      - id: detect-private-keys
```

It can help with readability if you order the hooks alphabetically so you may have something that reads like the following.

``` yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.5.0 # Use the ref you want to point at
    hooks:
      - id: check-added-large-files
        args: ["--maxkb=800"]
      - id: check-case-conflict
      - id: check-docstring-first
      - id: check-merge-conflict
      - id: check-toml
      - id: check-yaml
      - id: debug-statements
      - id: detect-private-keys
      - id: end-of-file-fixer
        types: [python]
      - id: fix-byte-order-marker
      - id: name-tests-test
        args: ["--pytest-test-first"]
      - id: no-commit-to-branch # Protects main/master by default
      - id: requirements-txt-fixer
      - id: trailing-whitespace
        types: [python, yaml, markdown]
```

After you have made changes to `.pre-commit-config.yaml` you _have_ to stage them for committing, if you don't the
`pre-commit` programme will complain about it being unstaged.

``` bash
❱ cd python-maths
❱ git commit --allow-empty -m "Trying to commit without staging .pre-commit-config.yaml"
[ERROR] Your pre-commit configuration is unstaged.
`git add .pre-commit-config.yaml` to fix this.
```

Whenever you modify, add or delete content to `.pre-commit-configy.yaml` you must therefore stage and commit the
changes (**NB** make sure youre are)

``` bash
❱ git add .pre-commit-config.yaml
❱ git commit -m "pre-commit : Exclude large files and detect private keys"
❱ git push
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 6: Add the `forbid-new-submodules` hook id to the `pre-commit-hooks` configuration

:::::::::::::::::::::::: solution

## Solution

The following line should be added under the `hooks:` section of the `- repo:
https://github.com/pre-commit/pre-commit-hooks` repository configuration.

``` yaml
      - id: forbid-new-submodules
```

The file should then be staged, committed and pushed.

``` bash
❱ git add .pre-commit-config.yaml
❱ git commit -m "pre-commit : adds the forbid-new-modules hook"
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Adding repos

The definitive [list][pc-hooks] of `pre-commit` repos is maintained on the official website. Each entry links to the
GitHub repository and most contain in their `README.md` instructions on how to use the hooks.

<!-- ::::::::::::::::::::::::::::::::::::: challenge -->

<!-- ## Challenge 7: Add the `numpydoc` repo, exclude the `tests/` and `doc/` directories and run it against-->
<!-- the code base -->

<!-- The [numpydoc](https://github.com/numpy/numpydoc) repo defines hooks that check the Python docstrings -->
<!-- conform to the [Numpydoc style guide](https://numpydoc.readthedocs.io/en/latest/format.html). Following the  -->
<!-- instructions add the repo to the `.pre-commit-config.yaml` (on a new branch) -->

<!-- :::::::::::::::::::::::: solution -->

<!-- ## Solution -->

<!-- Create a branch to undertake the work on. -->

<!-- ``` bash -->
<!-- ❱ git switch main -->
<!-- ❱ git pull -->
<!-- ❱ git switch -c ns-rse/pre-commit-numpydoc -->
<!-- ``` -->

<!-- The following should be added to your `.pre-commit-config.yaml` -->

<!-- ``` yaml -->
<!--    - repo: https://github.com/numpy/numpydoc -->
<!--      rev: v1.6.0 -->
<!--      hooks: -->
<!--        - id: numpydoc-validation -->
<!--          exclude: | -->
<!--            (?x)( -->
<!--                tests/| -->
<!--                docs/ -->
<!--            ) -->
<!-- ``` -->

<!-- Check that the code base passes the checks, correct any errors that are highlighted. -->

<!-- ``` bash -->
<!-- ❱ pre-commit run numpydoc --all-files -->
<!-- ``` -->

<!-- The file should then be staged, committed and pushed. -->

<!-- ``` bash -->
<!-- ❱ git add .pre-commit-config.yaml -->
<!-- ❱ git commit -m "pre-commit : adds the numpydoc repo/hook" -->
<!-- ❱ git push -->
<!-- ``` -->

<!-- ::::::::::::::::::::::::::::::::: -->

<!-- :::::::::::::::::::::::::::::::::::::::::::::::: -->

## Local repos

Local repo are those that do not use hooks defined by others and are instead defined by the user. This comes in handy
when you want to run checks which have dependencies that are specific to the code such as running [pylint][pylint] which
needs to import all the dependencies that are used or run a test suite.

The `python-maths` module already has a section defined that runs `pylint` locally. When running on a repository it will
therefore be essential that you have a virtual/conda environment activated that has all the dependencies installed.

``` yaml
   - repo: local
     hooks:
       - id: pylint
         args: ["--rcfile=.pylintrc"]
         name: Pylint
         entry: python -m pylint
         language: system
         files: \.py$
```

Several of the configuration options we have already seen such as `id`, `args` and `files` but the `name:` field gives
the hook a name and the `entry:` defines what is actually run, in this case `python -m pylint` which will take the
define argument `--rcfile=.pylintrc`, and so what actually gets executed is

``` bash
python -m pylint --rcfile=.pylintrc
```

::::::::::::::::::::::::::::::::::::: callout

## Pylint configuration

The `.pylintrc` file is a configuration file for `pylint` that defines what checks are made.

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 9: Define local `pre-commit` repo to run a `pytest` hook

The `python-maths` repository has a suite of tests that can be run to ensure the code works as expected.

Pytest is run simply with `pytest`.

:::::::::::::::::::::::: solution

## Solution

Create a branch to undertake the work on.

``` bash
❱ git switch main
❱ git pull
❱ git switch -c ns-rse/pre-commit-pytest
```

The following should be added to your `.pre-commit-config.yaml`

``` yaml
   - repo: local
     hooks:
       - id: pytest
         name: Pytest
         entry: pytest
         language: system
```

Check that the code base passes the checks, correct any errors that are highlighted.

``` bash
❱ pre-commit run pytest --all-files
```

The file should then be staged, committed and pushed.

``` bash
❱ git add .pre-commit-config.yaml
❱ git commit -m "pre-commit : adds a local pytest repo/hook"
❱ git push
```

:::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::

## Keeping `pre-commit` tidy

`pre-commit` downloads and installs lots of code on your behalf, including virtual environments that are activated to
run the tests. It stores these in the `~/.cache/pre-commit/` directory and you will find a few common files (`.lock`,
`db.db` and `README`) along with a bunch of directories with hashed names. These directories are the code and
environments used to run the different hooks.

Over time and across multiple projects the size of this cache directory can grow so its good practice to periodically
tidy up and there are two commands for doing so, which you should run periodically.

### Cleaning and Garbage Collection

The `pre-commit clean` command will clean out files that have been left around periodically, these tend not to be too
large so are less of a problem.

Cached virtual environments can grow to be quite large though, but they can be easily tidied up using the `pre-commit
gc` command (`gc` stands for Garbage Collection.

## Going further

Despite the name `pre-commit` actually supports hooks at many different stages
[stages](https://pre-commit.com/index.html#confining-hooks-to-run-at-certain-stages). Whether these run will depend on
where they are defined to run in the `.pre-commit-hooks.yaml` of the repo you are using, but they can also be
over-ridden locally by setting the `stages`.

There are also [top-level](https://pre-commit.com/index.html#pre-commit-configyaml---top-level) configuration options
where you can set a global file include (`files: "<pattern>"`) and exclude (`exclude: "<pattern>"`) pattern which would
apply across all configured repositories.

## `ci:`

There is one section of the configuration which we haven't covered yet, the `ci:` section defined at the bottom. This
controls how `pre-commit` runs and is used in Continuous Integration which is the topic of our next chapter.

We've seen how hooks and in particular the [pre-commit][pc] suite can be used to automate many tasks such as running
linting checks on your code base prior to commits. A short coming of this approach is that whilst the configuration file
(`.pre-commit-config.yaml`) may live in your repository it means that every person contributing to the code has to
install the hooks and ensure they run locally.

Not everyone who contributes to your code will do this that is where [pre-commit.ci][pc-ci] comes in handy as it runs
the Pre-commit hooks as part of the Continuous Integration on GitHub which is the focus of the next episode.

::::::::::::::::::::::::::::::::::::: keypoints

- Hooks are actions run by Git before or after particular events such as `commit`, `push` and `pull` via scripts.
- They are defined in Bash scripts in the `.git/hooks` directory.
- The `pre-commit` framework provides a wealth of hooks that can be enabled to run, by default, before commits are made.
- Each hook can be configured to run on specific files, or to take additional arguments.
- Local hooks can be configured to run when dependencies that will only be found on your system/virtual environment are
  required.
- Use hooks liberally as you develop your code locally, they save you time.

::::::::::::::::::::::::::::::::::::::::::::::::

[bash]: https://www.gnu.org/software/bash/
[black]: https://black.readthedocs.io/en/stable/index.html
[gh]: https://github.com
[gl]: https://gitlab.com
[grep]: https://en.wikipedia.org/wiki/Grep
[miniconda3]: https://docs.anaconda.com/free/miniconda/
[pc]: https://pre-commit.com
[pc-ci]: https://pre-commit.ci
[pc-hooks]: https://pre-commit.com/hooks
[pc-hooks-repo]: https://github.com/pre-commit/pre-commit-hooks
[pylint]: https://pylint.org
[python]: https://python.org
[pm]: https://github.com/ns-rse/python-maths
[ruff]: https://astral.sh/ruff
[yaml]: https://yaml.org
