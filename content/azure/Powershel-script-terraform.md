---
title: "Run a Powershell Script from Github on Azure VM in a Terraform file"
date: 2021-03-04T10:25:42Z
tags: 
- powershell
- terraform
- azure
draft: false
image: "/image/AzTerrCustScript/azterrcustscript.png"
---
This will be short one and mostly will just be an example of how to do it without much pizazz.

Though not completely bereft of it, since the people want a show!!

This didn't seem to be coveed in Terraform AzureRM docs or any another blog I could find, at least not clearly. 

As part of throwing up a sandbox ansible environment I was trying to figure out a way to run the ansible script in github that opens up comms on the machine being spun up so I could connect. 

Your use case may vary but basically the premise is that the file is kept somewhere in github and you're wanting to run it on your VM as it spins up. 

Yes there are other ways to do similar kind of thing. Those have posts already. I needed this. I did this. I share this. 

There's a snippet below if that's all you need but here's a [link](https://github.com/gabrielmccoll/azurevmansible/blob/main/terraform/main.tf) to the github repo where the full shebang is. 


``` powershell
resource "azurerm_windows_virtual_machine" "vm" {
  resource_group_name = azurerm_resource_group.rg.name
  name = var.nameofall
  location = var.location
  network_interface_ids = [azurerm_network_interface.nic.id]
  size = "Standard_D2_v3"
  os_disk {
    name              = "osdisk"
    caching           = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  source_image_reference {
    publisher = "MicrosoftWindowsServer"
    offer     = "WindowsServer"
    sku       = "2016-Datacenter"
    version   = "latest"
  }
  ### This is terrible security
  admin_username = "adminuser12" 
  admin_password = "P@$$word1234!"
  computer_name = "ansiblevm"

  ### YOU NEED THIS BIT TO RUN THE BELOW 
  provision_vm_agent = true
  allow_extension_operations = true
}


resource "azurerm_virtual_machine_extension" "vmext" {
  name                 = azurerm_windows_virtual_machine.vm.name
  virtual_machine_id   = azurerm_windows_virtual_machine.vm.id
  publisher            = "Microsoft.Compute"
  type                 = "CustomScriptExtension"
  type_handler_version = "1.10"
  
  ### THIS PART IS ALL NEEDED, INCLUDING THE WEIRD SETTINGS BIT.  
  settings = <<SETTINGS
    {
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File ConfigureRemotingForAnsible.ps1",
        "fileUris": ["https://raw.githubusercontent.com/ansible/ansible/devel/examples/scripts/ConfigureRemotingForAnsible.ps1"]
    }
SETTINGS

}
```

To get the full criteria of stuff you can put into the settings above, you can consult the ARM template page. 
Pretty sure you can just adapt what you see and pop them in, using what I'm showing above as a guide. 

https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-windows#extension-schema


Hope that's useful to someone else. 