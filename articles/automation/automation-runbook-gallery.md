---
title: Runbook and module galleries for Azure Automation | Microsoft Docs
description: Runbooks and modules from Microsoft and the community are available for you to install and use in your Azure Automation environment.  This article describes how you can access these resources and to contribute your runbooks to the gallery.
services: automation
documentationcenter: ''
author: eslesar
manager: jwhit
editor: tysonn

ms.assetid: d3fee7b4-630a-4c10-8425-9bf51d7c9e58
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte;bwren

---
# Runbook and module galleries for Azure Automation
Rather than creating your own runbooks and modules in Azure Automation, you can access a variety of scenarios that have already been built by Microsoft and the community.  You can either use these scenarios without modification or you can use them as a starting point and edit them for your specific requirements.

You can get runbooks from the [Runbook Gallery](#runbooks-in-runbook-gallery) and modules from the [PowerShell Gallery](#modules-in-powerShell-gallery).  You can also contribute to the community by sharing scenarios that you develop.

## Runbooks in Runbook Gallery
The [Runbook Gallery](http://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) provides a variety of runbooks from Microsoft and the community that you can import into Azure Automation. You can either download a runbook from the gallery, which is hosted in the [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload), or you can directly import runbooks from the gallery from either the Azure classic portal or Azure portal.

You can only import directly from the Runbook Gallery using the Azure classic portal or Azure portal. You cannot perform this function using Windows PowerShell.

> [!NOTE]
> You should validate the contents of any runbooks that you get from the Runbook Gallery and use extreme caution in installing and running them in a production environment.|
> 
> 

### To import a runbook from the Runbook Gallery with the Azure classic portal
1. In the Azure portal, click, **New**, **App Services**, **Automation**, **Runbook**, **From Gallery**.
2. Select a category to view related runbooks, and select a runbook to view its details. When you select the runbook you want, click the right arrow button.
   
    ![Runbook gallery](media/automation-runbook-gallery/runbook-gallery.png)
3. Review the contents of the runbook and note any requirements in the description. Click the right arrow button when you’re done.
4. Enter the runbook details and then click the checkmark button. The runbook name is already filled in.
5. The runbook appears on the **Runbooks** tab for the Automation Account.

### To import a runbook from the Runbook Gallery with the Azure portal
1. In the Azure portal, open your Automation account.
2. Click on the **Runbooks** tile to open the list of runbooks.
3. Click **Browse gallery** button.
   
    ![Browse gallery button](media/automation-runbook-gallery/browse-gallery-button.png)
4. Locate the gallery item you want and select it to view its details.
   
    ![Browse gallery](media/automation-runbook-gallery/browse-gallery.png)
5. Click on **View source project** to view the item in the [TechNet Script Center](http://gallery.technet.microsoft.com/).
6. To import an item, click on it to view its details and then click the **Import** button.
   
    ![Import button](media/automation-runbook-gallery/gallery-item-detail.png)
7. Optionally, change the name of the runbook and then click **OK** to import the runbook.
8. The runbook appears on the **Runbooks** tab for the Automation Account.

### Adding a runbook to the runbook gallery
Microsoft encourages you to add runbooks to the Runbook Gallery that you think would be useful to other customers.  You can add a runbook by [uploading it to the Script Center](http://gallery.technet.microsoft.com/site/upload) taking into account the following details.

* You must specify *Windows Azure* for the **Category** and *Automation* for the **Subcategory** for the runbook to be displayed in the wizard.  
* The upload must be a single .ps1 or .graphrunbook file.  If the runbook requires any modules, child runbooks, or assets, then you should list those in the description of the submission and in the comments section of the runbook.  If you have a scenario requiring multiple runbooks, then upload each separately and list the names of the related runbooks in each of their descriptions. Make sure that you use the same tags so that they show up in the same category. A user will have to read the description to know that other runbooks are required the scenario to work.
* Add the tag "GraphicalPS" if you are publishing a **Graphical runbook** (not a Graphical Workflow). 
* Insert either a PowerShell or PowerShell Workflow code snippet into the description using **Insert code section** icon.
* The Summary for the upload is displayed in the Runbook Gallery results so you should provide detailed information that helps a user identify the functionality of the runbook.
* You should assign one to three of the following Tags to the upload.  The runbook is listed in the wizard under the categories that match its tags.  Any tags not on this list are ignored by the wizard. If you don’t specify any matching tags, the runbook is listed under the Other category.
  
  * Backup
  * Capacity Management
  * Change Control
  * Compliance
  * Dev / Test Environments
  * Disaster Recovery
  * Monitoring
  * Patching
  * Provisioning
  * Remediation
  * VM Lifecycle Management
* Automation updates the Gallery once an hour, so you won’t see your contributions immediately.

## Modules in PowerShell Gallery
PowerShell modules contain cmdlets that you can use in your runbooks, and existing modules that you can install in Azure Automation are available in the [PowerShell Gallery](http://www.powershellgallery.com).  You can launch this gallery from the Azure portal and install them directly into Azure Automation or you can download them and install them manually.  You cannot install the modules directly from the Azure classic portal, but you can download them install them as you would any other module.

### To import a module from the Automation Module Gallery with the Azure portal
1. In the Azure portal, open your Automation account.
2. Select **Modules** under **Shared Resources** to open the list of modules.
4. Click **Browse gallery** from the top of the page.
   
    ![Module gallery](media/automation-runbook-gallery/modules-blade.png) <br>
5. On the **Browse gallery** page, you can search by the following fields:
   
   * Module Name
   * Tags
   * Author
   * Cmdlet/DSC resource name
6. Locate a module that you're interested in and select it to view its details.  
   When you drill into a specific module, you can view more information about the module, including a link back to the PowerShell Gallery, any required dependencies, and all of the cmdlets and/or DSC resources that the module contains.
   
    ![PowerShell module details](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. To install the module directly into Azure Automation, click the **Import** button.
   
    ![Import module button](media/automation-runbook-gallery/module-import-button.png)
8. When you click the Import button, on the **Import** pane, you see the module name that you are about to import. If all the dependencies are installed, the **OK** button is activated. If you are missing dependencies, you need to import those before you can import this module.
9. Click **OK** to import the module. While Azure Automation imports a module to your account, it extracts metadata about the module and the cmdlets.
   
    ![Import module page](media/automation-runbook-gallery/module-import-blade.png)
   
    This may take a couple of minutes since each activity needs to be extracted.
10. You receive an initial notification that the module is being deployed and another notification when it has completed.
11. After the module is imported, you can see the available activities, and you can use its resources in your runbooks and Desired State Configuration.

## Requesting a runbook or module
You can send requests to [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  If you need help writing a runbook or have a question about PowerShell, post a question to our [forum](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## Next Steps
* To get started with runbooks, see [Creating or importing a runbook in Azure Automation](automation-creating-importing-runbook.md)
* To understand the differences between PowerShell and PowerShell Workflow with runbooks, see [Learning PowerShell workflow](automation-powershell-workflow.md)

