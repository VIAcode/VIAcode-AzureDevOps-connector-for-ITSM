# Azure DevOps connector for ServiceDesk Plus MSP user guide
This guide is based on version **MVP2** of Azure DevOps Connector for **ServiceDesk Plus MSP**.
- [Azure DevOps connector for ServiceDesk Plus MSP deployment and configuration guide](#azure-devops-connector-for-servicedesk-plus-msp-deployment-and-configuration-guide)
  - [Before you begin](#before-you-begin)
  - [Deploy from Azure Marketplace](#deploy-from-azure-marketplace)
  - [Configuration of Azure DevOps connector](#configuration-of-azure-devops-connector)
    - [Installation](#installation)
    - [Settings](#settings)
  - [Delegation](#delegation)
    - [Supported sync Scenarios](#supported-sync-scenarios)
      - [Azure DevOps to SDP sync](#azure-devops-to-sdp-sync)
      - [SDP to Azure DevOps sync](#sdp-to-azure-devops-sync)
      - [Merge](#merge)
  - [Remove Azure DevOps connector](#remove-azure-devops-connector)
  - [Upgrade Azure DevOps connector](#upgrade-azure-devops-connector)
  - [Troubleshooting](#troubleshooting)
  - [Azure DevOps project configuration](#azure-devops-project-configuration)
  - [ServiceDesk Plus MSP configuration](#servicedesk-plus-msp-configuration)
    - [Add custom Statuses](#add-custom-statuses)
    - [Add custom Priorities](#add-custom-priorities)
    - [Create Additional fields for the Account](#create-additional-fields-for-the-account)
    - [Add  Incident Additional fields in Helpdesk Customizer](#add--incident-additional-fields-in-helpdesk-customizer)
    - [Add Additional fields to Template](#add-additional-fields-to-template)
    - [Create Custom Triggers](#create-custom-triggers)
    - [Add Azure DevOps connector files to execute in SDP](#add-azure-devops-connector-files-to-execute-in-sdp)
## Before you begin

**Prerequisite:** 

- [Service Desk Plus MSP](https://www.manageengine.com/products/service-desk-msp/)  **build 10530**  already installed and configured. [Follow configuration Prerequisties.](#Service-Desk-Plus-MSP-configuration)
- [Existing Project in Azure DevOps configured](#Azure-DevOps-project-configuration) for **Scrum process**.


## Deploy from Azure Marketplace

[Coming soon] Navigate to [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) and find ["Azure DevOps connector"](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/) offer.

## Configuration of Azure DevOps connector 

After you have selected appropriate software plan you need to configure the deployment.

### Installation

![Basics](./media/azDevOpsConnector/create-az-devops-sdp.png)

- Choose a subscription to deploy the managed application.
- Create a new Resource Group.
- Select a region.
- Provide a name for your application's managed resource group. Note: you can specify name of managed resource group according to your naming convention.
- Press "Next : Settings >" button.

### Settings

To enable Azure DevOps Connector for ServiceDesk Plus you have to specify API key of the account that has administrator or SDAdmin role in the ServiceDesk Plus.  
  Note. All delegation related actions will be processed on behalf of this user.

![Settings](./media/azDevOpsConnector/settings.png)

- Press "Next : Review + create >" button.

## Delegation

To delegate a ticket open it in ServiceDesk Plus MSP and select the "Delegate" in Custom menu. 

![delegateItem](./media/azDevOpsConnector/Delegate.png)

Upon successful delegation ticket status will be changed to [Delegated] in ServiceDesk Plus MSP.

If all parts were successfully configured, each time upon delegation a new note will be added to the SDP request, which will contain delegation details and link to PBI created in Azure DevOps board. 

**Note.** Notes, Requesters replies, Technician replies existed <u>before delegation</u> will be synced to Azure DevOps workitem.

![ExtRequestSuccessfullyCreated](./media/azDevOpsConnector/ExtRequestSuccessfullyCreated.png)

### Supported sync Scenarios

Supported workitem type by Azure DevOps connector **Product Backlog Item**.

#### Azure DevOps to SDP sync

**Workitem updates sync**

In case PBI status changed or PBI updated new note will be added in SDP for the delegated ticket. 

**Workitem Status  sync**

Changing status to [Done] in Azure DevOps will make the state change from [Delegated] to [Pending Resolved] in ServiceDesk Plus MSP.

**Workitem Discussion  sync**

Discussions commented  in Azure DevOps will be synced to ServiceDesk Plus MSP as notes. 

#### SDP to Azure DevOps sync

**Requesters replies sync**

In case requester who owns the request or requester with whom request has been shared clicks [Reply] in ServiceDesk Plus MSP form and leaves a comment, this comment will be synced to Azure DevOps board. (All replies that have <span  style="color:red">red</span> envelope icon in ServiceDesk Plus MSP will be synced).

**Important!** 
The following sync scenarios are not supported: 

- Notes from Requester in SDP are not synced to Azure DevOps board. 
-  Replies from Technician in SDP are not synced to Azure DevOps board. 
-  Notes from Technician in SDP are not synced to Azure DevOps board. 

#### Merge

**Requests merged before delegation**
Information about requests that were merged before delegation will be synced to Azure DevOps discussion without loss. 

**Requests merged after delegation**
Merged requests after delegation will not be synced with Azure DevOps because they are not considered as replies.

## Remove Azure DevOps connector

**Step 1:** Go to Resource Group where the Managed Application installed. This resource group name **does not** start with "mrg-".

**Step 2:** Select this Application and click "Delete" button, confirm the deletion by typing "Yes" on the sidebar, then click "Delete". Deletion of the Managed Application will consequently delete the managed resource group with "mrg-" prefix and all of its content.

**Step 3:** (optional) If the Managed Application Resource Group is empty - only Managed Application was stored there - you can delete this Resource Group as well.

## Upgrade Azure DevOps connector

New version of container image with increased version will automatically replace the old one.

## Troubleshooting

* Logs

  In case of any issue encountered with Azure DevOps connector, please check Azure DevOps connector logs. Logs are located in the managed resource group: mrg-<id> in  App Service - Deployment Center - Logs tab. 

* Service Hooks

  To sync Azure DevOps work items with requests in ServiceDesk Plus MSP 2 web hooks are created automatically on first delegation. Both web hooks must have "Enabled" state. To check status of web hooks please go to the Azure DevOps Projects Settings - General - Service hooks. 

  In case sync of work items stopped working, please check the web hooks state and re-enable them. 

## Azure DevOps project configuration

Before generating access token, [create Azure DevOps project.](https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project?view=azure-devops&tabs=preview-page)

**Obtaining the Azure DevOps token**

Please choose "Personal access tokens" option on Azure DevOps personal settings to obtain Azure DevOps token.

![DevOpsKey](./media/azDevOpsConnector/devOpsKey.png)

Press "New Token" button.

![NewToken](./media/azDevOpsConnector/newToken.png)

In opened dialog window please complete the following settings:

- Token name
- Token Organization
- Define token expiration date
- Grant token "Read, write, & manage" permissions with work items
- Press "Create" button

![TokenSettings](./media/azDevOpsConnector/tokenSettings.png)

Make sure you've copied the token. **It is not stored and you will not be able to see it again.**

![SuccessToken](./media/azDevOpsConnector/successToken.png)

## ServiceDesk Plus MSP configuration

Login to ServiceDesk Plus MSP with administrator credentials. 
Configure necessary fields from the "Admin" menu. 

### Add custom Statuses

In Helpdesk Customizer - Status create new statuses: 
- "Delegated"
- "Pending Resolved"
Statuses are used by Azure DevOps connector to map Azure DevOps priorities  with statuses in ServiceDesk Plus. 

### Add custom Priorities

In addition to existing default priorities in ServiceDesk Plus MSP Azure DevOps connector works with custom created priorities. 

In  ServiceDesk Plus MSP go to Helpdesk Customizer - Status and create new statuses: 
- "Critical"

- "Very low" 

The below table explains ServiceDesk Plus MSP priority mapping  with Azure DevOps product backlog item Priority.

| Priority in ServiceDesk Plus | Priority in Azure DevOps PBI |
| ---------------------------- | ---------------------------- |
| Not Specified                | 2                            |
| Critical                     | 1                            |
| High                         | 2                            |
| Medium                       | 3                            |
| Normal                       | 3                            |
| Low                          | 4                            |
| Very low                     | 4                            |

The below table explains ServiceDesk Plus MSP priority mapping  with Azure DevOps product backlog item Severity.

| Priority in ServiceDesk Plus | Severity  in Azure DevOps PBI |
| ----------------------------- | ---------------------------- |
| Not Specified                 | 3-Medium |
| Critical                      | 1                           |
| High                          | 2-High             |
| Medium                        | 3-Medium                     |
| Normal | 3-Medium |
| Low                           | 4-Low                        |
| Very low                      | 4-Low                        |

### Create Additional fields for the Account

In Account Details - Account - Additional fields create following fields of type "Single-line":

- AzureDevOpsOrganization
- AzureDevOpsProject
- AzureDevOpsArea
- AzureDevOpsToken

![AccountAdditionalFields](./media/azDevOpsConnector/AccountAdditionalFields.png)

You need to fill these values with your Azure DevOps data for each account to make Azure DevOps connector sync data. 

Below you can see an example of filled values for the selected Account in ServiceDesk Plus:

![Organization](./media/azDevOpsConnector/Organization.png)

### Add  Incident Additional fields in Helpdesk Customizer

Go to Helpdesk Customizer - Incident Additional fields.

Add following fields: 

- Azure DevOps Connector Data, type: Numeric
- Delegated request link, type: Single Line

![incidentAdditionalFields](./media/azDevOpsConnector/incidentAdditionalFields.png)

### Add Additional fields to Template

You can add *Azure DevOps Connector Data, Delegated request link* to Incident Template or any other template that will be used to delegate requests using Azure DevOps connector.

### Create Custom Triggers

In Helpdesk Customizer - Custom Triggers create custom trigger. 

![AddCommentCustomTrigger](./media/azDevOpsConnector/AddCommentCustomTrigger.png)

Action name: Add comment

Checked checkbox "This custom trigger is applicable for all accounts"

Execute the Action: When a new reply received - Any Time

Match the below criteria: Delegated request link - is not - null

Perform Action: Webhook Action - Add - Template name "Add comment"

![AddCommentActionConfiguration](./media/azDevOpsConnector/AddCommentActionConfiguration.png)

Example of Webhook configuation: 

![WebHookInSDP](./media/azDevOpsConnector/WebHookInSDP.png)

```php

URL :
POST : https://example.com/api/Comment/Add

Headers :
Content-Type:application/x-www-form-urlencoded

Parameters :

Body :
workItemId=$UDF_LONG2&requestId=$RequestId&organization=$ATTRIBUTE_301&project=$ATTRIBUTE_302&area=$ATTRIBUTE_303&token=$ATTRIBUTE_304

```

where
* example.com - domain name of website that hosts Azure DevOps connector service
* ATTRIBUTE_\<integer\> - variables that represent additional fields for the Account: AzureDevOpsOrganization, AzureDevOpsProject, AzureDevOpsArea, AzureDevOpsToken
*  UDF_LONG\<integer\> - ID of Azure DevOps Connector Data Incident additional field

### Add Azure DevOps connector files to execute in SDP

Add link to Create_Azure_DevOps_Work_Item.html in Request custom menu template and add special Azure DevOps Connector files to SDP host. 
In case SDP installed on Windows server system drive default path will look as follows: C:\Program Files\ManageEngine\ServiceDeskPlus-MSP\integration\resources 

In SDP installation folder do following actions: 

1. Put file Create_Azure_DevOps_Work_Item.html 
2. Create folder with name "scripts" in "resources" and put file "Create_Azure_DevOps_Work_Item.js" there
3. Create folder with name "css" in "resources" and put file "Create_Azure_DevOps_Work_Item.css" there