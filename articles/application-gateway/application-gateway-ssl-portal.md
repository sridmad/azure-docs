---
title: Configure SSL offload - Azure Application Gateway - Azure Portal | Microsoft Docs
description: This article provides instructions to create an application gateway with SSL offload by using the Azure portal
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn

ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu

---
# Configure an application gateway for SSL offload by using the Azure portal

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-ssl-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)
> * [Azure classic PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway can be configured to terminate the Secure Sockets Layer (SSL) session at the gateway to avoid costly SSL decryption tasks to happen at the web farm. SSL offload also simplifies the front-end server setup and management of the web application.

## Scenario

The following scenario takes you through configuring SSL offload on an existing application gateway. The scenario assumes that you have already followed the steps to [create an application gateway](application-gateway-create-gateway-portal.md).

## Before you begin

To configure SSL offload with an application gateway, a certificate is required. This certificate is loaded on the application gateway and is used to encrypt and decrypt the traffic sent via SSL. The certificate needs to be in Personal Information Exchange (.pfx) format. This file format allows you to export the private key that is required by the application gateway to perform the encryption and decryption of traffic.

## Add an HTTPS listener

The HTTPS listener looks for traffic based on its configuration and helps route the traffic to the back-end pools. To add an HTTPS listener, follow these steps:

   1. Browse to the Azure portal and select an existing application gateway.

   2. Select **Listeners**, and then select the **Add** button to add a listener.

   ![Application Gateway Overview pane][1]


   3. Fill out the following required information for the listener and upload the .pfx certificate:
      - **Name**: The friendly name of the listener.

      - **Frontend IP configuration**: The front-end IP configuration that is used for the listener.

      - **Frontend port (Name/Port)**: A friendly name for the port used on the front end of the application gateway and the actual port that is used.

      - **Protocol**: A switch to determine if HTTPS or HTTP is used for the front end.

      - **Certificate (Name/Password)**: If SSL offload is used, a .pfx certificate is required for this setting. A friendly name and password are also required.

   4. Select **OK**.

![Add a listener pane][2]

## Create a rule and associate it to the listener

The listener has now been created. Next, create a rule to handle the traffic from the listener. Rules define how traffic is routed to the back-end pools based on multiple configuration settings. These settings include the protocol, the port, and the health probes, and whether cookie-based session affinity is use. To create and associate a rule to the listener, follow these steps:


   1. Select the **Rules** of the application gateway, and then select **Add**.

   ![Application Gateway Rules pane][3]


   2. Under **Add basic rule**, enter a friendly name for the rule in the **Name** field, and then select the **Listener** created in the previous step. Select the appropriate **Backend pool** and **HTTP setting**, and then select **OK**.

   ![HTTPS Settings window][4]

The settings are now saved to the application gateway. The save process for these settings may take a while before they are available to view through the portal or through PowerShell. After it is saved, the application gateway handles the encryption and decryption of traffic. All traffic between the application gateway and the back-end web servers will be handled over HTTP. Any communication back to the client, if initiated over HTTPS, will be returned to the client encrypted.

## Next steps

To learn how to configure a custom health probe with Azure Application Gateway, see [Create a custom health probe](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

