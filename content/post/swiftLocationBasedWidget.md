+++
date = "2016-03-24T11:04:12+01:00"
title = "Swift-ly creating a location-based iOS Today Widget"
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

With the latest update of Bikey, I added a Today Widget (Extension), providing users with quick and easy access to information about their nearest bike station.

![Bikey today extension][swiftLocationBasedWidgetImage1]
[swiftLocationBasedWidgetImage1]: /images/posts/swiftLocationBasedWidgetImage1.png "Bikey today extension"

Creating such a Today Widget, which is based on the user’s location, is accomplished as follows (this assumes that location access for the containing app/widget has been granted);

* Add a Today Widget to our app. This gives us a ready-made view controller which implements the NCWidgetProviding protocol. This protocol provides the ability to customize the appearance and behavior of our Widget. We add the following properties to our view controller;

``` swift
// This is used to let our widget know an update is required or not
private var updateResult = NCUpdateResult.NoData
// We use a lazy instance of CLLocationManager to get the user's location
lazy private var locman = CLLocationManager()
```

* In our viewDidLoad and viewWillAppear functions we set our location manager delegate and request location updates respectively;


``` swift
override func viewDidLoad() {
   super.viewDidLoad()

   locman.delegate = self
}
override func viewWillAppear(animated: Bool) {          
   super.viewWillAppear(animated)
   locman.startUpdatingLocation()
}
```

* In the CLLLocationMangerDelegate function didUpdateLocations,we use our retrieved location data, and set our Widget update result to NewData;


``` swift
func locationManager(manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
   // Do stuff with the retrieved location, update our display and  
   then set our update result to NewData
   // doStuff()
   updateDisplay()
   updateResult = .NewData
}
```
* Finally, in our NCWidgetProviding protocol function widgetPerformUpdateWithCompletionHandler, we use our update result to indicate that a Widget update is required;


```swift
func widgetPerformUpdateWithCompletionHandler(completionHandler: ((NCUpdateResult) -> Void)) {
   // If an error is encountered, use NCUpdateResult.Failed
   // If there’s no update required, use NCUpdateResult.NoData
   // If there’s an update, use NCUpdateResult.NewData
   completionHandler(updateResult)
}
```

That’s it! We can now display location-based data in our iOS Today Widget.

Of course, we can iterate on this and add error handling etc., but this is enough to get us started.

The full Gist can be found [here](https://gist.github.com/superpeteblaze/eca594903710ab032086).
