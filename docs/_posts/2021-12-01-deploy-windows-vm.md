---
layout: post
title:  Use Bicep to deploy a virtual machine
author: David Smatlak
permalink: deploy-windows-vm
comments: false
description: This article describes how to use a Bicep file to deploy a Windows virtual machine on Azure.
---

This article describes how to use a Bicep file to deploy the infrastructure for a Windows virtual
machine (VM) on Microsoft Azure. Bicep files use a simpler syntax to create templates that deploy
cloud resources. When a Bicep file is deployed, it's converted to JSON.

## Prerequisites

- Azure subscription. If you don't have an Azure subscription you can create a [free account](https://azure.microsoft.com/free/).
- [Visual Studio Code](https://code.visualstudio.com) and the [Bicep extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).
- [PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) and the [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) module.
- [Bicep CLI](https://docs.microsoft.com/azure/azure-resource-manager/bicep/install#azure-powershell).

## Review the Bicep file

The Bicep file deploys all the resources needed to run a VM. The file uses defaults for the VM size
and OS version, but there are other allowed values.

The following resources are deployed:

- Network security group that only allows VNet source and destination addresses and allows RDP
  traffic.
- Public IP address that's used by the bastion.
- Virtual network (VNet) that isn't exposed the public internet and includes a subnet for the VM and
  the basion subnet that must be named _AzureBastionSubnet_.
- Network interface card that associates a VM to a subnet.
- Bastion host that creates a secure connection to the virtual machine. With a bastion, the VMs
  don't need public IP addresses.
- A Windows VM that only connects to the VNet and is isolated from the public internet. The VM has a network interface card and an OS disk.

Open a new file in Visual Studio Code. Then, copy and paste the following Bicep file and save it as
_main.bicep_ on your computer.

```plaintext
@description('Enter a location. Defaults to the resource group location.')
param location string = resourceGroup().location

@description('Enter a Windows virtual machine name with a maximum of 12 characters.')
@maxLength(12)
param vmName string

@description('Enter the admin user account name with a maximum of 20 characters.')
@maxLength(20)
param adminUsername string

@description('Enter the admin account password with a minimum of 12 characters.')
@minLength(12)
@secure()
param adminPassword string

@description('Enter the virtual machine size.')
@allowed([
  'Standard_A1_v2'
  'Standard_A2_v2'
  'Standard_B1ms'
  'Standard_B1s'
])
param vmSize string = 'Standard_B1s'

@description('Select a Windows version.')
@allowed([
  '2019-Datacenter-smalldisk'
  '2019-Datacenter'
  '2016-Datacenter'
  '2022-datacenter'
])
param osVersion string = '2019-Datacenter-smalldisk'

var bastionHostName = 'bastion'
var bastionSubnet = '10.0.1.0/26'
var bastionSubnetName = 'AzureBastionSubnet'
var bastionPublicIpName = 'bastionPublicIP'
var nsgName = 'NSG'
var computerName = '${vmName}-vm'
var nicName = '${vmName}-nic'
var osDiskName = '${vmName}-osdisk'
var vnetName = 'VNet1'
var vnetPrefix = '10.0.0.0/16'
var subnetName = 'vmSubnet1'
var subnetPrefix = '10.0.0.0/24'

resource nsg 'Microsoft.Network/networkSecurityGroups@2022-01-01' = {
  name: nsgName
  location: location
  properties: {
    securityRules: [
      {
        name: 'RDP-allow'
        properties: {
          description: 'Allow inbound RDP connections'
          protocol: 'Tcp'
          sourcePortRange: '*'
          destinationPortRange: '3389'
          sourceAddressPrefix: 'VirtualNetwork'
          destinationAddressPrefix: 'VirtualNetwork'
          access: 'Allow'
          priority: 200
          direction: 'Inbound'
        }
      }
    ]
  }
}

resource bastionPublicIp 'Microsoft.Network/publicIPAddresses@2022-01-01' = {
  name: bastionPublicIpName
  location: location
  sku: {
    name: 'Standard'
    tier: 'Regional'
  }
  properties: {
    publicIPAllocationMethod: 'Static'
    publicIPAddressVersion: 'IPv4'
    idleTimeoutInMinutes: 4
  }
}

resource vnet 'Microsoft.Network/virtualNetworks@2022-01-01' = {
  name: vnetName
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vnetPrefix
      ]
    }
    subnets: [
      {
        name: subnetName
        properties: {
          addressPrefix: subnetPrefix
          networkSecurityGroup: {
            id: nsg.id
          }
        }
      }
      {
        name: bastionSubnetName
        properties: {
          addressPrefix: bastionSubnet
        }
      }
    ]
  }
}

resource nic 'Microsoft.Network/networkInterfaces@2022-01-01' = {
  name: nicName
  location: location
  properties: {
    networkSecurityGroup: {
      id: nsg.id
    }
    ipConfigurations: [
      {
        name: 'ipConfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          subnet: {
            id: '${vnet.id}/subnets/${subnetName}'
          }
        }
      }
    ]
  }
}

resource bastionHost 'Microsoft.Network/bastionHosts@2022-01-01' = {
  name: bastionHostName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipConfig'
        properties: {
          publicIPAddress: {
            id: bastionPublicIp.id
          }
          subnet: {
            id: '${vnet.id}/subnets/${bastionSubnetName}'
          }
        }
      }
    ]
  }
}

resource windowsVM 'Microsoft.Compute/virtualMachines@2022-03-01' = {
  name: computerName
  location: location
  properties: {
    hardwareProfile: {
      vmSize: vmSize
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: nic.id
          properties: {
            deleteOption: 'Detach'
            primary: true
          }
        }
      ]
    }
    osProfile: {
      adminUsername: adminUsername
      adminPassword: adminPassword
      computerName: computerName
      windowsConfiguration: {
        enableAutomaticUpdates: true
        provisionVMAgent: true
      }
    }
    storageProfile: {
      imageReference: {
        publisher: 'MicrosoftWindowsServer'
        offer: 'WindowsServer'
        sku: osVersion
        version: 'latest'
      }
      osDisk: {
        name: osDiskName
        createOption: 'FromImage'
        osType: 'Windows'
        managedDisk: {
          storageAccountType: 'Standard_LRS'
        }
      }
    }
  }
}

output computerName string = windowsVM.properties.osProfile.computerName
```

For more information about each resource type's API versions:

- [Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Network/bastionHosts](https://docs.microsoft.com/azure/templates/microsoft.network/bastionhosts)
- [Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

## Deploy the resources

To deploy the infrastructure and virtual machine use Azure PowerShell. For this example, you'll
create a new resource group named _vmdeployRG_.

### Connect to Azure

To run Azure PowerShell commands, connect to your Azure subscription.

1. Sign in to the [Azure portal](https://portal.azure.com).
1. From Visual Studio Code, open a terminal: **Terminal** > **New Terminal**.
1. Run `Connect-AzAccount`.
1. A browser window opens and prompts you to choose an account for Azure authentication.

After you connect, your subscription information is displayed in the terminal.

If you have multiple Azure subscriptions, use [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext)
to change the subscription.

### Deployment commands

To deploy the resources, run the following commands:

```powershell
New-AzResourceGroup -Name vmdeployRG -Location westus
New-AzResourceGroupDeployment -ResourceGroupName vmdeployRG -TemplateFile main.bicep -Name demoDeploy
```

The deployment command will prompt you for three values. Make note of the username and password because you'll need it later to access the VM.

- vmName
- adminUsername
- adminPassword

[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)
creates the resource group. [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment)
deploys the resources. The `ResourceGroupName` parameter specifies where to deploy the resources and
`TemplateFile` is the file name in the current directory. The `Name` parameter is the deployment
name.

When the deployment is complete, the results are shown in the terminal.

### Deployment results

You can use the deployment name to view details about a deployment:

- From the portal, go to the resource group's **Settings** > **Deployments** and select the
  deployment name. In this example, _demoDeploy_. Expand **Deployment details** to see the list of resources that were deployed.
- Use Azure PowerShell commands like [Get-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroupdeployment)
  or for details about each operation, [Get-AzResourceGroupDeploymentOperation](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroupdeploymentoperation).

   ```powershell
   Get-AzResourceGroupDeployment -ResourceGroupName vmdeployRG
   Get-AzResourceGroupDeploymentOperation -DeploymentName demoDeploy -ResourceGroupName vmdeployRG
   ```

## Check the resources

After a successful deployment, the resources are visible in the resource group's **Overview**.
Select a resource to view more details about its configuration.

You can test the connection to the VM with the bastion host.

1. In the resource group, select the VM.
1. From **Overview**, select **Connect**.
1. Select **Bastion**.
1. Enter the VM credentials.

The Windows desktop for the VM is displayed. When you're finished, select **Start** > **Power** >
**Disconnect**.

## Clean up the resources

When you're finished with the VM and want to remove all the resources, delete the resource group.

```powershell
Remove-AzResourceGroup -Name vmdeployRG
```

To sign out of Azure from your terminal session use the following command:

```powershell
Disconnect-AzAccount
```

## More information

For learn more about resources in this article, see the following articles:

- [Windows virtual machines in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview)
- [Network security groups](https://docs.microsoft.com/azure/virtual-network/network-security-groups-overview)
- [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)
