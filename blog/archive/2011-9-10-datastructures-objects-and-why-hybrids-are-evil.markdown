---
layout: post
author: joseph
date: 2011-9-10 12:31:50
title: Datastructures, Objects, and why Hybrids are evil
categories: [data structures, hybrids, objects]
---

I was writing this code that let's interviewees code problems for me, and it verifies that the interviewee actually writes something that works. I got to a point in my printer classes that I wasn't liking, and I thought I'd share it.

<!-- more -->

I have a printer that allows the candidate to print something, which actually verifies that they're code up to that point is correct. It looks something like this

{% gist 1208486 Hybrid.cs %}

It works exactly as I expected it to, but there was something I didn't like about it. I didn't like the Counter. I didn't want the candidates messing with it. My first inclination was to change the setter to private, but it got me to thinking.

The Console was definitely intended to be an Object. The whole point was to let the candidate use the Print method. What I had done, however, was create a Hybrid. It's a Hybrid because having the Counter property also makes Console a Data Structure. That's why I wasn't liking it. To the outside world, this class was a Hybrid, and I wanted it to be an Object. I needed my testing framework to know what the Counter was to test that the candidate had actually called Print enough times, though. So how do I fix it? Another problem was that even if I made Counter with a private setter, it would still look like a Data Structure. You might argue that a property is just a fancy kind of method, but it still _**feels**_ like a Data Structure.

Ultimately, I decided on this

{% gist 1208486 Object.cs %}

which I think looks a _**lot**_ better from the outside, since you only have the ability to use the Console as an Object.

You may ask


> Why are Hybrids evil in the first place?


Well, I think it was best framed by Robert C. Martin in [Clean Code](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_1?ie=UTF8&qid=1315672495&sr=8-1)


> This issue would be a lot less confusing if data structures had public variables and no functions, whereas objects had private variables and public functions. However there are frameworks and standards (ie beans) that demand even simple data structures have accessors and mutators. ...

This confusion leads to unfortunate hybrid structures that are half object and half data structure. They have functions that do significant things, and they also have either public variables, or public accessors and mutators, that for all intents and purposes, make the private variables public, tempting other external functions to use those variables the way a procedural program would use a data structure.

Such hybrids make it hard to add a new function but also make it hard to add new data structures. They are the worst of both worlds. Avoid creating them. They are indicative of a muddled design whose authors are unsure of - or worse, ignorant of - whether they need protection from functions or types.
