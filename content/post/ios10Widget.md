+++
date = "2016-08-16T11:04:12+01:00"
title = "iOS 10: Getting your Widget ready in 2 simple steps"
description = "iOS 10 turns widgets in to super-widgets. They now appear on the Home screen..."
tags = [
    "ios",
]
categories = [
    "iOS",
]

+++

iOS 10 turns widgets in to super-widgets. They now appear on the Home screen (3D touch on the app icon), and on the Search screen (swipe to the right on the Home screen or the Lock screen). But with [great power, comes great responsibility](https://www.youtube.com/watch?v=b23wrRfy7SM).

If your app currently has a widget (i.e Today extension), you need to do a little bit of work to get it ready for it’s big day in September. This is how to get your widget iOS 10 ready in 2 simple steps.

## 1. Get it on the Home screen

Getting your existing Today widget to display on the home screen via 3D touch is easy. Simply add an UIApplicationShortcutWidget entry to your app pList file specifying the bundle identifier of the widget you want to display, for example;

```
<key>UIApplicationShortcutWidget</key
<string>com.yourName.yourApp.yourWidget</string>
```

## 2. A widget people want to look at

As Apple state, our “goal should be to design a widget that people want to add to the Search screen”. With iOS 10, the design of widgets has changed, and we most likely need to update our designs. For example, without changing anything, this is how the current [Bikey](https://itunes.apple.com/ie/app/bikey/id1048962300?mt=8) widget looks on iOS 10;

![Bikey widget][ios10WidgetImage1]
[ios10WidgetImage1]: /images/posts/ios10WidgetImage1.png "Bikey widget"

It looks pretty bad, and clearly needs to be updated. The Human Interface Guidelines are [here](https://developer.apple.com/ios/human-interface-guidelines/extensions/widgets/) to help. In Bikeys case, these two in particular are of interest;

1. “In general, use the system font in black or dark gray for text”.

2. “Avoid customizing the background of a widget.”

Updating our designs based on the above, the post-op Bikey widget now looks perfectly at home with it’s siblings;

![Bikey widget][ios10WidgetImage2]
[ios10WidgetImage2]: /images/posts/ios10WidgetImage2.png "Bikey widget"

That’s it. Just (hopefully) two simple steps to get your app widget ready for it’s big day. Roll on September.
