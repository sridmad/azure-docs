---
title: Getting started with Azure CDN | Microsoft Docs
description: This topic shows how to enable the Azure Content Delivery Network (CDN). The tutorial walks through the creation of a new CDN profile and endpoint.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''

ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: mazha

---
# Getting started with Azure CDN
This article describes how to enable Azure CDN by creating a new CDN profile and endpoint.

> [!IMPORTANT]
> For an introduction to CDN and a list of features, see [CDN Overview](cdn-overview.md).
> 
> 

## Create a new CDN profile
A CDN profile is a collection of CDN endpoints.  Each profile contains one or more CDN endpoints.  You may wish to use multiple profiles to organize your CDN endpoints by internet domain, web application, or some other criteria.

> [!NOTE]
> An Azure subscription has default limits for the following resources:
> - The number of CDN profiles that can be created
> - The number of endpoints that can be created in a CDN profile 
> - The number of custom domains that can be mapped to an endpoint
>
> For information about CDN subscription limits, see [CDN limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> CDN pricing is applied at the CDN profile level. If you wish to use a mix of Azure CDN pricing tiers, you will need multiple CDN profiles.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Create a new CDN endpoint
**To create a new CDN endpoint**

1. In the [Azure portal](https://portal.azure.com), navigate to your CDN profile.  You may have pinned it to the dashboard in the previous step.  If you not, you can find it by clicking **Browse**, then **CDN profiles**, and clicking on the profile you plan to add your endpoint to.
   
    The CDN profile blade appears.
   
    ![CDN profile][cdn-profile-settings]
2. Click the **Add Endpoint** button.
   
    ![Add endpoint button][cdn-new-endpoint-button]
   
    The **Add an endpoint** blade appears.
   
    ![Add endpoint blade][cdn-add-endpoint]
3. Enter a **Name** for this CDN endpoint.  This name is used to access your cached resources at the domain `<endpointname>.azureedge.net`.
4. In the **Origin type** dropdown, select your origin type.  Select **Storage** for an Azure Storage account, **Cloud service** for an Azure Cloud Service, **Web App** for an Azure Web App, or **Custom origin** for any other publicly accessible web server origin (hosted in Azure or elsewhere).
   
    ![CDN origin type](./media/cdn-create-new-endpoint/cdn-origin-type.png)
5. In the **Origin hostname** dropdown, select or type your origin domain.  The dropdown lists all available origins of the type you specified in step 4.  If you select *Custom origin* as your **Origin type**, enter the domain of your custom origin.
6. In the **Origin path** text box, enter the path to the resources you want to cache, or leave blank to allow cache any resource at the domain you specified in step 5.
7. In the **Origin host header**, enter the host header you want the CDN to send with each request, or leave the default.
   
   > [!WARNING]
   > Some types of origins, such as Azure Storage and Web Apps, require the host header to match the domain of the origin. Unless you have an origin that requires a host header different from its domain, you should leave the default value.
   > 
   > 
8. For **Protocol** and **Origin port**, specify the protocols and ports used to access your resources at the origin. At least one protocol (HTTP or HTTPS) must be selected. Use the CDN-provided domain (`<endpointname>.azureedge.net`) to access HTTPS content. 
   
   > [!NOTE]
   > The **Origin port** only affects what port the endpoint uses to retrieve information from the origin.  The endpoint itself is only available to end clients on the default HTTP and HTTPS ports (80 and 443), regardless of the **Origin port**.  
   > 
   > **Azure CDN from Akamai** endpoints do not allow the full TCP port range for origins.  For a list of origin ports that are not allowed, see [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > Accessing CDN content using HTTPS has the following constraints:
   > 
   > * You must use the SSL certificate provided by the CDN. Third-party certificates are not supported.
   > * HTTPS support for Azure CDN custom domains is available only with **Azure CDN from Verizon** products (Standard and Premium). It is not supported on **Azure CDN from Akamai** products. For more information, see [Configure HTTPS on an Azure CDN custom domain](cdn-custom-ssl.md).
  
9. Click the **Add** button to create the new endpoint.
   
   After the endpoint is created, it appears in the list of endpoints for the profile.
    
   ![CDN endpoint][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Because it takes time for the registration to propagate, the endpoint will not be immediately available for use.  For <b>Azure CDN from Akamai</b> profiles, propagation usually completes within one minute. For <b>Azure CDN from Verizon</b> profiles, propagation usually completes within 90 minutes, but in some cases can take longer.
    > 
    > Users who try to use the CDN domain name before the endpoint configuration has propagated to the POPs can receive HTTP 404 response codes.  If it's been several hours since you created your endpoint and you're still receiving 404 responses, see [Troubleshooting CDN endpoints returning 404 statuses](cdn-troubleshoot-endpoint.md).
    > 
    > 

## See Also
* [Controlling caching behavior of requests with query strings](cdn-query-string.md)
* [How to Map CDN Content to a Custom Domain](cdn-map-content-to-custom-domain.md)
* [Pre-load assets on an Azure CDN endpoint](cdn-preload-endpoint.md)
* [Purge an Azure CDN Endpoint](cdn-purge-endpoint.md)
* [Troubleshooting CDN endpoints returning 404 statuses](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
