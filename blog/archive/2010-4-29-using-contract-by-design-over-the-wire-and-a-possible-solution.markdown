---
author: joseph
comments: false
date: 2010-4-29 08:10:29
layout: post
slug: using-contract-by-design-over-the-wire-and-a-possible-solution
title: Using Contract By Design over the wire and a possible solution
categories: [contracts]
---

I’ve been plagued with an issue that I have yet to find a solution for.

This is really more for my benefit so I can research this later, but basically when I create classes I tend to decouple them by using interfaces, especially between layers.

<!-- more -->

The problem is when you use a service layer, such as WCF, that the class that is generated on the client side doesn’t carry the implementations that the server side class has.

Then I found [this article](http://blogs.microsoft.co.il/blogs/gilf/archive/2010/04/28/add-service-reference-how-to-avoid-generating-already-existing-classes.aspx) which might be my saving grace.  It lefts you actually reference the same class on the client side.  This might be solution I’m looking for.

Once I get a chance to test this out I’ll post the results with some code.
