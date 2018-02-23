+++
date = "2018-02-21T11:04:12+01:00"
title = "Code Coverage and Xcode"
tags = [
    "xcode",
    "swift",
    "testing"
]
categories = [
    "xcode",
    "swift",
    "testing"
]

+++
[Unit testing](https://en.wikipedia.org/wiki/Unit_testing) is invaluable in helping us to identify problems early in the development cycle, both in terms of the implementation and the specification. It also facilitates change, providing confidence that any future changes to the tested code won’t break it’s expected behaviour.
 
Key to unit testing is [code coverage](https://en.wikipedia.org/wiki/Code_coverage), which indicates what % of the tested code was executed when the corresponding unit tests were run. Generally, we want to have a high percentage of code coverage. 

Xcode 7 [introduced](https://developer.apple.com/library/content/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/07-code_coverage.html) a code coverage feature which allows us to measure and visualize code coverage.

In this short post, we will learn:

* How to measure code coverage in Xcode
* How to increase code coverage

For the purposes of demonstration, we will look at the unit tests for [BikeProvider](https://github.com/superpeteblaze/BikeProvider), a tiny Swift library I created to provide access to bike share information.

---

## How to measure code coverage in Xcode

To measure and visualize code coverage for a project, follow these steps;

* Enable code coverage date gathering. To do this, go to *Product->Scheme->Edit Scheme...* , and select *Test* from the left hand side menu. Under the *Info* section, check the *Gather coverage data* box.

![Enabling code coverage data gathering in Xcode][codeCoverageXcode_image1.png]
[codeCoverageXcode_image1.png]: /images/posts/codeCoverageXcode_image1.png "Enabling code coverage data gathering in Xcode"

* To run all unit tests for a test target, in this case BikeProvider, first open the *Test navigator* in Xcode’s navigation pane. Then, select the small *play* button next to your test target name.

![Running all unit tests for a test target via Xcode’s test navigator][codeCoverageXcode_image2.png]
[codeCoverageXcode_image2.png]: /images/posts/codeCoverageXcode_image2.png "Running all unit tests for a test target via Xcode’s test navigator"

* Now open the *Report navigator* in Xcode’s navigation pane, and select the Test report from the list. This should open up a list of the tests what were just run, and indicate which tests passed or failed. In this case, they all passed.

![Xcode test report][codeCoverageXcode_image3.png]
[codeCoverageXcode_image3.png]: /images/posts/codeCoverageXcode_image3.png "Xcode test report"

* To view code coverage, select the Coverage tab. Xcode will display the overall code coverage for the framework, and we can expand this to get coverage data on individual files and functions.


![Code coverage, expanded to show coverage for individual files and functions][codeCoverageXcode_image4.png]
[codeCoverageXcode_image4.png]: /images/posts/codeCoverageXcode_image4.png "Code coverage, expanded to show coverage for individual files and functions"

---

## How to increase code coverage

Now that we know how to measure unit test code coverage, lets see how we can increase it.

A good approach to take is to work through all the types in our project, adding tests where possible for each type. 

This can be broken down into two steps;

1. If possible, ensure every public method has at least one corresponding test

*(Note that this might not always be desirable, depending on the testing approach being taken, e.g [behaviour-driven development](https://codeutopia.net/blog/2015/03/01/unit-testing-tdd-and-bdd/))*

We will concentrate on `CityProvider.swift` (whose coverage report is expanded in the previous image), which right now has ~36% code coverage. 

This file consists of 4 public static methods and 1 private static method. It currently has 3 corresponding tests. We should ideally be testing each ‘unit’ of code, so we can first start by adding a test for each for our 4 public methods. 

Once we have at least one test for each method, we run the tests again, and check our coverage;

![Increasing code coverage by adding more unit tests][codeCoverageXcode_image5.png]
[codeCoverageXcode_image5.png]: /images/posts/codeCoverageXcode_image5.png "Increasing code coverage by adding more unit tests"

Awesome, just by adding 1 more test, we have increased code coverage for `CityProvider.swift` to 75%

2. Increase code coverage for each public method by investigating exactly which lines were executed

Now that we have at least some coverage for every method in `CityProvider.swift`, we can take advantage of Xcode’s code coverage measuring capabilities and take a look at exactly what lines of code are being executed by our tests. 

In the coverage report, double-click the file we want to investigate, in this case `CityProvider.swift`. This will open the source editor, but it will look a bit different now that we have enabled code coverage reporting. We now see that on the left, the source editor is highlighting code what has **not** been executed by our tests; i.e it highlights areas that **require** code coverage. Hovering over one of these red-colored areas further highlights the lines which were not executed;

![Unexecuted code highlighted in the Xcode source editor][codeCoverageXcode_image6.png]
[codeCoverageXcode_image6.png]: /images/posts/codeCoverageXcode_image6.png "Unexecuted code highlighted in the Xcode source editor"

Using the above information, we can reason about how we can write further tests to ensure the above code is executed.

In this case, adding only two more test ensures that nearly 100% of the tested method was executed (i.e there is no red! 😃).

![Increasing code coverage by examining the executed lines, and adding unit tests][codeCoverageXcode_image7.png]
[codeCoverageXcode_image7.png]: /images/posts/codeCoverageXcode_image7.png "Increasing code coverage by examining the executed lines, and adding unit tests"

Awesome! We learned how to measure code coverage in Xcode, and we looked at one approach to iteratively increasing our code coverage for a selected file.

We can now apply what we learned to all code that we write. 🎉

---

That’s it! 📱🚀👍🏽
