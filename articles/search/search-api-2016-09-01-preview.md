---
title: Azure Search Service REST API Version 2016-09-01-Preview | Microsoft Docs
description: Azure Search Service REST API Version 2016-09-01-Preview includes experimental features such as Synonyms and moreLikeThis searches.
services: search
documentationcenter: na
author: mhko
manager: jlembicz
editor: ''

ms.assetid: 3dba3bf8-9c83-42f6-82bc-04727bd11037
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 10/25/2017
ms.author: nateko

---
# Azure Search Service REST API: Version 2016-09-01-Preview
This article is the reference documentation for `api-version=2016-09-01-Preview`. This preview extends the current generally available version, [api-version=2016-09-01](https://msdn.microsoft.com/library/dn798935.aspx), by providing the following experimental features:

* [Synonyms API](search-synonyms.md) to upload synonym maps and expand search.
* [`moreLikeThis` query parameter](search-more-like-this.md) to find documents that are relevant to a specific document.

Please ensure to target the preview API version `api-version=2016-09-01-Preview` to try these experimental features. The following example illustrates how the preview api version is specified in making a more-like-this query.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> Preview features are available for testing and experimentation with the goal of gathering feedback and are subject to change. **We strongly advise against using preview APIs in production applications.**

Azure Search service is available in multiple versions. Please refer to [Search Service Versioning](http://msdn.microsoft.com/library/azure/dn864560.aspx) for details.
