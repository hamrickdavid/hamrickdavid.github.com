--- 
layout: post 
title: "Adding Properties to an Objective-C Category - Revisted" 
---

In my [last post](/2012/02/12/Adding-Properties-to-an-Objective-C-Category.html) I wrote about adding properties to a category in objective-c. I have ended up using this feature quite a bit recently so I wrote a macro that will add the appropriate methods.

Before, when I wanted to add a category I would have to manually add the setter and getter.

<noscript>
	static char kDHStyleKey;

	@interface UIView (DHStyleManager)
	@property (nonatomic, copy) NSString* styleName;
	@end

	@implementation UIView (DHStyleManager)
	@dynamic styleName;
	- (void)setStyleName:(NSString *)styleName
	{
		objc_setAssociatedObject(self, &kDHStyleKey, styleName, OBJC_ASSOCIATION_COPY);
	}

	- (NSString*)styleName
	{
		return objc_getAssociatedObject(self, &kDHStyleKey);
	}

	@end
</noscript>
<script src="https://gist.github.com/2820879.js?file=Example1.m"></script>
	
Now, all I need to do is use the macro to create the getters and setters.

<noscript>
	@interface UIView (DHStyleManager)
	@property (nonatomic, copy) NSString* styleName;
	@end
	
	@implementation UIView (DHStyleManager)
	ADD_DYNAMIC_PROPERTY(NSString*,styleName,setStyleName);
	
	@end
</noscript>
<script src="https://gist.github.com/2820879.js?file=Example2.m"></script>

All you need to do to use this macro is put this in a header file.

<noscript>
	#define ADD_DYNAMIC_PROPERTY(PROPERTY_TYPE,PROPERTY_NAME,SETTER_NAME) \
	@dynamic PROPERTY_NAME ; \
	static char kProperty##PROPERTY_NAME; \
	- ( PROPERTY_TYPE ) PROPERTY_NAME \
	{ \
		return ( PROPERTY_TYPE ) objc_getAssociatedObject(self, &(kProperty##PROPERTY_NAME ) ); \
	} \
	\
	- (void) SETTER_NAME :( PROPERTY_TYPE ) PROPERTY_NAME \
	{ \
		objc_setAssociatedObject(self, &kProperty##PROPERTY_NAME , PROPERTY_NAME , OBJC_ASSOCIATION_RETAIN); \
	} \
</noscript>
<script src="https://gist.github.com/2820879.js?file=Example3.m"></script>