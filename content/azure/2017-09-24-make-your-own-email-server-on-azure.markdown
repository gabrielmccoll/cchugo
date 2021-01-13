---
title: Make your own Email Server on Azure
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



# It‘s mostly for bragging rights

Heya, so first off before we even get started, as simple as this is ‘ it‘s something you‘re probably not going to want to bother with unless you‘re interested in tech / IT stuff.

Secondly, this is not actually supported in Azure without using the SendGrid or similar type relay as per the bottom of this post as per[https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/. ](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/)

Mines is working, others work but don‘t throw this up in mission critical production or anything.

Assuming you do want to get your hands dirty, I‘m going to show you how to setup a working email server, in Azure, using Namecheap as your registrar.  
 Because that‘s what I did and I‘m not THAT altruistic that I‘ll try lots of other stuff for you.  
 You lazy slag.

Similarly, I‘m not going to go hard into the basics because honestly, if you‘re going to host your own email server, you‘re going to need to be robust enough to do a little bit of thinking on your own.

I‘ll be basing this guide off Namecheap, but feel free to use the generic instructions in the mailinabox guide if you‘re not. Also I‘m assuming you‘re using box.mydomain.com as your name as per the mailinabox guide so it‘s on you if you‘re wanting different names.


## Requirements

1. Azure Account.
2. Domain Name owned.
3. Windows PC (maybe, You might be able to run this script with Powershell Core/ 6 I just haven‘t tried it) .
4. Bit of the ol‘ IT skills and bravery specifically some Azure and a LITTLE bit of Linux
5. ┬ú4 a month it looks like so far.

**Note**: this doesn‘t actually take that much of your time, say a couple hours. It might take a couple of days waiting on DNS and stuff to get fully up and running though. Patience and all that.

Most of the really clever stuff was done by the amazing mailinabox script and project:  
[https://mailinabox.email/](https://mailinabox.email/)

My contribution is the script to make up the Azure Ubuntu Server that you will then put mailinabox onto:  
[https://github.com/gabrielmccoll/MailinBoxAzure](https://github.com/gabrielmccoll/MailinBoxAzure)

This blog post is basically an addendum to the [mailinabox guide](https://mailinabox.email/guide.html), and specific [Azure](https://portal.azure.com/) related issues I found. Open the guide and have it alongside this one. I recommend you read this all before you start. Neither of the guides will take more than a few minutes each to read and some of it you might not even need to do if you get lucky with your IP.


## Summary of how build your own email server:

1. Build your Azure server using the script provided.
2. Check the server IP addresses on blacklists and swap it if it‘s guff.
3. Go to Namecheap and set the DNS records.
4. SSH using putty to your Azure server and run the mailinabox script and follow the instructions in the guide.


# 1. Build the Azure Server

[https://github.com/gabrielmccoll/MailinBoxAzure](https://github.com/gabrielmccoll/MailinBoxAzure)

Okay so my script can be run with just a few tweaks, or maybe not even any.

Depending on where you are, you‘ll need to change the VM Size. I‘m using the new preview B sized VM machines. These are good for this as you get a decent server for cheap, based on the idea you barely use all the cpu most of the time.

They‘re not available everywhere so you might want to switch to an basic A1 or so.  
 You take a look and make your own decision. I started off bigger and then dropped size when I checked I was barely using stuff.  
[https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-general](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes-general)

The script makes the server, the network, the subnet, the network level firewall with the correct ports open, the public ip address and the network card. It also adds in the Reverse Fully Qualified Domain Name.

**NOTE: it sets the server up to use username and password rather than SSH keys.  
****I recommendyou switch to SSH once you‘re setup and ready.**


# 2. This is Clem Fandango, Can you hear me?

So one of the problems you might have once you‘re setup if that your IP you‘ve been given by Azure is on blocklists.

The first thing to do is to check the below blocklists and see if you‘re on it. I don‘t think anyone ever escapes the multirbl list entirely. My IP is on a few .ru russian lists. I‘m not going to be too worried about it as long as the other 2 come back clean. I checked about 5 IPs from Azure, out of curiosity. 4 out of 5 came back totally clean (except for russians),1 was on 1 blocklist. That‘s not bad.

### Blacklist Checkers

[https://mxtoolbox.com/  
](https://mxtoolbox.com/) [http://multirbl.valli.org/lookup/](http://multirbl.valli.org/lookup/) [  
 https://www.blacklistmaster.com/](https://www.blacklistmaster.com/)

If your IP IS on lots of block lists, just go into the Azure portal, make up some new public IPS (STATIC!), check those and attach the good one to the NIC created with the script

##### Get off Microsofts Blacklist

You should then test emailing gmail and hotmail / outlook since those are two big ones and see if you go in spam. For hotmail, I got a rejection email back saying

> Hotmail-com.olc.protection.outlook.com[65.55.92.136] said: 550 SC-001  
>  (SNT004-MC1F14) Unfortunately, messages from MY IP weren‘t sent.  
>  Please contact your Internet service provider since part of their network  
>  is on our block list.

I then had to fill the form in to get off Microsoft‘s block list, and was up and running and cleared within 24 hours.

##### Get off Microsofts Blacklist

[https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wfname=capsub&productkey=edfsmsbl3&ccsid=635688189955348624&wa=wsignin1.0](https://support.microsoft.com/en-us/getsupport?oaspworkflow=start_1.0.0.0&wfname=capsub&productkey=edfsmsbl3&ccsid=635688189955348624&wa=wsignin1.0)


# 3. Where am I going Bruh?

You need stuff to be looking at your server for your domain so you need to setup the glue records / name servers. Here‘s how you do that.

Sign into Namecheap

Click on Manage for the Domain you‘re looking for, then advanced DNS

Go to to bottom, and add personal name servers for ns1.box and ns2.box . The Ip is the Public IP of your Azure server you created above. (This is why we checked the blacklists already, you‘d need to redo this if your IP changed). You do NOT need to add .mydomain.com to this. It‘ll do it automatically when you assign the nameservers.

Take mental note of the DNSSEC part, you‘ll come to this page to set that up. I‘m not providing instructions, mailinabox will do all that for you. It comes later.

![namcheapdns](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/namcheapdns.jpg?resize=525%2C538)



Then head to the Domain tab and change the dns to the ns1.box.mydomain.com and ns2.box.mydomain.com

As with all DNS , this could take 48 hours before it starts working. You don‘t need to wait right now tho.

![namcheapdns2](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/namcheapdns2.jpg?resize=525%2C352)




# 4. Prepare Ship, for ludicrous speed.

Finally. All that prep. So bored.

Now onto the good bit. Open Putty and SSH to your Azure Server be like:

adminusernameIsetduringthescript@55.344.21.22 (Public Ip of your server)

And follow all the instructions ( there‘s not many).

The Mailinabox guide will take you the rest of the way. It‘s well written.

**NOTE:** worth just sitting watching it as it does its thing. It only takes a few minutes and you don‘t want to miss the window to say Y to the Let‘s Encrypt free certificate for SSL


# .. Okay ÔÇª Now what?

The server is running !

go to your box.domain.com/mail site (if DNS is finished) and try sending some mails.  
 I found issues with sending to Hotmail like I said in section 3 but gmail worked fine.

### What if it doesn‘t work and I get put in spam still?

You might need to try a relay to go between them and in fact that‘s the only official supported way with Azure. I suggest Sendgrid as you can add it in the Azure Portal (just search for it like you would add anything in Azure) and get 25,000 emails a month free. That should be fine for you. You then login and manage it using the Azure portal as it sets up the name and password etc automatically.

![](https://cloudconfusionsa.blob.core.windows.net/blogimages/2017/sednazuree.jpg?resize=212%2C192)



These are the instructions you need. [https://sendgrid.com/docs/Integrate/Mail_Servers/postfix.html  
](https://sendgrid.com/docs/Integrate/Mail_Servers/postfix.html)

You‘ll have to ssh to your mailbox then navigate to /etc/postfix/main.cf and use some basic linux skills to back it up and edit it. . It is okay to add the block of text into the end. You‘ll get a warning a couple of settings are duplicated and overwritten but it works fine.  
 Make sure you take a cp of main.cf before overwriting it.

I also found the instructions I needed to start , stop and reload the instructions were

> Sudo Postfix Stop && Sudo Postfix Start && Sudo Postfix Reload

However, I found that I didn‘t need to do this as I figured out how to get my IP removed anyway. I‘m just posting what I found to help anyone else.

You also will want to set up white labelling on Sendgrid (find the menu item when you log in ‘ reminder, you log in by clicking manage in the Azure Portal)

That‘s as simple as going to box.yourdomain.com/admin then signing in and choosing Custom DNS from the menu then adding the records it tells you from Sendgrid

> Important ‘ Remember the period at the end of the cname e.g. make sures it‘s  email@yourdomain.com cname sendgrid.net.   << that full stop at end of .net (or whatever the record is supposed to be) is v important.


# Can I use apps?

Ya sure can little buddy!

You get a Calendar and Contacts too and I found it all autosynced if I used the Outlook app and just put my username and password in. I also got it working in Mail app on android using SSL as the settings.

The instructions are at your newly setup mailbox, using the mailinabox username and password ( MIGHT BE DIFFERENT THAN YOUR AZURE SERVER USERNAME AND PASS IF YOU‘VE NOT SWITCHED TO JUST SSH)

https://box.yourdomain.com/admin


