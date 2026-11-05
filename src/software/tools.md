# Required Tools

## Text Editor

The most important part about working on codebases is actually writing the
code. Some popular editors are:

 - [VSCode](https://code.visualstudio.com/)
 - [Pycharm](https://www.jetbrains.com/pycharm/)

There are many others, but these are very common so we will cover them in this
part of the guide. If you have any questions about how to use your specific
editor, feel free to drop questions in the MS Teams Channel.

### VSCode

You can download vscode from [their website](https://code.visualstudio.com/).
It should run on most major operating system.

VSCode will then need to be setup with [extensions for
Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).
This can be done from vscode by pressing `Ctrl+P` (or `Cmd+P` on a mac), paste
in the command `ext install ms-python.python` and press `Enter`.

### PyCharm

The community edition of PyCharm should be fine for all UAARG work. By default
it should be ready to work with our repositories.

PyCharm can be downloaded from the [Jetbrains
website](https://www.jetbrains.com/pycharm/).

## Github

We use git a lot (see more details below). One of the many components of git is
a source code host. We use Github for this purpose.

> It is important to note that git and github are **different things**. This is
> a common source of confusion. Some of the differences are detailed below in
> the "Git" section.

### Create an Account

You will need to create a Github account in order to read and write to our
common code repositories.

To create an account, visit [github.com](https://github.com/), click "Sign Up"
and follow the instructions. If you find that you already have an account, make
sure you can actually log into it.

You should now be able to log into [github.com](https://github.com/) and should
have a github **username**.

### Get Repository Access

Once you have created a github account, please share your username with a team
lead. They will add you to the [UAARG github
organization](https://github.com/uaarg).

In the meantime, once you install git (instructions below), you should be able
to view and download all public code from the [UAARG
organization](https://github.com/uaarg).

When you are granted access to the github organization, you will be able to
push branches/commits and create issues/prs on our repositories.

## Git

We use git to manage all of our source code. It lets us:

 - Track changes to the code over time
 - Collaborate on the code base concurrently
 - Compare versions of the software at different points in time

Using Git is absolutely necessary. It is also an industry standard tool. Most
software organizations will use git or something like it.

### Background

Git is always confusing because it's **decentralized**. It is very much like
email. You may setup an email address through GMail, iCloud, outlook, or use
one from the University of Alberta. These are called **email providers**. Git's
equivalent would be source code hosting services. Some popular source code
hosts are Github, Gitlab, sourcehut, Bitbucket, etc.

Then there are email **clients**. For email there is the Apple mail app,
android mail app, outlook, and thunderbird. These connect to any **email
provider**. This way everyone gets to choose their favourite **email provider**
and **email client**, and then they will always be guaranteed to work together
as it's all email. Git has many different clients, there is a command-line
interface (CLI), there are many graphical applications and most
IDEs/code-editors include a git client too.

UAARG has chosen Github as our **source code host**. You will just need to
choose a **git client**. If you do not like your git client, you have the
freedom of using a different one. You may even use multiple if it is easier.

### Choosing and Installing a Git Client

If you are completely new to git and do not have any experience with the tool,
I recommend a GUI interface like [Github Desktop](https://desktop.github.com/).
You can always install other tools later. Github desktop also let's you avoid
much of the complexity of authorization with [github.com](https://github.com).

Most IDEs and some text editors will ship with a git client as well. These will
work just fine as well.

### Configuration

Every git client will need to be configured with three things:

 1. Your name
 2. Your email (this can be, ie. your @ualberta address)
 3. Your source code host authentication details

#### Github Desktop

Github desktop can be configured by following [these
docs](https://docs.github.com/en/desktop/installing-and-configuring-github-desktop/installing-and-authenticating-to-github-desktop/authenticating-to-github-in-github-desktop)
under the section "Authenticating an account on Github".

#### VSCode

You will need to install first install git using [this
website](https://git-scm.com/downloads). All of the options presented by the
installer can be left to their default/recommended values.

Then sign into Github using the "Accounts" menu in the lower right of the
editor.

#### PyCharm

Jetbrains (the creator of PyCharm) has a [guide on setting up version control
for github](https://www.jetbrains.com/help/pycharm/github.html).

#### Git CLI

Github has a set of handy docs on setting up the git CLI for use with Github.

The first part (items 1 and 2 above) is [detailed
here](https://docs.github.com/en/get-started/quickstart/set-up-git#setting-up-git).

Then the last part (authentication, item 3 above) is [explained
here](https://docs.github.com/en/get-started/quickstart/set-up-git#authenticating-with-github-from-git).

You will have to choose between authenticating with either HTTPS or SSH. If you
don't know much about either, or which one to choose, then use HTTPS.

When using HTTPS, you will need to then cache your credentials (otherwise you
will need to paste in a *very long* key every time you use your git client.) The
[github cli](https://github.com/cli/cli#installation) is a good choice for
this. After it has been installed, run `gh auth login` and it will guide you
through the rest.

### Using and Learning Git

Git is both powerful and very complex. Many people get tripped up on it. So
don't feel bad if you mess up or need help. Remember, we can always help by
dropping questions in the MS Teams.

Github has an excellent set of resources on getting started with git. Here are
a few good articles:

 - [About Git (recommended)](https://docs.github.com/en/get-started/using-git/about-git)
 - [Pushing commits to a remote](https://docs.github.com/en/get-started/using-git/pushing-commits-to-a-remote-repository)
 - [Pulling changes from a remote](https://docs.github.com/en/get-started/using-git/getting-changes-from-a-remote-repository)

We also have a (simple) [git tutorial](https://github.com/uaarg/gitTutorial)
that may be useful if you are using the command line.

## Python

Python can be downloaded from [python.org](https://www.python.org/). The latest
version should be fine. **Make sure it's at least version 3.10.**

> NOTE: PyCharm users should already have python installed as part of the IDE.
> You should be able to skip this section.

If you are a MacOS or other UNIX-like OS user, python can also be downloaded
from your package manager (brew/apt/pacman/pkg/etc...). If you are using these
tools, I will assume that you know how to install python >= 3.10 :)

### Installation

If you downloaded python from [python.org](https://www.python.org/), running
the installer should be enough. You can leave every option on the default or
recommended setting.

### Usage

#### Terminal

Often our documentation asks that you run commands in a terminal. If you have
no experience using the terminal, then feel free to avoid it. Where you cannot
avoid it's usage, opening a terminal in your editor
([vscode](https://code.visualstudio.com/docs/terminal/basics) /
[pycharm](https://www.jetbrains.com/help/pycharm/terminal-emulator.html))

The documentation should share what commands to run. Generally these will be:

 - `python3` (or just `python` on windows) -- Run the python interpreter
 - `python3 -m venv` -- Used to setup a virtual environment
 - `ls` (or `dir` on windows) -- Show files in the current directory
 - `mkdir` -- Create a folder/directory
 - `cd` -- Move into a folder/directory
 - `cd ..` -- Move up a folder/directory (in other words, move out of the current folder/directory)

#### Language

It would be helpful to have some python (or at least general programming
experience). But newcomers are always accepted.

Python maintains [this list of
resources](https://wiki.python.org/moin/BeginnersGuide/Programmers) for
learning Python.

You should also be learning python in almost any programming course at the
University of Alberta. Profs and TAs are great resources. UAARG members are
also here to help :)

Python is a large language, you don't need to be an expert here. Questions are
always encouraged!

## Troubleshooting

If you run into any issues, we can help. Feel free to drop a message in the MS
Teams chat. Often when issues arise, they indicate that we should to a better
job here in these docs or in our codebases.
