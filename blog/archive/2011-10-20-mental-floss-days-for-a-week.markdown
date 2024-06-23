---
author: joseph
comments: false
date: 2011-10-20 08:00:50
layout: post
slug: mental-floss-days-for-a-week
title: 'Mental Floss: Days for a Week'
wordpress_id: 834
categories: [mental floss, solid, viewmodel series]
---

So as I build the week up, I'm adding Days onto it, but what do they look like?

<!-- more -->

A Day is pretty simple. It has the DateTime that says what actual day it belongs to. It also has a list of Events that belong to it. A Day can also indicate whether or not it's available for reading, or if it has events. This is important because if a day is available, then the UI needs to let the user select that Day, and if it has events, then the day should show those events in the UI.

``` c#
    public class Day
    {
        public DateTime Date { get; set; }

        private IList<Event> Events { get; set; }

        public Day()
        {
            Events = new List<Event>();
        }

        public bool HasEvents()
        {
            return Events.Count > 0;
        }

        public void AddEvent(Event eventForDay)
        {
            Events.Add(eventForDay);
        }

        public IList<Event> GetEvents()
        {
            return Events;
        }

        public string GetReadingAvailability()
        {
            return IsAvailable() ? "date_is_available" : 
              HasEvents() ? "date_has_event" : "");
        }

        private bool IsAvailable()
        {
            return !HasEvents() && IsDayAvailableForReading();
        }

        private bool IsDayAvailableForReading()
        {
            return IsDayAvailableForReading(Date);
        }

        public static bool IsDayAvailableForReading(DateTime dateTime)
        {
            return new AvailabilityChecker()
              .IsDayAvailableForReading(dateTime);
        }

        public string GetReadableValue()
        {
            return Date.ToString("MMMM dd, yyyy");
        }
    }
```

This concludes the implementation of all the pieces necessary to build the ViewModel. The View itself could be built on any technology stack to show UI appropriate to the rules we outlined so far, which is the ultimate goal of having our ViewModel separated from the View itself.

This does **not**, however, tell us what our business rules are, or how their implemented, which is the next part of the series: the Model.
