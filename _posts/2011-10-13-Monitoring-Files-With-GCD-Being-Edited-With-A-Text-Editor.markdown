--- 
layout: post 
title: "Monitoring Files With GCD Being Edited With A Text Editor"
---

I recently posted about [monitoring files with GCD](/2011/10/10/handling-filesystem-events-with-gcd.html) and this approach works very well. I was using this in an iOS application and would edit the configuration file with [TextMate](http://macromates.com/) and whenever I save it, it would dynamically reload the file. However whenever I opened the file with Vim or XCode the file would receive the notification the first time and then not again after that.

Certain editors appear to be deleting the file and then re-saving it. Or at the very least I am receiving the `DISPATCH_VNODE_DELETE` flag set in dispatch source event handler. So in order to have the desired behavior of monitoring the file continuously I had to modify my event handler a bit differently.

<script src="https://gist.github.com/1285920.js?file=example.m"></script>

This method listens for events on that file descriptor and when the file is deleted it calls itself again, setting up a new event handler. This solves the problem that certain editors were causing by deleting the files before saving them.


## Grand Central Dispatch dispatch source flags ##

It was not immediately obvious to me how to see which flags were set on a dispatch source event. But after asking a [question on Stack Overflow](http://stackoverflow.com/questions/7748725/grand-central-dispatch-gcd-dispatch-source-flags) I found the answer in apple's documentation in the [Concurrency Programming Guide](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html#//apple_ref/doc/uid/TP40008091-CH103-SW10). All you need to do is call the `dispatch_source_get_data` function and it will return the flags that are set. Here is the relevant line from the guide: 

>>For a descriptor dispatch source that monitors file system activity, this function returns a constant indicating the type of event that occurred. For a list of constants, see the dispatch_source_vnode_flags_t enumerated type.
