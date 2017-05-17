+++
title = "Swift + Firebase Database + UITableView = Paging table view 👍🏼"
author = "Pete Smith"
date = "2017-05-18T19:15:24+01:00"
tags = [
    "firebase",
    "ios",
]
categories = [
    "Firebase",
    "iOS",
]

+++

## Why are you writing about this?

3 reasons:

* UITableViews are an often used iOS UI component. 

* Displaying data retrieved from a remote source (i.e a server-side API) in such a UITableView is a very common scenario. 

* Being able to page this displayed data (i.e fetch one page of data at a time) is a very desirable feature of such displayed data.

## Sure, don’t most RESTful APIs support this?

They sure do. Most RESTful APIs allow the caller to specify a page parameter.

## So what’s the problem?

If you are using a Firebase Database for your remote date storage, and using it’s realtime synchornization feature to keep your local client-side data up to date, you don’t get paging for free.

## What’s Firebase Database?

From the docs:

> The Firebase Realtime Database is a cloud-hosted database. Data is stored as JSON and synchronized in realtime to every connected client. When you build cross-platform apps with our iOS, Android, and JavaScript SDKs, all of your clients share one Realtime Database instance and automatically receive updates with the newest data.

## Ok, so how do we page when using Firebase Database?

*Disclaimer: The solution below works when we know the value of the last element of the last ‘page’ i.e the entry that would be displayed last as we scroll to the very bottom of all our pages*

Firebase database works by allowing us to listen, or subscribe, to a remote database. Then, when the remote database is updated, i.e a new value is added to our remote database, the subscriber is notified of the update and receives the changes. 
We can use a Firebase Database to implement a standard iOS scrolling table of elements. A naive approach might be to just subscribe to all database changes. However this would mean when we initially subscribe to listen for changes, we will receive all elements in the database. This could be a large amount of data, and might mean a slow initial load time 😭
Much better would be to limit the number of database elements we receive on initial subscription, and then ‘page’ through the rest of the elements, fetching a limited number each time.
To help us think this through, let’s imagine we have a remote Firebase Database which has 120+ elements stored in order of creation. We want to page through these elements in starting with the newest, 30 elements at a time, until we reach the last element, which will be the oldest element.

## Oh nice! Please show me the code!

* In the viewDidLoad() method of our view controller, subscribe to listen for database events. We will continue to listen for events as the user interacts with the table, pages etc:

<script src="https://gist.github.com/superpeteblaze/5ec31bb6c733bc58eecb1179676cb624.js"></script>

* Work out when we need to get the next ‘page’ of elements (with UITableView, this is often done in the tableView(:willDisplay:forRowAt:) UITableViewDelegate method. As we know the value of the last element (the oldest element), we can decide if we page or not by comparing the last element we have fetched with the known oldest element:

<script src="https://gist.github.com/superpeteblaze/ee8c16b28f28f2fcd61723e3c8ca447d.js"></script>

* Once we know we need to page, perform a one time query of our remote database, fetching the next set of elements:

<script src="https://gist.github.com/superpeteblaze/1a6333bf009cae87c5e2063cd08c3b50.js"></script>

Awesome! We now have paging working with a Firebase Database.

---

That’s it! 📱🚀👍🏽
