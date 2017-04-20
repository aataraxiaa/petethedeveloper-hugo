+++
date = "2017-04-20T09:30:21+01:00"
title = "iOS — Identifying Memory Leaks using the Xcode Memory Graph Debugger"
tags = [
    "xcode",
    "ios",
]
categories = [
    "Xcode",
    "iOS",
]

+++

In this short post I describe,
* What the Xcode memory graph debugger is
* How to use it, and some tips
* It’s pros/cons

---

## What is it
TL;DR: In one sentence, the memory graph debugger helps to answer the following question: **Why does an object exist in memory?**

The Xcode memory graph debugger helps find and fix retain cycles and leaked memory. When activated, it pauses app execution, and displays objects currently on the heap, along with their relationships and what references are keeping them alive.

---

## How to use it
3 quick steps to identifying retain cycles and memory leaks:

* 1. Opt in to stack logging integration via the Xcode scheme editor, as follows:

![EnablingMallocLogging][retainCyclesMemoryLeaksImage1]
[retainCyclesMemoryLeaksImage1]: /images/posts/retainCyclesMemoryLeaksImage1.png "Enable Malloc stack logging for live allocations"

Note that we only enable logging of ‘Live Allocations’. This has a lower overhead than ‘All Allocations’ when debugging, and is all we need to identify retain cycles and leaks.

* 2. Perform whatever app actions you want to analyse (the actions you suspect are causing retain cycles and leaks), and enter memory graph debugging mode by selecting its debug bar button:

![Thememorygraphdebuggingbutton][retainCyclesMemoryLeaksImage2]
[retainCyclesMemoryLeaksImage2]: /images/posts/retainCyclesMemoryLeaksImage2.png "The memory graph debugging button"

* 3. The memory graph debugger pauses app execution and displays the following:

![Xcodememorygraphdebuggingmode][retainCyclesMemoryLeaksImage3]
[retainCyclesMemoryLeaksImage3]: /images/posts/retainCyclesMemoryLeaksImage3.png "Xcode memory graph debugging mode"

On the left, the debug navigator displays the app’s heap contents

Selecting a type/instance from the debug navigator shows the instances references in the centre pane

Selecting an instance in the centre reference pane displays general object memory information and the allocation backtrace in the right-hand inspector pane

Leaks are displayed in the debug navigator as follows:

![Leaksdisplayedinthedebugnavigator][retainCyclesMemoryLeaksImage4]
[retainCyclesMemoryLeaksImage4]: /images/posts/retainCyclesMemoryLeaksImage4.png "Leaks displayed in the debug navigator"

---

## Tips

* #1: To help identify memory leaks, we can filter the heap contents to only show leaks using the following:

![Filteringformemoryleaks][retainCyclesMemoryLeaksImage5]
[retainCyclesMemoryLeaksImage5]: /images/posts/retainCyclesMemoryLeaksImage5.png "Filtering for memory leaks"

* #2: The runtime issue navigator is also useful, displaying the total number of leaks identified:

![Lotsof(apparent)memoryleaks!][retainCyclesMemoryLeaksImage6]
[retainCyclesMemoryLeaksImage6]: /images/posts/retainCyclesMemoryLeaksImage6.png "Lots of (apparent) memory leaks!"

---

## The good and the bad

* **Good:** We can get lucky and find some easy to identify leaks (simple retain cycles). For example — An object capturing itself in a closure property. This is easily fixed using a closure capture list to weakly capture the reference.

* **Bad:** False positives (is it a leak?). For example — When creating a UIButton object and adding it to a UIToolBars items array, we have seen it identified as a memory leak but we fail to see why.

---

## Useful links

* https://developer.apple.com/videos/play/wwdc2016/410/
* https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/debugging_with_xcode/chapters/special_debugging_workflows.html#//apple_ref/doc/uid/TP40015022-CH9-DontLinkElementID_1
* https://useyourloaf.com/blog/xcode-visual-memory-debugger/

---

That's it! 📱🚀👍🏽
