+++
date = "2016-06-28T11:04:12+01:00"
title = "Swift and State management"
description = "I was recently refactoring a UIViewController which had a large number of states. The view..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

I was recently refactoring a UIViewController which had a large number of states. The view controller’s state could transition to one or more other states, depending on a number of variables (do we have the user’s current location, have we successfully retrieved data, is this a fresh launch, or was the app backgrounded). When the state did change, I needed to perform certain actions to configure our view controller.


It’s quite a common scenario, and not overly complicated, but I thought Swift would help come up with a clean solution. Here is the approach I took.

Swift [enums](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Enumerations.html) provide our states. This gives us a type-safe way to work with a finite set of states.

```swift
enum State {
case initial
case launched
case launchingWithLocation
case launchingWithoutLocation
case running
case backgrounded
case fromBackground
case fromBackgroundWithLocation
case fromBackgroundWithoutLocation
}
```

To control state transitioning, we use a method which takes a number of parameters. These parameters are the variables that help decide which state we want to transition to. As not all states will care about the value of each parameter, we give them default values. We use a Swift tuple to create a composite state of our current state and our parameters. We then use a switch statement to change state, depending on the tuple. If we don’t care about the value of one of our tuple elements, we simply ignore it using _ in a case statement.

```swift
private func changeState(hasLocation: Bool = true, isBackgrounding: Bool = false) {
    let compositeState = (state, hasLocation, isBackgrounding)
    switch compositeState {
    case (.launched, true, false):
       state = .launchingWithLocation
    case (.launched, false, false):
       state = .launchingWithoutLocation
    case (.launched, _, true):
       state = .fromBackground
    case (.launchingWithLocation, _, false):
       state = .running
    case (.running, _, true):
       state = .backgrounded
    case (.backgrounded, _, _):
       state = .fromBackground
    case (.fromBackground, true, _):
       state = .fromBackgroundWithLocation
    case (.fromBackground, false, _):
       state = .fromBackgroundWithoutLocation
    case (.fromBackgroundWithLocation, true, _):
       state = .running
    default:
       break
    }
}
```

Lastly, to perform certain actions when our state does change, we use the extremely useful didSet property observer. [Property observers](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Properties.html#//apple_ref/doc/uid/TP40014097-CH14-ID262) observe a property’s value, and allow us to respond to changes of the value. The didSet property observer is called immediately after a new value is stored. We also make use of the oldValue parameter. This is a constant parameter automatically passed to didSet which contains the old property value. In our case, we use the oldValue property to perform different actions for different states depending on what the old state value was.

```swift
private var state = State.initial {
    didSet {
       switch state {
       case .launched:
          // Do launch specific stuff, like get user's location
          break
       case .launchingWithLocation:
          // Yeah! We have location, do stuff like make API calls
          // We can also act depending on how we got to this state
          switch oldValue {
          case .launched:
             // Do launch with location specific stuff
             break
          case .fromBackground:
             // Do from background with location specific stuff
             break
          default:
             break
          }
       case .launchingWithoutLocation:
          // Sad, no location, but do some stuff anyway  
          break
       case .running:
          // Woohoo, we are fully running so do cool stuff here
          break
       case .backgrounded:
          // Backgrounded, do stuff like stop location updates
          break
       case .fromBackground:
          // Coming from background, do stuff like re-start location
          break
       case .fromBackgroundWithLocation:
          // Yeah! Back AND have location, make API calls
          break
       default:
          break
       }
    }
}
```

That’s it! Gist [here](https://gist.github.com/superpeteblaze/4114e34bdff308f49b0fa6ffddb65095).
