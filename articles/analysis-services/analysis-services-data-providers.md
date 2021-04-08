---
title: Client libraries required for connecting to Azure Analysis Services | Microsoft Docs
description: Describes client libraries required for client applications and tools to connect Azure Analysis Services
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''

ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/01/2017
ms.author: owend

---

# Client libraries for connecting to Azure Analysis Services

Client libraries are necessary for client applications and tools to connect to Analysis Services servers. 

Analysis Services utilize three client libraries. ADOMD.NET and Analysis Services Management Objects (AMO), are managed client libraries. The Analysis Services OLE DB provider (MSOLAP DLL) is a native client library. Typically, all three are installed at the same time. Azure Analysis Services requires the latest versions. 

Microsoft client applications such as Power BI Desktop and Excel install all three client libraries. However, depending on the version or frequency of updates, client libraries may not be the latest versions required by Azure Analysis Services. The same applies to custom applications or other interfaces such as AsCmd, TOM, ADOMD.NET. These applications require manually installing the libraries. The client libraries for manual installation are included in SQL Server feature packs as distributable packages. However, these client libraries are tied to the SQL Server version and may not be the latest.  

Client libraries for client connections are different from data providers required to connect from an Azure Analysis Services server to a data source. To learn more about datasource connections, see [Datasource connections](analysis-services-datasource.md).

## Download the latest client libraries  
Use the following client libraries if you are in a production environment and require fully released and supported versions.

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## Next steps
[Connect with Excel](analysis-services-connect-excel.md)    
[Connect with Power BI](analysis-services-connect-pbi.md)
