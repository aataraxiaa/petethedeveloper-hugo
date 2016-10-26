+++
date = "2016-09-24T11:04:12+01:00"
title = "Swift 3 & CocoaPods"
description = "Have a CocoaPod which you updated to Swift 3? Great! Want to submit this updated Podspec to..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

Have a CocoaPod which you updated to Swift 3? Great! Want to submit this updated Podspec to CocoaPods trunk? Awesome! Right, just run pod spec lint, and then…oh wait…it didn’t lint…

## The problem

Yep, you most likely got “ ‘Use Legacy Swift Language Version’ (SWIFT_VERSION) is required…”. Why? Well, Xcode 8 supports multiple Swift toolchains, and SWIFT_VERSION is an Xcode build setting indicating which Swift version to use when building your project. This build setting is required to be configured correctly for targets which use Swift. When CocoaPods is linting your pod it builds it, but this build will fail if the SWIFT_VERSION is not set.

## The solution

You need at least version 1.1.0.rc.2 of CocoaPods. Check your version

You need at least version 1.1.0.rc.2 of CocoaPods. Check your version

```
pod --version
```

Uninstall your current version

```
gem uninstall cocoapods.
```

Install the required version

```
gem install cocoapods -v 1.1.0.rc.2
```

Now, we need to specify our pod should be built with Swift 3, as the default version used is Swift 2.3. Create a .swift-version file in your root project directory

```
touch .swift-version
```

Edit this using your favorite editor

```
3.0
```

Finally, run

```
pod spec lint
```

...watch it succeed, and have a celebratory cup of tea!
