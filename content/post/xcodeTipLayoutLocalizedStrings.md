+++
date = "2018-04-07T11:04:12+01:00"
title = "Xcode Quick Tip – Testing your layout for localization"
tags = [
    "xcode",
    "testing",
    "localization"
]
categories = [
    "xcode",
    "testing",
    "localization"
]

+++

The Zendesk [Support](https://itunes.apple.com/us/app/zendesk-support/id1174276185?mt=8) app supports a number of languages, meaning that the strings we display in the app change, depending on the device language set.

Recently we encountered buggy UI component positioning when the device language was changed from the default language (English). This is shown below with Dutch.

![UI component (UISwitch) incorrectly positioned due to ambiguous constraints][xcodeTipLayoutLocalizedStringsImage1.png]
[xcodeTipLayoutLocalizedStringsImage1.png]: /images/posts/xcodeTipLayoutLocalizedStringsImage1.png "UI component (UISwitch) incorrectly positioned due to ambiguous constraints"

Due to some ambiguous auto-layout constraints, the UILabel containing the string increases in size when the string grows in length, causing the next UI element, a UISwitch, to be pushed outside the bounds of the containing view.

We needed to repeatedly reproduce the issue as we investigated and resolved this bug. Rather than changing the device language, which is cumbersome, we stumbled upon a testing method known as [pseudolocalization](https://en.wikipedia.org/wiki/Pseudolocalization).

Pseudolocalization is a testing method used to test internationalization which involves replacing the default language strings with altered versions. For exmaple, we might replace “Login” with “Login Login Login Login”.

We then discovered a very useful option in the Xcode scheme editor which enabled us use pseudolocalization to test our layout, shown below.

![Xcode scheme setting allowing us to use pseudolocalization to test layout][xcodeTipLayoutLocalizedStringsImage2.png]
[xcodeTipLayoutLocalizedStringsImage2.png]: /images/posts/xcodeTipLayoutLocalizedStringsImage2.png "Xcode scheme setting allowing us to use pseudolocalization to test layout"

Using this setting, all strings in the app are replaced with pseudo-translations consisting of the original string repeated twice. Shown below is the app running with this setting enabled, which results in the default strings being replaced by pseudo-translations.

![Pseudo-translations being displayed, highlighting an auto-layout bug][xcodeTipLayoutLocalizedStringsImage3.png]
[xcodeTipLayoutLocalizedStringsImage3.png]: /images/posts/xcodeTipLayoutLocalizedStringsImage3.png "Pseudo-translations being displayed, highlighting an auto-layout bug"

Using the pseudolocalization Xcode scheme setting, we were able to reproduce the reported issue quickly and implement a fix! 🎉 Our updated UI is shown below.

![Pseudo-translations being displayed with the auto-layout bug fixed!][xcodeTipLayoutLocalizedStringsImage4.png]
[xcodeTipLayoutLocalizedStringsImage4.png]: /images/posts/xcodeTipLayoutLocalizedStringsImage4.png "Pseudo-translations being displayed with the auto-layout bug fixed!"

---

## In Summary
* Pseudolocalization is a testing method used to test internationalization which involves replacing the default language strings with altered versions.

* Xcode includes a scheme setting which allows us to test our UI layouts using pseudolocalization.

* Pseudolocalization should be part of normal feature development.
