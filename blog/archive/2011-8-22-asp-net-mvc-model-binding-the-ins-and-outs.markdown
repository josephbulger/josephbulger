---
author: joseph
comments: false
date: 2011-8-22 12:00:18
layout: post
slug: asp-net-mvc-model-binding-the-ins-and-outs
title: 'ASP.NET MVC Model Binding: The Ins and Outs'
wordpress_id: 629
categories: [asp.net mvc, model binding]
---

Model Binding can be a tricky thing to get right with MVC. Dealing with flat POCOs works fine, but when you start getting into more complex, truly object oriented domain objects, things get out of hand pretty quickly.

<!-- more -->

Let's say you have a package class like this:

{% gist 1154718 Model.cs %}

The Package is basically flat at this point. Wiring this up with Model Binding in MVC is trivial. Just create your controller to handle the class when an action get's posted like:

{% gist 1154718 Controller.cs %}

and make a view that uses the class as it's model:

{% gist 1154718 View %}

This code works fine. The problem is I don't like the code! My package class is all wrong. All those properties should be inside an Address Class, which is **used by **the Package.

Once you make a more complicated model than a flat one, everything starts breaking. The problem is that so many things break you have to go through and fix all the pieces one at a time, so we'll have to go through each of these issues in a post by themselves.

The first thing we're going to address is this: when you change the Package to have an Address, the View shows **nothing** on a GET. What happened to the View? We'll cover that next.
