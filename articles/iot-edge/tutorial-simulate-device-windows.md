---
title: Simulate Azure IoT Edge on Windows | Microsoft Docs 
description: Install the Azure IoT Edge runtime on a simulated device in Windows and deploy your first module
services: iot-edge
keywords: 
author: kgremban
manager: timlt

ms.author: kgremban
ms.reviewer: elioda
ms.date: 11/16/2017
ms.topic: article
ms.service: iot-edge

---

# Deploy Azure IoT Edge on a simulated device in Windows -  preview

Azure IoT Edge enables you to perform analytics and data processing on your devices, instead of having to push all the data to the cloud. The IoT Edge tutorials demonstrate how to deploy different types of modules, built from Azure services or custom code, but first you need a device to test. 

In this tutorial you learn how to:

1. Create an IoT Hub
2. Register an IoT Edge device
3. Start the IoT Edge runtime
4. Deploy a module

![Tutorial architecture][2]

The simulated device that you create in this tutorial is a monitor on a wind turbine that generates temperature, humidity, and pressure data. You're interested in this data because your turbines perform at different levels of efficiency depending on the weather conditions. The other Azure IoT Edge tutorials build upon the work you do here by deploying modules that analyze the data for business insights. 

## Prerequisites

This tutorial assumes that you're using a computer or virtual machine running Windows to simulate an Internet of Things device. 

>[!TIP]
>If you're running Windows in a virtual machine, enable [nested virtualization][lnk-nested] and allocate at least 2GB memory. 

1. Make sure you're using a supported Windows version:
   * Windows 10 
   * Windows Server
2. Install [Docker for Windows][lnk-docker] and make sure it's running.
3. Install [Python 2.7 on Windows][lnk-python] and make sure you can use the pip command.
4. Run the following command to download the IoT Edge control script.

   ```cmd
   pip install -U azure-iot-edge-runtime-ctl
   ```

> [!NOTE]
> Azure IoT Edge can run either Windows containers or Linux containers. If you're running one of the following Windows versions, you can use Windows containers:
>    * Windows 10 Fall Creators Update
>    * Windows Server 1709 (Build 16299)
>    * Windows IoT Core (Build 16299) on a x64-based device
>
> For Windows IoT Core, follow the instructions in [Install the IoT Edge runtime on Windows IoT Core][lnk-install-iotcore]. Otherwise, simply [configure Docker to use Windows containers][lnk-docker-containers]. Use the following command to validate your prerequisites:
>    ```powershell
>    Invoke-Expression (Invoke-WebRequest -useb https://aka.ms/iotedgewin)
>    ```


## Create an IoT hub

Start the tutorial by creating your IoT Hub.
![Create IoT Hub][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## Register an IoT Edge device

Register an IoT Edge device with your newly created IoT Hub.
![Register a device][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## Configure the IoT Edge runtime

Install and start the Azure IoT Edge runtime on your device. 
![Register a device][5]

The IoT Edge runtime is deployed on all IoT Edge devices. It comprises two modules. The **IoT Edge agent** facilitates deployment and monitoring of modules on the IoT Edge device. The **IoT Edge hub** manages communications between modules on the IoT Edge device, and between the device and IoT Hub. When you configure the runtime on your new device, only the IoT Edge agent will start at first. The IoT Edge hub comes later when you deploy a module. 


Configure the runtime with your IoT Edge device connection string from the previous section.

```cmd
iotedgectl setup --connection-string "{device connection string}" --auto-cert-gen-force-no-passwords
```

Start the runtime.

```cmd
iotedgectl start
```

Check Docker to see that the IoT Edge agent is running as a module.

```cmd
docker ps
```

![See edgeAgent in Docker](./media/tutorial-simulate-device-windows/docker-ps.png)

## Deploy a module

Manage your Azure IoT Edge device from the cloud to deploy a module which will send telemetry data to IoT Hub.
![Register a device][6]

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]


## View generated data

In this tutorial, you created a new IoT Edge device and installed the IoT Edge runtime on it. Then, you used the Azure portal to push an IoT Edge module to run on the device without having to make changes to the device itself. In this case, the module that you pushed creates environmental data that you can use for the tutorials. 

Open the command prompt on the computer running your simulated device again. Confirm that the module deployed from the cloud is running on your IoT Edge device. 

```cmd
docker ps
```

![View three modules on your device](./media/tutorial-simulate-device-windows/docker-ps2.png)

View the messages being sent from the tempSensor module to the cloud. 

```cmd
docker logs -f tempSensor
```

![View the data from your module](./media/tutorial-simulate-device-windows/docker-logs.png)

You can also view the telemetry the device is sending by using the [IoT Hub explorer tool][lnk-iothub-explorer]. 

## Next steps

In this tutorial, you created a new IoT Edge device and used the Azure IoT Edge cloud interface to deploy code onto the device. Now, you have a simulated device generating raw data about its environment. 

This tutorial is the prerequisite for all of the other IoT Edge tutorials. You can continue on to any of the other tutorials to learn how Azure IoT Edge can help you turn this data into business insights at the edge.

> [!div class="nextstepaction"]
> [Develop and deploy C# code as a module](tutorial-csharp-module.md)

<!-- Images -->
[2]: ./media/tutorial-install-iot-edge/install-edge-full.png
[3]: ./media/tutorial-install-iot-edge/create-iot-hub.png
[4]: ./media/tutorial-install-iot-edge/register-device.png
[5]: ./media/tutorial-install-iot-edge/start-runtime.png
[6]: ./media/tutorial-install-iot-edge/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md