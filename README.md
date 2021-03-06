# Overview (Version: 0.2)
This blueprint creates a fully functional spoke landing zone that can be used by business units. The created environment enforces a set of Governance, Compliance and Security measures and establishes connectivity to a Hub VNet.

## Version History
- 0.1 Initital commit
- 0.2 Added ```Azure Bastion``` and ```Cost Management Budget```

## Architecture Diagram

![Overview](media/AzureSpokeBlueprintOverview.png)
 
## Prerequisites 
- An Azure ```Hub-Subscription``` that hosts the a Hub-VNet and a region central Log Analytics workspace
- An Azure ```Spoke-Subscription``` that can be used to bootstrap 
- A ```User assigned - Managed Service Identity``` with the following permissions: 
  -  ```Spoke-Subscription``` : ```Owner``` 
  -  ```Hub-Subscription``` : ```Network Contributor``` and ```Log Analytics Contributor``` 
 
## What is done within this blueprint?
 
This blueprint allows a zero touch deployment of a spoke environment. THe blueprint allows the central IT to bootstrap and provide spoke environments to business units.
 
### Creation of the following ```Core Resources``` within a Spoke-Subscription:
 
- Resource Group for Core Services/Resources
- Log Analytics Workspace
- Key Vault (Premium)
- Virtual Network (incl. Bastion Subnet) 
- Recovery Services Vault for Backup and Restore
- Storage Account for Diagnostics
- Azure Automation Account - Configured with the Azure Resource Optimization (ARO) Toolkit (Developed by Microsoft CSEO)
- Sample Resource Group for "App001"
- Resource Group for NetworkWatcher service
- Azure Bastion Service
- Budget (Azure Cost Management)
 
### Deployment/Configuration of the following ```Core Settings```:
 
- Peering to a Hub VNet 
- Send Subscription Activity Logs to Log Analytics
- Activate Security Center "Standard" with Log Analytics Integration
- Deploy Log Analytics Agents for Windows and Linux VMs
- Deploy Dependency Agents for Windows and Linux VMs
- Deploy prerequisites to enable Guest Configuration Policy on Windows and Linux VMs
- Deploy Network Watcher RG and activate the service for the region
 
### Configuration of the following ```Core Security Settings```:
 
- Advanced Threat Protection for Cosmos DB Accounts
- Enable Auditing on SQL servers
- Advanced Threat Protection on Storage Accounts
- Enable Monitoring in Azure Security Center
- Auditing SQL DB transparent data encryption
- Require encryption on Data Lake Store accounts
- Threat Detection on SQL servers
- Deploy Microsoft IaaSAntimalware extension for Windows Servers
- Deploy Advanced Data Security on SQL servers
 
### Highlights: 
- Cross Subscription aware:
  - Cross Subscription VNet Peering for advanced Hub-Spoke scenarios
  - Cross Subscription connection to a central Log Analytics workspace to store ```Activity Logs``` and ```Security Center Data Collection Logs```
- Roll out of Azure Resource Optimization (ARO) Toolkit in Azure Automation (Developed by Microsoft CSEO) for optimized VM Management. 
- Deployment of several Logging and Monitoring agents
- Assignment of a Azure Budget (configurable)
- Assignment of recommended default ```Tags``` on the Core Services Resource Group:</p> 
 ![Tags Demo](media/Tags.png)
 
## How do I import the blueprint?
 
1. Download the repository as a zip file:</p>
   ![Download Demo](media/download_repo.gif)
2. Upload zip file via the Azure CloudShell:</p>
   ![Upload Demo](media/upload_zipfile.gif)
3. Expand the zip file using Powershell:</p>
   ```Expand-Archive -Path AzureSpokeBlueprint-master.zip -DestinationPath AzureSpokeBlueprint -force```</p>
4. Change directory into the created folder:</p>
   ```cd ./AzureSpokeBlueprint/AzureSpokeBlueprint-master```</p>
5. If it isn't already installed, follow the instructions in [Add the Az.Blueprint module](https://docs.microsoft.com/de-de/azure/governance/blueprints/how-to/manage-assignments-ps#add-the-azblueprint-module) to install and validate the ```Az.Blueprint``` module from the PowerShell Gallery.</p>
6. Import the blueprint into your management group:</p>
   ```Import-AzBlueprintWithArtifact -Name 'Azure_Spoke_Blueprint' -ManagementGroupId 'xxxxxxxxx-xxxx-xxxxx-xxx-xxxxx-xxxxx' -InputPath ./Azure_Spoke_Blueprint```</p>
 
## How do I assign the blueprint? 
1. Right-click the imported blueprint draft and select ```Publish blueprint```</p>
2. Enter a version number (e.g. 0.1) and some comments and click ```Publish```</p>
3. Right-click published  blueprint and select ```Assign blueprint```</p>
4. Change the basic settings according to your ```Region``` of choice and for Managed Service Identity select ```User assigned``` as seen below:</p>
   ![Assign Demo1](media/assign_blueprint1.gif)
   </p>
   Note: Change the first field "Subscription" accordingly (if needed)</p>
5. Fill in the central ```Blueprint Parameter``` as needed. These parameters are used by multiple artifacts to and are therefore "Global Parameters": </p>
   ![Assign Demo2](media/Parameter.JPG)
6. Fill in the ```Artifact Parameter``` as needed. See the naming pattern below:</p>
- Log Analytics Workspace: ```CompanyPrefix```-core-001-log
- Diagnostics Storage Account: ```CompanyPrefix```core001diag
- Azure Key Vault: ```CompanyPrefix```-core-001-kv 
- Azure Recovery Services Vault: ```CompanyPrefix```-core-001-vault
- Azure VNet: ```CompanyPrefix```-core-001-vnet
- Azure Subnet: ```CompanyPrefix```-core-001-subnet
- Azure Bastion: ```CompanyPrefix```-core-001-bastion
- Azure Automation Account: ```CompanyPrefix```-core-aa-```UniqueString```</p>
7.  Click ```Assign``` to assign the blueprint and begin to bootstrap the ```Spoke-Subscription```
 
## What is the result? 
 
There are two major deployments that are being created. The first one being ```resources``` that can be used by the Business Unit. The second one being ```Policies``` that enforces a set of Governance, Compliance and Security measures.    
 
1. Deployment of Azure resources:</p>
![Assign Demo1](media/Result1.png)</p></p>
2. Deployment of Azure policies:</p>
![Assign Demo1](media/Result2.png)</p></p>
 
## FAQ
 
### The rollout of the Log analytics agent does not work
You need to be a bit more patient. Installing and configuring all agents is a time consuming process. This can take up to 1 hour.

### Know issues when it comes to Budget assignments
The Start date needs to be the first day of the month.

### Can/should I enter two diffrent Log Analytics workspaces?
Avoid multi-homing, it can have undesired outcomes. For more information check out our recommendations
[here](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/design-logs-deployment#recommendations) and 
[here](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-designing-an-azure-sentinel-or-azure-security/ba-p/832574#)
