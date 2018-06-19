+++
date = "2018-06-19T11:04:12+01:00"
title = "iOS How-to — Add Adaptive constraints to support a Universal App"
tags = [
    "ios",
    "xcode",
    "interfacebuilder"
]
categories = [
    "ios",
    "xcode",
    "interfacebuilder"
]

+++

Xcode’s interface builder allows us to configure layouts which will automatically change shape and size based on a range of environment variables, such as:

* Device screen size (e.g iPhone or iPad)
* Device Orientation (portrait or landscape)
* Adaptation (multi-tasking on iPad)

In this post, we will learn how to add `NSLayoutConstraints` in interface builder which adapt to changes in these variables, allowing us to build universal apps which run on both iPhone and iPad.

---
## Terminology

First, some useful terminology.

**Trait** — Each of the environment variables described above is a trait; screen size, orientation, and adaptation.

**Device configuration** — A devices configuration is a combination of the three traits mentioned above; screen size, orientation (portrait or landscape), and adaptation (iPads running in multitasking mode display in a split-screen, referred to as an adaptation).

**Size class** — A size class is a trait identifies the relative amount of width (horizontal) or height (vertical) available for a view. Size classes depend on the three traits already described. There are two size classes defined by interface builder; compact (in portrait mode, iPhone width is compact, iPhone height is regular), and regular (in landscape mode, iPhone width is regular, iPhone height is compact).

---
## Adding adaptive constraints

To demonstrate how to add adaptive constraints, we will use the following simple (and slightly contrived!) example of adding a single UIView to another view.

Our goal is to add constraints which adapt and change based on environment variables. This will result in three layout variations:

1. iPhone, portrait — Our added view should be rectangular, pinned to the left side of the device, and centered vertically.

2. iPhone, landscape — Our added view should be square, pinned to the top of the device, and centered horizontally.

3. iPad, portrait or landscape — Our added view should be rectangular, pinned to the right of the device, and centered vertically.

*Note: We are only adapting our constraints for two of the three traits mentioned at the beginning of this post. We are not adapting based on iPad adaptation (mulit-tasking split-screen). In other words, we are dealing only with full screen mode on iPad. This is purely for conciseness. The steps described below can easily be applied to this trait.*

* We start in interface builder with a UIViewController which contains only it’s default UIView

![UIViewController with it's default UIView][adaptiveConstraintsImage1.png]
[adaptiveConstraintsImage1.png]: /images/posts/adaptiveConstraintsImage1.png "UIViewController with it’s default UIView"

* We add a new UIView as a subview, and change its background color to make it easily identifiable

![Subview added as a child view of the default UIView][adaptiveConstraintsImage2.png]
[adaptiveConstraintsImage2.png]: /images/posts/adaptiveConstraintsImage2.png "Subview added as a child view of the default UIView"

* Now, open the device configuration pane by clicking the ‘View as:…’ button at the bottom left of the interface builder.

![Interface builder's device configuration pane][adaptiveConstraintsImage3.png]
[adaptiveConstraintsImage3.png]: /images/posts/adaptiveConstraintsImage3.png "Interface builder's device configuration pane"

* The device configuration pane, shown above, allows us to select a device, orientation, and adaptation (if an iPad device is selected). In the image above, the selected device is an iPhone 8 Plus, and the orientation is portrait. The device configuation pane also shows us the size classes for the selected device configuration. In this case the width size class is compact, and the height size class is regular. This is indicated by the ‘(wC hR)’ text at the top of the device configuration pane.

* We will now add constraints for our first layout variation, iPhone in portrait orientation. Click the ‘Vary for Traits’ button at the bottom right. A popover appears, allowing us to select which size class traits we want to create a layout variation for, e.g width, height, or both. Selecting a size class trait creates a variation based on the size class for the currently selected device configuration. e.g If we select width, the variation will be for all compact widths, as that is the width size class for the currently selected device configuration. In our case, we want to create a variation based on both the width and height size classes of the selected device configuration. Select both of these in the popover.

![Both width and height size classes selected][adaptiveConstraintsImage4.png]
[adaptiveConstraintsImage4.png]: /images/posts/adaptiveConstraintsImage4.png "Both width and height size classes selected"

* Click outside the popover to remove it, and we will now add our constraints for this layout variation. Remember, on iPhone in portrait, our added view should be rectangular, pinned to the left side of the device, and centered vertically. Add a width constraint of 100 to our added view, and a height constraint of 600. Then constrain the view’s leading edge to the safe-area’s leading edge, and constrain the view’s center Y value to the safe-area’s center Y value. Once we have added the constraint, click the ‘Done Varying’ button in the bottom right. The added constraints and resulting layout are shown below.

![Active constraints for our compact width, regular height (portrait iPhone) device configuration][adaptiveConstraintsImage5.png]
[adaptiveConstraintsImage5.png]: /images/posts/adaptiveConstraintsImage5.png "Active constraints for our compact width, regular height (portrait iPhone) device configuration"

* Now we move on to our second layout variation, iPhone in landscape orientation. Our currently selected device configuration is iPhone 8 in portrait, so in the device configuration pane, change the orientation to landscape. Interface builder will update to reflect this change in a number of places. First, the interface builder Canvas updates to show the outline of an iPhone in landscape orientation. Second, the text at the top of the device configuration pane updates to show the new size classes. In this case, the new size classes are regular width and compact height, ‘(wR hC)’. Third, the constraints we previously added for our first device configuration are now shown disabled, appearing faded in the interface builder Document Outline. All of these changes are shown in the image below.

![Interface builder changes caused by a change in device configuration][adaptiveConstraintsImage6.png]
[adaptiveConstraintsImage6.png]: /images/posts/adaptiveConstraintsImage6.png "Interface builder changes caused by a change in device configuration"

* As before, we click the ‘Vary for Traits’ button. In the popover that appears, select both the width and height size classes. This particular configuration applies only to iPhones in landscape orientation. Next, we add the constraints. Remember, for this configuration, our view should be square, pinned to the top of the device, and centered horizontally. Add a width constraint of 300 to our added view, and a height constraint of 300. Then constrain the view’s top edge to the safe-area’s top edge, and constrain the view’s center X value to the safe-area’s center X value. Once we have added the constraint, click the ‘Done Varying’ button in the bottom right. The constraints we added for this configuration will appear active, while the constraints we added for the previous configuration will appear faded, indicating they are inactive. See the image below.

![Active constraints for our regular width, compact height (landscape iPhone) device configuration][adaptiveConstraintsImage7.png]
[adaptiveConstraintsImage7.png]: /images/posts/adaptiveConstraintsImage7.png "Active constraints for our regular width, compact height (landscape iPhone) device configuration"

* Lastly, our third layout variation, iPad in portrait or landscape orientations. But wait… why are we using one layout variation for both landscape and portrait? Well, the reason is that both the portrait and landscape configurations of an iPad have regular width and regular height size classes (ignoring the Adaptation i.e only considering full-screen iPad configurations). This means that any constraint variations we add for regular width and regular height configurations will apply to both portrait and landscape orientations on iPad. Awesome, we continue. Use the device configuration pane to select the device configuration, selecting either portrait or landscape (ignore the ‘Adaptation’ options, as we said we are only considering full-screen iPad configurations), and click the ‘Vary for Traits’ button. As before, select both width and height size classes from the popover, and add constraints which will result in our view being rectangular, pinned to the right of the device, and centered vertically. Once we have added the constraints click the ‘Done Varying’ button. The constraints and the resulting layouts are shown in the images below.

![Active constraints for our regular width, regular height (portrait iPad) device configuration][adaptiveConstraintsImage8.png]
[adaptiveConstraintsImage8.png]: /images/posts/adaptiveConstraintsImage8.png "Active constraints for our regular width, regular height (portrait iPad) device configuration"

![Active constraints for our regular width, regular height (landscape iPad) device configuration][adaptiveConstraintsImage9.png]
[adaptiveConstraintsImage9.png]: /images/posts/adaptiveConstraintsImage9.png "Active constraints for our regular width, regular height (landscape iPad) device configuration"

* We have now added constraints for our three device configurations. To quickly check that the constraints we added are correct, we can again select each device configuration, and observe whether the relevant constraints in the Document Outline are active. Also, it’s worth checking the Canvas as we to note how the layout appears.

---
## Summary

So what have we learned?

* We learned that Xcode and interface builder enable us to adapt our layouts based on a number of **traits**.

* We learned how we can use interface builder to select a particular **device configuration**, and then add constraints which only apply to that configuration.

* We also learned that of particular important when creating such layout variations is the configuriaton’s **size classes**, which are based on width and height.

* Using all of these, we learned how to adapt an example layout based on the device configuration, varying a UIView’s position and size depending on whether the device was iPhone or iPad, and whether the orientation was portrait or landscape.

Awesome! We can now build apps which are universal, running and looking great on both iPhone and iPad. 🎉

The Xcode project for this post is [here](https://github.com/superpeteblaze/BlogPost_AdaptiveConstraints).

---

That's it! 📱🚀👍🏽
