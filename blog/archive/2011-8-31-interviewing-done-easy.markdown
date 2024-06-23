---
author: joseph
comments: false
date: 2011-8-31 12:00:57
layout: post
slug: interviewing-done-easy
title: 'Interviewing: Done Easy'
wordpress_id: 626
categories: [leading, interview]
---

When I develop applications, any time I can forgo repeating myself I usually take the opportunity to save myself the time. The same applies to a lot of my daily routine at work. If there's a way I can do things better, I'm all about it. So when I started conducting interviews, the first thing I started thinking about was how I can make this as stream lined as possible.

<!-- more -->I've talked about 2 questions I ask my interviewees. The second question is about letting the interviewee show me if they can explain basic development principles, in layman's terms. There's not a lot of streamlining that can be done there, to be honest. Ask the question, let them talk. However, the first question is a programming question, and getting the user set up to handle the question can be tricky depending on how they want to answer it.

How do I know that the interviewee answered the question correctly? Usually, I can just eye ball it, and I'm right about 95% of the time, but I wanted something better. I wanted to present the interviewee with something that removed the possibility of me screwing up, but also of them misunderstanding the problem.

So what I did was create a [project on github](https://github.com/josephbulger/Interviewing) that has different versions of the question. So far I have one in javascript, and one in C#.

The javascript version runs using jQuery and uses QUnit to verify the user has coded the problem successfully. I have a runner html file that the interviewee can fire up in their browser of choice and it will automatically validate if what they did is correct. The great thing is, if it works, everything "green lights", whereas if they're wrong, it "red lights". I mean that literally, so I can visually see within a split second if the code is good or not.

The C# version is similar, but it presented itself with some different dilemmas. For example, with C# usually people are going to want to showcase their talents using Visual Studio. The problem is, how do I get my test framework to the interviewee? The traditional approach would be to send it to them in an email or something, but I want to rule out the possibility of the interviewee trying to pull one over on me, or coming up with excuses like "I couldn't load what you sent me", or something to that extend. Instead, I built a nuget package, and I've pushed it up to the [nuget gallery](http://nuget.org/).

This has a few benefits. The first being that anyone running VS 2010 with the nuget extension installed can find and install my interview questions. Below I'm going to include a video of myself doing it just to show how simple it actually is. The second benefit is that there's no guess work involved, so the interviewee can't give excuses. All they need is VS 2010 and internet, which if they're being interviewed by me, they already have internet. If the interviewee doesn't have VS, they can just use notepad and I can copy paste what they've done on my side and run it.

Either way, there's no excuse, and it's _**fast**_.

How fast is it? Check out this video of me setting it up on a new project:

{% video http://vimeo.com/28377656 500 375 %}