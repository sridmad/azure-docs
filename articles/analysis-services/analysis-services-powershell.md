﻿---
title: Manage Azure Analysis Services with PowerShell | Microsoft Docs
description: Azure Analysis Services management with PowerShell.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: 

ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: owend

---

# Manage Azure Analysis Services with PowerShell

This article describes PowerShell cmdlets used to perform Azure Analysis Services server and database management tasks. 

Server management tasks such as creating or deleting a server, suspending or resuming server operations, or changing the service level (tier) use Azure Resource Manager (AzureRM) cmdlets. Other tasks for managing databases such as adding or removing role members, processing, or partitioning use cmdlets included in the same SqlServer module as SQL Server Analysis Services.

## Permissions
Most PowerShell tasks require you have Admin privileges on the Analysis Services server you are managing. Scheduled PowerShell tasks are unattended operations. The account running the scheduler must have Admin privileges on the Analysis Services server. 

For server operations using AzureRm cmdlets, your account or the account running scheduler must also belong to the Owner role for the resource in [Azure Role-Based Access Control (RBAC)](../active-directory/role-based-access-control-what-is.md). 

## Server operations 
Azure Analysis Services cmdlets are included in the [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) component module. To install AzureRM cmdlet modules, see [Azure Resource Manager cmdlets](/powershell/azure/overview) in the PowerShell Gallery.

|Cmdlet|Description| 
|------------|-----------------| 
|[Add-AzureAnalysisServicesAccount](/powershell/module/azurerm.analysisservices/add-azureanalysisservicesaccount)|Adds an authenticated account to use for Azure Analysis Services server cmdlet requests.| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|Gets details of a server instance.|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|Creates a server instance.|   
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|Removes a server instance.|  
|[Restart-AzureAnalysisServicesInstance](/powershell/module/azurerm.analysisservices/restart-azureanalysisservicesinstance)|Restarts an instance of Analysis Services server in the currently logged in environment; specified in Add-AzureAnalysisServicesAccount command.|  
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|Resumes a server instance.|  
|[Suspend-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|Suspends a server instance.| 
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|Modifies a server instance.|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|Tests the existence of a server  instance.| 

## Database operations

Azure Analysis Services database operations use the same [SqlServer](https://www.powershellgallery.com/packages/SqlServer) module as SQL Server Analysis Services. However, not all cmdlets are supported for Azure Analysis Services. 

The SqlServer module provides task-specific database management cmdlets as well as the general-purpose Invoke-ASCmd cmdlet that accepts a Tabular Model Scripting Language (TMSL) query or script. The following cmdlets in the SqlServer module are supported for Azure Analysis Services.

  
|Cmdlet|Description|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Add a member to a database role.| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|Backup an Analysis Services database.|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Remove a member from a database role.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Execute a TMSL script.|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|Process a database.|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|Process a partition.| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|Process a table.|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|Merge a partition.|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|Restore an Analysis Services database.| 
  

## Related information

* [Download SQL Server PowerShell Module](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [Download SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer module in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://msdn.microsoft.com/library/mt712541.aspx)
