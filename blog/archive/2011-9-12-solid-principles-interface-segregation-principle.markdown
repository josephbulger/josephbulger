---
author: joseph
comments: false
date: 2011-9-12 12:00:26
layout: post
slug: solid-principles-interface-segregation-principle
title: 'SOLID Principles: Interface segregation principle'
wordpress_id: 722
categories: [interface segragation principle, solid, solid series]
---

Interface Segregation Principle (ISP), focuses on the idea that it's better to have many small specific interfaces that define one concept, then to have one big contract that encompasses many concepts in one.

<!-- more -->I think a prime example of an interface that violates this principle would be [MembershipProvider ](http://msdn.microsoft.com/en-us/library/f1kyba5e.aspx)class which is commonly used in ASP.NET applications. Granted, this isn't technically an interface (it's actually an abstract class), but it demonstrates the principle perfectly. If you've ever tried to make your own custom membership provider, and you've had to implement this beast, you already know the pain involved. There are a ridiculous number of methods involved with this class. There are so many, actually, that the best practice inside many .NET circles has been to simply throw a NotImplementedException for any methods you don't want to spend the time implementing.

The solution would be to actually break up this abstract class into many smaller classes, so the developer could extend the parts that needs to be customized, and leave the rest alone.

Want more info? [Check out this dime cast](http://www.dimecasts.net/Content/WatchEpisode/94).
