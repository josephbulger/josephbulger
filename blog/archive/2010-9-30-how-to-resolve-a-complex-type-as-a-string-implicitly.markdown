---
author: joseph
comments: false
date: 2010-9-30 17:00:00
layout: post
slug: how-to-resolve-a-complex-type-as-a-string-implicitly
title: How to resolve a complex type as a string implicitly
wordpress_id: 305
categories: [authorization]
---

Along the same lines as resolving a complex type in a conditional, I also want to be able to take the same Authorization Result, and use it to broadcast a message to the system (or user), and tell them why couldn’t they be authorized.<!-- more -->

An example of the behavior I’m looking for is something like this:

``` c#
var authorizationResult = Is.CurrentUser.AuthorizedTo.DoSomething();
if (!authorizationResult)
    throw new AuthorizationException(authorizationResult);
```

The Authorization Exception takes a String in it's constructor, just like a typical Exception. It does not take an AuthorizationResult. So how am I able to just pass in an authorizationResult like that? Again, the implicit operator is all we need:

``` c#
public class AuthorizationResult
{
    public bool IList<reason> Reasons { get; set; }
    public static implicit operator string(AuthorizationResult authorizationResult)
    {
        if (authorizationResult.With(x =&gt; x.Reasons) == null)
            return false;
        var result = new StringBuilder();
        foreach (var reason in authorizationResult.Reasons)
        {
            result.AppendLine(reason.Message);
        }
        return result.ToString();
    }
}
```

Now I can use AuthorizationResult implicitly as both a bool and a string.
