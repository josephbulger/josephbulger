---
author: joseph
comments: false
date: 2011-9-16 18:00:33
layout: post
slug: solid-principles-single-responsibility-principle
title: 'SOLID Principles: Single responsibility principle'
wordpress_id: 738
categories: [single responsibility principle, solid, solid series]
---

I saved [Single Responsibility Principle ](http://en.wikipedia.org/wiki/Single_responsibility_principle)(SRP) for last. I think it's the most important principle to understand and to utilize correctly. I would even go so far as to say it's the most important principle to follow.

<!-- more -->

So why is SRP so important? I guess it's all in it's definition. A class or file should only have one reason to change. If it has more than one reason to change, then your code will become brittle and difficult to maintain. The problem with SRP is how difficult it is to follow. SRP feels a lot more like an art than a science at times. You get into situations where you're not sure whether or not a class is only doing one thing, and if you need to further abstract away code into separate classes.

As a simple example, let's say you have a Car that needs to be able to start it's Engine

{% gist 1208417 BadCar.cs %}

This Car knows _**way**_ too much about it's Engine. If the Engine's starting sequence ever needs to be changed, you have to actually go into the Car class to change it! That just doesn't make any sense. What we should be doing instead is abstracting away that functionality inside the Engine class and allowing the Car to simply start the Engine when it needs to.

{% gist 1208417 Engine.cs %}

This Engine prevents other classes from using it improperly. Part of learning how to effectively use SRP is to identify when you're exposing too much of a class. In the previous Engine the Car was calling each function inside the Engine. This better designed Engine hides this functionality from outsiders, so the Car now has no choice but to use only the start method.

The Car now has to look something like this

{% gist 1208417 GoodCar.cs %}

This is a class structure that utilizes SRP.

Want more info? [Check out this dime cast](http://www.dimecasts.net/Content/WatchEpisode/88).
