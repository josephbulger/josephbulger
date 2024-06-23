---
author: joseph
comments: false
date: 2010-9-30 07:38:57
layout: post
slug: how-to-use-a-complex-type-in-a-conditional
title: How to use a complex type in a conditional
wordpress_id: 299
categories: [authorization]
---

I am building a basic authorization framework, and I have really liked the use of it so far.

It basically looks something like this<!-- more -->

``` c#
var result = Is.CurrentUser.AuthorizedTo.DoSomething();
```

The result that I get back is not actually a boolean, as you might thing.  It's a complex type that I built so I could not only determine if authorization was successful or not, but also **why not**.

So you can do this:

``` c#
result.IsAuthorized
```

and you can also do this:

``` c#
result.WhyNot
```

WhyNot will actually give you back a list of Reasons, which you can then use to inform the system (or user) why they can't do something.

So what's the problem? Sometimes I just don't care why not, I just want to do the check quickly and be done with it, which makes me want to do something like this instead:

``` c#
if (Is.CurrentUser.AuthorizedTo.DoSomething())
```

This won't compile, because my complex type can't resolve to a bool.  Not to worry, though, implicit operator to the rescue:

``` c#
public class AuthorizationResult
{
        public bool IsAuthorized { get; set; }
        public static implicit operator bool(AuthorizationResult authorizationResult)
        {
            if (authorizationResult == null)
                return false;
            return authorizationResult.IsAuthorized;
        }
}
```

Next post I'll talk about how I do the same thing for outputting the reasons implicitly.
