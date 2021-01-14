---
layout: single
title: The JFDI guide to Docker Swarm and ADO Agents
date: 2020-06-06
tags: [containers,docker,JFDI,IAC]
image: "/image/AzureDevopsDockerSwarm/DockerAgent4.png"
---

## Welcome back to the JFDI series!
Date: 06-07-2020


### UPDATE 12-06-2020

The requirement to add the tag :194 to the docker image is no longer needed. I've managed to add a :latest tag which means you can leave it off and it will pull the latest container version down, whatever that happens to be.
Secondly, if using the latest image, your ADOAgent will register with a random name. Meaning you can scale your swarm up to 2-3 ADO Agents if you want to try that.

#### Disclaimer

There is a LOT of info out there about best practices, which tools are good, which tools are garbage and the absolute best way to do everything.  


This blog series is about something different. It’s about just getting on with it and starting to try things.  

It’s about trying something with the minimum of fuss without painting yourself into a corner. 
It won’t be "how to be the best", it’s **"how to get past decision paralysis and just start learning"**

Let me be absolutely clear - nothing I write a JFDI guide about am I an expert in - and that's the point. You don't have to be expert to try something. You don't need one standing by.

If you are an expert, please let me know in response to wherever I've posted if I'm saying something outright wrong or scary. 

JFDI means Just Do It without Fuss.  What’s that ? Those letters don’t line up? 
My bad.. 
 
## Note

Container tutorials nearly all have a linux vibe to them. This focuses on Windows mostly because that's what I have and am most familiar with and I needed Windows Azure DevOps agents, plus other people are already covering Linux. 

Linux is the original and best for this sort of thing but not always a good option if you're all Microsoft.  I.e. if you want to run Windows only workloads and/or don't have a scooby about Linux.

I'll call out implementation stuff that is probably not prod ready. 

Don't stress about doing this. It's playing. 
It's the art of the possible. 
Spark that innovation gland, which is definitely a real thing and not the ramblings of a simpleton.


## Agenda 

1. We're gonna use a pre-existing Docker Agent image I already made to create a container, that will then download and install the ADO Pipeline Agent. 
2. The Container based Agent will then self register with Azure Devops Default Agent Pool. 
3. The Agent will run ONE job. 
4. The Container will unregister the agent and die.
5. Docker Swarm will then see that we don't have a Container running and will create and register a new Agent, ready for another job.  

The Container is a super basic Windows Server 2019 based one. 
This gives you the nice container lifecycle whistlewetting. 
One Process, does one thing, then dies and is replaced.
Your whistle is gonna be SO moist after this.
 
#### Requirements   

- [Docker for Desktop](https://www.docker.com/products/docker-desktop) (or anything else you've got like a VM with docker on it from AWS or Azure). You need Win10 Pro or Enterprise if you're windows for Hyper-V. That link has all the explanations.
- [Azure DevOps account](https://dev.azure.com/)
- Internet connection
- basic Docker skills
- I do all this on a single Windows 10 pro laptop, so anything along those lines should be fine.
- small amount of moxy
- weak lemon drink
  
##### Optional 

- [Visual Studio Code](https://code.visualstudio.com/download) for running the commands in and saving the text of the commands but you don't need it. A Powershell window will work fine. 

##### Why Not?

- use Kubernetes? 

    *way harder to get going for a simple Windows test and play. It also doesn't work on Windows, you have to create the management setup on linux vms. A much steeper onboard ramp*

- Some other thing that takes way more effort?


    *Because that's not the point of this type of post. This is to help people just get shifting.*

- Just not bother because it's not Linux? *Yes that's a real actual thing people comment when I share these posts.* 

    *Because it's not Linux vs Windows containers as an option for some places. It's windows containers or no containers and I believe containers have value on windows only setups too.*


#### Out of Scope explanations: 

- How to sign up for Azure Devops.
- How to write a Dockerfile ([You can check out my previous JFDI post for that](https://cloudconfusion.co.uk/JFDI-guide-to-docker/)
- How to get this production ready for your super important real job) 
- Troubleshooting beyond what I've wrote here. Your setup might be different for some reason.
- Indepth explanation of the dockerfile.  It has been tweaked to work better and actually deregister the agent but nearly all of it plus the script etc came from Microsoft themselves [here](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/docker?view=azure-devops) 

#### Links:
[Dockerhub Agent by me](https://hub.docker.com/repository/docker/gabrielmccoll/windowsadoagent)

[The Scripts for all this, Dockerfile etc](https://github.com/gabrielmccoll/AzureDevopsDockerPipelineAgent)


### FINALLY - Let's GOOOOOOOO

Hope you all ready for what is basically Docker Swarm by a Dummy. 
Not a typo.

With Docker Swarm what you do basically is create a Swarm of nodes (computers).  
That Swarm node can be a manager or a worker or both. 
You create a Service that uses the Swarm and that Service does tasks.   
e.g. The Service is ADOAgents.  Tasks are when it spins up a new container for the ADO agent. 
That's a not great explanation but it's enough to push on with.
 
We'll be setting our desktop up to be both for purposes of this demo / play.  

You don't have to install anything extra to use Swarm, if you've managed to run the Docker hello-world and you're on Windows Containers, you should be ready to go.  

#####Step 1 - cut a hole in.. Nope. I mean Initialise a Docker swarm. 
You have to put in your private IP Address for your interface (ipconfig to find it)

```
docker swarm init --advertise-addr=192.168.1.221 --listen-addr 192.168.1.221:2377 
```

![Docker Init](/image/AzureDevopsDockerSwarm/DockerSwarmInit.png)

#####Step 2 - Create the ADO agent service. 

This takes a bit of explaining.  [Here's the Microsoft site but I'll call out what's going on](https://docs.microsoft.com/en-us.virtualization/windowscontainers/manage-containers/swarm-mode). 
We're creating a service, the service is called ADOAgent. Endpoint mode has to be DNS Round Robin for windows.  So just ignore that. 
The -e parts are Environmental Variables. These get passed to the Container when it spins up from the Image.   So that's your https://dev.azure.com/yourorg, your API key that has permissions to read and create Agent Pools and the name the Agent will have when it registers. 
Those get used in the script that runs when the Container spins up. (start.ps1 - go look at the dockerfile or github linked above.)
gabrielmccoll/windowsadoagent:194 is the Image used to create the containers for this Service. (check my dockerhub for more info on it)
**This leaves the API key in plain text and is bad security. You need to fix that before getting more serious but for now, just pass it**

```
docker service create --name=ADOAgent --endpoint-mode dnsrr -e AZP_URL=<Azure DevOps instance> -e AZP_TOKEN=<PAT token> -e AZP_AGENT_NAME=DockerContainerAgent  gabrielmccoll/windowsadoagent:194
```
Please note. I don't have a 'latest' tag at present. So select the correct version (194 as pictured) 

![Docker Swarm Service Create ](/image/AzureDevopsDockerSwarm/DockerAgent1.png)


Assuming it said the service converged (ooo so fancy talk!), you can then list the services and see your shiny new service


![Docker Swarm Service List ](/image/AzureDevopsDockerSwarm/DockerAgent3.png)

Once you get the service ID from the list command, you can list the task associated with that service ID  (you only need the first few letters of the service. hence the hilarious 'pp' in the screen shot)

![Docker Swarm Service Tasks ](/image/AzureDevopsDockerSwarm/DockerAgent2.png)

After a few minutes, you should see the Agent registered in your Default Agent Pool in Azure Devops.

![ADO Container Agent added to pool, no jobs ran ](/image/AzureDevopsDockerSwarm/DockerAgent4.png)


You can either create a sample pipeline or use the DockerAgenttest.yml in my linked github. 
Run it! 

![ADO Pipeline Agent tests ](/image/AzureDevopsDockerSwarm/DockerAgent5.png)

If you go in to check your job, you'll find that it requests you give it permission. This is because a new pipeline needs authorised to use Agent Pools. You'll only need to do this once. You don't need to do it per Agent, just per Agent Pool. 

![ADO Pipeline Agent needs authorised for a new Pipeline ](/image/AzureDevopsDockerSwarm/DockerAgent6.png)

![ADO Pipeline Agent needs authorised for a new Pipeline Permit ](/image/AzureDevopsDockerSwarm/DockerAgent7.png)

You can watch it run and see it complete.

![ADO Pipeline Agent watch it run](/image/AzureDevopsDockerSwarm/DockerAgent8.png)

It should then unregister from the Pool.

![ADO Pipeline Agent then it unregisters ](/image/AzureDevopsDockerSwarm/DockerAgent9.png)


Docker Swarm will then start a new Task for the service (remember the command docker service ps <serviceid>(pp was the example id))

![ADO Pipeline Agent gets spun up again by Docker Swarm](/image/AzureDevopsDockerSwarm/DockerAgent10.png)

After a few minutes, you will see a fresh new version of the Agent, added to the Agent Pool.  (see how there's no 'last run')

![ADO Pipeline Agent registers again, for a next job ](/image/AzureDevopsDockerSwarm/DockerAgent11.png)


If you're done playing, you can now remove the service. (it just echoes the service id back at you when you rm)

![Cleanup the docker service ](/image/AzureDevopsDockerSwarm/DockerAgent12.png)

### remember to leave the swarm with 
```docker swarm leave --force```
(not pictured)


You'll need to delete the Agent from ADO 
since this is not a graceful shut down of the container. 

![ADO Pipeline Agent needs removed manually if you're killing it all.](/image/AzureDevopsDockerSwarm/DockerAgent13.png)

And you're done!

Hopefully that's helped spark up some fire in your belly and a hunger to play more. 


### Improvements / next steps

There are many options to improve this !

- Give the agent a random name so you can have more Container agents running at once

- Improve the security of the API key environment variable

- Kubernetes instead of Swarm

- Make the Docker Image capable of more workloads by upgrading the Dockerfile.

go forth and improve! (if you get anything good feel free to fire a Issue into my Github repo to help me out.)