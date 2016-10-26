+++
date = "2016-02-14T11:04:12+01:00"
title = "Referencing a weak ‘self’ in Swift Protocol Extensions"
description = "I recently released Bikey for iOS. It’s an app that helps you to find the nearest city bike station..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

I recently released Bikey for iOS. It’s an app that helps you to find the nearest city bike station, providing you with information such as available bikes/parking spaces, distance to station etc.

![Bikey screenshot][weakSelfSwiftProtocolImage1]
[weakSelfSwiftProtocolImage1]: /images/posts/weakSelfSwiftProtocolImage1.png "Bikey screenshot"

It’s written entirely in Swift, and during it’s development I encountered an interesting problem.

I had created a protocol with an accompianing protocol extension. The extension contained a base implementation of one of my protocol functions. In this function implementation, I was making a network request which needed to call another of the protocol functions in it’s completion closure. Being mindful of retain cycles, I wanted to use a weak reference to self when calling this other protocol function. I have re-created this scenario in the following code;

![Code snippet 2][weakSelfSwiftProtocolImage2]
[weakSelfSwiftProtocolImage2]: /images/posts/weakSelfSwiftProtocolImage2.png "Code snippet 2"

As you can see, trying to create a weak reference to self doesn’t work. But why? Well, the reason is that we can’t create a weak reference to a value type (e.g. Array, String, etc…) because these are structs and not classes. We can only create weak references to class types.

So, how to solve this? It turns out there are a couple to ways to solve this, but the solution I chose was to simply indicate that the protocol is of class type, as follows;

![Code snippet 3][weakSelfSwiftProtocolImage3]
[weakSelfSwiftProtocolImage3]: /images/posts/weakSelfSwiftProtocolImage3.png "Code snippet 3"

This allowed me to safely reference self in my closure, which in turn meant that Bikey finally made it in to the app store.
