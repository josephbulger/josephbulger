---
author: joseph
comments: false
date: 2011-10-17 08:00:02
layout: post
slug: mental-floss-the-month-on-the-calendar
title: 'Mental Floss: The Month on the Calendar'
wordpress_id: 817
categories: [mental floss, solid, viewmodel series]
---

So we've seen what the Calendar looks like, and how it's building it's Months, but what goes into building a Month?

<!-- more -->So building a Month is all about one thing: building it's weeks. The only other thing I need from a month is the ability to get it's name (i.e. January, February, etc.). My implementation of this looks something like this:

{% gist 1291245 Month.cs %}
