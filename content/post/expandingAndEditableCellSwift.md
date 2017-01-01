+++
date = "2017-01-01T11:04:12+01:00"
title = "Expanding & editable iOS table cell in Swift"
description = "We recently implemented a feature which allows users to enter arbitrary amounts of text..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

We recently implemented a feature which allows users to enter arbitrary amounts of text into a UITextView, which is itself contained in a UITableViewCell. The requirement’s were as follows;

1. The textview should not be scrollable. Rather, the textview should expand/collapse as the size of it’s content increases/decreases. The textview never get’s smaller than a specified base size
2. The cell should expand/collapse as it’s contained textview expands/collapses


Below, I describe how we did it.

---

## The Implementation
The implementation is broken down into 3 steps;

* When the content of the textview changes, calculate the size of the frame required to display all the content. This is calculated in the UITextViewDelegate textViewDidChange(textView:) method using the following;

```
/* Calculate the frame size required to display the contained
content */
let fixedWidth = textView.frame.size.width
let newSize = textView.sizeThatFits(CGSize(width: fixedWidth, height: .greatestFiniteMagnitude))
var newFrame = textView.frame
```

* If the size of the frame required to display the text is different than the current textview frame size, update the size of the textview frame. Remember that we never update the size of the textview frame to be smaller than our specified base frame size;

```
// Our base height
let baseHeight: CGFloat = 50
/* Our new height should never be smaller than our base height, so use the larger of the two */
let height: CGFloat = newSize.height > baseHeight ? newSize.height : baseHeight
newFrame.size = CGSize(width: max(newSize.width, fixedWidth), height: height)
```

* When the frame size of the textview changes, update the size of out table cell to match that of the contained textview size. We use a delegate protocol to tell our UITableViewController to update our cell height. We pass the frame height we calculated, which is then returned in the UITableViewDelegate tableView(:heightForRowAt:) method as the height for our cell;

```
// Save our new height
editingCellHeight = newHeight

// Disabling animations gives us our desired behaviour
UIView.setAnimationsEnabled(false)

/* These will causes table cell heights to be recaluclated,
without reloading the entire cell */
tableView.beginUpdates()
tableView.endUpdates()

// Re-enable animations
UIView.setAnimationsEnabled(true)
```

---

## The Result

![Result][expandingEditableCellImage1]
[expandingEditableCellImage1]: /images/posts/expandingEditableCellImage1.gif "Result"

---

That’s it. 

A full [sample project](https://github.com/superpeteblaze/ExpandingEditableCell) is on git.

Also, here is a [Gist](https://gist.github.com/superpeteblaze/81d4588cfd850b9c1316628993c1f3d3) with a UITextView extension for calculating the new required height. 

If you found this useful, please like, share etc. Thanks!
