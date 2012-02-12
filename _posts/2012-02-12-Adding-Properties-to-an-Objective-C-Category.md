--- 
layout: post 
title: "Adding Properties to an Objective-C Category" 
---

Let's say you have a category that needs to store some information. Unfortunately you can't add an instance variable, but you can add something called an associated reference. From the [documentation](https://developer.apple.com/library/ios/#DOCUMENTATION/Cocoa/Conceptual/ObjectiveC/Chapters/ocAssociativeReferences.html):

> Associative references, available starting in Mac OS X v10.6, simulate the addition of object instance variables to an existing class

To create an association you use the `objc_setAssociatedObject` function and to retrieve an association use the `objc_getAssociatedObject` method. 

## Using an associated reference as storage for a property ##

We can use this technique to make a built in class have a property that we want. In this example, I am storing the name of a style that I want to assign to a UIView.

<script src="https://gist.github.com/1810860.js?file=UIViewDHStyleManager.h"></script>

<script src="https://gist.github.com/1810860.js?file=UIViewDHStyleManager.m"></script>

When instances of UIView are released they will also release their associated references.

This technique let's use set this custom property on plain old UIViews. If you only need to do something simple like add a property, this provides a nice alternative to subclassing.

<script src="https://gist.github.com/1810860.js?file=Test.m"></script>