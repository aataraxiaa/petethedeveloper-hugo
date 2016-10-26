+++
date = "2016-08-09T11:04:12+01:00"
title = "Migrating an App to Swift 3"
description = "I recently migrated Bikey to Swift 3…and I actually (reasonably) enjoyed the process. I..."
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

I recently migrated [Bikey](https://itunes.apple.com/ie/app/bikey/id1048962300?mt=8) to Swift 3…and I actually (reasonably) enjoyed the process. I based my migration strategy on Jesse Squires excellent [post](http://www.jessesquires.com/migrating-to-swift-3/), and the official migration [guide](https://swift.org/migration-guide/).

Here I describe a possible approach to Swift 3 app migration which you may find useful, based on how it went for me.

## The Approach

We approach the migration process in two main stages; dependency migration and main app migration.

### Dependencies

1. Run the Xcode Swift Migration Assistant to migrate our own frameworks/pods that we use. Ensure all framework tests are passing.

2. Check third-party frameworks for available Swift 3 branches. Open PRs for third-party frameworks that do not yet have Swift 3 branches. As above, migrate these using the Xcode Swift Migration Assistant. If PRs cannot be opened (e.g no Github source access and no response from current maintainers), fork repositories and migrate. Ensure all framework tests are passing.

3. Update the app podfile to use the Swift 3 branch of all Swift frameworks, using the ‘:branch => …’ podfile specifier. If we needed to fork a repository we also update the podfile to point to the correct repository location.

### The App
1. Run the Xcode Swift Migration Assistant to migrate each app target to Swift 3. For example, start with the main app target, then any extension targets, then test targets etc. It’s easier to do this one target at a time, rather than deal with the entire code base at once.

2. Fix any issues the Swift Migration Assistant missed. It will miss a few.

3. Optionally manually refactor code according to the Swift API guidelines. Although no necessary to get your app building, I thoroughly recommend this. The guidelines make sense, and we all love clean code.

4. Ensure all app tests are passing.

## Notes
* The Xcode Swift Migration Assistant is not perfect, it misses things, but in general it works well.

* Migrate using the latest Xcode beta (beta 5), to avoid going from beta 2 to beta 3, beta 3 to beta 4 etc.

* When presented with the Swift Migration Assistant code changes, read through all of them. It’s good to double check and it’s an easy way to get your mind in Swift 3 mode.

* I encountered an issue where third-party framework A had a dependency on third-party framework B, which had not yet been migrated. I needed to fork B, migrate it, and then override B’s source location in my own podfile. See [this](https://github.com/cocoapods/cocoapods/issues/3901) thread for more.

That’s it. Happy migrating!
