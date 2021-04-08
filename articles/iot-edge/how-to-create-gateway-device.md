---
# Mandatory fields. See more on aka.ms/skyeye/meta.
title: Create a gateway device with Azure IoT Edge | Microsoft Docs 
description: Use Azure IoT Edge to create a gateway device that can process information for multiple devices
services: iot-edge
keywords: 
author: kgremban
manager: timlt

ms.author: kgremban
ms.date: 11/15/2017
ms.topic: article
ms.service: iot-edge

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.devlang:devlang-from-white-list
# ms.suite: 
# ms.tgt_pltfrm:
# ms.reviewer:
---

# Create an IoT Edge gateway device to process data from other IoT devices - preview

There are two ways to use IoT Edge devices as gateways:

* Connect downstream devices that use [Azure IoT device SDK][lnk-devicesdk];
* Connect devices that do not use the IoT Hub protocol.

When you use an IoT Edge device as a gateway you get the following advantages:

* **Connection multiplexing**. All devices connecting to IoT Hub through an IoT Edge device will use the same underlying connection.
* **Traffic smoothing**. The IoT Edge device will automatically implement exponential backoff in case of IoT Hub throttling, while persisting the messages locally. This will make your solution resilient to spikes in traffic.
* **Limited offline support**. The gateway device will store locally messages that cannot be delivered to IoT Hub.

In this article we will call *IoT Edge gateway* an IoT Edge device used as a gateway.

>[!NOTE]
>Currently:
> * Downstream devices are not able to authenticate with the gateway if the gateway is disconnected from IoT Hub; and
> * IoT Edge devices cannot connect to IoT Edge gateways.

## Use the Azure IoT device SDK

The Edge hub that is installed in all IoT Edge devices exposes the following primitives to downstream devices:

* device-to-cloud and cloud-to-device messages;
* direct methods; and
* device twin operations.

>[!NOTE]
>Currently, downstream devices are not able to use file upload when connecting through an IoT Edge gateway.

When you connect devices to an IoT Edge gateway using the Azure IoT device SDK, you need to:

* Set up the downstream device with a connection string referring to the gateway device hostname; and
* Make sure that the downstream device trusts the certificate used to accept the connection by the gateway device.

When you install the Azure IoT Edge runtime using the control script, a certificate is created for the Edge hub, as you did in the tutorial Install IoT Edge on a simulated device on [Windows][lnk-tutorial1-win] and [Linux][lnk-tutorial1-lin]. This certificate is used by the Edge hub to accept incoming TLS connections, and has to be trusted by the downstream device when connecting to the gateway device.

You can create any certificate infrastructure that enables the trust required for your device-gateway topology. In this article, we assume the same certificate setup that you would use to enable [X.509 CA security][lnk-iothub-x509] in IoT Hub, which involves a X.509 CA certificate associated to a specific IoT hub (the *IoT hub owner CA*), and a series of certificates, signed with this CA, installed in the IoT Edge devices.

>[!IMPORTANT]
>Currently, IoT Edge devices and downstream devices can only use [SAS tokens][lnk-iothub-tokens] to authenticate with IoT Hub. The certificates will be used only to validate the TLS connection between the leaf and gateway device.

Our configuration uses **IoT hub owner CA** as:
* a signing certificate for the setup of the IoT Edge runtime on all IoT Edge devices, and as
* a public key certificate installed in downstream devices.

This will result in a solution that enables all devices to use any IoT Edge device as a gateway, as long as they are connected to the same IoT hub.

### Create the certificates for test scenarios

You can use the sample Powershell and Bash scripts described in [Managing CA Certificate Sample][lnk-ca-scripts] to generate a self-signed **IoT hub owner CA** and device certificates signed with it.

1. Follow step 1 of [Managing CA Certificate Sample][lnk-ca-scripts] to install the scripts. Make sure to clone from the `modules-preview` branch:
                
                git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
2. Follow step 2 to generate the **IoT hub owner CA**, this file will be used by the downstream devices to validate the connection.

Use the following instructions to generate a certificate for your gateway device.

#### Bash

* Run `./certGen.sh create_edge_device_certificate myGateway` to create the new device certificate.  
  This will create the files .\certs\new-edge-device.* that contain the public key and PFX and .\private\new-edge-device.key.pem that contains the device's private key.  
* In the `certs` directory run `cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem` to get the full chain of the device public key.
* `./private/new-edge-device.cert.pem` contains the device's private key.

#### Powershell

* Run `New-CACertsEdgeDevice myGateway` to create the new device certificate.
  This will create files myEdgeDevice* that contain the public key, private key, and PFX of this certificate.  When prompted to enter a password during the signing process, enter "1234".


>[!IMPORTANT]
>This sample is meant only for test purposes. For production scenarios, refer to [Secure your IoT deployment][lnk-iothub-secure-deployment] for the Azure IoT guidelines on how to secure your IoT solution, and provision your certificate accordingly.

### Configure an IoT Edge device as a gateway

In order to configure your IoT Edge device as a gateway you just need to configure to use the device certificate created in the previous section.

We assume the following file names from the sample scripts above:

| Output | Bash script | Powershell |
| ------ | ----------- | ---------- |
| Device certificate | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| Device private key | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| Device certificate chain | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| IoT hub owner CA | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

 Analogously to the installation described in Deploy Azure IoT Edge on a simulated device in [Windows][lnk-tutorial1-win] or [Linux][lnk-tutorial1-lin], you have to provide the above information to the IoT Edge runtime. 
 
 In Linux:

        sudo iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem

In Windows:

        iotedgectl setup --connection-string {device connection string}
            --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
            --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
            --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
            --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
            --owner-ca-cert-file {full path}/RootCA.pem

By default the sample scripts do not set a passphrase to the device private key. If you set a passphrase, add the following parameter:

        --device-ca-passphrase {passphrase}

The script will prompt to set a passphrase for the Edge Agent certificate.
You have to restart the IoT Edge runtime after this command.

### Configure an IoT Hub device application as a downstream device

A downstream device can be any application using the [Azure IoT device SDK][lnk-devicesdk], such as the simple one described in [Connect your device to your IoT hub using .NET][lnk-iothub-getstarted].

First, a downstream device application has to trust the **IoT hub owner CA** certificate in order to validate the TLS connections to the gateway devices. This step can usually be performed in two ways: at the OS level, or (for certain languages) at the application level.

For instance, for .NET applications, you can add the following snippet to trust a certificate in PEM format stored in path `certPath`. If using the above script, the path will reference `certs/azure-iot-test-only.root.ca.cert.pem` (Bash), or `RootCA.pem` (Powershell).

        using System.Security.Cryptography.X509Certificates;
        
        ...

        X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
        store.Open(OpenFlags.ReadWrite);
        store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
        store.Close();

Performing this step at the OS level is different between Windows and across Linux distributions.

The second step is to initialize the IoT Hub device sdk with a connection string referring to the hostname of the gateway device.
This is done by appending the `GatewayHostName` property to your device connection string. For instance, here is a sample device connection string for a device, to which we appended the `GatewayHostName` property:

        HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com

These two steps will enable your device application to connect to the gateway device.

## Use other protocols

One of the main functions of a gateway device is to interpret protocols of downstream devices. To connect devices that do not use the IoT Hub protocol, you can develop an IoT Edge module which performs this translation and connects on behalf of the downstream device to IoT Hub.

You can decide to create a *transparent* or *opaque* gateway:

| &nbsp; | Transparent gateway | Opaque gateway|
|--------|-------------|--------|
| Identities that are stored in the IoT Hub identity registry | Identities of all connected devices | Only the identity of the gateway device |
| Device twin | Each connected device has its own device twin | Only the gateway has a device and module twins |
| Direct methods and cloud-to-device messages | The cloud can address each connected device individually | The cloud can only address the gateway device |
| [IoT Hub throttles and quotas][lnk-iothub-throttles-quotas] | Apply to each device | Apply to the gateway device |

When using an opaque gateway pattern, all devices connecting through that gateway share the same cloud-to-device queue, which can contain at most 50 messages. It follows that the opaque gateway pattern should be used only when very few devices are connecting through each field gateway, and their cloud-to-device traffic is low.

When implementing an opaque gateway, your protocol translation module uses the module connection string.

When implementing a transparent gateway, the module creates multiple instances of the IoT Hub device client, using the connection strings for the downstream devices.

The [Azure IoT Edge Modbus Module][lnk-modbus-module] is an open implementation of a protocol-adaptor module for an opaque gateway.

## Next steps

- [Understand the requirements and tools for developing IoT Edge modules][lnk-module-dev].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus