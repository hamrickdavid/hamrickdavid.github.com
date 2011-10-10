--- 
layout: post 
title: "Handling Filesystem Events with GCD"
---

I wrote a post about how I use [kqueue to monitor the filesystem](/2011/10/09/kqueue-in-cocoa.html) for changes in my configuration files. This method has been working for me for some time now, but I'm glad that other readers pointed out a flaw in it. [Mike Ash](http://www.mikeash.com/), of the [Friday Q&A fame](http://www.mikeash.com/pyblog/), [noted](http://www.reddit.com/r/programming/comments/l6j3g/using_kqueue_in_cocoa/c2q74yy) that you can use GCD to monitor filesystem events. I believe that you should always work at the highest level of abstraction possible and this seemed like it would reduce the amount of code needed to achieve my goal, being notified when a certain file changes.

If you can target 10.6+ or iOS4+ you can use [Grand Central Dispatch](http://en.wikipedia.org/wiki/Grand_Central_Dispatch) to handle filesystem notifications. [Dispatch Event source](http://developer.apple.com/library/mac/#documentation/Darwin/Reference/Manpages/man3/dispatch_source_create.3.html) can be used to monitor lot's of different things such as sockets, signals, and processes. We will be using it to monitor a virtual filesystem node (vnode).

<script src="https://gist.github.com/1274571.js?file=gcdtest.m"></script>

This method of monitoring a file is much simpler. We don't need to create a custom class and it is much shorter in length. I will be switching away from using kqueue to using this method since I don't need to maintain anything less than 10.6 or iOS4.