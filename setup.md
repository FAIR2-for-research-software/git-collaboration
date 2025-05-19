---
title: Setup
---

## Git

There are many Git clients (porcelains) out there and many Integrated Development Environments (IDEs) support Git
actions and facilitate using the bewildering array of options, but this course teaches the Command Line Interface (CLI)
to Git as it means we have a consistent interface to teach the _principles_ that you can take away to your own choice of
Git client. Below there are instructions for installing Git on each of the three most common operating systems.

Please complete these setup tasks **before** attending the course. If you have any issues getting setup please either
contact an instructor in advance or arrive early and seek assistance from an instructor.

<!-- ## Example Repository -->

<!-- This course uses a GitHub Template that you will, in small groups, create a copy of and collaborate on. Using this -->
<!-- template and setting it up is part of the course, you do not need to set it up in advance. -->

::::::::::::::::::::::::::::::::::::::: discussion

## Install Git

As this is a course about using [Git][git] you will need to have it installed on your computer. If you're already using
Git then the chances are high you already have it installed or it may be integrated into your Integrated Development
Environment (IDE).

For consistency across operating systems this course uses the Command Line Interface (CLI) to Git and instructions below
will guide you through installation on different Operating Systems. The principles can be applied to any Git Porcelain
(client) that you choose, including IDEs, although not all will support all of the functions introduced here (e.g. the
RStudio Git interface is very basic).

:::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::: solution

### Windows

You can use the [official binary][gitWin] but we encourage you to use [Git for Windows][git4windows] which includes the
Bash shell and there are excellent
[instructions](https://carpentries.github.io/workshop-template/install_instructions/#shell) on how to install this on
the Carpentries installation instructions for the Bash Shell (**NB** the Git instructions for Windows on this page
direct the reader to the Bash Shell instructions as they are bundled together).

:::::::::::::::::::::::::

:::::::::::::::: solution

### MacOS

[Git][git] is not included in the MacOS distribution by default. The [official instructions][gitMac] suggests using
either [Homebrew](https://brew.sh/), [MacPorts](https://www.macports.org/) or
[Xcode](https://developer.apple.com/xcode/).

#### Homebrew

``` bash
brew install git
```

#### MacPorts

``` bash
sudo port install git
```

:::::::::::::::::::::::::

:::::::::::::::: solution

### Linux

Most GNU/Linux distributions will have Git installed by default. If not you can install it using the package manager for
your distribution. This will vary between distributions but some common ones are shown below. They assume you have
`root` (administrator) access to your system via `sudo`. If `sudo` is not configured but you have `root` access then
`su` and remove the `sudo` prefix from the following commands.

#### Arch

``` bash
sudo pacman -Syu git
```

#### Debian/Ubuntu

``` bash
sudo apt-get install git
```

#### Fedora

``` bash
sudo dnf install git
```

#### Gentoo

``` bash
sudo emerge -av dev-vcs/git
```

:::::::::::::::::::::::::

## GitHub

You will also need an account on [GitHub][gh]. If you do not already have one please
[register](https://github.com/signup), if you have an academic email address such as `@<institute>.ac.uk` or
`@<institute.edu>` then registering with this address will give you access to a few more features.

## SSH Keys

::::::::::::::::::::::::::::::::::::::: discussion

## ESSENTIAL

You **MUST** generate an SSH key _using a secure password_, then add the public component to your [GitHub
account][github_ssh].

:::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::: solution

### Generate SSH Keys

There is a detailed [article][ssh-keygen] on creating SSH keys under Linux and OSX that works with Git Bash too. It is
recommended to use the newer [ed25519][ssh-ed25519] algorithm. In a terminal or Git Bash shell, you can do this using
the following commands.

```bash
ssh-keygen -a 100 -t ed25519
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/neil/.ssh/id_ed25519):
Enter passphrase for "id_ed25519" (empty for no passphrase):
Enter same passphrase again:
```

As shown above, you will need to confirm the location that the SSH keys will be saved to (hit enter to select the
default which will be `~/.ssh/id_ed25519`) and _then_ enter a secure passphrase (you will be asked to enter it twice to
confirm you know what it is, do not forget it!).

This creates two files in the `~/.ssh/` directory by default, the private key (`~/.ssh/id_ed25519'`) and the public key
(`~/.ssh/id_ed25519.pub`). These are text files and it is the contents of the later that you need to add to GitHub (see
next solution). You can view the contents of the `~/.ssh/id_ed25519.pub` file that you need to copy to your GitHub
account with.

```bash
cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIARcKip5cMGhQvOooF0sAFTvLAj8R7wz+7cPV9BndPtL neil@haldane
```

:::::::::::::::::::::::::

:::::::::::::::: solution

### Adding Keys to GitHub (All OSs)

Once you have created your SSH key you need to copy it to your account, got to _Settings > SSH and GPG Keys_ and click
on the _New SSH key_ button. Enter a name for your key, set the **Key type** to _Authenticaion Key_ and paste your
public key (the contents of the file ending in `.pub`) into the **Key** box then click the _Add SSH key_ button.

:::::::::::::::::::::::::

:::::::::::::::: solution

### Checking your key works

You can test if you have setup your SSH key by running the following command which should produce similar output.

```bash
ssh -T git@github.com
Hi ns-rse! You've successfully authenticated, but GitHub does not provide shell access.
```

If you do **not** see the above successful authentication message please get in touch **before** the course starts.
:::::::::::::::::::::::::

## Conda Environments

::::::::::::::::::::::::::::::::::::: callout

## Anaconda Licensing

It is important to fully understand and adhere to the [Anaconda Licensing][anacondalicense] which permits
the use of their software (including Miniconda) in educational and research environments _only_ if there is no
commercial benefit. If the work you undertake involves commercial collaboration you should seek alternative solutions
for virtual environments (e.g. [miniforge3][miniforge3] or [virtualenvwrapper][virtualenvwrapper]).

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: discussion

In order to teach this course we need some code that is version controlled and some tasks to complete. [Python][python]
has been chosen as the language to fulfill that task. You do _not_ need to know Python in order to complete the course,
the code you need to use is all provided and can be copy and pasted.

However, you _do_ need what is known as a "_Virtual Environment_" setup to be able to install various programmes and run
the checks that are part of this course. To that end you should install
[Miniconda3][miniconda3]/[miniforge3][miniforge3] or another tool for managing Virtual Environments
(e.g. [virtualenvwrapper][virtualenvwrapper]) on your system prior to attending the course.

If you are already familiar with Python and Virtual Environments you can simply create a fresh virtual environment to
use for the course.

:::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::: solution

### Installing Miniconda/Miniforge3

Please follow the instructions at [Installing Miniconda](https://docs.anaconda.com/free/miniconda/miniconda-install/)
for your Operating System.

Should you chose to use `miniforge3` the downloads and installation instructions for different operating systems can be
found [here][miniforge3-install].

:::::::::::::::::::::::::

:::::::::::::::: solution

### Creating A Virtual Environment

You will have to create a virtual environment to undertake the course. If you have installed Miniconda as described
above you open a terminal (Windows use the Git Bash Shell) and create a Virtual Environment called `git-collaboation`.

``` bash
conda create --name git-collab python=3.11
conda activate git-collab
```

:::::::::::::::::::::::::

[anacondalicense]: https://www.anaconda.com/blog/update-on-anacondas-terms-of-service-for-academia-and-research
[gh]: https://github.com
[git]: https://git-scm.com/
[github_ssh]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account
[gitMac]: https://git-scm.com/download/mac
[gitWin]: https://git-scm.com/download/win
[git4windows]: https://carpentries.github.io/workshop-template/install_instructions/#shell
[miniconda3]: https://docs.anaconda.com/free/miniconda/
[miniforge3]: https://conda-forge.org/
[miniforge3-install]: https://github.com/conda-forge/miniforge
[python]: https://python.org
[ssh-ed25519]: https://blog.g3rt.nl/upgrade-your-ssh-keys.html
[ssh-keygen]: https://www.digitalocean.com/community/tutorials/how-to-create-ssh-keys-with-openssh-on-macos-or-linux
[virtualenvwrapper]: https://rse.shef.ac.uk/blog/2024-08-13-python-virtualenvwrapper/
