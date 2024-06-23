---
layout: post
title: "Node Gotchyas and the Atom Editor"
date: 2014-08-24 20:21:33 -0400
comments: false
categories: [node, atom]
---

I've been building a system on my free time with node, and I thought I would
give the new atom editor a whirl.

It turns out, there was a little gotchya that I didn't expect.

<!-- more -->

When I ran my node server through my normal terminal, everything would work
fine. However, when I ran it through a plugin on atom (term2), I would get an
internal server error every time I ran it.

I use gulp, so the command between each bash shell was very simple. I literally
just ran "gulp" and it would run through all the tasks I had created.

After tracing it down for around an hour, I finally discovered that when I
ran it through terminal, it was running under development, but when I was
running it in atom, it was running under production.

I verified this in multiple ways, but the final mark was when I ran "printenv"
in both shells. On my terminal, NODE_ENV wasn't defined at all, but in atom,
NODE_ENV was set to production.

I'm going to have to trace this back to see if this is something that atom is
doing explicitly or if this is happening for some other reason, but at least
for now I understand what it was, and it actually showed me that I had a config
problem between my dev and prod environments!
