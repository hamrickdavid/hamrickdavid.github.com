--- 
layout: post 
title: "Using UIWebView as a JavaScript Interpreter" 
---

I've written before about wanting to create [metadata driven applications](http://www.davidhamrick.com/2012/01/02/Goal-For-2012-Create-Metadata-Driven-Applications.html) but did not go in to specifics about how to achieve that. For many of the applications I have worked on that means creating plist files that ship with the application. Those plist files might contain details about what URLs to access in staging/test/production or styling details for UI elements.

This approach works well but I have always wished for more expressiveness, especially as these files grew in size as more and more things needed to be configured. I wanted to be able to use a scripting language to create this metadata. I even went as far as creating my own small scripting language based on Scheme. Creating my own scripting language had it's advantages, but mostly disadvantages, mostly that I was spending time maintaining it instead of working on the logic that I wanted from the script.

As a recent node.js convert, I decided to see if I could embed JavaScript in my application. I see that many other have had success embedding a full interpreter. Dominic Szablewski embedded Safari's Javascript engine ([JavaScriptCore](http://www.phoboslab.org/log/2011/06/javascriptcore-project-files-for-ios)) in his iOS app and Couchbase embedded Firefox's JavaScript engine ([SpiderMonkey](https://github.com/couchbaselabs/iMonkey)) for scripting CouchDB. While I considered doing this, I thought it may be overkill for my simpler needs. All I wanted to do was input a script and output some JSON that had the metadata that I wanted. But there was another option, use the built in UIWebView. 

## Example ##

I created a small adaptor class called JavascriptContext. This may seem like an unnecessary class but if I ever need to replace the UIWebView with a full-featured interpreter all of my interactions with the UIWebView will all be happening in 1 class and I can replace them all at once.

<script src="https://gist.github.com/1805900.js?file=JavascriptContext.h"></script>

<script src="https://gist.github.com/1805900.js?file=JavascriptContext.m"></script> 

<script src="https://gist.github.com/1805900.js?file=Example.m"></script>

To be honest I was somewhat surprised it was this easy. I thought that using the JavaScript interpreter would require having some HTML loaded, or that it would be extremely slow. The way that I used it was to construct an object and then use JSON.stringify to return the value to my app, then use JSONKit to convert that in to an NSDictionary.

Using a UIWebView instead of embedding a proper interpreter reminded me of something that [Chris Hanson](http://eschatologist.net/blog/?p=232) said.

> Always use the highest-level abstraction available to you, and drop down to lower-level abstractions when measurement shows that they are needed.

If measurements show me that my approach is using too much memory, or too slow, I will then consider another approach. Until that happens I will use the more straight-forward approach.

