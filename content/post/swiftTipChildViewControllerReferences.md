+++
date = "2017-04-04T11:04:12+01:00"
title = "Swift tip: A better way to get references to container child view controllers"
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

[Container Views](https://developer.apple.com/library/content/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html) allow us to add child view controllers via Storyboard.  Child view controllers are convenient as they allow us to break up a complicated view and view controller in to separate, reusable views, each with their own view controller.

As child view controllers are used to separate related functionality, it is common to need references to our child view controllers in our main parent view controller. When child view controllers are added via Storyboard, we don’t implicity get these references. 

A common way to get such references is via the prepare(for:sender:) method of a view controller. When a (parent) view controller has child view controllers, this method will be called when the parent view controller is initialized. Provided we have given each parent-to-child segue an identifier in our storyboard, we can then get our references as follows:

<script src="https://gist.github.com/superpeteblaze/252ca4b7141aec353279e08b2345c2bf.js"></script>

Above, we see that to get a reference for each child view controller involves 2 steps:

1. We first match the segue identifier against a String
2. We then downcast and optionally bind the destination UIViewController to our specific UIViewController subtype

Not too bad, but it does involve 2 steps, and it matches against a String. Perhaps we could do better.

## A better way

Rather than match against a String, and then downcast and optionally bind, how about we switch on the destination UIViewController. We can then match and cast all in one go:

<script src="https://gist.github.com/superpeteblaze/8d630e6621746af01bf46390a1dd3589.js"></script>

---

That’s it!. If you found this useful, please like/share/comment etc.
