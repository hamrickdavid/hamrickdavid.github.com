--- 
layout: post 
title: "Monitoring Files With GCD Being Edited With A Text Editor"
---

I recently posted about [monitoring files with GCD](/2011/10/10/handling-filesystem-events-with-gcd.html) and this approach works very well. I was using this in an iOS application and would edit the configuration file with [TextMate](http://macromates.com/) and whenever I save it, it would dynamically reload the file. However whenever I opened the file with Vim or XCode the file would receive the notification the first time and then not again after that.

Certain editors appear to be deleting the file and then re-saving it. Or at the very least I am receiving the `DISPATCH_VNODE_DELETE` flag set in dispatch source event handler. So in order to have the desired behavior of monitoring the file continuously I had to modify my event handler a bit differently.

	- (void)watchConfigFile:(NSString*)path;
	{
		dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
		int fildes = open([path UTF8String], O_EVTONLY);

	    __block typeof(self) blockSelf = self;
		__block dispatch_source_t source = dispatch_source_create(DISPATCH_SOURCE_TYPE_VNODE,fildes, 
																  DISPATCH_VNODE_DELETE | DISPATCH_VNODE_WRITE | DISPATCH_VNODE_EXTEND | DISPATCH_VNODE_ATTRIB | DISPATCH_VNODE_LINK | DISPATCH_VNODE_RENAME | DISPATCH_VNODE_REVOKE,
																  queue);
		dispatch_source_set_event_handler(source, ^
		{
			unsigned long flags = dispatch_source_get_data(source);
			if(flags & DISPATCH_VNODE_DELETE)
			{
				dispatch_source_cancel(source);
				[blockSelf watchStyleSheet:path];
			}
			// Reload config file
		});
		dispatch_source_set_cancel_handler(source, ^(void) 
		{
			close(fildes);
		});
		dispatch_resume(source);
	}

This method listens for events on that file descriptor and when the file is deleted it calls itself again, setting up a new event handler. This solves the problem that certain editors were causing by deleting the files before saving them.


## Grand Central Dispatch dispatch source flags ##

It was not immediately obvious to me how to see which flags were set on a dispatch source event. But after asking a [question on Stack Overflow](http://stackoverflow.com/questions/7748725/grand-central-dispatch-gcd-dispatch-source-flags) I found the answer in apple's documentation in the [Concurrency Programming Guide](http://developer.apple.com/library/ios/#DOCUMENTATION/General/Conceptual/ConcurrencyProgrammingGuide/GCDWorkQueues/GCDWorkQueues.html#//apple_ref/doc/uid/TP40008091-CH103-SW10). All you need to do is call the `dispatch_source_get_data` function and it will return the flags that are set. Here is the relevant line from the guide: 

>>For a descriptor dispatch source that monitors file system activity, this function returns a constant indicating the type of event that occurred. For a list of constants, see the dispatch_source_vnode_flags_t enumerated type.
