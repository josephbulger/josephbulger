---
layout: post
title: "Setting up BitSync on my NAS"
date: 2014-03-02 13:36:53 -0500
comments: false
categories: [sync]
keywords: "bittorrent sync, dropbox, skydrive, onedrive, nas, peer to peer"
description: "What options are out there to setup a dropbox or sky drive 'like' infrastructure inside your very own home? Try using peer to peer syncing technology like BitTorrent Sync, and run it on your own homegrown NAS"
---

I've been playing around with [BitTorrent Sync](http://www.bittorrent.com/sync) lately, and recently I discovered that they have an ARM variant. So that got me thinking, maybe I could put this on my NAS at home.

<!-- more -->

First things first, I grabbed the arm download [here](http://www.bittorrent.com/sync/downloads). Make sure you pick the ARM version.

Next I extracted the files on my computer. The only file I actually care about in there was the bitsync program. I took that file and I uploaded it into one of my NAS' shares.

Next thing I did was ssh into my NAS.

In my ssh terminal I created a new directory just to hold the btsync program. I called it bitsync. Then I moved the file from the share I put bitsync in to this new folder.
 
> $ mv /shares/Public/btsync /bitsync/btsync

Now it's in a place that isn't visible to other users of the NAS, which is nice because I don't have to worry about other users messing with it. Next step is to grant execute priviledges.

> $ chmod +x /bitsync/btsync

Now run

> $ /bitsync/./btsync

and that started it up. Now, that works great for a one time use, but if the NAS restarts then I have to manually ssh in and start it up again. Not very cool, so naturally I had to get it to start on boot.

> $ sudo nano /etc/init.d/btsync

I'm going to create a script to start up btsync on init.

```
	#! /bin/sh
	# /etc/init.d/btsync
	#
	 
	# Carry out specific functions when asked to by the system
	case "$1" in 
	start)
	    /bitsync/btsync
	    ;;
	stop)
	    killall btsync
	    ;;
	*)
	    echo "Usage: /etc/init.d/btsync {start|stop}"
	    exit 1
	    ;;
	esac
	 
	exit 0
```

This let's my start and stop the btsync program, and the NAS will know to toggle the starting or stopping depending on what the program is already doing.

Hit Ctrl + X to save, and make sure to confirm the save with Y (and hit enter). Once you've finished saving, you'll be back in the terminal.

Next thing I had to do was set up priveledges on the file.

> $ sudo chmod 755 /etc/init.d/btsync

So once you've done that, you can now test it out by doing.

> $ sudo /etc/init.d/btsync start

to start and

> $ sudo /etc/init.d/btsync stop

to stop it. Finally we need to set up the service to start at boot up, so

> $ sudo update-rc.d btsync defaults

At this point, I had everything set up and working great.

I went to [http://[NASIP]:8888/](/#) and bam, hit my bittorrent sync url with no worries.

One quick note: using the web server access point, when you add sync folder, you have to actually create the folders separately and then add an already existing folder through the web ui.

On my setup, I have a shared folder that holds all the data that I'm serving up.

So I went in there through my NAS interface and created a new share and I called it "sync". I gave a couple users access.

Then I went back into my ssh terminal and I created a separate directory outside of the shares to hold the bitsync program. One hiccup I ran into was that I had some permission problems on the directories I created with my NAS user. The underlying issue was pretty simple. I had taken away rights on my NAS user, and then created the directories. I then added the rights to the user to the sync folder I talked about earlier. However, the NAS wasn't smart enough to actually go through all the sub folders and set up privs inside the sync folder. I was told that I could restart the NAS and it would fix itself, but since I was still setting this up, I opted to just delete the folders, and readd them with the users' privs the way I wanted them to be to begin with. 

Worked like a charm.