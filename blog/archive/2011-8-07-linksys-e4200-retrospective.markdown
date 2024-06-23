---
author: joseph
comments: false
date: 2011-8-07 16:25:13
layout: post
slug: linksys-e4200-retrospective
title: 'Linksys E4200: Retrospective'
wordpress_id: 455
categories: [linksys, router, wireless]
---

I just recently retired my [WRT54G](http://en.wikipedia.org/wiki/Linksys_WRT54G_series) in favor or a Wireless N Router.  I ended up going with the [Linksys E4200](http://homestore.cisco.com/en-us/Routers/Linksys-E4200-MaximumPerformance-Wirelessn-router_stcVVproductId122703236VVcatId551966VVviewprod.htm) for a number of reasons.<!-- more -->

First of all, it's a high performance router, and so far it's lived up to that expectation.  What do I mean by a high performance router?


> It's capable of handling 450 Mbps on the 5GHz band.


We'll get to the band in a second.  This is going to be extremely useful for me later on when I start setting up a NAS in my house to stream content.

Secondly, it's a dual band router.  What exactly is a dual band router? It's pretty simple actually.


> The router has the ability to host two networks (3 if you include the guest network): one on a 5GHz band, and another on a 2.4 GHz band.


Now, there are a couple catches here.  First, this router is a **true** dual band router.  What I mean by that is that it can run both bands **simulataneously**.  If you're in the market for a new router, you have to keep this in mind because you could be looking at a router that says it's dual band, but when you bring it home, it can't run them both at the same time, you have to choose which one to roll with.

For me, I have to have both bands up at the same time, which brings me to the second catch.  If you have devices (PCs, IPods, IPhones, Androids, etc.)  then it's likely that some are going to run on Wireless-G and some have Wireless-N.  In my household, most of the devices run on Wireless-G, and I have a couple that can run Wireless-N.

Here's the catch.  If you have a Wireless-N capable router and you have devices that are connecting to it via Wireless-G and Wireless-N **on the same band**, then the router has to choose the lesser of the two, which means everyone on that band is stuck on Wireless-G.  This goes for all routers, by the way.  This is where the two bands come into play.  If you have two bands, you can set one band up to run Wireless-N (5 GHz), and you can set up the other to be Wireless-G (2 GHz).

This brings me to the first complaint that I have about the E4200.  When I originally set up the router, I **assumed** that the router would be configured out of the box to handle the exact scenario that I described previously.  However, what I came to find out was that my router, out of the box, was configured in some kind of Mixed Mode setting for **both bands**.  This wreaked havoc on my network for a day or two because I didn't realize what was going on, and chalked it up to Comcast screwing with my internet again (they have a tendency to do this).  However, after sitting down for a second and thinking about it, I decided to go digging around in the settings and that's when I discovered the mixed mode issue.

This wouldn't have normally been a problem, except for one thing:


> I have a couple of devices at home that are Wireless-N but they only operate on the 2.4 GHz band.


This caused a war to ensue on the router between my G devices and my N devices, the winner being no one. Long story short, I ended up doing a manual set up on my router, where the 5GHz band was configured to run only Wireless-N and the 2.4 GHz band was configured Wireless-G only.  Why did I do it that way?  Because Wireless-G devices can only operate on 2.4 GHz band, and the devices that I have that only handle Wireless-N on the 2.4 band automatically negotiate down to Wireless-G when connecting to the router on 2.4 now, which is what would happen to them anyway.

Once I did that the war ended and now everything is playing nicely together.  There has been one other snag along the way, but I'll save that for another post.
