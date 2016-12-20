+++
date = "2016-12-19T11:04:12+01:00"
title = "iOS custom keyboard dismissal with Swift"
description = "On a recent project, we were charged with implementing a custom, and slightly..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

On a recent project, we were charged with implementing a custom, and slightly complicated, design for dismissing the keyboard. In this post I describe the requirements we needed to meet, some dead-ends, and the implementation we came up with.

---

## The scenario
We have a tableview will some cells. The last cell in the tableview contains a UITextfield which is editable, and the user can enter text in this textfield. 

## The requirements
1. The cell always stays above the keyboard. The bottom of the cell is constrained exactly to the top of the keyboard

2. When the user scrolls the UITableView down, the keyboard moves relative to the scroll position. You could think of this as the last cell pushes or pulls the keyboard when it moves. Requirement #1 should always be met.

![Keyboard scroll down][customKeyboardDismissaliOSSwiftImage1]
[customKeyboardDismissaliOSSwiftImage1]: /images/posts/customKeyboardDismissaliOSSwiftImage1.png "Keyboard scroll down"
Custom keyboard dismissal with the table cell pushing the keyboard as it is scrolled down

![Keyboard scroll up][customKeyboardDismissaliOSSwiftImage2]
[customKeyboardDismissaliOSSwiftImage2]: /images/posts/customKeyboardDismissaliOSSwiftImage2.png "Keyboard scroll up"
When the table cell is scrolled up, the keyboard is pulled back up, but does not move up above it’s original position

---

## The most obvious dead-end

UITableView has a property keyboardDismissMode of type UIScrollViewKeyboardDismissMode. One of these modes is interactive. It sounded promising;

“The keyboard follows the dragging touch offscreen, and can be pulled upward again to cancel the dismiss.”

But...did it meet our requirements?

1. Requirement # 1…**No**
2. Requirement # 2…**Yes**

One out of two doesn’t cut it.

## Other dead-ends

Can the keyboard be manually controlled? Can we move the keyboard ourselves, relative to our scroll position? **No**

What about using constraints? Can we constrain the keyboard to the last cell of the tableview, and iOS will take care of the rest? **No**

---

## The Idea

We could not manually control the keyboard position, we could just show/hide it at will. However, what about making it appear that we were manually controlling the position of the keyboard? Yes. This sounded promising. We broke down how this might work;

* When the user enters begins editing. We simply show the keyboard. This will happen automatically when the UITextField becomes the first responder.

* To get the last UITableViewCell in the correct position, right above the keyboard, we change the UITableView content inset when the keyboard appears. The inset will be equal to the height of the keyboard. 

* The **magic**. When the user is editing, and then scrolls down to dismiss the keyboard, we take a snapshot of the keyboard. We can then dismiss the keyboard immediately, replacing it with the ‘fake’ keyboard image. Now as the user scrolls up/down, we can move the keyboard image up/down with it.

## The Implementation

* We register for keyboard notifications, enabling us to respond to keyboard-related events;


```
// Register for keyboard notifications
NSNotificationCenter.defaultCenter().addObserver(self, selector: #selector(ZDSCompositionPresenter.keyboardWillShow(_:)), name: UIKeyboardWillShowNotification, object: nil)
```

* The user begins editing by selecting the last cell, causing the contained UITextView to become the first responder. As we have registered for keyboard notifications, we can respond to the keyboard being shown by setting the UITableView content inset to the height of the keyboard;


```
guard let userInfo = notification.userInfo, keyBoardValueEnd = userInfo[UIKeyboardFrameEndUserInfoKey] as? NSValue else {
   return
}
// Get the keyboard height
let keyboardRect = keyBoardValueEnd.CGRectValue()
let keyboardHeight = keyboardRect.height
// Set the table content inset to the keyboard height
tableView.contentInset.bottom = keyboardHeight
```

* Implement the UIScrollView delegate method scrollViewDidScroll:. Here, based on the UIScrollView/UITableView contentOffset, we calculate if the user scrolled up or down. If the user scrolled down, they are beginning to dismiss the keyboard. We immediately capture a snapshot of the entire screen contents using the following UIImage extension;


```
/**
Captures a snapshot image of the entire screen
- returns: Image of the current screen content
*/
static func screenSnapshot() -> UIImage? {
  let imgSize = UIScreen.mainScreen().bounds.size
  UIGraphicsBeginImageContextWithOptions(imgSize, false, 0)

  for window in UIApplication.sharedApplication().windows {
    if window.screen == UIScreen.mainScreen() {
      window.drawViewHierarchyInRect(window.bounds, afterScreenUpdates: true)
    }
  }

  let img = UIGraphicsGetImageFromCurrentImageContext()
  UIGraphicsEndImageContext()
  return img
}
```

* As the above captures the entire window contents, we then change the frame of the captured image based on a stored keyboard frame value, so that it only contains the keyboard portion of the image;


```
if let kbFrame = keyboardFrame {
   let frame = CGRect(origin: CGPoint(x: 0, y: 0), size: kbFrame.size)
   keyboardSnapshotImageView.frame = frame
   keyboardSnapshotImageView.heightAnchor.constraintEqualToConstant(kbFrame.height).active = true
   keyboardSnapshotImageView.widthAnchor.constraintEqualToConstant(kbFrame.width).active = true
   keyboardSnapshotImageView.image = keyboardSnapshot
}
```

* We add our captured snapshot image of the keyboard as a subview to the view which contains our UITableView, and constrain the keyboard snapshot view to the bottom of it’s superview. We also remove the actual keyboard without animation, so that it immediately disappears;

```
if let tableSuperView = tableView.superview {
  tableSuperView.addSubview(keyboardSnapshotImageView)

  // Add constraint and save the reference
  keyboardBottomConstraint = NSLayoutConstraint(item: keyboardSnapshotImageView, attribute: .Bottom, relatedBy: .Equal, toItem: tableSuperView, attribute: .Bottom, multiplier: 1.0, constant: 0.0)

  tableSuperView.addConstraint(keyboardBottomConstraint!)

  // Remove the actual keyboard without animation
  UIView.setAnimationsEnabled(false)

  compositionCell?.firstResponder = false
  UIView.setAnimationsEnabled(true)
}
```

* As we have saved a reference to our keyboard bottom constraint, we can now move the keyboard snapshot image up/down in our scrollViewDidScroll: delegate method based on the change relative to the starting position of the keyboard before the user scrolled;


```
// change is calculated based on our original keyboard
// position before dismissal
keyboardBottomConstraint?.constant = change
```

---

## The result

The implementation satisfies both requirements;

1. The cell always stays above the keyboard. The bottom of the cell is constrained exactly to the top of the keyboard
2. When the user scrolls the UITableView down, the keyboard moves relative to the scroll position.


![Result][customKeyboardDismissaliOSSwiftImage3]
[customKeyboardDismissaliOSSwiftImage3]: /images/posts/customKeyboardDismissaliOSSwiftImage3.gif "Result"

## * The caveat
Custom keyboard types. The method of capturing the screen contents by drawing what is in the window view hierarchy does not work for custom keyboards such as Google’s Gboard.
