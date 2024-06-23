---
author: joseph
comments: false
date: 2011-10-16 16:46:08
layout: post
slug: mental-floss-the-calendar-viewmodel
title: 'Mental Floss: The Calendar ViewModel'
wordpress_id: 806
categories: [mental floss, solid, viewmodel series]
---

So my ViewModel basically starts with the idea of a Calendar and it's Events. So what did I end up making that look like? Let's disregard the markup for now, because how it looks isn't really what we're talking about here. What we're talking about is how the Calendar and it's Events are _**modeled**_.

<!-- more -->

So my Calendar looks like this:

``` c#
    public class Calendar
    {
      public Calendar()
      {
          Months = new Dictionary<int, Month> 
            {{DateTime.Today.Month, new Month(DateTime.Today.Month)}};
      }

      protected IDictionary<int, Month> Months { get; set; }

      public Month GetCurrentMonth()
      {
          return GetMonth(DateTime.Today.Month);
      }

      public Month GetNextMonth()
      {
          return GetMonth(DateTime.Today.Month + 1);
      }

      public Month GetMonth(string month)
      {
          return GetMonth(
            DateTime.ParseExact(
              month, "MMMM", CultureInfo.CurrentCulture)
            .Month);
      }

      public Month GetMonth(int monthValue)
      {
          if (!Months.ContainsKey(monthValue))
              Months.Add(monthValue, new Month(monthValue));

          return Months[monthValue];
      }

      public static DayOfWeek GetStartDay()
      {
          return DayOfWeek.Monday;
      }

      public static DayOfWeek GetLastDay()
      {
          return DayOfWeek.Sunday;
      }

      public void IncludeEvents(IList<Event> events)
      {
          foreach (var @event in events)
          {
              IncludeEvent(@event);
          }
      }

      private void IncludeEvent(Event eventToInclude)
      {
          var allDaysQuery = from week in GetMonth(
              eventToInclude.Day.Month)
                .GetWeeks()
            from day in week.GetDays()
            select day;

          var filteredDays = from day in allDaysQuery
                             where day.Date == eventToInclude.Day.Date
                             select day;

          var dayToAddEventTo = filteredDays.First();

          dayToAddEventTo.AddEvent(eventToInclude);
      }
    }
```

The Calendar is really only concerned with one thing: showing events that belong to it. In order to accomplish this goal, the Calendar must be able to include events into the months on the Calendar.

In order to do this, the Calendar has to be able to build months, and then include the events into the days of those months. All of this logic takes place in the IncludeEvent method, which utilizes quite a few other classes to accomplish this.

In the next post, we'll take a look at the other classes we used in the ViewModel to accommodate our Calendar
