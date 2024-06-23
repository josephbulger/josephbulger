---
layout: post
title: "When is it ok to violate the D in SOLID?"
date: 2014-03-03 21:37:04 -0500
comments: false
categories: [solid, violating solid, dependency inversion]
keywords: "dependency inversion principle, solid, uncle bob, software craftsmanship, violate"
---

I'm about to begin my annual re-read of [Clean Code](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882), but before I start this year I thought it would be idea to touch on a subject that I haven't actually seen a lot of discussion on.

That issue begins with a premise: we all violate SOLID principles in our code. We *have* to. You can't write any non-trivial project without violating at least one of the principles. Most of the time you'll find yourself choosing one over another, and that is where I thought an intersting topic of conversation could be had.

When is it ok to violate SOLID principles? For this post, specifically the [D](http://en.wikipedia.org/wiki/Dependency_inversion_principle)

<!-- more -->

One other thing before we really start diving in. This topic is going to be highly subjective, by it's nature I presume. I'm speaking of my current experience, which admittedly is not as extensive as a lot of software craftsman out there. I do, however, think I have enough experience to at least provide some insight into the discussion. If nothing else, I'll be able to read this in 6 months and laugh at myself for being so dumb.

Let's get one thing clear right off the bat. It's not the Dependency **Injection** Principle, it's the Dependency **Inversion** Principle (DIP for short). Meaning, you are inverting the dependencies so that high level policy does not depend on low level details. I'm going to assume you know what that entails.

So when is ok to violate this principle?

My experience so far has been this. When I am building software, I specifically pay very close attention to one thing in regards to DIP: my application boundaries. Across boundaries, I make it a point to never violate DIP. If you don't know what I'm referring to when I talk about application boundaries, then you need to read up on Component Based Design. In general, though, I'm talking about breaking up your application into Components that are specific to a type of user. It should be completely based on a business vertical. Once you have all your Components identified, then you can start drawing boundaries around them. This is where things get interesting. The general rule is very simple: do not have your dependencies go both ways across boundaries.

For example, let's say you have a Component called Scheduling, and another component called Packaging. These two Components interact with each other, so there is a boundary there. The question is, what direction should the dependency go? The answer of course, using DIP, is to figure out which Component is the higher level policy, and which is a detail. In this example, Scheduling is the higher level policy, and Packaging is just a detail of that overall workflow (albeit for a different user base). This means that the dependency arrows goes across the Scheduling / Packaging boundary pointing from Packaging over to Scheduling.

So on my projects, it is never ok to violate DIP when dealing with Component boundaries. However, inside a Component it's not as clear cut. For example, inside the Scheduling Component there may be a workflow that exists only there, and it doesn't venture outside of the component. No other Component ever sees this workflow, no one even knows it exists. On top of that, the code footprint for that workflow is small. In situations such as this, DIP can and will be violated in favor of adhering more strictly to other SOLID principles.

I want to also be clear about one thing here. Notice how I said that DIP can be violated *in favor of other principles*. I do not advocate violating principles out of sheer laziness. That is not the point. The point is, as a craftsman, there will be times where you have to choose certain principles over others. And so, what I'm really getting at here is, inside a Component boundary, it's ok to violate DIP in favor of say, for example, SRP. In that case SRP may take a higher precedent than DIP. That would not be the case if you were crossing Component boundaries, but since you're not, then other principles can take higher priority.

This brings up an interesting question, though. Is there a SOLID principle that you should never, under any circumstances, violate? I'll leave that one for another time.