---
layout: single
title: The JFDI guide to AWS IAC
date: 2019-04-05 09:30:34 +0059
category: 
tags: [aws,cloudformation,JFDI,IAC]
---


## Welcome to the first in the JFDI series!

There is a LOT of info out there about best practices, which tools are good, which tools are garbage and the absolute best way to do everything.  

This blog series is about something different. It’s about just getting on with it and starting to try things.  
It’s about trying something with the minimum of fuss without painting yourself into a corner. 
It won’t be "how to be the best", it’s **"how to get past decision paralysis and just start learning"**

At the end of the post will be the "DO MORE!” section that will have ways you can build on the skeleton framework we go over in this post.

JFDI means Just Do It without Fuss.  What’s that ? Those letters don’t line up? 
My bad.. 
 

## Agenda 

 We’re going to get you the means to get started using Cloudformation to deploy Infrastructure as Code and build up a VM (ec2)  with an attached Firewall (security group).  In the real world you’d grow this to add it into a VPC , Subnet and so on but this is a JFDI post. It’s not what it’s about. It’s about wetting your whistle. 
 Minimum of fuss.   
 We’re going to get your code writing local environment set up decently though, so that it’s easy to continue to go further. Little bit beyond minimum, but worth it.  
 
#### Requirements  (I’ll explain some of these more later)
 
- [AWS account - free tier is all fine](https://aws.amazon.com/console/)
- [Visual Studio Code](https://code.visualstudio.com/download)
- Cloudformation plugin by aws-scripting-guy
- vscode-cfn-lint plugin by kddejong
- [Python 3.x](https://www.python.org/downloads/)
- cfn-lint (from PiP)   
- About half an hour or less
  
#### Out of Scope explanations: 
- **IAC - Infrastructure as Code**  I’m not going to go over the benefits of this.   JFDI!

- **Source Control / Git**  I’m not going to go over the benefits of this or even mention it much in this post. That being said, JFDI.  It’s not that hard to get a one person flow going. 
- JSON templates instead of YAML. 



## Let’s FDI !

At a high level here’s what we’re going to do

- Install a plugin that both helps format YAML and offers snippers for Cloudformation Resources (READ THE INSTRUCTIONS ON THE PLUGIN, You have to edit the settings to stop incorrect red lines.)

- Install a plugin that "lints" your Cloudformation template and tells you whether your template is hot garbage or whizzbang good. The only two standards worth using.  ISO has nothing on them.  This one’s got a few more steps.  

- We’re going to write a template. Save it. 

- Open the AWS console and tell it to use it to build some dope infrastructure. Dope is a word the kids use to mean good, not silly as you might expect. 


**Round 1 - FIGHT!**

Get an AWS account signed into,  Download Visual Studio Code, Install Python.  
There’s links from the requirements section. Why you even here if you’ve not done the requirements? 

Make sure Python is on your PATH too ideally.  So if you type “python"" in bash, or powershell it knows what it is. 

If you’re using a different shell you probably know enough to fix errors like it not being on the path. 
If stuck, type words into google.

**Round 2 - FIGHT!**

Open Admin Powershell / Bash whatever. You can even go to the python REPL I believe and do it there if you failed to get it on the path.   

type "pip install cfn-lint"

This is a Cloudformation python based linter provided by AWS. It can be run against templates from the cmdline.
Luckily, someone made a VScode plugin that runs it against your template in VScode and that makes it a lot easier. 
  
  ![](/image/aws.iac/IMG_0650.JPG)
  
  
  Once that’s in and successful. Open up VScode and install the following two extensions.
  Take note, the linter extension is just using that python program you installed in the background.
  So if you didn’t do that, it won’t work. 
  
  ![](/image/aws.iac/IMG_0647.JPG)
  
  ![](/image/aws.iac/IMG_0648.JPG)
 
 
 Okay, we are ready to go !
 
 **Round 3 - FIGHT!**
 
 
 Hope you’re ready for a lot of pictures. Maybe a Gif. Who knows. Am feelin’ saucy. 
 
 
 First thing to do is create a new yaml document.   
 Once you've got that, make sure terminal is open at the bottom from the View menu so you can see any Problems on the problem tab.

 Now being typing "start". 

 
 At which point you should see an autocomplete menu come up.   (see pic)
 If it doesn't, then something is wrong with the extensions. 
 Restart VScode and try again.   


![start snippet](/image/aws.iac/C9BEF6E2-59B8-4F0B-BD3A-096E2CFE1CE7.jpeg)


Hit TAB to autocomplete and it will show you the following. 
You don't NEED all those fields and this is JFDI so let's delete it to the bare minimum.


![blank initial start template no trim](/image/aws.iac/7E970EB3-411F-42BB-9C09-4A96493078A8.png)






This is a lot less intimidating. 

Notice for the Description I don't have to use quotes.


![trim start template ](/image/aws.iac/30BA0C05-CFE5-4639-B817-81E9E068B9D9.jpeg)


with YAML, it's all about indentation.

So come down a line and in one tab and begin typing ec2-instance , highlight the correct one and tab again.


![Adding EC2 instance](/image/aws.iac/145D3B07-A654-4884-9E02-7271E86331B6.jpeg)


Once again, you end up with way more stuff than you need to JFDI so let's trim it up.



![non trimmed ec2 snippet](/image/aws.iac/6B287667-3669-4C1B-B75F-BDFF5031BD68.png)


Just an example

Here is what it looks like when the linter thinks you're doing it wrong.

Null Value at line etc.   

This means, it's not filled in and needs it to be since you've specified the Key with no Value; 

But we're going to just delete it, along with the other unneeded ones.


![showing an error in linting - ec2 instance](/image/aws.iac/B64932D1-F5E0-419C-B02B-10544B11C069.jpeg)


Where did I get the AMI name  (instanceId)? 



https://aws.amazon.com/amazon-linux-ami/
![amazon Linux AMi](/image/aws.iac/4CE002B0-1ABE-4B9B-8BD2-3B12F3269643.jpeg)



And Boom. 

That's enough for the EC2 instance. Let's get the security group added.

Note how adding the security group MUST align with the EC2 instances.

Everything down that line should be a new Resource. 
  
![adding sec group snippet, showing misalign](/image/aws.iac/E75AB929-584C-4400-BF94-7230D26612C1.jpeg)


Now we've got the security group.

What use is a security group without rules though.

As per pic. Add the rule. 


![adding sec group rule init](/image/aws.iac/64300F4E-12AF-45C1-91D5-256D3EA1F813.jpeg)


Notice it will come in misaligned.

The "-" under SecurityGroup Ingress is how you would add multiple rules. Each one Tagged with that.

We are only having one but it's came in misaligned.  The First line for IPProtocol works ok. 
The next 3 are no use.



  
![sec group rule 1 - wrong align](/image/aws.iac/328DB083-D93B-440C-9EA3-818ED4ED0FFC.jpeg)



Shifted Along and looking NOICE! 

Notice how the problems have vanished. 

![security group ingress rule 2](/image/aws.iac/0919B7B8-3CDA-4302-B72D-0D1EF2F55ECD.jpeg)


And there we nearly are.

the complete template except... for the EC2 instance. 

How does it know to use that Security group ?

That SecurityGroupID doesn't look correct. 

In fact, I don't know the SecurityGroupID because the Security Group doesn't exist yet?!

Aw Naw!



![complete template no ref tho](/image/aws.iac/37E4F9B6-2DD0-4D15-AE62-4D193C09164C.jpeg)


Luckily AWS has some Intrinsic Functions that give you cosmic powers.

In this case we're using a simple !Ref to reference something else in the template using the name of the Resource within the template.  (secGroupName)


![complete template with ref](/image/aws.iac/3E8CE061-B4FB-41AB-9D4E-CF2727E05920.jpeg)


Okay, we're done. 

The linter says no problems.

Time to launch this into space. 

Log into AWS console.

Go To Cloudformation and Create Stack.



![create stack start aws console](/image/aws.iac/96C71862-5F36-40A4-9B70-F78DFC125015.jpeg)



Upload the template (make sure you've saved it!)


![upload the template](/image/aws.iac/28CABF50-5A86-4B2E-BE1E-B509CB338878.jpeg)


Give your stack a name.


![new stack name in aws console](/image/aws.iac/7B610F2E-7E38-414E-8920-9B5B3F1CC1E6.jpeg)

Just next through everything until Create.

Then watch this little firework display of excitement.

![creating the stack from template aws console](/image/aws.iac/E263EBF8-18F2-40B8-8F08-5E6DB05A5A51.jpeg)


In the Cloudformation console ,  you can click on the stack, Resources, see what Resources were create

Ooo Sensual.


![Stack creation](/image/aws.iac/0F6E8677-F3D3-436C-A809-191015EF018A.jpeg)


Drill into the EC2 Instance, We can see it has our Security Group


![showing stack ceated with instance having sec group](/image/aws.iac/8766EE62-A638-441B-85DE-BED0D09CFBDF.jpeg)




Drill into our Security Group, we can see it's got the Security rules.

![showing sec group created in aws with the rule](/image/aws.iac/BF62ABC2-BA4A-45BD-9372-71A69F954AD1.jpeg)







# Now Delete The Stack To Clean Up the Resources !




YES!



JFDoneIt.


Well Done.


## DO MORE!

Now if you want to get better and expand this example further.


- Add a VPC and Subnet 
- Have it run from Codepipeline after you check in to CodeCommit. 
- Use Parameters to make the template more reusable



