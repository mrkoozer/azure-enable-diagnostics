# Azure Enable Diagnostics

azure-enable-diagnostics automates enabling Monitoring Diagnostics on Azure VMs.

For details on the context for this utility, see [Enabling Azure Diagnostics](https://www.cloudyn.com/blog/)

For each subscription assigned to the entered Azure login credentials:
   * Register the Resource Provider: "Microsoft.Insights"
   * Create or choose a Standard storage account to use for each resource group location
   * For chosen running VMs, enable Monitoring Diagnostics, set the Storage
     * For Linux, enable: Basic metrics
     * For Windows, enable: Basic metrics, Network metrics, Diagnostics infrastructure logs

The user can control:
* which subscriptions
* which storage
* which VMs: Classic/Arm, Windows/Linux
* whether to override existing diagnostics settings

The outcome of the script is logged to file.


#Prerequisites

##Once off
Start "Windows PowerShell" with "Run as Administrator"
```PowerShell
Install-Module Azure
Install-Module AzureRM -Force
```

##Per session
Type the following to enable running an unsigned script in PowerShell session.
```PowerShell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Process
```
Type "Y" to change the execution policy.
When you close session, the policy will return to default.


#Parameters
* **DeploymentModel**
    * "Arm" / "Classic"  (Mandatory)
    * Arm refers to "Azure Resource Manager"
    * Classic refers to "Service Manager"
* **OsType**
    * "Windows" / "Linux"
    * Optional. Default = both
* **ChooseSubscription**
    * Prompt the user to choose which subscriptions assigned to the current user should have their subscriptions enabled.
    * Default = true
* **ChooseStorage**
    * Prompt the user to choose/create which Standard Storage Accounts should be used per ResourceGroup-Location combination.
    * Default = false
* **ChooseVM**
    * Prompt the user to choose which VMs should have Diagnostics enabled.
    * Default = false
* **OverrideDiagnostics**
    * Whether to override diagnostics of VMs that already have diagnostic settings enabled.
    * Default = false


#Examples
Enable Diagnostics on all ARM specific VMs, within specific subscriptions.
Automatically determine storage account to be used
```PowerShell
.\EnableDiag.ps1 –DeploymentModel 'Arm'
```
Enable Diagnostics on specific ARM VMs, within specific subscriptions.
Automatically determine storage account to be used
```PowerShell
.\EnableDiag.ps1 –DeploymentModel 'Arm' -ChooseSubscription -ChooseVM
```
Enable Diagnostics on specific Classic VMs, within specific subscriptions, choosing storage
```PowerShell
.\EnableDiag.ps1 –DeploymentModel 'Classic' -ChooseSubscription -ChooseVM -ChooseStorage
```
Enable Diagnostics on specific Classic Linux VMs, within specific subscriptions, overriding existing diagnostic settings.
Automatically determine storage account to be used
```PowerShell
.\EnableDiag.ps1 –DeploymentModel 'Classic' -OsType 'Linux' -OverrideDiagnostics
```

#Troubleshooting
## Security warning when running the script
You may get the following warning 3 times when running script:
```PowerShell
"Run only scripts that you trust. While scripts from the internet can be useful, this script can potentially harm your
computer. If you trust this script, use the Unblock-File cmdlet to allow the script to run without this warning
message. Do you want to run C:\..\EnableDiag.ps1?"
[D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"):
```
Press "R".
Alternatively
```PowerShell
Unblock-File EnableDiag.ps1
Unblock-File ArmModule.ps1
Unblock-File ClassicModule.ps1
Unblock-File CommonModule.ps1
```

## Azure Portal currently doesn’t support configuring virtual machine diagnostics using JSON
After enabling diagnostics on a Classic VM, you may see the following message in the Portal UI:
"The Azure Portal currently doesn’t support configuring virtual machine diagnostics using JSON. Instead, use PowerShell or CLI to configure diagnostics for this machine".
This message is should automatically disappear when you recheck the VM a bit later.
