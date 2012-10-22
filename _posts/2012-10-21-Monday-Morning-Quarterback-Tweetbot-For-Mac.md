--- 
layout: post 
title: "Monday Morning Quarterback - Tweetbot for Mac" 
---

Last week, Tapbots released Tweetbot for Mac. They surprised many by charging $20, which many considered to be a high price for a Twitter client. Tapbots [wrote a blog post](http://tapbots.com/blog/news/tweetbot-mac) that explained their pricing decision. Twitter [recently changed](https://dev.twitter.com/blog/changes-coming-to-twitter-api) their API policies to limit the number of individual user tokens that an application can have. Once a new application has 100,000 users they have to obtain permission to obtain more user tokens.

This means that Tweetbot for Mac has a constrained supply. The Econ 101 level theory of supply and demand would say that holding everything else equal, if supply is decreased, then the price will increase and a lower quantity will be sold.

Many users say that the price is too high for them, and that they won't pay $20 for a Twitter client. This is exactly what you would expect to happen. If everybody was willing to pay $20, then Tapbots would sell their 100,000 copies relatively quickly and there would be a shortage for others users who wanted to purchase it, perhaps at a higher price.

## Why is Tweetbot being sold on the Mac App Store? ##

Applications for OS X have an advantage that iOS application do not have. You can sell them directly from your website instead of being forced to use the Mac App Store (MAS). This means that you don't have to pay 30% of your revenue to Apple, but only a small percentage to a payment processor.

First let's make a few assumptions about Tweetbot's userbase.

1. There is no software piracy (There is)
2. Each customer only uses one user token (They won't)
3. All users of the beta versions will purchase the full version (They won't)

Tweetbot currently costs $20 per customer, with a maximum of 100k customers. $20/user x 100,000 users = $2,000,000. However, Apple charges a 30% tax for being in the App Store which would result in a theoretical maximum revenue of $1,400,000.

**Is being on the Mac App Store worth up to $600,000 in lost revenue?**

There are of course several reasons why being on the Mac App Store makes sense. 

* Apple handles all of the payment processing for you.
* Apple handles the update mechanisms for you
* Being featured or on the top charts can be a great way for new customers to discover your app
* There are some APIs that are not available for non-MAS apps eg. Notifications, iCloud sync

I personally do not think that any of these factors are important enough to give up that much revenue. Tapbots currently does not have a payment processor set up, but could set it up with [Stripe](https://stripe.com/) or a similar service with a few days work. Similarly, the update mechanism could have been solved with a framework like [Sparkle](http://sparkle.andymatuschak.org/). 

Being featured on the MAS can be big win for developers, and Tweetbot is currently the #2 application (behind Mac OS X Mountain Lion). The crucial question is whether or not Tweetbot would sell out it's 100,000 tokens without having the Mac App Store. Perhaps it would take 6 weeks instead of 4 weeks. But having hundreds of thousands of dollars in additional revenue seems like it would be worth the wait.

Ultimately Tapbots knows what is best for them, and they decided that being on the MAS was a good decision. Hindsight is 20/20 and things would be much different if they had launched and nobody wanted to buy their app if it wasn't on the MAS. Tweetbot is interesting because it has a limited supply, something that software rarely has. Do the normal rules still apply? We'll have to wait and see.

