---
title: Rules for naming Azure Data Factory entities | Microsoft Docs
description: Describes naming rules for Data Factory entities.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: jhubbard
editor: monicar

ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: shlo

---
# Azure Data Factory - naming rules
The following table provides naming rules for Data Factory artifacts.

> [!NOTE]
> This article applies to version 2 of Data Factory, which is currently in preview. If you are using version 1 of the Data Factory service, which is generally available (GA), see [naming rules in Data Factory version1](v1/data-factory-naming-rules.md).

| Name | Name Uniqueness | Validation Checks |
|:--- |:--- |:--- |
| Data Factory |Unique across Microsoft Azure. Names are case-insensitive, that is, `MyDF` and `mydf` refer to the same data factory. |<ul><li>Each data factory is tied to exactly one Azure subscription.</li><li>Object names must start with a letter or a number, and can contain only letters, numbers, and the dash (-) character.</li><li>Every dash (-) character must be immediately preceded and followed by a letter or a number. Consecutive dashes are not permitted in container names.</li><li>Name can be 3-63 characters long.</li></ul> |
| Linked Services/Tables/Pipelines |Unique with in a data factory. Names are case-insensitive. |<ul><li>Maximum number of characters in a table name: 260.</li><li>Object names must start with a letter, number, or an underscore (_).</li><li>Following characters are not allowed: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |
| Resource Group |Unique across Microsoft Azure. Names are case-insensitive. |<ul><li>Maximum number of characters: 1000.</li><li>Name can contain letters, digits, and the following characters: “-”, “_”, “,” and “.”</li></ul> |

## Next steps
Learn how to create data factories by following step-by-step instructions in [QUickstart: create a data factory](quickstart-create-data-factory-powershell.md) article. 