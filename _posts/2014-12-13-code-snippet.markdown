---
layout: post
title:  "Post With A Code Snippet"
date:   2014-12-13
---

<p class="intro"><span class="dropcap">Y</span>ou'll find this post in your <code>_posts</code> directory - edit this post and re-build (or run with the <code>-w</code> switch) to see your changes! To add new posts, simply add a file in the <code>_posts</code> directory that follows the convention: YYYY-MM-DD-name-of-post.ext.</p>

Jekyll also offers `powerful` support for code snippets:

```js
if (flags.length === 0 && args.length === 0) {
  var lines = [this.line('resources','header')];
  var rs = this.resources.all();
  if (Object.keys(rs).length === 0) return 'No resources yet!';

  for (var r in rs) {
    var longName = this.resources.library[r].longName;
    var amount = rs[r] + ' ' + this.resources.library[r].unit;
    lines.push(this.line([longName,amount],'justify'));
  }

  return lines;
} else {
  return this.runner.runCommand('resource --help');
}
```

```js
cool.thin.call(this,args);
```

Check out the [Jekyll docs][jekyll] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh].

[jekyll-gh]: https://github.com/mojombo/jekyll
[jekyll]:    http://jekyllrb.com
