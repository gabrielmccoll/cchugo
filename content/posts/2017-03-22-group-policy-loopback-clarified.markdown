---
title: Group Policy Loopback Clarified
date: '2017-03-22 14:51:57'
layout: single
tags:
- group-policy-object
- loopback
- gpo
- microsoft
- server-2012-r2
- windows
---



## Group Policy Loopback. It‘s a saucy wench.

For those not in the know, GPO loopback makes it so User policies apply to Computer objects. It‘s predominantly used for kiosk / shared PCs where you don‘t want anything to change, regardless of which User sits at it.

There‘s two modes, Merge and Replace.

![](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/2017-03-22-14_33_42-Group-Policy-Management.png?resize=525%2C184)

The following clarity is brought to you based on the idea you know a bit about GPO and how to apply it. Some points follow just as a reminder.

1. Group Policies are linked to an OU. It applies to everything in that OU and OUs nested below it unless: WMI filtered, SecurityFiltered, Delegation rights are changed,the GPO status is disabled or GPO Inheritance is disabled on the OU.
2. Loopback setting applies to **EVERY COMPUTER** the GPO applies to. It is a setting applied tothe computer. It is **NOT** just a setting that applies to othersettings in that GPO. i.e. if you put it at the top of domain, you Loopback process **EVERY PC IN THE DOMAIN** for **EVERY POLICY**. This will seriously impact your User specific policies.
3. There are two modes. Merge and Replace. Merge will take User settings (which turn into Computer settings because of Loopback) and push them together with the Computer settings. Replace will ignore User settings that only apply to the User. If the User settings are applying to Computer object, it will take them. Examples follow because honestly that‘s a nightmare to try and read.

Here, is how we‘ve got it setup. On the Left are how the GPOs are applied. There is no WMI/Security/Delegation filtering. On the right is the objects in the OUs.

![Loopback](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Loopback.jpg.png?resize=525%2C421)

In this example, We have Loopback Merge at the top of the domain. Now that Only contains loopback settings. No other settings. Once again, Loopback is a setting itself that applies to Computer objects, it is NOT an extra setting to apply to the rest of the settings in that GPO. (you don‘t turn loopback on just for this gpo).

User1 logs into TCUTIL. All the policies in green apply. TCUTIL gets Computer settings, User Settings 2 and User Settings because the loopback applies those User Settings 2 to the computer object. User1 receives User Settings and it‘s in Merge mode so those pull through and get merged

![Merge](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Loopbackmerge3.jpg-OneDrive.jpg?resize=525%2C420)

Example 2, exactly the same but we‘re in Replace mode.  
 Now, User1 doesn‘t get User Settings because the policy is not in the same OU as the computer object. It‘s only applying to a User object and Replace isn‘t interested in that. So it‘s binned.

![Replace](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Loopbackreplace2.jpg-OneDrive.png?resize=525%2C416)

Example 3. Someone‘s went mental and got 2 loopbacks running wild. A Replace at the top of the domain and Merge further down. Standard GPO order of application goes so the Merge wins. It is treated the same as Example 1.

![ReplaceandMerge](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/Loopbackrepmerge.jpg-OneDrive.jpg?resize=525%2C423)





Hopefully that‘s helped explain how it works. I got caught by the idea that it applies to the other settings in the policy and isn‘t a global setting in it‘s own right for a long time.

Best practice is to minimise loopbacks, or restrict to an OU for kiosk style machines.

