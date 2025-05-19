---
title: "Continuous Integration"
teaching: 10
exercises: 2
---

:::::::::::::::::::::::::::::::::::::: questions

- How can I get a computer to automate tasks?
- How do I shorten the feedback loop when developing code?
- How can I use GitHub Actions/GitLab CI?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Use and configure Pre-commit.ci
- Use Continuous Integration to have computers run checks and tests automatically.
- Building Websites with Actions
- Running actions locally

::::::::::::::::::::::::::::::::::::::::::::::::

## Continuous What?

We've seen how to run hooks locally and automatically in relation to events in the Git cycle, but it is also possible,
and indeed desirable, to run hooks automatically on GitHub in response to different events. This is known as _Continuous
Integration_ or _Continuous Delivery_ depending on the actions taken and their effects.

Examples of actions that might be undertaken in this manner include...

- Running the test suite for a package.
- Building and deploying a website.
- Building the software package and deploying it to the package repository (e.g. PyPI or CRAN).
- Uploading archives of work to a Figshare repository such as [ORDA][orda]
- Running pre-commit checks online.

The list of options is vast and there are whole ecosystems for the different Forge's as we shall discover.

::::::::::::::::::::::::::::::::::::: callout

## CI on other Forges

This course focuses on [GitHub][gh] but there are similar systems available for other Forges. All use [YAML][yaml]
configuration files to configure the system and very similar syntax.

## GitLab

[GitLab][gl] has an equivalent system named [CI/CD][gl-cicd].

## ForgeJo

[Forgejo][fg] have their own [action][fg-actions], a system very similar to GitHub.

::::::::::::::::::::::::::::::::::::::::::::::::

## GitHub Actions

GitHub makes available a series of Virtual Machines in the cloud to undertake the tasks that you wish to perform via
[GitHub Actions][gh-actions]. These define the events which will run, the conditions under which they will run and can
call other actions that have been developed and shared by others on their repositories or even in the [Actions
Marketplace][gh-actions-market].

### Configuration

Configuration of actions that run in GitHub is via [YAML][yaml] files that reside in `.github/workflows/`.

::::::::::::::::::::::::::::::::::::: callout

## workflow and actions

Quite why the directory isn't `.github/actions/` is a mystery as it would align better.

The terms terms "workflow" and "action" may be used interactively when teaching the material but every effort has been
made in the written material to use the term `action` when unless specifically referring to the directory.

::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: instructor

Make sure to ask the class who is familiar with YAML so you can gauge the level of experience in the room. If no one has
come across it before take things a little slower and explain how the structure works.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Lets take a look at the structure of the existing actions define in the `.github/workflow` directory of the
[python-maths][python-maths] repository we have been working with.

``` bash
ls -lha .github/workflows/
drwxr-xr-x neil neil 4.0 KB Sat Feb 17 07:28:30 2024  .
drwxr-xr-x neil neil 4.0 KB Sat Feb 17 07:28:30 2024  ..
.rw-r--r-- neil neil 639 B  Sat Feb 17 07:28:30 2024    test-python-package.yaml
```

There is just one file in this directory `test-python-package.yaml`, we can use `cat
.github/workflows/test-python-package.yaml` to concatenate the file and see its contents.

``` yaml
name: Python package

on:
  push:
    branches: main
  pull_request:
    branches: main

jobs:
  tests:
    name: Tests (${{ matrix.os }}, ${{ matrix.python-version }})
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: ["ubuntu-latest", "macos-latest", "windows-latest"]
        python-version: [3.10, 3.11, 3.12]

    steps:
      - uses: actions/checkout@v4
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          python -m pip install .[dev,tests]
      - name: Test with pytest
        run: |
          pytest

```

::::::::::::::::::::::::::::::::::::: callout

## YAML

[YAML][yaml] (which stands for _YAML Ain't Markup Language_) is a common format for defining hierarchical data
structures. It is a super-set of JSON (JavaScript Object Notation) that many find more flexible (in part because of the
ability to have comments) and is regularly used for configuration files.

::::::::::::::::::::::::::::::::::::::::::::::::

<!-- markdownlint-disable-next-line MD001 -->
#### Fields

The syntax defined in the Workflow

- `name` : defines the name of the Action.
- `on` : defines the events the Action will be run on, here you can see that it will be run on both `push` events and
  `pull_request` which occur on the `main` branch.
- `jobs` : this defines the jobs that are undertaken and is a bit more complex.
  - `tests` : this is the name of the job that is subsequently defined, here it is `tests` because the section defines
    running the tests.
    - `name` : The name for the test, this is a combination of the subsequent `matrix.os` and `matrix.python-version`
    - `runs-on` : defines the operating system/virtual machine that will be used to run the job, here it is set to
      `ubuntu-latest` and will use the most recent Ubuntu image available.
    - `strategy` : defines how the job will run, it has two sub-settings.
      - `fail-fast` : Currently set to `false`, but if `true` then any step failing cancels all other jobs in the
        defined matrix.
      - `matrix` : This is a neat way of defining more than one operating system, and in this case Python version on
        which to run the tests under. These combine to increase the number of virtual machines that are spun up and the
        tests are run under.
        - `os` : defines the operating system on which to run the tests, there are many available, including older
          versions of each.
        - `python-version` : defines which Python versions to run the tests under.
    - `steps` : Defines the different steps that will be run on each virtual machine.
      - `uses` : This first instance uses the [`actions/checkout@v4`](https://github.com/actions/checkout) which is an
        action provided by GitHub that checks out the repository the workflow belongs to. You will want to include this
        as the first step in almost all of your actions.
      - `name` : A description of the next step, in this case _Set up Python_
      - `uses` : Runs the [`actions/setup-python@v5`](https://github.com/actions/setup-python) which will install
        Python, which version is defined under the `with` that follows.
      - `with` : Defines what version of the following items to use.
        - `python-version` : Uses one of the Python versions defined above under `matrix.python-version`
      - `name` : The next step is to _Install dependencies_
      - `run` : This step defines shell commands that are run, by virtue of the vertical bar (`|`), each command you
        wish to run should be on its own line. These next two lines upgrade `pip` the programme that installs Python
        packages and then installs the cloned package from the current directory, along with all dependencies, including
        those required for `dev` and `tests`.
      - `name` : The last step is to _Test with pytest_ and runs the tests.
      - `run` : Another short shell command that runs the tests by invoking `pytest` which will have been installed as
        one of the dependencies in the previous step.

#### Actions...in Action

Earlier in the course you will have made Pull Requests and merged changes into the `main` branch. These will have
triggered actions and we can now go and look at the log-files from running those actions.

In the GitHub repository of `python-maths` that you are collaborating on navigate to the _Actions_ tab and you should
see a list of actions listed.

### MarketPlace

There are a lot of actions available that can be run in the `steps` section of your custom action. The [GitHub
Marketplace][gh-actions-market] provides a central place to search for solutions so you don't have to reinvent the
wheel.

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 1: Add the Python Coverage GitHub Action to `python-maths`

In your pairs add the [Python Coverage][pycov] GitHub Action to the `python-maths` repository.

Work together on the solution. Create GitHub issues and assign them and undertake the work on a new branch and make the
following changes...

1. Enable `pytest` to create a coverage report to a file by adding the option `--cov-report coverage.xml` to the
   `pytest` call.
2. Run `coverage` on that file with `coverage xml coverage.xml` on the next line after within the `run: |` section.
3. Add the YAML section for `- name: Get Cover` _after_ the section that runs `pytest`.

:::::::::::::::::::::::: solution

## Adding Python Coverage

After creating an issue and assigning it you can create a new branch with the following.

``` bash
git switch main
git pull
git switch -c ns-rse/4-python-coverage
```

The configuration you need to add changes the call to `pytest` to summarise coverage and output to a file and then calls
the `coverage` action using that file.

``` yaml
      - name: Test with pytest
        run: |
          pytest --cov-report coverage.xml
          coverage xml coverage.xml
      - name: Get Cover
          uses: orgoro/coverage@v3.1
          with:
            coverageFile: coverage.xml
            token: ${{ secrets.GITHUB_TOKEN }}

```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::

## Pre-commit.ci

We saw in the _Hooks_ episode how to use [pre-commit][precommit] hooks to run certain tasks prior to making
commits to your feature branch. [pre-commit.ci][precommit-ci] extends this and uses the same configured hooks to
automatically check that code submitted in Pull Requests passes these same checks.

This can be useful to capture instances where `pre-commit` may have been disabled locally or if you receive
contributions from outside of the core development team and contributor has not enabled `pre-commit` in their local
workflow as it will run the formatting and linting tests, correct where possible and make commits directly to the branch
in the Pull Request and inform if there were errors that could not be automatically corrected.

### Setup

To get setup with [pre-commit.ci][precommit-ci] navigate to the page and use the button to _Sign In With GitHub_. Once
you have logged in select your profile and click on the _Manage repos on GitHub_ link. You may be asked to complete your
two-factor-authentication (2FA) at this point, but you should be taken to your accounts settings page (you can always
navigate there using _Settings > Applications_). By default pre-commit.ci requires

- **Read** access to issues, merge queues and metadata.
- **Read** and **write** access to code, commit statuses, pull requests and workflows.

There are then two potions for _Repository access_ you can either grant access to all repositories that you own, or you
can select specific repositories. It is generally preferable to only allow access to specific repositories. The dialog
that appears allows you to search for a repository that you wish to grant access to.

### Configuration

You can configure the behaviour of [pre-commit.ci][precommit-ci] via the `.pre-commit-config.yaml`. The full
specification is detailed in the [documentation][precommit-ci-docs] and is shown below.

``` yaml
ci:
    autofix_commit_msg: |
        [pre-commit.ci] auto fixes from pre-commit.com hooks

        for more information, see https://pre-commit.ci
    autofix_prs: true
    autoupdate_branch: ''
    autoupdate_commit_msg: '[pre-commit.ci] pre-commit autoupdate'
    autoupdate_schedule: weekly
    skip: []
    submodules: false
```

::::::::::::::::::::::::::::::::::::: challenge

## Challenge 2: Add pre-commit.ci to your `python-maths` repository

In your pairs add an appropriate configuration section the `.pre-commit-config.yaml` on a new branch on the
`python-maths` repository push the changes to GitHub and make a Pull Request. You should...

- Set the `autoupdate_schedule` to `monthly`.
- Customise both `autofix_commit_msg` and `autoupdate_commit_msg` fields.
- Configure the `pylint` and `pytest` hooks to be skipped in pre-commit.ci.

You are free to use the [pre-commit.ci][precommit-ci] documentation to help guide you.

:::::::::::::::::::::::: solution

## `.pre-commit-config.yaml`

``` yaml
ci:
    autofix_commit_msg: |
        [pre-commit.ci] Linting code with pre-commit hooks.
    autofix_prs: true
    autoupdate_branch: ''
    autoupdate_commit_msg: '[pre-commit.ci] Automatically updating pre-commit'
    autoupdate_schedule: monthly
    skip: [pylint, pytest]
    submodules: false
```

:::::::::::::::::::::::::::::::::
:::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints

- Continuous Integration/Delivery is a useful method of checking code _before_ it enters the `main` branch.
- GitHub uses Actions that are defined by YAML configuration files under `.github/workflow/`.
- Actions can be restricted to events/branches/tags.

- [pre-commit.ci][precommit-ci] allows integration of [pre-commit][precommit] hooks in GitHub Actions.

::::::::::::::::::::::::::::::::::::::::::::::::

[fg]: https://forgejo.org
[fg-actions]: https://forgejo.org/docs/next/user/actions/
[gh]: https://github.com
[gh-actions]: https://docs.github.com/en/actions
[gh-actions-market]: https://github.com/marketplace?type=actions
[gl]: https://gitlab.com
[gl-cicd]: https://docs.gitlab.com/ee/ci/
[orda]: https://orda.shef.ac.uk/
[precommit]: https://pre-commit.com/
[precommit-ci]: https://pre-commit.ci/
[precommit-ci-docs]: https://pre-commit.ci/#configuration
[pycov]: https://github.com/marketplace/actions/python-coverage
[python-maths]: https://github.com/ns-rse/python-maths
[yaml]: https://yaml.org
