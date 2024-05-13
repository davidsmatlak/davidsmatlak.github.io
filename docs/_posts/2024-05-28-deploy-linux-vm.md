---
layout: post
title:  Use Bicep to deploy an Ubuntu Linux virtual machine in Azure
author: David Smatlak
permalink: deploy-linux-vm
comments: false
description: This article describes how to use a Bicep file to deploy an Azure virtual network, network security group, and an Ubuntu Linux virtual machine.
---

This article describes how to use a Bicep file to deploy an Azure virtual network, network security group, and an Ubuntu Linux virtual machine. Bicep syntax improves the authoring experience for Azure resource deployments compared to traditional Azure Resource Manager templates written in JSON. When a Bicep file is deployed, the code is converted to JSON to create Azure resources.

## Prerequisites

- Azure subscription. If you don't have an Azure subscription you can create a [free account](https://azure.microsoft.com/free/).
- [Visual Studio Code](https://code.visualstudio.com) and the [Bicep extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep).
- [PowerShell](https://learn.microsoft.com/powershell/scripting/install/installing-powershell) and the [Azure PowerShell](https://learn.microsoft.com/powershell/azure/install-az-ps) module.
- [Bicep CLI](https://learn.microsoft.com/azure/azure-resource-manager/bicep/install).

## Review the Bicep file

The Bicep file deploys all the resources needed to create a virtual machine (VM). The file uses defaults for the VM size and operating system version, but there are other allowed values. The Bicep file includes a `newOrExisting` parameter so that it's reusable to create more VMs in the same virtual network (VNet) and subnet.

The following resources are deployed:

- Network security group that allows internal VNet connections and blocks external connections.
- Virtual network that includes a subnet for the virtual machine.
- Public IP address that's used for SSH connections and uses just-in-time (JIT) connections.
- Network interface that's associated with the VM's IP addresses.
- An Ubuntu Linux virtual machine.

Open a new file in Visual Studio Code. Then, copy and paste the following Bicep file and save it as
_linux-vm.bicep_ on your computer.

```bicep
@description('Enter a location. Defaults to the resource group location.')
param location string = resourceGroup().location

@description('Enter a Linux virtual machine name with a maximum of 61 characters. During deployment the suffix -vm is added to the name (64 characters total).')
@maxLength(61)
param vmName string

@description('Enter the admin user account name with a maximum of 20 characters.')
@maxLength(20)
param adminUsername string

@description('Enter the virtual machine size. Default is Standard_B1s. Other allowed values are Standard_A1_v2, Standard_A2_v2, and Standard_B1ms.')
@allowed([
  'Standard_A1_v2'
  'Standard_A2_v2'
  'Standard_B1ms'
  'Standard_B1s'
])
param vmSize string = 'Standard_B1s'


@description('Select a Linux version. Default is Ubuntu Focal 20-04 LTS Gen2.')
@allowed([
  'Ubuntu-Focal-20-04'
  'Ubuntu-Jammy-22-04'
  'Ubuntu-Lunar-23-04'
])
param osVersion string = 'Ubuntu-Focal-20-04'


@description('Enter the name of an exising SSH public key in Azure.')
param publicSSHKeyName string

@description('Name of resource group where public SSH keys are stored.')
param sskKeyRG string

@description('New or existing VNet, Subnet, and NSG')
@allowed([
  'new'
  'existing'
])
param newOrExisting string

var vnetNsgName = 'netsecgroup'
var computerName = '${vmName}-vm'
var nicName = '${vmName}-nic'
var osDiskName = '${vmName}-osdisk'
var publicIpName = '${vmName}-publicip'
var vnetName = 'vnet01'
var vnetPrefix = '10.0.0.0/16'
var subnetName = 'vmSubnet01'
var subnetPrefix = '10.0.0.0/24'

// Linux versions
var linuxVersion = {
  'Ubuntu-Focal-20-04': {
    publisher: 'canonical'
    offer: '0001-com-ubuntu-server-focal'
    sku: '20_04-lts-gen2'
    version: 'latest'
    }
  'Ubuntu-Jammy-22-04': {
    publisher: 'canonical'
    offer: '0001-com-ubuntu-server-jammy'
    sku: '22_04-lts-gen2'
    version: 'latest'
  }
  'Ubuntu-Lunar-23-04': {
    publisher: 'canonical'
    offer: '0001-com-ubuntu-server-lunar'
    sku: '23_04-gen2'
    version: 'latest'
  }
}

// Create new network security group
// VNet network security group with no port rules
// Use Defender JIT to protect port 22
resource vnetNsgNew 'Microsoft.Network/networkSecurityGroups@2023-09-01' =
  if (newOrExisting == 'new') {
    name: vnetNsgName
    location: location
  }

// Use existing network security group
resource vnetNsgExisting 'Microsoft.Network/networkSecurityGroups@2023-09-01' existing =
  if (newOrExisting == 'existing') {
    name: vnetNsgName
  }

// Create new virtual network
resource vnetNew 'Microsoft.Network/virtualNetworks@2023-09-01' =
  if (newOrExisting == 'new') {
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
              id: vnetNsgNew.id
            }
          }
        }
      ]
    }
  }

// Use existing virtual network
resource vnetExisting 'Microsoft.Network/virtualNetworks@2023-09-01' existing =
  if (newOrExisting == 'existing') {
    name: vnetName
  }

resource vmPublicIp 'Microsoft.Network/publicIPAddresses@2023-09-01' = {
  name: publicIpName
  location: location
  sku: {
    name: 'Basic'
  }
  properties: {
    publicIPAllocationMethod: 'Dynamic'
  }
}

resource nic 'Microsoft.Network/networkInterfaces@2023-09-01' = {
  name: nicName
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipConfig1'
        properties: {
          privateIPAllocationMethod: 'Dynamic'
          publicIPAddress: {
            id: vmPublicIp.id
          }
          subnet: {
            id: ((newOrExisting == 'new')
              ? '${vnetNew.id}/subnets/${subnetName}'
              : '${vnetExisting.id}/subnets/${subnetName}')
          }
        }
      }
    ]
    networkSecurityGroup: {
      id: ((newOrExisting == 'new') ? '${vnetNsgNew.id}' : '${vnetNsgExisting.id}')
    }
  }
}

// Use existing SSH key
resource sshkey 'Microsoft.Compute/sshPublicKeys@2023-09-01' existing = {
  name: publicSSHKeyName
  scope: resourceGroup(sskKeyRG)
}

resource linuxVM 'Microsoft.Compute/virtualMachines@2023-09-01' = {
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
      computerName: computerName
      linuxConfiguration: {
        disablePasswordAuthentication: true
        ssh: {
          publicKeys: [
            {
              keyData: sshkey.properties.publicKey
              path: '/home/${adminUsername}/.ssh/authorized_keys'
            }
          ]
        }
        provisionVMAgent: true
      }
    }
    storageProfile: {
      imageReference: linuxVersion[osVersion]
      osDisk: {
        name: osDiskName
        createOption: 'FromImage'
        osType: 'Linux'
        managedDisk: {
          storageAccountType: 'Standard_LRS'
        }
        deleteOption: 'Delete'
      }
    }
  }
}

output computerName string = linuxVM.properties.osProfile.computerName
output vmPrivateIpAddress string = nic.properties.ipConfigurations[0].properties.privateIPAddress
output vmResourceId string = linuxVM.id
```

For more information about each resource type's API versions:

- [Microsoft.Network/networkSecurityGroups](https://learn.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/virtualNetworks](https://learn.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/publicIPAddresses](https://learn.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/networkInterfaces](https://learn.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/sshPublicKeys](https://learn.microsoft.com/azure/templates/microsoft.compute/sshpublickeys)
- [Microsoft.Compute/virtualMachines](https://learn.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

## Review the parameters file

Use a parmeters file to provide parameter values. With a parameter file you don't need to respond to prompts for parameter values during the deployment.

The following values are examples and you can choose to use different values.

- The `using` value specifies the path and name of the Bicep file to deploy.
- The `newOrExisting` parameter is important.
  - The first time deployment uses `new` so that a new VNet, subnet, and network security group are created.
  - For subsequent deployments, use `existing` so that you don't redeploy the resources.
- Replace all the placeholder values with values for your your deployment.

Copy and paste the following code into a file named _linux-vm.bicepparam_ on your computer.

```bicep
using './linux-vm.bicep'

param vmName = 'vmName placeholder'
param adminUsername = 'vmAdministratorName placeholder'
param vmSize = 'Standard_B1s'
param osVersion = 'Ubuntu-Lunar-23-04'
param sskKeyRG = 'SSH key resource group name placeholder'
param publicSSHKeyName = 'public SSH key name placeholder'
param newOrExisting = 'new'
```

For more information, go to the [parameters file](https://learn.microsoft.com/azure/azure-resource-manager/bicep/parameter-files) documentation.

## Create a public key

As shown in the Bicep file and parameters file you need a public/private key pair to login to the Linux VM. For this example the SSH key is in a resource group that's separate from the VM and it's resources. In the Bicep file, `scope` is used to reference the SSH key's resource group.

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Enter _SSH keys_ into the search and select it from the list.
1. Select **Create**.
1. Create a new resource group named _demoSshKeyGroup_.
1. Enter _demo-public-ssh-key_ as the **Key pair name**.
1. Select _Generate new key pair_ in **SSH public key source**.
1. Select **Review + create**, then **Create**.
1. Select **Download the private key and create resource**.

You can go to **SSH keys** in the portal to view the public key. The Bicep file uses the name and resource group to get the value and add the public key to the VM. The private key's location is used in a later step when you use SSH and connect to the VM.

## Deploy the resources

To deploy the infrastructure and virtual machine use Azure PowerShell. For this example, create a new resource group named _demoVmDeployGroup_.

### Connect to Azure

To run Azure PowerShell commands to deploy resources with your Bicep file, connect to your Azure subscription. Azure PowerShell version 12 updated the sign in process. For more information, go to [interactive authentication](https://learn.microsoft.com/powershell/azure/authenticate-interactive).

Replace `11111111-1111-1111-1111-111111111111` with your Azure tenant ID. You can get your tenant ID from the portal by doing a search for _Microsoft Entra ID_ and going to the **Overview** page.

1. From Visual Studio Code, open a PowerShell terminal: **Terminal** > **New Terminal**.
1. Run `Connect-AzAccount -Tenant 11111111-1111-1111-1111-111111111111`.
1. A browser window opens and prompts you to choose an account for Azure authentication.

After you connect, your subscription information is displayed in the terminal.

If you have multiple Azure subscriptions, use [Set-AzContext](https://learn.microsoft.com/powershell/module/az.accounts/set-azcontext) to change the subscription.

### Deployment commands

To deploy the resources, run the following commands:

```powershell
New-AzResourceGroup -Name demoVmDeployGroup -Location westus

$deployparms = @{
  ResourceGroupName = "demoVmDeployGroup"
  TemplateFile = "linux-vm.bicep"
  TemplateParameterFile = "linux-vm.bicepparam"
  Name = "deployLinuxVM01"
}

New-AzResourceGroupDeployment @deployparms
```

- [New-AzResourceGroup](https://learn.microsoft.com/powershell/module/az.resources/new-azresourcegroup) creates the resource group.
- `$deployparms` uses [splatting](https://learn.microsoft.com/powershell/module/microsoft.powershell.core/about/about_splatting) for readaility and to create parameter values for the deployment command.
  - `ResourceGroupName` parameter specifies where to deploy the resources.
  - `TemplateFile` is the Bicep file name in the current directory.
  - `TemplateParameterFile` is the Bicep parameters file name in the current directory.
  - `Name` is the deployment name. If you run subsequent deployments and want to preserve each deployments history, increment the number in the deployment name, like _deployLinuxVM02_.
- [New-AzResourceGroupDeployment](https://learn.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) deploys the resources.

When the deployment is complete, the results are shown in the terminal.

### Deployment results

You can use the deployment name to view details about a deployment:

1. From the portal, go to the resource group _demoVmDeployGroup_.
1. From the **Overview** page you can view the deployed resources.
1. Select **Settings** > **Deployments**.
1. Select the deployment name, in this example, _deployLinuxVM01_.
1. Expand **Deployment details** to see the list of resources that were deployed. You can also view the deployments inputs, outputs, template, and operation details.

## Configure remote access

Configure the virtual machine's just-in-time policy to allow connections on SSH port 22. The public IP address is shown on this page.

1. In the portal, go to the _demoVmDeployGroup_ resource group and select the VM.
1. Select **Connect** > **Connect**
1. Verify **Connecting using** shows the public IP address or select it from the drop down.
1. Select **Configure for this port** which should specify Port 22.
1. Select **Configure** and for **Source IP** select your **Local machine IP**.
1. Select **Request access**.

Port 22 is now configured for your computer's IP address to SSH into the virtual machine. The configuration creates two rules in your Network Security Group. The first is a `deny` on Port 22 for any protocol from any source to your VM subnet address. The `deny` rule is persistent. The second rule is an `allow` for your local computer's IP address to access Port 22. By default, the `allow` rule is active for three hours. When the rule expires, it's removed from the network security group and SSH access from your computer's IP address is denied.

From **Just-in-time policy** you can select **Conigure** to edit settings or delete the request. You can also view the Microsoft Defender just-in-time policy from the Network Security Group. For more information, go to [Enable just-in-time access on VMs](https://learn.microsoft.com/azure/defender-for-cloud/just-in-time-access-usage).

## Connect to VM

1. Open a PowerShell session on your computer.
1. Enter the following command and replace `<vmAdministratorName>`,  `<vmIpAddress>`, and `<path to private key>` with your values. The path to your private key is the location where you downloaded the file.

   ```
   ssh <vmAdministratorName>@<vmIpAddress> -i <path to private key>
   ```

1. On the first login with a new SSH key, you're prompted about the fingerprint, type `yes` to continue.
1. SSH connects to your Ubuntu Linux VM and opens a command prompt. The placeholder values for `<vmAdministratorName>@<VmName>` are replaced by your VM administrator name and VM name.

   ```
   <vmAdministratorName>@<VmName>:~$
   ```

1. Type `exit` to disconnect from the VM.


## Clean up the resources

When you're finished with the VM and want to remove all the resources, delete the resource groups.

```powershell
Remove-AzResourceGroup -Name demoVmDeployGroup
Remove-AzResourceGroup -Name demoSshKeyGroup
```

Delete the the private key that was downloaded when you created your public/priavte key pair.

Sign out of Azure from your terminal session.

```powershell
Disconnect-AzAccount
```

## More information

For learn more about resources in this article, see the following articles:

- [Virtual machines in Azure](https://learn.microsoft.com/azure/virtual-machines/overview)
- [Network security groups](https://learn.microsoft.com/azure/virtual-network/network-security-groups-overview)
- [Understanding just-in-time (JIT) VM access](https://learn.microsoft.com/azure/defender-for-cloud/just-in-time-access-overview)
