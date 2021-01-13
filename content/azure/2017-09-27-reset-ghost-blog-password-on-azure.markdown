---
title: Reset Ghost blog password on Azure
date: '2017-09-24 13:53:45'
layout: single
category: azure
tags:
- email
- foss
- exchange
- azure
- linux
- namecheap
- mailinabox
- mail-server
- postfix
- powershell
- open-source
- ubuntu
---

# The ole locked out with keys inside


so I made a Ghost blog on an App service on Azure and immediately forgot the password. Didn‘t store it in Lastpass. Nothing. Full on chimp mode. No worries I‘ll click to reset my password link. Easy.

Oh yeah, I didn‘t hook it up to any email so it can‘t mail me a link.  
 No problem, I did it in a hurry so it‘s probably one of these standard dev passwords I use for testing. nope. Okay.. I‘ll try ...locked out Well.. shit.

### The Hunt for a solution begins!

It didn‘t take that long to find some nice chap telling how to reset and unlock. I‘ll repeat some of his stuff here for completeness but shout out to[http://sharadchhetri.com/2015/06/07/ghost-blog-reset-password-and-activate-user-from-sqlite/](http://sharadchhetri.com/2015/06/07/ghost-blog-reset-password-and-activate-user-from-sqlite/)

So I had two main problems.

1. The webapp on Azure panel doesn‘t give great direct access to files to get ghost.db
2. wasn‘t really sure how to sqlite3 on windows too easy since I couldn‘t find basic installer or anything.

### Here‘s what I did..

You will need:

1. Bash on Ubuntu (Windows Subsystem for Linux)
2. A weak lemon drink
3. Communism

So I decided I was just going to pop open the Linux shell on Windows and use that to get sqlite3 much easier. But that‘s because I already had it ready to go. If you haven‘t then you‘ll need to figure something else out to get sqlite3.

If you‘re not using Windows Subsystem for Linux and installing Ubuntu from the Windows store then go enable that feature and do it. I will drink my weak lemon drink while I wait. You may need to join the insiders program if you haven‘t already but you should do that because only tramps aren‘t Insiders these days.

### Stage 1 ‘ gimme dat database

So log into your azure portal.

Go to the webapp in question and then go click the new thing and go

The App Service Editor

![app service editor](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/app-service-editor.jpg?resize=525%2C313)



Then click on the Explorer menu and navigate to ghost.db. Click it on the right there at the hyperlink and boom, it downloads to your Downloads folder.

![app service editor ghost.db](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/app-service-editor-ghost.db_.jpg?resize=525%2C218)

### Stage 2 ‘ Now what Brain?

Now you slap open Ubuntu, or gently caress if you‘re a caring sort.

and install sqlite3 (sudo apt-get install sqlite3)

once Installed

navigate to your documents folder. (from Ubuntu it will be cd /mnt/c/Users/USER/Downloads)

then sqlite3 ghost.db

you‘ll get an sqllite prompt so type

UPDATE users SET password=‘$2b$10$MoxzGW2Lt6e.votfa8y/z.RbftTM.Id76YJMM3wdLuEMB4O7bjJFu' WHERE email = 'name@domain.com‘;

This will set the password of whichever email address to password (change it once you‘re in. Don‘t forget it this time), and remember the ;

Once that happened, I uploaded it back. Tried it and was reminded I was still locked out ya total gimp. I hadn‘t unlocked my user account

Sooo lucky I still had the ubuntu shell open

sqlite3 ghost.db (again!)

UPDATE users SET status=‘active‘ WHERE email = 'name@domain.com‘;

NOW you‘re reset AND unlocked. Easy GG.

### Wait, how do I upload it??

![app service editor ghost.db](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/app-service-editor-ghost.db_.jpg?resize=525%2C218)



Go back to there and drag and drop, it‘ll ask you if you want to overwrite.

BOOM, You are back baby!

What about the Communism ? It was just a [red herring](http://www.imdb.com/title/tt0088930/).




