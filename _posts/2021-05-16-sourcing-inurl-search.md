---
layout: post
title: "Sourcing with inurl to search insights"
description: "Sourcing with inurl. Search with inurl to research data and insights."
author: tom
image: assets/images/1.jpg
tags: [sourcing, research]
---

Sourcing with inurl. Search with inurl to research data and insights.

Sourcing with `inurl` is a basic foundational sourcing technique. It unlocks so many secrets of the internet. In this article I am going to focus using inurl to crawl mail archive websites.


## Basics of sourcing with inurl

Go to google and type this:

	inurl:email

The results might depend on your location but most likely you get the same.

The first result says `Email - Wikipedia` in which the url has the word `email`.

The second result says `Sign in - Google Accounts` and the url also has the word `email`.

The third result says `Webmail` with a url `email.secureserver.net`.

This strategy of "word in a url" is often used for SEO. One of the results says `Email marketing made simple` and the url contains this string `beginners-guide-to-email-marketing` with the word `email` in the url.


## Mail Archives

That was all the theory you have to know about `inurl`. Now let's find the secrets of the internet.

Let me introduce you to my old friend `GNU Mailman`. It's an open source free software for managing mailing lists. These mailing lists are often published on the web. I hope I caught your attention by now.

Here is the official website of [GNU Mailman](https://list.org/). At the bottom it links to a page called [The Mail Archive](https://www.mail-archive.com/). It says `Search the archived list`.

Search for `python`.

The results show `1 - 100 of 5059213 matches` and the top of the page shows links to a few mailing lists. One of them says `code-quality`. Let's explore.

It shows a list of `Messages by Thread` and a search box. Can I type `gmail`?

No results.

Let's go back and click on a thread `Disable QA for part of file`.

It shows the back and forth conversation about this topic. You don't see the email addresses but you can see their names. If you click on a name, it will show you all threads sent by that person.

I clicked on that person and clicked on one of their threads. I don't quite understand the context of their conversation but I see part of their email address.

The replies back and forth show part of the `from` header. It has their full name, their beginning and end of their email address, such as: `2017-09-08 First Last (first...@yahoo.com)`.


Let's go back to the `Mail Archive` search box and type `kubernetes`. Oh yeah everyone is looking for someone that knows this. I studied this a bit. It's not rocket science. Don't know what the big fuss is about it.

I clicked on the first link. Another thread, another rabbit hole.

It says that they fixed some documentation about Kubernetes.

It has the full name and part of the email of the person that started the thread.

The url has these words `commits`, `camel.apache.org`. Not sure what they mean. Let's see.


## Google search: inurl:commits inurl:apache

It shows 258,000 results.

The first one is from `github`. OK, I assume a lot of results are going to come from Github if they use the word `commits` in the url. Let's remove github. This shows 121,000 results.

	inurl:commits inurl:apache -github.com

Now all the results have the word `apache` in the url. It looks like they are all the same website `lists.apache.org` and many say `Pony Mail` or `Apache Mail Archives`.

I opened the first result just to see. Looks like the threads are from a bot.

The top left of the website has a logo that says `Pony Mail`. Clicked there and it says `Welcome to Pony Mail...pick a mailing list domain`. Mmm so many, which one to pick.

It has a directory so I clicked on the letter `S`. I am looking to see if there is a `Spark` mailing list.

Yes there is one. It's full of thread messages.

I clicked on the thread with the most replies.

Similar content with back and forth messages, including the header of the author that sent the message. It has their full name and partial email address.

The full address is displayed when someone replies and part of the previous content is shown. Such as this:

	from: Ned Flanders <ned.fl...@gmail.com>
	date: 2021/03/18
	I know right?

	2021/03/17 Homer Simpson <homer.simpson@gmail.com>
	Hey Spark is awesome

This website has so many lists. It shows `665 active lists` with almost 5,000 people online.


## inurl:lists

From our previous experiment I saw that the Apache website has the word `lists` in the url.

Try these experiments:

* inurl:lists
* inurl:lists inurl:archives
* inurl:lists gmail
* inurl:lists kubernetes

Let's try this Google search `inurl:lists kubernetes`

There are mixed results, not all of them are mailing lists but we can filter the noise. Change search to `inurl:lists kubernetes -stackoverflow.com -github.com`.

One of the results is the mailing list for `Cloud Native Computing Foundation`. Mixed results in this one.

Let's modify the search to `inurl:lists kubernetes (archive | mailing)`

Found a result that says `Mailing Lists - Jenkins`. Jenkins is a technology used by DevOps. Navigating this result I found the mailing list is kept on Google Groups using this url `groups.google.com/g/jenkinsci-users`. This one has 19,000 threads.

Similar to other results, each thread shows a conversation between users, and it displays their full name, followed by either a partial email or full email.


## Keep searching with inurl

Keep trying out queries with `inurl`. I found a mailing list from MIT, an old list from Apple developers, and many other interesting results.

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)