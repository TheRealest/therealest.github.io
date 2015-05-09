---
title: hub+git makes Github better
date: 2015-04-28
---
<p class="intro"><span class="dropcap">G</span>it is an incredible version control system for projects both large and small, and Github is a perfect companion for hosting repositories. But Git was never designed to be used with Github; the opposite is true. There is a tool called Hub which gives Git a whole new set of commands specifically targeted at Github users, and I'll show you how to set it up so you won't even realize you're using it until you need it.</p>

This post will have three short parts:

  1. [Going over the `hub` commands](#the-hub-command)
  2. [Installing `hub`](#)
  3. [Setting aliases to give `git` superpowers](#)

<p class="note"><code>hub</code> has default arguments for many commands that fill in either your current repo or your own username if one is not specified. To make things clearer in the examples, I will use my Github username, <code>TheRealest</code>, whenever <code>hub</code> fills in your own username, and my <code>dotfiles</code> repo when <code>hub</code> fills in your current repo. Whenever you should explicitly state a repo or username I will use the fantastic <code>ionic</code> repo by <code>driftyco</code>. When we're talking about forks I'll use <code>s10wen</code> as my upstream for <code>dotfiles</code> (which is who I originally forked it from).</p>

### The `hub` command
`hub` is an addon for `git` which provides a set of commands that make working with Github a lot easier. I won't go over every single one because the examples in the `hub` help docs are pretty self explanatory, but just to give you an idea of what `hub` offers I'll list my most used features.

#### Github link shortcuts

It can be a pain to have to switch between the browser and the terminal to copy Github links over for commands like `clone` and `remote`. `hub` makes this easier by allowing you to use the familiar `username/repo` syntax to refer to a repo on Github. Cloning a repo becomes as easy as:

```console
> git clone TheRealest/dotfiles
```

This works for any command that expects a link to a repo. If you omit the slash and only use one argument `hub` will assume it is either a username (with your current repo name) or a repo name (with your username) depending on the command:

```console
> cd ~/projects/dotfiles
> git remote add upstream s10wen
git remote add s10wen git://github.com/s10wen/dotfiles.git

> cd ~/new_projects
> git clone dotfiles
git clone git://github.com/TheRealest/dotfiles.git
```

#### New commands

There are a few new commands `hub` provides to allow you to perform actions from the command line that are normally restricted to the website. If you can get used to these commands, you only really need to use github.com to view existing issues and pull requests (if you've found a good way to review, comment on, merge, or close issues and pull requests from the command line, please let me know!).

The first of these is `create`, a variation on the standard `init`. While `init` sets up a directory as a git repo, `create` also goes out and creates a repo with the same name on Github, setting up an `origin` remote for you.

```console
git create
```

#### Sprucing up old favorites

If you want to fork a repo with the intention of working on it on your local machine, the easiest way to do so is to clone the original repo with git clone username/repo command and then do git fork from inside your newly cloned project. This will do the same thing as clicking the fork button on Github, though you may need to mess with the remotes a bit. After a clone and fork from the command line you will have an origin remote pointing to the original repo, and a remote named after your username pointing to your fork. I usually rename these so that they use the more typical upstream/origin naming convention.

pull-request just creates a pull request for you on Github. By default it uses master as the base for the pull request, and if your repo is a fork it uses the upstream master branch. You can specify a branch as the base of your pull request with -b (still on the upstream repo by default if you're working on a fork).

After you install hub you will want to alias the hub command to git so you don't have to remember when to use one or the other. Fortunately hub provides a convenience method that determines the appropriate aliasing syntax based on your system, so you only need to add one line to your .bash_profile or another file your shell sources on login:

eval "hub alias"