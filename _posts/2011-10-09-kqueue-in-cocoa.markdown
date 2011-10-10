--- 
layout: post 
title: "Using kqueue in Cocoa"
---

I prescribe to the philosophy of keeping details out of code, and so I often use configuration files to organize those details. In Cocoa and Cocoa Touch, that generally means keeping them in a plist, but could easily be JSON, XML, or YAML. 

I recently had a list of changes that I needed to make to a project. These were all small fixes that didn't involve a lot of work and they were all details that I had specified in a configuration file. While making these fixes I would change one of the values, recompile the app, and see that the change I made had the desired effect. But since this configuration file was being read at run-time I realized that I could reload the configuration file and skip the step of recompiling.

## Using Stat ##

My first guess at how to solve this problem was by calling [stat](http://linux.die.net/man/2/stat) on a timer to check to see the last modified time and reload the file if necessary. Here is a small example example to demonstrate

<script src="https://gist.github.com/1274228.js?file=StatTest.c"></script>

If we were to use this in a cocoa application I would wrap that in an objective-c method, but this demonstrates the technique.

This seemed to be an inefficient way to do it since most of the time it wouldn't have changed. This would have to be running in a different thread or as part of a timer attached to the run loop. I think that there is a better way to do this.

## Using kqueue ##

[Kqueue](http://www.freebsd.org/cgi/man.cgi?query=kqueue&sektion=2) is a BSD system call that allows you to receieve event notifications. These are not limited to filesystem notifications but that is how we will be using them. We want to receive a notification every time test.plist is changed. This method seems to be much more efficient that polling the filesystem for changes.

<script src="https://gist.github.com/1274228.js?file=kQueueTest.c"></script>

## Using kqueue in Cocoa ##

So now that we have a basic command line prototype of what we want, it's time to turn it in to something that we can use inside of our cocoa app. I've create a simple wrapper around kqueue called FileSystemWatch with two methods. The first one is called `watchFileAtPath:target:action:`. You simply pass in the path to the file and a method to be called when there is a change on that file.

<script src="https://gist.github.com/1274228.js?file=FileSystemWatch.h"></script>

<script src="https://gist.github.com/1274228.js?file=FileSystemWatch.m"></script>

Here is an example of how you would use this class.

<script src="https://gist.github.com/1274228.js?file=Example1.m"></script>

After you use it, you need to call `stopWatching` before you release it.

<script src="https://gist.github.com/1274228.js?file=Example2.m"></script>

## In Practice ##

I've been using this method for some time in both OS X and iOS applications. And while it's not really of any use on iOS since  you can't modify configuration files while it's running, it is very useful to have in the simulator. 

One of the difficulties in using this in Cocoa Touch application is the path to the file you want to modify is actually in your source directory, not in your bundle's directory. So you have to pass in an absolute path instead of a relative one. This becomes a problem when a project is being used on multiple people's machines, all of which keep the project in different locations. I'm still working on a solution to that problem.

Discussion on [Reddit](http://www.reddit.com/r/programming/comments/l6j3g/using_kqueue_in_cocoa/)

## Update ##

It was pointed out to me that using GCD can be a better way to listen for filesystem notifications. I wrote a follow up post that discusses that [here](http://www.davidhamrick.com/2011/10/10/handling-filesystem-events-with-gcd.html).
