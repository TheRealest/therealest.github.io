---
title: hub+git makes Github better
date: 2015-05-11
---
<p class="intro"><span class="dropcap">G</span>it is an incredible version control system for projects both large and small, and Github is a perfect companion for hosting repositories. But Git was never designed to be used with Github; the opposite is true. There is a tool called Hub which gives Git a whole new set of commands specifically targeted at Github users, and I'll show you how to set it up so you won't even realize you're using it until you try to use someone else's workstation without it.</p>

This post will have three short parts:

  1. [Going over the `hub` commands](#the-hub-command)
  2. [Installing `hub` and setting a command line alias](#installing-hub)
  3. [My `.gitconfig` aliases for `hub`](#my-git-aliases)

<p class="note"><code>hub</code> has default arguments for many commands that fill in either your current repo or your own username if one is not specified. To make things clearer in the examples, I will use my Github username, <code>TheRealest</code>, whenever <code>hub</code> fills in your own username, and my <code>dotfiles</code> repo when <code>hub</code> fills in your current repo. Whenever you should explicitly state a repo or username I will use the fantastic <code>ionic</code> repo by <code>driftyco</code>. When we're talking about forks I'll use <code>s10wen</code> as my upstream for <code>dotfiles</code> (which is who I originally forked it from).</p>

### The `hub` command
`hub` is an addon for `git` which provides a set of commands that make working with Github a lot easier. I won't go over every single one because the examples in the [`hub` help docs](https://hub.github.com/hub.1.html) (also reachable via `man hub`) are pretty self explanatory, but just to give you an idea of what `hub` offers I'll list my most used features.

A little further down this post I'll go over how to alias `hub` to `git`. Since you'll basically always be using `hub` with this alias on your machine, I'm going to use the `git` command rather than `hub` in the examples below.

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

If you want to fork a repo with the intention of working on it on your local machine, the easiest way to do so is to clone the original repo with `git clone username/repo` and then do `git fork` from inside your newly cloned project. This will do the same thing as clicking the fork button on Github, though you may need to mess with the remotes a bit. After a `clone` and `fork` from the command line you will have an `origin` remote pointing to the original repo, and a remote named after your username pointing to your fork. I usually rename these so that they use the more typical `upstream`/`origin` naming convention.

`pull-request` just creates a pull request for you on Github. By default it uses `master` as the base for the pull request, and if your repo is a fork it uses the upstream `master` branch. You can specify a branch as the base of your pull request with `-b` (still at the upstream repo by default if you're working on a fork).

Finally, `browse` is a quick way to open pages on github.com in your browser. Without any arguments it opens your repo page (or the page of the upstream repo if you're working on a fork), but there options for going straight to a specific issue or pull request, commit, or even another repo entirely. The `-u` command will output the URL instead of opening the page, and if you're better with bash than I am you could probably pipe this into `hub`'s beefed up `merge` command which can take a link to a pull request or commit.

#### Sprucing up old favorites

As I mentioned above, `hub` will let you specify a github.com link for `merge`, `cherry-pick`, and `checkout`. You could use this to work with pull requests, but I find it so easy to merge a pull request and delete the feature branch on Github that I haven't really used this feature.

Last but certainly not least, you can push a branch to multiple remotes at the same time like this:

```console
> git push origin,deploy master
git push origin master
git push deploy master
```

You can do the same with `fetch`, and if you specify a remote name that doesn't exist, `hub` will try to create a new remote before fetching. The way `hub` creates the remote is best shown through example:

```console
> git fetch s10wen,origin
git remote add s10wen git://github.com/s10wen/dotfiles.git
git fetch s10wen
git fetch origin
```

### Installing `hub`

Installing `hub` is as simple as:

```console
> brew install hub
```

To check that everything is installed correctly try the following:

```console
> hub version
git version 2.3.5
hub version 2.2.0
```

You should get version numbers for both `git` and `hub`.

After you install `hub` you will want to alias the `hub` command to git so you don't have to remember when to use one or the other. Fortunately `hub` provides a convenience method that determines the appropriate aliasing syntax based on your system, so you only need to add one line to your `.bash_profile` or another file your shell sources on login:

```console
> eval "$(hub alias -s)"
```

Now any time you use the `git` command in your terminal, `hub` will deal with what it can and then delegate the rest to `git`. Now your `git` has superpowers!

### My `git` aliases

I'm a big fan of using aliases with `git`, and `hub` is no exception. Here's an excerpt from my `~/.gitconfig` file with a list of my aliases for `hub` commands:

```console
[alias]
  pr = pull-request
  pro = pull-request -o
  prmks = pull-request -b TheRealest -o
  cl = clone
  bw = browse
  bwi = "! f() { hub browse -- issues/$1; }; f"
```

The difference between `pr` and `pro` is that `pro` will open the page of the pull request on Github after it is created. I use `prmks` for submitting pull requests at [MakerSquare](http://makersquare.com/) where we always base our pull requests on a branch named after our username, to keep all of the students' work organized.

The `bw` command opens the project page on Github, while `bwi` takes a number and opens the page of the pull request or issue with that number. The bash function is necessary because `git` automatically inserts a space between aliases and further arguments, but the `hub` command requires that `issues/##` not have a space when used with the `browse` command.