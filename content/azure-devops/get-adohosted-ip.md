---
title: "Get Azure Devops Microsoft Hosted Agent IP"
date: 2021-01-16T17:28:17Z
tags: 
- powershell
- azure devops
image: "/image/AdoHostIP/adohostedipthumb.png"
---

# Hello there ! 
New blog, new post.  

I know what you're thinking, how can he keep up this breakneck speed of posting a whole 500 words every few months?!
I'm just that good basically. Also no quality checks. Just. None. 
Keeping it nice and quick then, but we'll both be satisfied. 

Shortish script to get you the external IP for Microsft Hosted Agents in Azure DevOps (ADO).
It's in Powershell and will work on ubuntu or the windows agent as far as I've tested. 
I save this as a file called 

*Get-ExternalIP.ps1*

``` powershell 
#bit of redundancy in case one of them is down.
$sites = @("http://ipinfo.io/ip"
"http://ifconfig.me/ip"
"http://smart-ip.net/myip")

#make sure we're pulling back the IP address
$pattern = "^([1-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])(\.([0-9]|[1-9][0-9]|1[0-9][0-9]|2[0-4][0-9]|25[0-5])){3}$"

#get the first site that responds and then return the IP addess
foreach ($site in $sites) {
    $IP = Invoke-RestMethod -Method get -Uri $site -TimeoutSec 15
    if ($IP -match $pattern) {
        $IP 
        break
    }
}

```

### Why am I even using this?

Say you've got an Azure Function with a public URL, you want to lock it down to just your internal work IPs.
Then you try to deploy an update via the microsoft hosted agent and boom, IP denied!!!.

This is because the Microsoft hosted agent can have one of like 10,000 IPs and you don't want to allow-list all of them.
You can use Service Tags but it's STILL a lot of jabornis who can attack you that way. 

So what you do is have steps in ADO like this.

1. Get the Agent IP
2. Add the Agent IP to the allow-list on the function (it's using a different auth plane as it's on the Azure Platform plane and not accessing the function plane)
3. Deploy the Function
4. Remove the Agent IP from the allow-list (because it will just grow)


Here's example scripts that call them:

*Add-AzureFunctionAllowHostedAgentIP.ps1*

both are in the same folder. 

``` Powershell

param (
    [Parameter(Mandatory)]$SubcriptionName,
    [Parameter(Mandatory)]$ResourceGroupName,
    [Parameter(Mandatory)]$FunctionAppname, 
    [Parameter(Mandatory)]$slot 
)

$IP= $(./Get-ExternalIP.ps1) + "/32" #using previous script

Get-AzSubscription -SubscriptionName $SubcriptionName |Select-AzSubscription
#get the next free priority, you can apparently share a priority with other things but it seems bad practice. 
#it might not be, might be fine and this is just drama in the script. 
$priorities = (Get-AzWebAppAccessRestrictionConfig -ResourceGroupName $ResourceGroupName -Name $FunctionAppname -SlotName $slot).MainSiteAccessRestrictions.Priority| Sort-Object 
$count = 1
do {
    
    $priority = $priorities[0] + $count
    $count = $count + 1

} until ($priority -notin $priorities)

Add-AzWebAppAccessRestrictionRule -ResourceGroupName $ResourceGroupName -WebAppName $FunctionAppname `
-Name ADOAgent `
-Description "The Hosted Agent deploying the app" `
-Priority $priority `
-Action Allow `
-IpAddress $Ip `
-SlotName $slot
```


*Remove-AzureFunctionAllowHostedAgentIP.ps1*


``` Powershell

param(
    [Parameter(Mandatory)]$SubcriptionName, 
    [Parameter(Mandatory)]$ResourceGroupName,
    [Parameter(Mandatory)]$FunctionAppname,
    [Parameter(Mandatory)]$slot 
)


Get-AzSubscription -SubscriptionName $SubcriptionName |Select-AzSubscription

Remove-AzWebAppAccessRestrictionRule -ResourceGroupName $ResourceGroupName -WebAppName $FunctionAppname -Name ADOAgent -SlotName $slot

```


And here's a sample snippet of YAML to use them

``` yaml
# Sample Pipeline for Hosted IP

trigger:
  - none

pool:
  vmImage: 'ubuntu-16.04'

variables: 
  SubscriptionName: 'Your Subname and Service Connection Name'
  ResourceGroup: 'The Resource Group the Function is in'
  TerraformWorkingDirectory: "$(Pipeline.Workspace)/s/$(Build.Repository.Name)"
  functionAppName: 'Name of the function'
  # Agent VM image name for function
  vmImageName: 'windows-2019'

steps:
### Extra steps here to build the function app. Cut for clarity.
## will need some tweaking for spacing etc too and stages added etc. 
    - checkout: self

    - task: AzurePowerShell@5
        displayName: "Adding PipelineAgent IP to Network Access"
        inputs:
        azureSubscription: '$(SubscriptionName)'
        ScriptType: 'FilePath'
        ScriptPath: './AzureFunctionNetworkRule/Add-AzureFunctionAllowHostedAgentIP.ps1'
        azurePowerShellVersion: 'LatestVersion'
        workingDirectory: 'AzureFunctionNetworkRule'


    - task: AzureFunctionApp@1
        displayName: " Deploying the Function App previously build"
        inputs:
        azureSubscription: '$(SubscriptionName)'
        appType: 'functionApp'
        appName: '$(functionAppName)'
        package: '$(Pipeline.Workspace)/drop/$(functionAppName).zip'
        deploymentMethod: 'runFromPackage'


    - task: AzurePowerShell@5
        displayName: "Removing PipelineAgent IP to Network Access"
        inputs:
        azureSubscription: '$(SubscriptionName)'
        ScriptType: 'FilePath'
        ScriptPath: './AzureFunctionNetworkRule/Remove-AzureFunctionAllowHostedAgentIP.ps1'
        azurePowerShellVersion: 'LatestVersion'
        workingDirectory: 'AzureFunctionNetworkRule'


```

That's it. 

Might be useless to many of you, might be a better way to handle it but it works.  

Leave comments below for questions. Yes, Yes you have to have disqus etc. I don't want spammed.
Alternative is I just turn off comments. 