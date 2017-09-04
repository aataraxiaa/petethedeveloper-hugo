+++
date = "2017-09-04T19:38:39+01:00"
title = "iOS Quick tip — How to duplicate a UIButton using Swift"
tags = [
    "swift",
    "ios",
    "uikit"
]
categories = [
    "swift",
    "iOS",
    "UIKit"
]

+++

Recently, a feature required that we create a copy of a `UIButton`. The duplicated `UIButton` needed to look the same as the original, and targets/actions set on the original `UIButton` needed to be set on the duplicate.

On iOS, if an object conforms to the `NSCopying` protocol, we can arbitrarily create copies of it. Unfortunately, `UIButton` does not conform to this protocol. 

Luckily, there is a way around this. Since `UIButton` (which is a `UIView`) can be archived, we can use `NSKeyedArchiver` and `NSKeyedUnarchiver` to create an `NSData` object representing our `UIButton`, and then de-serialize this object to get an identical duplicate of the original `UIButton`.

Then, once we have our identical looking `UIButton`, all we need to do is copy over all the target/action pairs from the original `UIButton` to our duplicate.

Below is a small Swift `UIButton` extension providing an implementation of the above approach.

<script src="https://gist.github.com/superpeteblaze/365fc75de4497a37ce0d69b5bec60a10.js"></script>
