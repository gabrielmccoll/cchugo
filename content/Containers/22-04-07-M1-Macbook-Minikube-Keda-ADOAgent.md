---
layout: single
title: Keda and Minikube on M1 Macbook for self triggering ADO Agents
date: 2022-04-07 
tags: [containers,docker,AzureDevOps,podman,minikube]
image: "/image/Containers/M1podmansetupADOagent/ADOlogo.jpeg"
draft: true
---

## Event based Scaling Kubernetes with Azure Devops 

My continuing adventures with trying new stuff on the M1 macbook. 
This my first macbook and first time using minikube and also keda and first time using arm64 for anything big. 
That's a smart combination to do right?
There's good guides all over. I've be done in no time 

## 9 hours 27 minutes later...

Welll okay. 
Instructions and Gotchas follow.


### Index 

Files: [Github link with files](https://github.com/gabrielmccoll/AzureDevopsContainerAgent)

1. install podman and setup podman machine
2. install kubernetes extension on vscode
3. install minikube and setup - gotchas with podman env and vscode
4. install keda - gotchas with versions pre 2.7 (unreleased)
5. Azure DevOps Agent build onto minikube cache
6. Running the sample from keda and changes required for podman


### install podman and setup podman machine

See [previous post](https://cloudconfusion.co.uk/containers/2022-04-04-m1-macbook-air-ado-agent-podman-container/) but also minikube requires 2 cpus so
the podman machine command has to have that and it might need root, not sure so I used root.

**INSERT COMMAND*

#### Troubleshooting 
rm -r ~/.redhat
rm -r ~/.minikube 

### install and setup minikube 

After you install this, Vscode no longer shows current containers etc in the Docker Extension gui if you'd setup like I said in previous post.  I'm not sure how to fix it (and do please but leave me an issue up at github if you know how) basically it's because minikube has it's own version of podman running inside it.   At present it is version 3.x, while podman is 4.0, which is why podman env doesn't work. 

Podman-env doesn't work at moment, here's how to test if it works with newest version of minikube and then revert back -
**TODO**


### install kubernetes (k8s) extension to vscode

Nothing fancy - just install this one and done.


### install keda 

Depending on what version of keda this might be easier or hard.  arm64 support isn't coming until 2.7.
That, at time of writing, isn't coming out for a couple months.  
I have used a version compiled by a very kind contributor as per this thread. 
This means you have to not use the helm chart installation method and instead use the straight yaml.
Modified to use the keda images mentioned above.   The file **TODO* in my github is what you need. 

### building the image onto minikube cache

There's a few ways to get the image onto minikubes catch , the only one I got working is using the 
```
minikube build -t dockeragent:latest . 
```
command in the same place (i.e. where the dockerfile etc is) I would normally run just straight podman build. 
This then resulted in the image showing up in the Docker extension in vscode as it seems to plug into the minikube podman
which gave me confidence it had worked.  

running minikube ssh  and then sudo podman image ls confirmed it. 

### Well we made it - running the K8s deployment of ADO Agent.

Following this article for the scheduledjobs part mostly but swapped it to engage with podman socket. 
I don't know if this is needed, I think podman install basically does that for you but I did it anyway and it worked. 
Watch out for history setting in the yaml, it's basically what stops pods from dropping off. 
i.e. if it's set to 2 and you run two jobs, you will see 2 completed pods if you run 'kubectl get pods' which take resources.
Also by the time of writing I might have swapped out putting the secret of the PAT straight in.  
In my experience all the Permissions needed for the PAT is 'read pipelines' and 'read/manage agent pools' 
You could potentially break this up into two PATs I think but I haven't tried it this iteratiojn. 

#### Quick Test
I've added a sleep into the basic pipeline just to make sure there's time for a queue to work.
Check polling time in the yaml and adjust as appropriate


