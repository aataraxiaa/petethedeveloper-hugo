+++
date = "2016-10-05T11:04:12+01:00"
title = "Swiftly changing a UISearchBar’s clear icon color"
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

*This is entirely based on [this](http://www.tanryan.com/2015/05/changing-icon-colors-on-uisearchbar-and-uitextfield/) Obj-C solution*

We recently needed to customize the color of a UISearchBar’s clear button. In case you are unfamiliar, this is the clear button.

![Searchbar before][uiSearchBarIconColorsImage1]
[uiSearchBarIconColorsImage1]: /images/posts/uiSearchBarIconColorsImage1.png "Searchbar before"

It’s actually part of the UITextField which is embedded in a UISearchBar.

So, how do we change the color of the clear icon? First, we checked the docs. Nothing. Really? Yes, really. Ok…quick search…and…perfect…we found the above post, which is a solution written in Obj-C.

Converting this to Swift gives us the following concise solution;

``` swift
if let searchTextField = searchController.searchBar.valueForKey("_searchField") as? UITextField, let clearButton = searchTextField.valueForKey("_clearButton") as? UIButton {
   // Create a template copy of the original button image
   let templateImage =  clearButton.imageView?.image?.imageWithRenderingMode(.AlwaysTemplate)
   // Set the template image copy as the button image
   clearButton.setImage(templateImage, forState: .Normal)
   // Finally, set the image color
   clearButton.tintColor = .redColor()
}
```

And viola, we have our desired clear icon color.

![Searchbar after][uiSearchBarIconColorsImage2]
[uiSearchBarIconColorsImage2]: /images/posts/uiSearchBarIconColorsImage2.png "Searchbar after"
