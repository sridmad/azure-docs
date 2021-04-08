---
title: Stream Azure Diagnostic Logs to Log Analytics | Microsoft Docs
description: Learn how to stream Azure diagnostic logs to a Log Analytics workspace.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem

---
# Stream Azure Diagnostic Logs to Log Analytics
**[Azure diagnostic logs](monitoring-overview-of-diagnostic-logs.md)** can be streamed in near real time to Azure Log Analytics using the portal, PowerShell cmdlets or Azure CLI.

## What you can do with diagnostics logs in Log Analytics

Azure Log Analytics is a flexible log search and analytics tool that enables you to gain insight into the raw log data generated from Azure resources. Some capabilities include:

* **Log search** - Write advanced queries over your log data, correlate logs from various sources, and even generate charts that can be pinned to your Azure dashboard.
* **Alerting** - Detect when one or more events match a particular query and become notified with an email or webhook call.
* **Solutions** - Use pre-built views and dashboards that give you immediate insight into your log data.
* **Advanced analytics** - Apply machine learning and pattern matching algorithms to identify possible issues revealed by your logs.

## Enable streaming of diagnostic logs to Log Analytics
You can enable streaming of diagnostic logs programmatically, via the portal, or using the [Azure Monitor REST APIs](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Either way, you create a diagnostic setting in which you specify a Log Analytics workspace and the log categories and metrics you want to send in to that workspace. A diagnostic **log category** is a type of log that a resource may provide.

The Log Analytics workspace does not have to be in the same subscription as the resource emitting logs as long as the user who configures the setting has appropriate RBAC access to both subscriptions.

## Stream diagnostic logs using the portal
1. In the portal, navigate to Azure Monitor and click on **Diagnostic Settings**

    ![Monitoring section of Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Optionally filter the list by resource group or resource type, then click on the resource for which you would like to set a diagnostic setting.

3. If no settings exist on the resource you have selected, you are prompted to create a setting. Click "Turn on diagnostics."

   ![Add diagnostic setting - no existing settings](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   If there are existing settings on the resource, you will see a list of settings already configured on this resource. Click "Add diagnostic setting."

   ![Add diagnostic setting - existing settings](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Give your setting a name and check the box for **Send to Log Analytics**, then select a Log Analytics workspace.
   
   ![Add diagnostic setting - existing settings](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

4. Click **Save**.

After a few moments, the new setting appears in your list of settings for this resource, and diagnostic logs are streamed to that workspace as soon as new event data is generated. Note that there may be up to fifteen minutes between when an event is emitted and when it appears in Log Analytics.

### Via PowerShell Cmdlets
To enable streaming via the [Azure PowerShell Cmdlets](insights-powershell-samples.md), you can use the `Set-AzureRmDiagnosticSetting` cmdlet with these parameters:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Note that the workspaceID property takes the full Azure resource ID of the workspace, not the workspace ID/key shown in the Log Analytics portal.

### Via Azure CLI
To enable streaming via the [Azure CLI](insights-cli-samples.md), you can use the `insights diagnostic set` command like this:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Note that the workspaceId property takes the full Azure resource ID of the workspace, not the workspace ID/key shown in the Log Analytics portal.

## How do I query the data in Log Analytics?

In the Log Search blade in the portal or Advanced Analytics experience as part of Log Analytics, you can query diagnostic logs as part of the Log Management solution under the AzureDiagnostics table. There are also [several solutions for Azure resources](../log-analytics/log-analytics-add-solutions.md) you can install to get immediate insight into the log data you are sending into Log Analytics.


## Next steps
* [Read more about Azure Diagnostic Logs](monitoring-overview-of-diagnostic-logs.md)
