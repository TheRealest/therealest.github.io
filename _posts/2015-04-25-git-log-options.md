---
title: Git log options
date: 2015-04-24
---
<p class="intro"><span class="dropcap">G</span>it log is an extremely useful tool for examining your git history on a project. If you use a lot of branches for development it can be difficult to figure out which have been merged or pushed, and <code>git log</code> can certainly help you get a quick overview of the status of everything. But if you just type <code>git log</code> you may not see the benefit because the default display mode is clunky without conveying a lot of information. Git log only really shines when you start using the array of flag options available for the command.</p>

<div class="note">I'll use a recent project I worked on for the examples because we used feature branches for development and it will be easy to see the difference between the various options.</div>

You can also skip ahead to the [cheatsheet at the bottom](#git-log-cheatsheet) of the post if you like.

### The default output

Running `git log` without any flags returns the most recent commits on the current branch in a very verbose way. Here's what it looks like for my project:

```console
> git log
commit 6b7a8b6d5f7ea448c98bdb7c69f73c89a0c05946
Author: bennlich <XXX>
Date:   Sun Apr 19 09:50:57 2015 -0700

    build

commit 130b23e85d2f7873593b89d226adb8b6d5834ed8
Merge: 00e674d 990f9cb
Author: Benny <XXX>
Date:   Sun Apr 19 09:50:11 2015 -0700

    Merge pull request #62 from cityknowledge/error-handling

    Error handling for promptCard and scheduleCard

commit 00e674d9551ce10315f5e4050162bb0e18a6c130
Merge: bf12849 5f49ca6
Author: Benny <XXX>
Date:   Sun Apr 19 09:47:02 2015 -0700

    Merge pull request #63 from cityknowledge/trip-events

    Trip Events System
```

`git log` includes the commit SHA, the author, commit date, and message of each commit. If a commit is a merge commit it also includes the SHAs of the parents. This is kind of a pain to try to mentally parse – there's too much text and not enough information. The commits are ordered by commit date and time, not ancestry. This can get confusing when work is being done on two different branches at the same time and then later merged, because the commits from both branches get mixed together in the log.

### The <code>--oneline</code> option

The one option I always use with `git log` is the `--oneline` option, which takes those big blocks of text for each commit and condenses it down to a single line. The output looks like this:

```console
> git log --oneline
6b7a8b6 build
130b23e Merge pull request #62 from cityknowledge/error-handling
00e674d Merge pull request #63 from cityknowledge/trip-events
5f49ca6 added basic leg-change event support
c57f655 fixed timeline initial display bug
29f77e6 added timeline event listeners
8a973f3 coded calculateCurrentLeg()
e9bb7fe filled route gaps with wait legs
8d0422d route bootstrapping + updated directives to match
990f9cb Add loading spinner and centered "no results" message
7b7d3f3 Fix schedule axis rendering
ec7cb98 Remove unused nav-bar element
f1d629f Update ionic
16b9599 basic structure of tripEventsService added
8e0ab2c stopped autocomplete from preventing submit
7e5d57c added error to promptCard
897f5d8 added 'loading' and 'no results' messages to schedule view
bf12849 build
89e49be Merge pull request #61 from cityknowledge/map-stuff
213846e Replace selectedDirectionsObject with selectedRouteIndex; replace gmap.triggerResize with gmap.reset
28ade92 Refactor gmapService, also fix renderDirections
```

You lose the date, author, and description info but retain the commit message and a shortened version of the commit SHA. For merge commits you're not seeing the parent commits either, but we can render that information in a clearer and more concise way with the next option.

### The <code>--graph</code> option

This is another flag I always use together with `--oneline`. Adding `--graph` to your `git log` command adds a graphical representation of branching and merging to the left side of the log. It looks something like this:

```console
> git log --oneline --graph
* 6b7a8b6 build
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| * 7b7d3f3 Fix schedule axis rendering
| * ec7cb98 Remove unused nav-bar element
| * f1d629f Update ionic
| * 8e0ab2c stopped autocomplete from preventing submit
| * 7e5d57c added error to promptCard
| * 897f5d8 added 'loading' and 'no results' messages to schedule view
* |   00e674d Merge pull request #63 from cityknowledge/trip-events
|\ \
| |/
|/|
| * 5f49ca6 added basic leg-change event support
| * c57f655 fixed timeline initial display bug
| * 29f77e6 added timeline event listeners
| * 8a973f3 coded calculateCurrentLeg()
| * e9bb7fe filled route gaps with wait legs
| * 8d0422d route bootstrapping + updated directives to match
| * 16b9599 basic structure of tripEventsService added
|/
* bf12849 build
*   89e49be Merge pull request #61 from cityknowledge/map-stuff
```

Each commit now has an asterisk beside it and you can follow the lines to see where branches were created or merged. This solves the issue of not losing the parent commit SHAs when we added the `--oneline` option; in fact, I think this is far easier to understand when you want to follow the parent branches of a merge this way.

### The <code>--decorate</code> option

The `--decorate` option adds branch names to the log output like so:

```console
> git log --oneline --graph --decorate
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| * 7b7d3f3 Fix schedule axis rendering
| * ec7cb98 Remove unused nav-bar element
| * f1d629f Update ionic
| * 8e0ab2c stopped autocomplete from preventing submit
| * 7e5d57c added error to promptCard
| * 897f5d8 added 'loading' and 'no results' messages to schedule view
* |   00e674d Merge pull request #63 from cityknowledge/trip-events
|\ \
| |/
|/|
| * 5f49ca6 added basic leg-change event support
| * c57f655 fixed timeline initial display bug
| * 29f77e6 added timeline event listeners
| * 8a973f3 coded calculateCurrentLeg()
| * e9bb7fe filled route gaps with wait legs
| * 8d0422d route bootstrapping + updated directives to match
| * 16b9599 basic structure of tripEventsService added
|/
* bf12849 build
*   89e49be Merge pull request #61 from cityknowledge/map-stuff

```

On the first line of the output you can see `(HEAD, origin/master, origin/HEAD, master)` between the commit SHA and the message. This shows that both my local `HEAD` and `master` branch, as well as the corresponding remote branches they track, all point to that commit. This is really nice for checking whether you've pushed your recent commits to a remote, because if haven't you would see `origin/HEAD` and `origin/master` further down in the history.

>Keep in mind that all of the commands we've used so far only show commits leading up to `HEAD`. Put another way, any commits which did not contribute to the current working tree are not going to appear in `git log`.

### The <code>--branches</code> option

Sometimes you might *want* to see all the commits for every branch to get a broader picture of your project. This is what the `--branches` option is for:

```console
> git log --oneline --graph --decorate --branches
* ada9f20 (origin/trip-events, trip-events) added note aboute naming convention conflicts (leg/route & step/leg)
* b0d1b12 fixed initial walking leg issue
* e8516b6 added fallbacks for correct initial wait leg creation, fixes #69
* f1a6004 fixed off by one bug in bootstrapping process
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| * 7b7d3f3 Fix schedule axis rendering
| * ec7cb98 Remove unused nav-bar element
| * f1d629f Update ionic
| * 8e0ab2c stopped autocomplete from preventing submit
| * 7e5d57c added error to promptCard
| * 897f5d8 added 'loading' and 'no results' messages to schedule view
* |   00e674d Merge pull request #63 from cityknowledge/trip-events
|\ \
| |/
|/|
| * 5f49ca6 added basic leg-change event support
| * c57f655 fixed timeline initial display bug
| * 29f77e6 added timeline event listeners
| * 8a973f3 coded calculateCurrentLeg()
| * e9bb7fe filled route gaps with wait legs
| * 8d0422d route bootstrapping + updated directives to match
| * 16b9599 basic structure of tripEventsService added
|/
* bf12849 build
*   89e49be Merge pull request #61 from cityknowledge/map-stuff
```

Now you can see that I have another branch called `trip-events` which is ahead of `master` and thus also the `HEAD` right now. Those commits have been hidden thus far because they weren't in the ancestry of the `HEAD`, but now with the `--branches` flag all commits in the ancestry of any branch (remote or local) is displayed.

Besides getting a bird's eye view of your project, the `--branches` flag can be helpful when you're pushing to multiple remotes at the same time, say github and your production server. To quickly ensure everything is up to date you can run this command and check that the remote branch names are all on the same commit line. You can also see if you've fetched commits from a remote repository but not merged them into your local branch yet (by using `git fetch` instead of `git pull`). If you see a remote branch ahead of the local branch tracking it (if `origin/master` were above `master` in the log, for example) you haven't merged those commits.

### Setting up aliases for <code>git log</code>

Between `--oneline`, `--graph`, `--decorate`, and `--branches`, you should be able to display and understand most of what you'll need to know about your commit history. Typing these flags and remembering which is which can be a pain though, so I would suggest setting up `git` aliases for the modes you use most often.

I prefer to have two aliases for logging, one with `--branches` and one without. Both aliases have the other three flags we've discussed. With this setup you can easily see a very dense history with SHAs, commit messages, branch names, and the graphical branching diagram, and you can decide whether you want to see just commits for the current branch or for all of them.

Here's what the entries look like in my `~/.gitconfig` file:

```console
[alias]
  l = log --oneline --graph --decorate
  ll = log --oneline --graph --decorate --branches
```

Note that those are lowercase letter `L`s, not number `1`s.

Now you can do `git l` to display only commits for the current branch, and `git ll` for all commits. The `git` command will add any additional arguments or flags to the end of your aliases when you use them, so `git ll` is equivalent to `git l --branches`. From here on out I will be using the `git l` alias in examples to shorten the command line input and make the new options introduced easier to recognize. If you want to use the example commands without defining the aliases just replace `git l` with `git log --oneline --graph --decorate` as defined above.

### Filtering commits

There are a few other options for filtering commits or limiting the number of commits displayed that you might find useful. You can use ranges to only display commits that are in *one* branch but not *another* by appending `another..one` to the end of your command:

```console
> git l master..trip-events
* ada9f20 (origin/trip-events, trip-events) added note aboute naming convention conflicts (leg/route & step/leg)
* b0d1b12 fixed initial walking leg issue
* e8516b6 added fallbacks for correct initial wait leg creation, fixes #69
* f1a6004 fixed off by one bug in bootstrapping process
```

If you compare this output to the output above you'll see this is just the four commits in the `trip-events` branch ahead of `master`. You can also use commit SHAs or tags instead of branches in the range.

You can limit the number of commits rendered with the `-n` flag. There are two ways to use `-n`: either with a number following the flag, or by replacing the `n` with a number (they do the same thing).

```console
> git l -n 5
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| * 7b7d3f3 Fix schedule axis rendering
| * ec7cb98 Remove unused nav-bar element

> git l -5
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| * 7b7d3f3 Fix schedule axis rendering
| * ec7cb98 Remove unused nav-bar element
```

If you're interested in the history of a specific file, you can include just the commits which make edits to a given file by adding a double dash followed by the file path:

```console
> git l -- server.js
* 332ed23 Move server routes and config to middleware subdirectory
* a69f2cd Rename /stripeCheckout to /checkout
* c725d87 Use router pattern for stripe-routes
* d28abea Add route for fetching bus locations
* accf202 Allow server to accept JSON posts; fix stripeCheckout bug
* 0a937f9 Add stripe handler to server
* 8df0aa3 Add 404
* 9192bd4 Rewrite server in express
* 7ff4378 created prototype branch
```

You can omit the `--` if there wouldn't be any confusion between your file path and a possible branch name or range, but it doesn't hurt to include it every time.

### Getting more detail out of your log

The oneline mode is nice for looking at a lot of commits at once, but you may want to include a little more detail in the history sometimes. There are two options which add information for each commit: `--name-status` and `-p`. The first lists which files were changed in each commit (the *status* of the affected *filenames*):

```console
> git l --name-status
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
| M     public/css/app.css
| M     public/js/controllers.js
| M     public/js/directives.js
| M     public/js/services.js
*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| | M   public/partials/ScheduleCard.html
| | M   scss/schedule.scss
| * 7b7d3f3 Fix schedule axis rendering
| | M   src/directives/scheduleCardDirective.js
| * ec7cb98 Remove unused nav-bar element
| | M   public/index.html
```

The second gives a full diff for each commit (also called a *patch*):

```console
> git l -p
* 6b7a8b6 (HEAD, origin/master, origin/HEAD, master) build
| diff --git a/public/css/app.css b/public/css/app.css
| index f6ce3bb..a95b0de 100644
| --- a/public/css/app.css
| +++ b/public/css/app.css
| @@ -1,17 +1,22 @@
|  /*!
| -  Ionicons, v1.5.2
| +  Ionicons, v2.0.1

    ...

*   130b23e Merge pull request #62 from cityknowledge/error-handling
|\
| * 990f9cb Add loading spinner and centered "no results" message
| | diff --git a/public/partials/ScheduleCard.html b/public/partials/ScheduleCard.html
| | index 2d7821c..d83ab46 100644
| | --- a/public/partials/ScheduleCard.html
| | +++ b/public/partials/ScheduleCard.html
| | @@ -1,9 +1,9 @@
| |  <div class="card schedule-card">
| | -  <div ng-show="isLoading()">
| | -    Loading
| | +  <div class="schedule-message" ng-show="isLoading()">
| | +    <ion-spinner></ion-spinner>
| |    </div>
```

These options can be a little overwhelming but when combined with the filtering arguments from the previous section you can often get a quick snapshot of what a specific commit or set of commits is actually *doing* to your project.

### `git log` cheatsheet

Here are all the options we've gone over together for your reference.

- `--oneline` (condenses commit info down to a single line with message and shortened SHA)
- `--graph` (adds graphical representation of branching and merging)
- `--decorate` (adds branch names like `HEAD`, `master`, `origin/master`)
- `--branches` (includes commits from all branches, not just the ones in the history of your `HEAD`)
- `ref_one..ref_two` (only displays commits from `ref_two` which are not in `ref_one`, where the `ref`s can be branch names, commit SHAs, or tags)
- `-- <filepath>` (only display commits which edit the given file)
- `-n <num>` and `-<num>` (only display the first `<num>` commits)
- `--name-status` (list files affected by each commit)
- `-p` (include the diff for each commit)

And my `git log` aliases:

```
[alias]
  l = log --graph --decorate --oneline
  ll = log --graph --decorate --oneline --branches
```
