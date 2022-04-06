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

### install and setup minikube 

After you install this, Vscode no longer shows containers etc in the gui if you'd setup like I said in previous post.  I'm not sure how to fix it but basically it's because minikube has it's own version of podman running inside it.   At present it is version 3.x, while podman is 4.0, which is why podman env doesn't work. 


