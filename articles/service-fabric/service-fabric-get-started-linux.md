---
title: Set up your development environment on Linux | Microsoft Docs
description: Install the runtime and SDK and create a local development cluster on Linux. After completing this setup, you will be ready to build applications.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''

ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/19/2017
ms.author: subramar

---
# Prepare your development environment on Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

To deploy and run [Azure Service Fabric applications](service-fabric-application-model.md) on a Linux development machine, install the runtime and common SDK. Once Service fabric and SDK are installed, optional SDKs for Java and .NET Core can also be installed.

## Prerequisites

The following operating system versions are supported for development:

* Ubuntu 16.04 (`Xenial Xerus`)

In addition Docker CE is required on the development machine.

* Docker CE
   
    Install Docker CE for amd64 using instructions at  https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/

## Update APT sources to install Service Fabric and `dotnet`
To install the SDK and the associated runtime package via the apt-get command-line tool, first update the Advanced Packaging Tool (APT) sources.

1. Open a terminal.
2. Add the Service Fabric and `dotnet` repos to APT sources list.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

3. Add the new Gnu Privacy Guard (GnuPG, or GPG) key to  APT keyring.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

4. Refresh your package lists based on the newly added repositories.

    ```bash
    sudo apt-get update
    ```

## Install Service Fabric and SDK for local cluster setup

5. Install Service Fabric and SDK package, confirm the installation, and agree to the license agreement.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   The following commands automate accepting the license for Service Fabric packages:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   ```

## Set up and start local cluster
  Once installation is complete start local cluster and verify it is running.

  1. Run the cluster setup script.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Open a web browser and go to [Service Fabric Explorer](http://localhost:19080/Explorer). Once cluseter is started browser should display the Service Fabric Explorer dashboard.

      ![Service Fabric Explorer on Linux][sfx-linux]

  At this point, pre-built Service Fabric application packages or new ones based on guest containers or guest executables can be deployed to the local cluster. To build new services by using the Java or .NET Core SDKs, follow the optional setup steps that are provided in subsequent sections.


  > [!NOTE]
  > The preview supports one-box and Azure Linux multi-machine clusters. Standalone clusters aren't yet supported in Linux.
  >

## Set up the Service Fabric CLI

The [Service Fabric CLI](service-fabric-cli.md) has commands for interacting with Service Fabric entities,
including clusters and applications.
Please follow the instructions at [Service Fabric CLI](service-fabric-cli.md) to install the CLI.


## Install generators for containers and guest-executables
Service Fabric provides Yoeman generators for creating Service Fabric applications. Please follow the steps below to install Service Fabric yeoman template generator.

1. Install nodejs and NPM

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Install [Yeoman](http://yeoman.io/) template generator using NPM

  ```bash
  sudo npm install -g yo
  ```
3. Install the Service Fabric Yeo container generator and guest execuatble generator using NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

After you have installed the generators, you should be able to create apps with guest executable or container services by running `yo azuresfguest` or `yo azuresfcontainer` respectively.

## Set up .NET Core 2.0 development

Install the [.NET Core 2.0 SDK for Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) to start [creating C# Service Fabric applications](service-fabric-create-your-first-linux-application-with-csharp.md). Packages for .NET Core 2.0 Service Fabric applications are hosted on NuGet.org, currently in preview.

## Set up Java development

To build Service Fabric services using Java, install JDK 1.8 and Gradle to run build tasks. The following snippet installs Open JDK 1.8 along with Gradle. The Service Fabric Java libraries are pulled from Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## Install the Eclipse Neon plug-in (optional)

You can install the Eclipse plug-in for Service Fabric from within the Eclipse IDE for Java Developers. You can use Eclipse to create Service Fabric guest executable applications and container applications in addition to Service Fabric Java applications.

1. In Eclipse, ensure that you have latest Eclipse Neon and the latest Buildship version (1.0.17 or later) installed. You can check the versions of installed components by selecting **Help** > **Installation Details**. You can update Buildship by using the instructions at [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update].

2. To install the Service Fabric plug-in, select **Help** > **Install New Software**.

3. In the **Work with** box, type **http://dl.microsoft.com/eclipse**.

4. Click **Add**.

    ![The Available Software page][sf-eclipse-plugin]

5. Select the **ServiceFabric** plug-in, and then click **Next**.

6. Complete the installation steps, and then accept the end-user license agreement.

If you already have the Service Fabric Eclipse plug-in installed, make sure that you have the latest version. You can check by selecting **Help** > **Installation Details** and then searching for Service Fabric in the list of installed plug-ins. If a newer version is available, select **Update**.

For more information, see [Service Fabric plug-in for Eclipse Java application development](service-fabric-get-started-eclipse.md).

## Update the SDK and runtime

To update to the latest version of the SDK and runtime, run the following commands:

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
To update the Java SDK binaries from Maven, you need to update the version details of the corresponding binary in the ``build.gradle`` file to point to the latest version. To know exactly where you need to update the version, you can refer to any ``build.gradle`` file in Service Fabric getting-started samples [here](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Updating the packages might cause your local development cluster to stop running. Restart your local cluster after an upgrade by following the instructions on this page.

## Remove the SDK
To remove the Service Fabric SDKs, run the following:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## Next steps

* Create and deploy your first Service Fabric application on Linux
    * [Java application using Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
    * [Java application using Service Fabric Plugin for Eclipse](service-fabric-get-started-eclipse.md)
    * [CSharp application](service-fabric-create-your-first-linux-application-with-csharp.md)
    * [Container application](service-fabric-get-started-containers-linux.md)
* [Prepare your development environment on OSX](service-fabric-get-started-mac.md)
* [Prepare a Linux development environment on Windows](service-fabric-local-linux-cluster-windows.md)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md)
* [Service Fabric Windows/Linux differences](service-fabric-linux-windows-differences.md)
* [Get started with Service Fabric CLI](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
