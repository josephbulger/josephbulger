---
author: joseph
comments: false
date: 2011-10-11 17:20:34
layout: post
slug: mental-floss-models-and-viewmodels-and-yes-theyre-different
title: 'Mental Floss: Models and ViewModels, and yes they''re different'
wordpress_id: 796
categories: [mental floss, solid, viewmodel series]
---

My wife recently asked me to work on a site for our son's class and while working on it I realized that what I was building was a pretty great example of my thoughts on how I feel that Models and ViewModels relate to each other.

<!-- more -->So before we get to any code I'd first like to explain how I see the Model and ViewModel shaping conceptually. The domain is basically as follows:

The system needs to be able to allow users to sign up as Readers for a particular day. The time that they are going to read is always the same, it's 10 AM for an hour. The days that a user can sign up to be a Reader are specific. For the most part, the days are Monday, Tuesday, Thursday and Friday, but there are also some other rules like the week of Thanksgiving there won't be any readings. Currently, this is being done by the teacher where she prints out a Reading Calendar that has the days for reading highlighted. What needs to be done is basically the same thing. We need to make a calendar that shows everyone all the days that are eligible for signing up, and let them sign up for those days. It also needs to show them the days that have already been signed up, and show the Reader for that day.

Ok, so that's the basics. Now, the question here is how is the Model and ViewModel broken down?

To illustrate this it would probably be better to start with the user experience, and hence the ViewModel. So the user will be presented with a Calendar, and they should be able to see Events that show up on the calendar, like they're most likely already used to. These Events will show who's signed up. Now, the user also needs to be able to Sign Up. That's the extent of the first version of the ViewModel I came up with. We have a Calendar, Events, and a SignUp.

Now, onto the Model. When the user signs up, they're telling the system that they want to be a Reader for a particular day. Once they become a Reader then the system shouldn't allow anyone else sign up on that day. So that's really the Model. We have a bunch of Readers and some logic that keeps you from signing up multiple Readers on the same day.

So how does the Model and ViewModel relate? Well, there are two key relationships here. First, when a user signs up, we'll have to map the SignUp over to a Reader. Second, when we show the user a calendar, we need to get all the Readers for those days, and map the Readers over to Events which the Calendar knows how to show.

Notice the last thing I said there. The **Calendar** knows how to _show_ **Events**, but it doesn't know_** anything**_ about Readers. The implications here allow your system to be truly decoupled and makes it much more maintainable.

So how did I implement this? I'll show you on my next post.
