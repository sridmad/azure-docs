---
title: 'Configure a web application firewall: Azure Application Gateway | Microsoft Docs'
description: This article provides guidance on how to start using a web application firewall on an existing or new application gateway.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn

ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: davidmu

---
# Configure a web application firewall on a new or existing application gateway with Azure CLI

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Learn how to create a web application firewall (WAF)-enabled application gateway. Also learn how to add a WAF to an existing application gateway.

The WAF in Azure Application Gateway protects web applications from common web-based attacks like SQL injection, cross-site scripting attacks, and session hijacks.

 Application Gateway is a layer-7 load balancer. It provides failover, performance-routing HTTP requests between different servers, whether they're on the cloud or on-premises. Application Gateway provides many application delivery controller (ADC) features:

 * HTTP load balancing 
 * Cookie-based session affinity 
 * Secure Sockets Layer (SSL) offload 
 * Custom health probes 
 * Support for multisite functionality
 
 To find a complete list of supported features, see [Overview of Application Gateway](application-gateway-introduction.md).

This article shows how to [add a web application firewall to an existing application gateway](#add-web-application-firewall-to-an-existing-application-gateway). It also shows how to [create an application gateway that uses a web application firewall](#create-an-application-gateway-with-web-application-firewall).

![Scenario image][scenario]

## Prerequisite: Install the Azure CLI 2.0

To perform the steps in this article, you need to [install the Azure command-line interface (Azure CLI) for Mac, Linux, and Windows](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2).

## WAF configuration differences

If you've read [Create an application gateway with Azure CLI](application-gateway-create-gateway-cli.md), you understand the SKU settings to configure when you create an application gateway. The WAF provides additional settings to define when you configure the SKU on an application gateway. There are no additional changes that you make on the application gateway itself.

| **Setting** | **Details**
|---|---|
|**SKU** |A normal application gateway without a WAF supports **Standard\_Small**, **Standard\_Medium**, and **Standard\_Large** sizes. With the introduction of a WAF, there are two additional SKUs, **WAF\_Medium** and **WAF\_Large**. A WAF is not supported on small application gateways.|
|**Mode** | This setting is the mode of the WAF. Allowed values are **Detection** and **Prevention**. When the WAF is set up in **Detection** mode, all threats are stored in a log file. In **Prevention** mode, events are still logged, but the attacker receives a 403 unauthorized response from the application gateway.|

## Add a web application firewall to an existing application gateway

The following command changes an existing standard application gateway to a WAF-enabled application gateway:

```azurecli-interactive
#!/bin/bash

az network application-gateway waf-config set \
  --enabled true \
  --firewall-mode Prevention \
  --gateway-name "AdatumAppGateway" \
  --resource-group "AdatumAppGatewayRG"
```

This command updates the application gateway with a WAF. To understand how to view logs for your application gateway, see [Application Gateway diagnostics](application-gateway-diagnostics.md). Due to the security nature of a WAF, review logs regularly to understand the security posture of your web applications.

## Create an application gateway with a web application firewall

The following command creates an application gateway with a WAF:

```azurecli-interactive
#!/bin/bash

az network application-gateway create \
  --name "AdatumAppGateway2" \
  --location "eastus" \
  --resource-group "AdatumAppGatewayRG" \
  --vnet-name "AdatumAppGatewayVNET2" \
  --vnet-address-prefix "10.0.0.0/16" \
  --subnet "Appgatewaysubnet2" \
  --subnet-address-prefix "10.0.0.0/28" \
 --servers "10.0.0.5 10.0.0.4" \
  --capacity 2 
  --sku "WAF_Medium" \
  --http-settings-cookie-based-affinity "Enabled" \
  --http-settings-protocol "Http" \
  --frontend-port "80" \
  --routing-rule-type "Basic" \
  --http-settings-port "80" \
  --public-ip-address "pip2" \
  --public-ip-address-allocation "dynamic" \
  --tags "cli[2] owner[administrator]"
```

> [!NOTE]
> Application gateways created with the basic WAF configuration are configured with CRS 3.0 for protections.

## Get an application gateway DNS name

After the gateway is created, the next step is to configure the front end for communication. When you use a public IP, the application gateway requires a dynamically assigned DNS name, which is not friendly. To ensure that users can hit the application gateway, use a CNAME record to point to the public endpoint of the application gateway. For more information, see [Configure a custom domain name for an Azure cloud service](../cloud-services/cloud-services-custom-domain-name-portal.md). 

To configure a CNAME record, retrieve details of the application gateway and its associated IP/DNS name by using the PublicIPAddress element attached to the application gateway. Use the application gateway's DNS name to create a CNAME record, which points the two web applications to this DNS name. We do not recommend using A records, because the VIP might change when the application gateway restarts.

```azurecli-interactive
#!/bin/bash

az network public-ip show \
  --name pip2 \
  --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## Next steps

To learn how to customize WAF rules, see [Customize web application firewall rules through the Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md).

[scenario]: ./media/application-gateway-web-application-firewall-cli/scenario.png
