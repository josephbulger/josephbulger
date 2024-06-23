---
author: joseph
comments: false
date: 2011-9-02 12:00:41
layout: post
slug: solid-principles-open-closed-principle
title: 'SOLID Principles: Open Closed Principle'
wordpress_id: 685
categories: [open closed principle, solid, solid series]
---

This has been done to death I think, but I wanted to take a shot at it. In fact, this is really more for my benefit than for anyone else. If I can manage to explain these concepts clearly enough then I think I have a firm understanding of what they actually stand for, and how I should apply them when I build things. The first thing we're going to get into is the [Open Closed Principle](http://en.wikipedia.org/wiki/Open/closed_principle).

<!-- more -->Why did I choose this one first? To be honest, this one was the last one that I found a use for. I understood the concept early on in my career, but I had issues figuring out when it was best to use it, and therefore I wouldn't really ever use it at all.

This made for some bad code. Some really bad code. But, such is the life of a developer. You basically go through 4 stages. First, you have no concept of some _thing_. Then you learn about this _thing_, which you then spend some time just understanding. Third comes the obsession stage, where you apply said _thing_ to everything you do. And lastly, comes I guess what you would call the Maturing stage, which is when you learn the appropriateness of this _thing_.

In our case today, we're talking about the [SOLID](http://en.wikipedia.org/wiki/Solid_(object-oriented_design)) principles, and specifically looking at OCP.

As I said before, this one took me some time to finally understand the usefulness of it. In fact, it was when I was reading [The Art of Unit Testing](http://artofunittesting.com/) that this light bulb went off in my head. Actually, it wasn't a light bulb, it was more like a giant flood light or something. By the way, if you're new to TDD or unit testing, you have to read this book by [@RoyOsherove](http://twitter.com/#!/RoyOsherove). The part that really opened my eyes was when Roy started talking about building seams into your unit tests.

Up until that point, I had very heavily relied on interfaces for my mocking techniques. He goes over that more in the book, but the problem I was facing was that I needed interfaces for **everything**. A lot of times, though, it was a huge over kill for what I was trying to do. A seam was **_perfect_**. A seam was also the huge flood light going off in my brain. Why? Because a seam is literally applying OCP to a method with the purpose of later faking that method.

Instead of describing what I'm saying I think so code might serve us better, so let's say you have some service class like this.

{% gist 1187529 SimpleServiceWithoutOCP.cs %}

Now when you're writing tests to make sure this guy works, you'll probably want to make sure that DoSomeWork actually does what it says it does, so you'd write a test that might look something like this.

{% gist 1187529 SimpleServiceTest.cs %}

So this test will create a mock (a type of fake) of your service, and then what we want to verify in this test is that when DoSomeWork is called, that it in turn will call DoWork on all the items that come from the Repository.

There's a problem, though. The problem is two fold. First, _**we don't want to use the real repository**_. If you don't understand why, read the book, trust me. Second, this code won't execute. You'll get a run time exception because there's no seam on CallTheRepositoryForSomeStuff. How do you make a seam? Well, you mark the method _virtual_, which allows sub-classes to change it's behavior.

{% gist 1187529 SimpleServiceWithOCP.cs %}

And _**that**_ is OCP in a nut shell. It's OCP because our mocking framework doesn't have to actually open the file and change the existing class. In fact, what it does under the hood is create another class that is a sub-class of SimpleService, and then change the new class to have it do something besides go the real repository. In our case, we're just returned a new list with one item in it.

Want more info on the Open/Closed Principle? [Check out this dime cast](http://dimecasts.net/Casts/CastDetails/90).
