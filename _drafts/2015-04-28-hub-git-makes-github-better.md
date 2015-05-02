---
title: hub+git makes Github better
date: 2015-04-28
---
<p class="intro"><span class="dropcap">G</span>it is an incredible version control system for projects both large and small, and Github is a perfect companion for hosting repositories. But Git was never designed to be used with Github; the opposite is true. There is a tool called Hub which gives Git a whole new set of commands specifically targeted at Github users, and I'll show you how to set it up so you won't even realize you're using it until you need it.</p>

This post will have three short parts:

  1. [Going over the `hub` commands](#the-hub-command)
  2. [Installing `hub`](#)
  3. [Setting aliases to give `git` superpowers](#)

### The `hub` command
`hub` is an addon for `git` which provides a set of commands that making working with Github a lot easier. I won't go over every single one because the examples in the `hub` help docs are pretty self explanatory, but just to give you an idea of what `hub` offers I'll list my most used commands.

#### github link shortcuts

It can be a pain to have to switch between the browser and the terminal to perform regular tasks involving Github links like `clone` and `remote`. `hub` makes this easier by allowing you to use the familiar username/repo syntax to refer to a repo on Github. Cloning a repo becomes as easy as:

```
git clone TheRealest/dotfiles
```

If you omit the repo name and just use a username it will assume you mean the repo with the current project name, for example the following adds a remote name `upstream` that points to the `s10wen/dotfiles` repo on Github:

```
cd ~/projects/dotfiles
git remote add upstream s10wen
```

This works for any command that expects a link to a repo. You can also add stuff to refer to commits or pull requests, for example to use with checkout:

(?)

#### New commands

There are a few new commands hub provides to allow you to perform actions that are normally restricted to the website from the command line. 0



