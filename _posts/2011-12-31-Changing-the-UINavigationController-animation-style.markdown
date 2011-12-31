--- 
layout: post 
title: "Changing the UINavigationController animation style"
---

I was working on project recently where I had the requirement to change between two sections of an application by fading between them. Since I needed to be able to go back to the first section from second section it was a prime candidate for a `UINavigationController`. I also wanted to have the ability to unload the first section if we were in a low memory environment (something that happens automatically with `UINavigationController`)

Changing the style of the animation turned out to be quite simple. All you need to do is disable animation on the `pushViewController:animated:` method and attach your own CAAnimation.

<noscript>
	CATransition *transition = [CATransition animation];
	transition.duration = 0.3f;
	transition.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
	transition.type = kCATransitionFade;
	[self.navigationController.view.layer addAnimation:transition forKey:nil];
	[self.navigationController pushViewController:nextVC animated:NO];
</noscript>
<script src="https://gist.github.com/1545353.js?file=demo.m"></script>

You can pop using the same method by replacing the last method with `popViewControllerAnimated:`

This demo video shows this trick switching back and forth between two view controllers.

<iframe src="http://player.vimeo.com/video/34415080?title=0&amp;byline=0&amp;portrait=0" width="400" height="576" frameborder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

This little trick saved a ton of time re-implenting UINavigationController just to change the animation style. Since I started using it in multiple places I ended up putting it in a category.

<noscript>
	@interface UINavigationController (Fade)

	- (void)pushFadeViewController:(UIViewController *)viewController;
	- (void)fadePopViewController;

	@end
</noscript>
<script src="https://gist.github.com/1545353.js?file=UINavigationController+Fade.h"></script>

<noscript>
	@implementation UINavigationController (Fade)

	- (void)pushFadeViewController:(UIViewController *)viewController
	{
	    CATransition *transition = [CATransition animation];
	    transition.duration = 0.3f;
	    transition.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
	    transition.type = kCATransitionFade;
		[self.view.layer addAnimation:transition forKey:nil];

		[self pushViewController:viewController animated:NO];
	}

	- (void)fadePopViewController
	{
		CATransition *transition = [CATransition animation];
	    transition.duration = 0.3f;
	    transition.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseInEaseOut];
	    transition.type = kCATransitionFade;
		[self.view.layer addAnimation:transition forKey:nil];
		[self popViewControllerAnimated:NO];
	}

	@end
</noscript>
<script src="https://gist.github.com/1545353.js?file=UINavigationController+Fade.m"></script>
