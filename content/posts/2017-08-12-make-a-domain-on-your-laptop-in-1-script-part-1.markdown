---
title: Make a domain on your laptop in 1 script - Part 1
layout: single
date: '2017-08-12 22:13:13'
tags:
- hyper-v
- powershell
- server2016
- testlab
---



## Wot are you on about ?

TBH this has been superceded by Lability which is much better. just google that. It's much better than the trash I did

If you‘re trying a lot of random stuff , you probably end up spinning up a lot of evaluation domains. 6 months is quite a long time but even so, remaking the whole thing is a total pain in the hoop.

Sure you can use Azure, and should for some of it. But what if you‘re going on a train journey or want to try new Server 2016 junk, Am I meant to install this stuff myself? using my hands and my eyes and my face ?. Yep. ┬áOR you can use this script I‘ve been working on. It only does the initial bit at the moment though

> **Egg Shen**: See? That was nothing. But that‘s how it always begins. Very small.

Now. Some caveats, It‘s not super optimised and it‘s a bit beta but it‘s not too shabby either.


## SHOW ME WHAT YOU GOT!

What this will do for you is

1. Extract your evaluation ISO
2. Create a customised autounattend.xml so you can make up your own Domain ame etc
3. Move the customised autounattend into the extracted files with an admin name and so on
4. Turn the extracted files with autounattend into a bootable iso again
5. Create a gen 1 VM in Hyper V ┬áwith VHD
6. Hook the ISo we made up to the VM and boot it up, starting the install.
7. All you need to do is manually select your install TYPE (gui/no gui) , rest should complete auto.


## That‘s pretty cool, you‘re v handsome.

Thanks ! Help me test it ? make it better?


## Do I need any stuff?

Yep, at the moment you will need

Evaluation Iso  
[https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016)  
 Hyper-V feature installed on.  
 Download and install 7Zip

You‘ll need to scroll through the script and edit some file paths / usernames /passwords but tbh you should be scrolling through scripts some random maniac has posted on a website before you run them on your PC.  
 Cmon, you‘re not an Apple user.  
 Or maybe you are. #PowershellCore.

You can get the script you need (Createfirstdconshot) from┬[https://github.com/gabrielmccoll/TestLab](https://github.com/gabrielmccoll/TestLab)






