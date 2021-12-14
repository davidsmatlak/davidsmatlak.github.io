---
layout: post
title:  Update Az PowerShell module
author: David Smatlak
permalink: update-az-powershell
comments: false
description: This article describes how to update the Az PowerShell modules with a PowerShell script.
---

This article describes how to update to the latest version of the Az PowerShell module with a
PowerShell script. The Az module contains a collection of modules that provide PowerShell cmdlets to
manage Azure resources. PowerShell and the Az modules run on Windows, macOS, and Linux.

## Prerequisites

- The latest version of [PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-powershell).
- [Visual Studio Code](https://code.visualstudio.com).
- **Optional**: [PowerShell extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell).

## Review the script

The script installs the modules for the current user. On a Windows system, the default location is
_$HOME\Documents\PowerShell\Modules_.

The script does the following operations:

- Displays prompts to confirm that you want to continue and status messages about the progress.
- Downloads the latest Az module version from [PowerShell Gallery](https://www.powershellgallery.com/packages/Az).
- Checks whether the Az module is already installed and reports the version.
- Compares the installed version to the updated version and prompts whether to continue.
- If there isn't a new version or if the user chooses not to upgrade, the script exits.
- Uninstalls all existing Az modules and installs the latest version.
- Displays the updated Az module version.

```powershell
Write-Host -Object "`nSearching PowerShell Gallery for a new Az version.`n"
$newmod = Find-Module -Name Az -Repository PSGallery

$installedmod = Get-InstalledModule -Name Az -ErrorAction SilentlyContinue

$update = ($newmod.Version -gt $installedmod.Version)

if ($update -eq "True") {
  Write-Host -Object "`n$($newmod.Name, $newmod.Version) is available.`n"
  $prompt = Read-Host "Do you want to uninstall the current version and upgrade? (y/n)"
  if ($prompt -eq "y") {
    Write-Host -Object "`nThe upgrade is in progress.`n"
    $AzModule = Get-InstalledModule -Name Az* -ErrorAction SilentlyContinue
    Write-Host -Object "`nPlease wait.`nAz modules are being removed from the current PowerShell session.`n"
    $AzModule | ForEach-Object { Remove-Module -Name  $_.Name -Force -ErrorAction SilentlyContinue }
    Write-Host -Object "`nPlease wait.`nAz modules are being uninstalled.`n"
    $AzModule | ForEach-Object { Uninstall-Module -Name  $_.Name -AllVersions }
    Write-Host -Object "`nPlease wait.`nThe Az modules are being downloaded and installed.`n"
    Install-Module -Name Az -Repository PSGallery -AllowClobber -Force -Scope CurrentUser
    $NewAzMod = (Get-InstalledModule -Name Az)
    Write-Host -Object "`nCompleted!`n$($NewAzMod.Name, $NewAzMod.Version) was installed.`n"
  }
  else {
    Write-Host -Object "`nYou chose not to upgrade. Try again later.`n"
  }
}
else {
  Write-Host -Object "`nNo update available.`n$($installedmod.Name, $installedmod.Version) is the current version.`n"
}
```

## Run the script

1. Open Visual Studio Code and create a new file.
1. Copy and paste the script into the new file. Save it as _upgrade-az-module.ps1_.
1. Start a new terminal from **Terminal** > **New terminal**.
1. Switch to the directory where you saved the file.

   ```powershell
   Set-Location -Path "location of your script file"
   ```

1. Run the script.

   ```plaintext
   .\upgrade-az-module.ps1
   ```

## Verify the version

Although the script displays the updated version, you can manually verify the version of the Az
module or any of the modules. To list modules that were installed by PowerShellGet, use [Get-InstalledModule](https://docs.microsoft.com/powershell/module/powershellget/get-installedmodule).

To check the Az module version:

```powershell
Get-InstalledModule -Name Az
```

```plaintext
Version  Name  Repository  Description
-------  ----  ----------  -----------
7.0.0    Az    PSGallery   Microsoft Azure PowerShell - Cmdlets to manage resources in Azure.
```

To view the versions of all the installed Az modules:

```powershell
Get-InstalledModule -Name Az*
```

```plaintext
Version   Name                   Repository    Description
-------   ----                   ----------    -----------
7.0.0     Az                     PSGallery     Microsoft Azure PowerShell - Cmdlets to manage resources in Azure.
2.7.0     Az.Accounts            PSGallery     Microsoft Azure PowerShell - Accounts credential management cmdlets for Azure Resource Manager
1.1.2     Az.Advisor             PSGallery     Microsoft Azure PowerShell - Azure Advisor Cmdlets for Advisor in Windows PowerShell
3.0.0     Az.Aks                 PSGallery     Microsoft Azure PowerShell - Azure managed Kubernetes cmdlets for Windows PowerShell
1.1.4     Az.AnalysisServices    PSGallery     Microsoft Azure PowerShell - Analysis Services cmdlets for Windows PowerShell
2.3.1     Az.ApiManagement       PSGallery     Microsoft Azure PowerShell - Api Management service cmdlets for Azure Resource
```

You can also view detailed information about a specific Az module version. For example, the
`Az.Storage` module.

```powershell
Get-InstalledModule -Name Az.Storage | Select-Object -Property *
```

The output is verbose and shows all the module's properties, like **Description**, **Author**, and **PublishedDate**.

```plaintext
Name          : Az.Storage
Version       : 4.1.0
Type          : Module
Description   : Microsoft Azure PowerShell - Storage service data plane and management
                cmdlets for Azure Resource Manager in Windows PowerShell and
                PowerShell Core.
                Creates and manages storage accounts in Azure Resource Manager.

                For more information on Storage,
                please visit the following: https://docs.microsoft.com/azure/storage/
Author        : Microsoft Corporation
CompanyName   : azure-sdk
Copyright     : Microsoft Corporation. All rights reserved.
PublishedDate : 12/7/2021 05:29:42
```

## More information

- [What is Azure PowerShell?](https://docs.microsoft.com/powershell/azure/what-is-azure-powershell)
- [The PowerShell Gallery](https://docs.microsoft.com/powershell/scripting/gallery/overview)
- [PowerShellGet module](https://docs.microsoft.com/powershell/module/powershellget)
