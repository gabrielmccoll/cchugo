---
title: SCCM Solved -Task Sequence Error - objects referenced in the task sequence
  cannot be found
date: '2017-02-03 13:42:29'
image: https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/55c1d277a4c9c39cea9ec16f0be623ee.jpeg
layout: single
tags:
- sccm
- solved
- task-sequences
- system-center-config-manager
---

## System Center Config Manager Task Sequences (SCCM).

##### Version: 1610

Mostly used for deploying Operating Systems through SCCM, these cheeky guys bring some fresh hell to your learning burden.

I had a sequence much like the below (apologies this is not my actual screen shot as I don't get the error anymore,I took this off Microsoft.com somewhere).

The objects referenced in the task sequence cannot be found. Verify that the objects exists and that the task sequence references the correct object name and location.

![The objects referenced in the task sequence cannot be found. Verify that the objects exists and that the task sequence references the correct object name and location.](https://social.microsoft.com/Forums/getfile/3306/)

What you mean can't be found. I added it from a list you nugget!

So I googled, much as you are doing now o humble seeker of knowledge.  
 I found a lot of smart people with a lot of great solutions that worked for lot of other people but my solution was not there.

It turned out to be surprisingly simple.

You might not know but I learnt from the great [Mick Pletcher ](http://mickitblog.blogspot.co.uk/)that you could create an application that didn't have any content location.

![SCCM Application Deployment Type](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/image-7-e1486128868260-300x66.png?resize=300%2C66)
This bit. SCCM Application Deployment Type

Like so.This means you don't actually pull anything down to the local machine to install. It's handy for v fat apps.  
 That being said, it causes the above error in task sequences.  
 So all I did was make the content location a folder with a small text file, just so it had something and boom. Task Sequence in SCCM was all fine.



AWWWWW YISSSSSS!




