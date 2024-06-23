---
layout: post
title: "Date shifting and Timezones"
date: 2014-08-25 19:11:55 -0400
comments: false
categories: [c#, timezone]
---

On one of my projects, we recently ran into an issue with the browser displaying
dates that were a day behind when the user was logged in on the Pacific time
zone.

<!-- more -->

I began looking at the project's source and realized that the client side code
had been compensating for the server's time zone by trying to time shift all
dates back to the time zone it should be in.

This solution ended up giving us a fairly complicated mess on our hands. The
essential problem was pretty simple, though. The server was passing dates back
to the client with the time zone specified. Our servers were in EST, so anyone
in PST would see a date that was time shifted by 3 hours. If the server would
just pass back dates with **no time zone** at all, then we'd be in the clear.

To do that, all we had to do was pass back dates by parsing them out with no
time zones, like this:

``` c#
  var datePassingBack = DateTime.SpecifyKind(dateTimeToParse,
    DateTimeKind.Unspecified);
```
