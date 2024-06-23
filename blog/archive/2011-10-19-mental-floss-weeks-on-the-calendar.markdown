---
author: joseph
comments: false
date: 2011-10-19 08:00:48
layout: post
slug: mental-floss-weeks-on-the-calendar
title: 'Mental Floss: Weeks on the Calendar'
wordpress_id: 823
categories: [mental floss, solid, viewmodel series]
---

Now we've gone through how the [month is modeled](/?p=817), we need to see how weeks are being built.

<!-- more -->When you first look at the month implementation, you might think that adding days to weeks is a simple matter of adding DateTime's, but what's happening behind the scenes is just a little bit more complicated. Why? Well, we need to track what events belong to what days, so in order to do that we can't just use a simple DateTime, we need something just a little bit more complicated.

My Week looks like this:

{% gist 1291245 Week.cs %}

When adding days to the week, I accept a DateTime and then build a list of Days into the Week. Later on, the calendar will add Events to these Days.
