---
author: joseph
comments: false
date: 2010-9-07 11:16:52
layout: post
title: When Windows tells you that it's not geniune...
categories: [windows]
---

I recently had to go through this because my windows installation started telling me it was not genuine and I needed to activate it.  This was completely false, being that the key I was using to activate the product was completely legit.

So if you find yourself in the same boat as me, and you are wondering what you should do, follow these steps.<!-- more -->

Once you have a valid Key it remains valid and it is that way that MS designed it. However, corrupted licensing files may occur once there is an infection or it could be more reasons around there. Nonetheless try this entering these command on an "elevated DOS prompt" (click start> type CMD> right click CMD icon and run as admin)

1. type slmgr /upk and press enter ( a notification should pop up that the PK has been uninstalled)

2. type slmgr /cpky and press enter (this clears out the PK from registry)

3. type slmgr /rilc and press enter ( this will reinstall licensing files)

4. restart your computer twice, (if it would ask you to enter the product key on the 1st restart click ask me later and restart once more)

5. After the 2nd restart, it will ask you to enter the PK, Yes enter it. It will automatically validate but maybe if you're lucky it would take it. If not,.. step 6.

6. click start  and type "slui 4" (without the quotes) and press enter. This will open the phone activation option. Choose your country correctly and phone in the activation server. At this point, the installation ID should be valid (unless you are using pirated copy or product key) and the phone activation server will give you the confirmation ID. It will ask you how many times you have installed your copy-- of course just say once.

[The original article I found this from was here.](http://social.technet.microsoft.com/Forums/en-US/w7itproinstall/thread/f0ec949f-4865-4a63-849c-af386de1b1fa)
