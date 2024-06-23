---
author: joseph
comments: false
date: 2011-9-14 18:00:18
layout: post
slug: solid-principles-dependency-inversion-principle
title: 'SOLID Principles: Dependency inversion principle'
wordpress_id: 725
categories: [dependency inversion principle, solid, solid series]
---

[Dependency Inversion Principle](http://en.wikipedia.org/wiki/Dependency_inversion_principle) has a dramatic effect on your code base. It has the potential to decouple your code in ways that you never would have thought possible before. Using a good IoC container can make all the difference as well.

<!-- more -->The best way to explain this principle would probably be an example.
Let's say you have a car like this

{% gist 1208366 CoupledCar.cs %}

Notice the engine that belongs to the car. First of all, the car is actually creating the engine. This means that the car is tightly coupled to the engine it's creating. Secondly, this creates a concrete coupling on the car to a specific kind of engine, the FourCylinderEngine.

A better solution would be to use Dependency Inversion. You should depend on an interface instead of a concrete class. Back in our example, our FoudCyclinderEngine looks like this

{% gist 1208366 FourCylinderEngine.cs %}

Notice how it implements Engine. That's the interface our Car should be using. There's one additional problem, though. Right now the Car is creating it's Engine, but you can't instantiate an interface, so what do you do? That's the key to Dependency Inversion. You're saying,

> a Car does not depend on knowing about how to create an Engine

It just _uses_ it. So the new Car class looks like this

{% gist 1208366 DecoupledCar.cs %}

So how does the engine get created? Obviously something is passing in the engine to the Car, but what? You need a new class. Maybe a CarManufacturer or something, which is responsible for manufacturing cars. Part of that responsibility would be putting an engine in a car.

Want more info? [Check out this dime cast](http://www.dimecasts.net/Content/WatchEpisode/96).
