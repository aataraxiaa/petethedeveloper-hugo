+++
date = "2017-01-15"
title = "Swift 3: Migrating a large project "
tags = [
    "swiftlang",
    "ios",
]
categories = [
    "Swift",
    "iOS",
]

+++

The official Swift 3 release is a distant (but fond) memory, yet many of us are only now migrating our code from Swift 2.x to Swift 3. We undertook this migration recently, and in this post I describe what we did, how we did it, what went well, and some things to look out for.

---

## What we did

Migration of a large Swift 2.3 (& Obj-C) iOS project to Swift 3.

## How long it took

3.5 days

## How we did it

We worked off a feature branch, separating out the migration in to (what we thought were logical) parts, with each part corresponding to a pull request. 

In order, the steps (pull requests) were;

**Update Podfile**: We updated our podfile to point to Swift 3 version of libraries we are using.

**Xcode migration tool run on main target #1**: This involved running the ‘Edit -> Convert -> To current Swift syntax’ tool and checking it’s changes. *Target build status= fail*

**Xcode migration tool run on main target #2**: Yes, the above process can, and should be performed again. It will most likely find more to migrate. *Target build status = fail*

**Xcode migration tool run on main target #3**: Yes, we ran it a third time and it found more changes to make. *Target build status = fail*

**Manual changes on main target #1**: We started with manual changes which we classified as ‘not too dangerous’ in terms of breaking our app. *Target build status = fail*

**Manual changes on main target #2**: We then moved on to changes which we classified as ‘scary’. In other words, any changes to code here should be specifically targeted when regression testing post-migration. *Target build status = **PASS!***

**Xcode migration tool run on test target**: Our test target contains far fewer Swift files than our main target. Thus, only one run of the migration tool was required. *Test running status = No*

**Manual changes on test target**: Again, our test target contains far fewer Swift files than our main target, so the number of manual changes required was minimal. *Test running status = **YES!***

---

## What the Xcode migration tool did well

* Simple type substitutions:

```
func lastViewed(Id: NSNumber) -> NSDate

=>

func lastViewed(_ Id: NSNumber) -> Date

```

* Access modifier substitutions:

```
private func loadFromStorage()

=>

fileprivate func loadFromStorage()
```

* Method signature changes (see the [Swift API guidelines](https://swift.org/documentation/api-design-guidelines/)):

```
storage.removeObjectForKey(cacheKey)

=>

storage.removeObject(forKey: cacheKey)

```

* Function argument labels. From the official Swift migration guide:

> The first argument label in functions is now considered API by default.
> The migrator adds underscore labels to preserve the existing API

```
func foo(bar: Int)

=>

func foo(_ bar: Int)
```

* Most cases (see the below section) involving Objective-C id being imported as Swift Any type, rather than AnyObject:

```
override func isEqual(object: AnyObject?) -> Bool

=>

override func isEqual(_ object: Any?) -> Bool
```

* Most cases (see the below section) relating to closure expressions now being non-escaping by default (closures now require an @escaping annotation if a closure argument can escape the function body):

```
func add(withAction action:() -> Void)

=>

func add(withAction action: @escaping () -> Void)
```

---

## Some things to look out for

* We use an internal library, written in Objective-C, which makes use of closures with parameters of the Id and NSError types. With previous versions of Swift, NSError was imported as…NSError, and Id was imported as AnyObject. However, with Swift 3, NSError is imported as Error, and ID is imported as Any. We found that closures which were declared in the importing target, where the closure parameter types were explicitly typed as the old NSError and AnyObject, were overlooked by the Xcode migration tool:

```
// Obj-C definition
typedef void (^ProviderSuccess)(id result);
typedef void (^ProviderError)(NSError *error);

// previously imported as
public typealias ProviderSuccess = (AnyObject) -> Swift.Void
public typealias ProviderError = (NSError) -> Swift.Void

// but now imported to Swift 3 as
public typealias ProviderSuccess = (Any) -> Swift.Void
public typealias ProviderError = (Error) -> Swift.Void

// ...but the following closure definitions in our importing
// library needed to be manually changed from
let providerSuccess = { (response: AnyObject) in ...}                   
let providerError =  { (error: NSError) in ... }

// to =>
let providerSuccess = { (response: Any) in ...}                 
let providerError =  { (error: Error) in ... }
```

* We make extension use of protocols in our code. Some of these protocols declare methods which take closure expressions as parameters. In types which conformed to such protocols, where it was clear from the protocol method definition that a closure parameter escaped, the @escaping annotation was correctly added to the method signature by the migration tool. However…the @escapingannotation was not added to the protocol method signature. Thus, as the method signatures in the protocol and the conforming type did not match, we ended up with build failures due to types conforming to protocols not implementing all required methods:

```
// Original protocol declaration
func delete(onSuccess: ProviderSuccess, onError: ProviderError)
func mark(onSuccess: ProviderSuccess, onError: ProviderError)

// needed to be manually updated to
func delete(onSuccess: @escaping ProviderSuccess, onError: @escaping ProviderError)
func mark(onSuccess: @escaping ProviderSuccess, onError: @escaping ProviderError)
```

---

## Final thoughts

A dreaded task that turned out not to be so dreadful. Approached with the right mindset, i.e a chance to improve our Swift knowledge, means it need not be a chore, but rather an opportunity. 

If you enjoyed this post, please recommend, share etc.
