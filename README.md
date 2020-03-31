# Azure Spoke Blueprint 
This blueprint creates a fully functional spoke landing zone that can be used by business units.

## What is done within this blueprint?

This blueprint allows a zero manual touch deployemnt of a spoke environment. THe blueprint allows the central IT to bootstrap and provide spoke environments to business units.   

### Creation of the following core ```resources``` within a Spoke-Subscription:

- Log Analytics Workspace
- Key Vault (Premium)
- Security Center (Standard) with Log Analytics Integration
- Virtual Network (incl. Core Subnet) 
- Recovery Services Vault for Backup/Restore
- Storage Account for Diagnostics
- Azure Automation Account - Configured with the Azure Resource Optimization (ARO) Toolkit (Developed by CSEO)
- Resource Group for "App001"

### Deployment/Configuration of the following core settings:
- Peering to a Hub VNet within another subscription 
- Send Subscription Activity Logs to Log Analytics
- Deploy Log Analytics Agents for Windows and Linux
- Deploy Dependency Agents for Windows and Linux
- Deploy Network Watcher RG and activate the service for the region
- Deploy Microsoft IaaSAntimalware extension for Windows Servers

### Configuration of the following Security Settings:
 
### Highlights: 
- Cross Subscription aware:
  - Cross Subscription VNet Peering for advanced Hub-Spoke scenarios
  - Cross Subscription connection to a central Log Analytics workspace to store ```Activity Logs``` and ```Security Center Data Collection Logs```
- Roll out of Azure Resource Optimization (ARO) Toolkit in Azure Automation (Developed by CSEO)
- Deploy all available agents for Logging and Monitoring 

## Prerequisites 
- An Azure ```Hub-Subscription``` that hosts the a Hub-VNet and a region central Log Analytics workspace
- An Azure ```Spoke-Subscription``` that can be used to assigned the blueprint to
- A ```User assigned - Managed Service Identity``` with ```Owner``` permissions on the Spoke-Subscriptions and ```Network Contributor``` as well as ```Log Analytics Contributor``` permissions on the Hub-Subscription 

## How do I import the blueprint?

1. Download the repository as a zip file:</p>
   ![Download Demo](media/download_repo.gif)
2. Upload zip file via the Azure CloudShell:</p>
   ![Upload Demo](media/upload_zipfile.gif)
3. Expand the zip file using Powershell:</p>
   ```Expand-Archive -Path AzureSpokeBlueprint-master.zip -DestinationPath AzureSpokeBlueprint -force```</p>
4. Change directory into the created folder:</p>
   ```cd ./AzureSpokeBlueprint/AzureSpokeBlueprint-master```</p>
5. Import the blueprint into your management group:</p>
   ```Import-AzBlueprintWithArtifact -Name 'Azure_Spoke_Blueprint' -ManagementGroupId 'xxxxxxxxx-xxxx-xxxxx-xxx-xxxxx-xxxxx' -InputPath ./Azure_Spoke_Blueprint```</p>

## How do I assign the blueprint? 
1. Right-click the imported blueprint draft and select ```Publish blueprint```</p>
2. Enter a version number (e.g. 0.1) and some comments and click ```Publish```</p>
3. Right-click published  blueprint and select ```Assign blueprint```</p>
4. Change the basic settings according to your ```Region``` of choice and for Managed Service Identity select ```User assigned``` as seen below:</p>
   ![Assign Demo1](media/assign_blueprint1.gif)
   </p>
   Note: Change the first field "Subscription" accordingly (if needed)</p>
5. Fill in the central ```Blueprint Parameter``` as needed. These parameters are used by multiple artifacts to and are therefore "Global": </p>
   ![Assign Demo2](media/Assignment2.JPG)
6. Fill in the ```Artifact Parameter``` as needed. See the naming pattern below:</p>
- Log Analytics Workspace: ```CompanyPrefix```-core-001-log
- Diagnostics Storage Account: ```CompanyPrefix```core001diag
- Azure Key Vault: ```CompanyPrefix```-core-001-kv 
- Azure Recovery Services Vault: ```CompanyPrefix```-core-001-vault
- Azure VNet: ```CompanyPrefix```-core-001-vnet
- Azure Subnet: ```CompanyPrefix```-core-001-subnet
- Azure Automation Account: ```UniqueString```-aa-automation</p>
7.  Click ```Assign``` to assign the blueprint and begin to bootstrap the ```Spoke-Subscription```
