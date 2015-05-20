---
title: A fork-clone bash script
date: 2015-05-19
---
<p class="intro"><span class="dropcap">F</span>orking and cloning a github repo to modify or play around with the code is an extremely common practice, but it's not exactly painless. Having to switch between the browser and the command line to fork and clone is often only a minor annoyance, but setting up your remotes can cause headaches, especially if you cloned the original repo rather than your own fork. Any time you have a repetitive task that's easy to mess up or has a lot of steps that are hard to remember, the answer is to <b>script it</b>.</p>

<p class="note">This script relies on <code>hub</code>, the command line tool from Github that gives <code>git</code> superpowers. If you've never used <code>hub</code>, check out <a href="{% post_url 2015-05-11-hub-git-makes-github-better %}">my post on <code>hub</code></a> before going any further with this post; it will be worth your while.</p>

1. [A need for a script](#a-need-for-a-script)
2. [Outlining-our-bash-script](#outlining-our-bash-script)
3. [The final version](#the-final-version)
4. [Setting an alias for your script](#setting-an-alias-for-your-script)

### A need for a script

If you spend a lot of time working with other peoples' repos you may find the fork and clone process to be all too familiar. Usually I find the repo on Github, click the fork button in the top right corner of the page, click the "copy clone URL" button on the next page, head over to my terminal to use the `git clone` command, `cd` into the new project folder, and then the fun begins of fixing my remotes the way I like them, usually with another trip back to the browser to get the link to the upstream repo.

As lazy programmers all these steps should be a huge red flag! That should be a hint that we would be served by scripting this process. Bash scripting may not sound like a good time to most of you, but for something this simple all you need to do is copy paste the list of commands you use into a file and add a little bash syntax so we can pass command line arguments to our script.

First we'll list out the commands we use for this process for a specific case, say forking and cloning my `dotfiles` repo. Keep in mind that bash scripts don't follow your `.bash_profile` aliases, so you need to use the explicit name of each command. In this case, that means using `hub` when we are actually using `hub` syntax, rather than relying on our normal `git` alias to figure it out.

### Outlining our bash script

Here are the steps we want to perform:

- clone the original upstream repo (`hub`)
- fork the repo on Github (`hub`)
- rename the origin remote to upstream
- rename the `TheRealest` (my username) remote to origin
- set the `master` branch to track my forked repo (the new `origin` remote)

And here's the translation of our pseudocode into bash:

```console
hub clone "TheRealest/dotfiles"
cd dotfiles
hub fork
git remote rename origin upstream
git remote rename TheRealest origin
git branch -u origin/master
```

Now we need to decide on the interface for our script -- what we want to type in to the terminal to make all of the above happen. I thought something like the following was intuitive:

```console
> fkcl TheRealest dotfiles
```

And now it's just a matter of connecting one to the other. You can access command line args inside a bash script with the variables `$1`, `$2`, `$3`, etc. So we'll swap out `TheRealest` with `$1`, and `dotfiles` with `$2` in our previous special case script. String concatenation in bash scripts is simple: just put the variables next to each other. Finally, we need to add a line at the top which allows us to run this file as a bash script.

### The final version

```bash
#!/bin/bash
# USAGE: fkcl <username> <repo>
hub clone $1"/"$2
cd $2
hub fork
git remote rename origin upstream
git remote rename TheRealest origin
git branch -u origin/master
```

We're done! Name the file something like `fork_clone.sh` and save it somewhere you'll remember later. I have a `bash_scripts` folder in my home directory for this purpose. You can then call the script from anywhere with:

```console
> ~/bash_scripts/fork_clone.sh TheRealest dotfiles
```

### Setting an alias for your script

If you want to be able to call it with `fkcl` like I did above, you just need to set up an alias somewhere that will be sourced by your shell login. If that doesn't mean anything to you, just find the `.bash_profile` file in your home folder, or create one if it does not exist, and add the following line:

```console
alias fkcl="~/bash_scripts/fork_clone.sh"
```

Restart your shell to test out the new alias. Happy fork-cloning!

