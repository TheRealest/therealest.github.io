---
title: "Successive digit shuffle: a puzzling algorithm"
date: 2015-05-05
---

<p class="intro"><span class="dropcap">G</span>iven a number, find the next larger number which can be made by shuffling its digits. For example, given <code>123</code>, return <code>132</code>. This is the kind of puzzle which leads people to an answer that takes enough work to seem right, while still being simple enough to make you feel clever. But as you test your solution on more and more cases your confidence begins to fade as you realize there's something a little more complex going on with this one.</p>

This is a problem provided to me by [kenning] during our cohort at [Makersquare]. I found it really interesting and surprisingly difficult to rigorously solve, and I was also interested in the time complexity of the solution, so I decided to write out my solution explicitly in code.

[kenning]: https://github.com/kenning
[Makersquare]: http://www.makersquare.com/

While the solution to this puzzle is certainly interesting in its own right, i found the journey equally so. I discussed this problem with several of my cohort mates at MKS and we all took remarkably similar paths to the final solution, all stopping at the *same* wrong answer thinking we were done. If you just want the spoilers you can skip down to the tl;dr, but I suggest you follow the path I and several others took to get there. 

### The approach

My approach with this puzzle, as is often the case with these types of "toy problems," is an iterative one. I try to outline a very basic, just "gets it done" solution in pseudocode first, where each line could be translated into a line or two of actual code. Then I try to think of test cases that might not be considered by my algorithm and patch it to deal with them until I've ended up with a general solution. Occasionally I find I've ended up with something unworkable and have to start all over, taking advantage of what I've learned. At some point in there I start converting pseudocode to actual code, which is a lot easier to test.

This is the method we will use here as well. 

### The first attempt

After playing around with some simple cases like `123` and `798`, my first thought was that you can take the ones digit and move along the numbers to the left until you find a lower digit. Then you insert the ones digit in its place, pushing everything to the right. In pseudocode it looks like this: