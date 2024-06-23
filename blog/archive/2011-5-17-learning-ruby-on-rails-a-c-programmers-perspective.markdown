---
author: joseph
comments: false
date: 2011-5-17 12:11:12
layout: post
slug: learning-ruby-on-rails-a-c-programmers-perspective
title: 'Learning Ruby on Rails: A C# programmer''s perspective'
wordpress_id: 398
categories: [learning ruby, rails, ruby]
---

I've been doing C# programming for some time now.  I've been investigating Ruby and talking to coworkers about it, but have yet to really dive in and start learning the language and it's web counterpart, Rails.

<!-- more -->

I recently came up with a fun concept that I wanted to try on the web and see how people liked it, and I thought it would be a great opportunity to try learning Ruby on Rails.  This will be a series of blog posts on my trials as a primarily Windows C# developer working with this new language and new stack.

Before I begin, I'm going to outline at a high level what I'm planning on doing.

I'm going to install a virtual machine with [Ubuntu ](http://www.ubuntu.com/)and run my ruby environment on that.  I know I could do this on Windows, but I recently had an experience with loading up [TeamCity ](http://www.jetbrains.com/teamcity/)on a derivation of CentOS and it was delightfully easy.  It's made me realize that if I'm going to be developing a Ruby on Rails app that more than likely when I go to deploy this app it will probably end up being easier if my local environment is close to what host environment I will be having.  And I will definitely be pushing my app out to a Linux distro, so therefore Ubuntu is my choice for my local OS.

Next, I'll be using [RubyMine ](http://www.jetbrains.com/ruby/)as my IDE. I know there's a lot of ruby devs out there using [VIM](http://en.wikipedia.org/wiki/Vim_%28text_editor%29), but I haven't ventured into that family of text editors yet, and although I'm aware it has features that are very compelling, it's probably too much for me to take on at this point.  After a while, when I start feeling comfortable in Ubuntu and RubyMine I might venture into the VIM space and see how that goes.

I plan to deploy the app using [Heroku](http://www.heroku.com/).  I don't have the desire to setup up a production stack.  I do it enough with my windows applications, and I'm growing more and more tired of doing this.  In retrospect, at this point it feels as if it's a solved problem, and for me Heroku does the job of being a service that solves it quite nicely for me to not worry about anything.

Up next in this series will be a step by step of how I set up my VM with Ubuntu.
