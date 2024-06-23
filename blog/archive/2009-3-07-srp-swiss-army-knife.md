---
author: joseph
comments: false
date: 2009-3-07 08:06:41
layout: post
slug: the-srp-swiss-army-knife
title: The SRP Swiss Army Knife
wordpress_id: 18
categories: [solid, srp]
---

I read this [blog](http://www.lostechies.com/blogs/gabrielschenker/archive/2009/01/21/real-swiss-don-t-need-srp-do-they.aspx) a few weeks back, and while the principles that the author was trying to convey I don’t disagree with, I did have a hard time stomaching the idea that Swiss army knives made for bad OOP design.<!-- more -->  Why?  Because I really like Swiss army knives!  I mean come on, what other device lets you have 100+ different kinds of knives, a compass, a light, a magnifying glass, and a USB drive all in one “disappears in your pocket” size container???

Ok so I know what most of you are thinking… I’m just PROVING that they violate SRP right?  Not actually.  In case you’re not familiar with the principle of SRP, check out this [wiki](http://en.wikipedia.org/wiki/Single_responsibility_principle).  The reason why I believe the Swiss army knife does NOT violate SRP is because my general rule of them is, if I can state the responsibility of the object in question in one single statement, then I have identified it’s SINGLE responsibility.  So I have to ask myself, what is the SINGLE thing that a Swiss army knife does?

_Responsibility statement_: ***A Swiss army knife is responsible for allowing it’s user to be able to use any tool that has been installed into it***.

Short, simple, and to the point.  Now, that sounds all fine and dandy, but if I don’t implement the code the way I’ve dictated in my statement, then I will violate SRP, so how is it that I build a Swiss army knife while preserving my responsibility statement?  Enter code.

``` c#
   public interface SwissArmyKnife
   {
       ITool PullOutTool(ITool tool);
       void PutAwayTool(ITool tool);
   
       IList Tools { get; }
   
       ITool ToolBeingUsed { get; set; }
   }
```

My interface defines any type of Swiss army knife I ever wish to make.  It’s only responsibility is to be able to use a tool that it has.  So then how do you use the Tool?  Well here’s how an ITool might look like:

``` c#
    public interface ITool
    {
        void Use();
    }
```

Now you can get any tool from the Swiss army knife and then use it.  Notice that it’s NOT the responsibility of the Swiss army knife to know HOW to USE the tool, only to get it out or put it away.

So what would violate SRP then?  Why did the author choose this particular product to harp on?  I think it was a matter of perspective really.  The article I read had numerous pictures of knives all decked out such as this one. Here is where I believe the real trickery is.  The author shows the pictures of all the swiss army knives with all the tools exposed.  If you really tried to use the knife like this, you would probably really screw up your hand!  That’s my point.  The knife is not of any use to anyone unless you’re using ONE tool at a time.  This is why SRP is not being violated.

You might take this a step further and say that you have a Swiss army Factory that would actually build the Swiss army knives, but that would be a discussion for another day.

Anyway, I hope everyone has found this interesting, till next time!
