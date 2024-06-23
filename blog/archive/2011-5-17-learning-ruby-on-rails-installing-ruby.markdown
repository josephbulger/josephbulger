---
author: joseph
comments: false
date: 2011-5-17 18:18:16
layout: post
slug: learning-ruby-on-rails-installing-ruby
title: 'Learning Ruby on Rails: Installing Ruby on Rails'
wordpress_id: 415
categories: [install, learning ruby, rails, ruby]
---

Next up on the list is installing Ruby on our Ubuntu instance.

<!-- more -->First thing we need to do is to run an update on Ubuntu to make sure we have all our tools up to date.

    
> sudo apt-get update


Then install aptitude and do an update on it. Aptitude is a new package management system for ubuntu.

> sudo apt-get aptitude 
> sudo aptitude update

After that we're going to need to install Git and curl which we can do by running

> sudo aptitude install build-essential git-core curl


We need Git and curl because we're going to be using them in order to bring down the RVM (Ruby Version Manager), which will be what we're using to install Ruby.

To install the RVM you need to run this command

> bash < <(curl -s https://rvm.beginrescueend.com/install/rvm)

And then you will need to edit a file to have Ubuntu point scripts in the right place with these two commands

> echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && source "$HOME/.rvm/scripts/rvm"' >> ~/.bashrc 

Then you will need to close your terminal and open up a new one

The next thing we'll need to do is to install a handful of other packages that we'll need for ruby to work (all in one line on the terminal!)
    
> sudo aptitude install build-essential bison openssl libreadline6 libreadline6-dev
> curl git-core zlib1g zlib1g-dev libssl-dev libyaml-dev libsqlite3-0
libsqlite3-dev sqlite3 libxml2-dev libxslt-dev autoconf

And at this point we're ready to actually install Ruby
   
> rvm install 1.9.2

After its installed we can tell RVM to use this version as the default ruby implementation from now on by saying
    
> rvm --default use 1.9.2

And you can verify that by executing
    
> ruby -v

Next thing to do is install Rails

> gem install rails

At this point you now have rails running on your box.

It's very easy to get a sample rails server up and running. Go to your terminal and run the following
    
> rails new myapp
> cd myapp
> bundle install

And once all the gems get installed for your app you can start your server up by 
    
> rails server

You'll need to right click on the url link and open it up in your browser to see.


