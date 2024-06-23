---
author: joseph
comments: false
date: 2011-5-21 18:28:29
layout: post
slug: learning-ruby-on-rails-installing-rubymine
title: 'Learning Ruby on Rails: Installing RubyMine'
wordpress_id: 440
categories: [learning ruby, rubymine]
---

RubyMine is a Jetbrains product for developing ruby applications.  I use a lot of Jetbrains products, which is why I'm giving RubyMine a run through.  I'm hoping it will be as good as their other products. Since I'm a .NET developer, I'm thinking it will help me bridge the gap between the typical environment I'm used to using (Visual Studio 2010) and RoR.<!-- more -->

Now, before we get to install RubyMine, there is one caveat that we have to take care of.  You have to have a java runtime on your linux distro in order to run RubyMine, but it can **not **be OpenJDK, so if you have that installed (I did), then you have to uninstall it first, and install another one.

For example:
    
> $ sudo apt-get install sun-java6-jre sun-java6-jdk


My screencast will **not **include this step, because I actually need java installed on my linux distro to **make **the screencast.

Once you have java installed go to [http://www.jetbrains.com/ruby/download/index.html](http://www.jetbrains.com/ruby/download/index.html) and download RubyMine.

I downloaded it into my home directory.

After you've downloaded it, then you need to extract it
    
> $ tar -xvzf rubymine-2.0.2.tar.gz

And move it to the opt folder
    
> $ sudo mv rubymine93.202 /opt/rubymine


Now we need to add an link to our java runtime so RubyMine will be able to see it
    
> $ sudo ln -s /usr/bin/java /bin/java

I also went ahead and added a shortcut to my panel so I can open it easily.

![Adding a shotcut](http://media.tumblr.com/tumblr_l6f7y4c2T61qbyy0y.png)

![There’s an icon at /opt/rubymine/bin/RMlogo.svg](http://media.tumblr.com/tumblr_l6f87jErnk1qbyy0y.png)

![Setting RubyMine Starting Point](http://media.tumblr.com/tumblr_l6f8efWaJW1qbyy0y.png)

And here's a video of the whole thing:


