--- 
layout: post 
title: "Using blocks for drawing to avoid subclassing in Objective-C" 
---


It is very common for a designer to ask for a 1 pixel tall bar here, or a small gradient there. This is a small request that isn't very hard, right? Subclass `UIView`, override `drawRect` and do the drawing using Core Graphics. But every time you do this you need to add a file to your project. And all this file does is include 1 drawRect method with likely very little code. It personally bothers me when I see lot's of these little classes that don't do very much.

### Block Based Solution ###

Instead of subclassing UIView every time we need to draw something what if we were to have one subclass that allowed us to pass in a block that performed the drawing code. So I've created a class called `DrawView` that does exactly that. It also passes itself and the graphics context since that was going to be needed in every block's implementation so including them as parameters reduced the amount of boiler plate code needed.

<script src="https://gist.github.com/1130891.js?file=DrawView.h"></script>
<noscript><pre>
	typedef void(^DrawView_DrawBlock)(UIView* v,CGContextRef context);

	@interface DrawView : UIView
	@property (nonatomic,copy) DrawableView_DrawBlock drawBlock;
	@end
</pre></noscript>

<script src="https://gist.github.com/1130891.js?file=DrawView.m"></script>
<noscript><pre>
	#import "DrawView.h"

	@implementation DrawView
	@synthesize drawBlock;

	- (void)dealloc
	{
	    [drawBlock release], drawBlock = nil;
	    [super dealloc];
	}

	- (void)drawRect:(CGRect)rect
	{
	    [super drawRect:rect];

	    CGContextRef context = UIGraphicsGetCurrentContext();

	    if(self.drawBlock)
	        self.drawBlock(self,context);
	}

	@end
</pre></noscript>

Using this class in action is easy. Simply instantiate a DrawView object and pass in a drawBlock with some Core Graphics code in there.

<script src="https://gist.github.com/1130891.js?file=gistfile1.m"></script>
<noscript><pre>
	
	DrawView* drawableView = [[[DrawView alloc] initWithFrame:CGRectMake(0,0,320,50)] autorelease];
	drawableView.drawBlock = ^(UIView* v,CGContextRef context)
	{
	    CGPoint startPoint = CGPointMake(0,v.bounds.size.height-1);
	    CGPoint endPoint = CGPointMake(v.bounds.size.width,v.bounds.size.height-1);

	    CGContextSetStrokeColorWithColor(context, [UIColor grayColor].CGColor);
	    CGContextSetLineWidth(context, 1);
	    CGContextMoveToPoint(context, startPoint.x + 0.5, startPoint.y + 0.5);
	    CGContextAddLineToPoint(context, endPoint.x + 0.5, endPoint.y + 0.5);
	    CGContextStrokePath(context);
	};
	[self.view addSubview:drawableView];

</pre></noscript>

Discussion on <a href="http://www.reddit.com/r/programming/comments/jd04w/using_blocks_for_drawing_to_avoid_subclassing_in/">Reddit</a>


