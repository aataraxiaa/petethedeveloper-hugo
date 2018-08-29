+++
date = "2018-08-29T11:04:12+01:00"
title = "Xcode 10's build system and Code Generation (R.swift)"
tags = [
    "ios",
    "xcode"
]
categories = [
    "ios",
    "xcode"
]

+++

Xcode 10 includes a [new build system](https://developer.apple.com/xcode/whats-new/), enable by default, which improves build preformance. This is a very welcome improvement.
[R.swift](https://github.com/mac-cain13/R.swift) is an open source library which makes is easier, and type-safe, to use resources such as images, fonts, and segues. It's a really cool library, and we have used it successfully for quite a while now.

However…

---
## The Problem

When building a project which uses R.swift with Xcode 10, the build will fail with the following error:

`error: Build input file cannot be found: /LOCATION/R.generated.swift`

## Why This Happens

R.swift is integrated in to projects using an Xcode build script phase. This phase generates a Swift file, `R.generated.swift` which will be compiled when the project is built.
The build fails because Xcode 10, with it's new parallelizing build system, is trying to locate the R.generated.swift file **before** it has been created.

## The Solution

Xcode 10 allows us to specify an output file for a build script phase. We can add our `R.generated.swift` file as an output file, as follows:

![Adding our R.generated.swift file as an output file in our Xcode build script phase][xcode10CodeGenerationImage1.png]
[xcode10CodeGenerationImage1.png]: /images/posts/xcode10CodeGenerationImage1.png "Adding our R.generated.swift file as an output file in our Xcode build script phase"

This results in a successful build. Awesome! 🎉

---

That's it! 📱🚀👍🏽
