---
layout: post
title: "Gotchya: Enumerable side effect I didn't see coming"
date: 2014-02-23 18:00:24 -0500
comments: false
categories: [gotchya series]
---

I was working on a feature recently when I came across a behavior that I wanted to highlight.

<!-- more -->

I was doing something like this

``` c#
	var newOrder = 0;

	foreach(var thing in someListImOrdering)
	{
		var theOldThing = someOtherListToLookAt
			.First(x => x.OrderNumber == newOrder++);
	}
```

I was expecting this to basically look through an enumeration and pull out the old thing that used to have the old order number that I am about to assign to the new thing that I'm ordering, and then afterwards bump up my `newOrder` counter.

So let's say that I'm looping through `someListImOrdering` and it has around 20 items, then I expected `newOrder` to go from 0 to 19.

However, if you look closely enough you can probably guess what will actually happen.

What it will actually do is bump up `newOrder` not only for every item in `someListImOrdering`, but during that iteration it will then bump it up subsequently while doing the `First` until it finds the thing it was looking for.

In my run, instead of getting `newOrder` to 19, it ended up at around 400 or so, because that's how many iterations I ended up going through by processing this foreach loop.

Lesson of the day, watch what you do when you iterate through your enumerables.