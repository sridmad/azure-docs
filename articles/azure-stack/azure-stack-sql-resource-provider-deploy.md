---
title: Using SQL databases on Azure Stack | Microsoft Docs
description: Learn how you can deploy SQL databases as a service on Azure Stack and the quick steps to deploy the SQL Server resource provider adapter
services: azure-stack
documentationCenter: ''
author: JeffGoldner
manager: bradleyb
editor: ''

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo

---

# Use SQL databases on Microsoft Azure Stack

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

Use the SQL Server resource provider adapter to expose SQL databases as a service of [Azure Stack](azure-stack-poc.md). After you install the resource provider and connect it to one or more SQL Server instances, you and your users can create:
- Databases for cloud-native apps
- Websites that are based on SQL
- Workloads that are based on SQL
You don't have to provision a virtual machine (VM) that hosts SQL Server each time.

The resource provider does not support all the database management capabilities of [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). For example, elastic database pools and the ability to dial database performance up and down automatically aren't available. However, the resource provider does support similar create, read, update, and delete (CRUD) operations. The API is not compatible with SQL DB.

## SQL Resource Provider Adapter architecture
The resource provider is made up of three components:

- **The SQL resource provider adapter VM**, which is a Windows virtual machine running the provider services.
- **The resource provider itself**, which processes provisioning requests and exposes database resources.
- **Servers that host SQL Server**, which provide capacity for databases, called Hosting Servers.

You must create one (or more) SQL servers and/or provide access to external SQL instances.

## Deploy the resource provider

1. If you have not already done so, register your development kit and download the Windows Server 2016 Datacenter Core image downloadable through Marketplace Management. You must use a Windows Server 2016 Core image. You can also use a script to create a [Windows Server 2016 image](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) - be sure to select the core option. The .NET 3.5 runtime is no longer required.

2. Sign in to a host that can access the Privileged Endpoint VM.

    a. On Azure Stack Development Kit (ASDK) installations, sign in to the physical host.

    b. On multi-node systems, the host must be a system that can access the Privileged Endpoint.

3. [Download the SQL resource provider binaries file](https://aka.ms/azurestacksqlrp) and execute the self-extractor to extract the contents to a temporary directory.

    > [!NOTE]
    > If you running on an Azure Stack build 20170928.3 or earlier, [Download this version](https://aka.ms/azurestacksqlrp1709).

4. The Azure Stack root certificate is retrieved from the Privileged Endpoint. For ASDK, a self-signed certificate is created as part of this process. For multi-node, you must provide an appropriate certificate.

    If you need to provide your own certificate, you need the following certificate:

    A wildcard certificate for \*.dbadapter.\<region\>.\<external fqdn\>. This certificate must be trusted, such as would be issued by a certificate authority. That is, the chain of trust must exist without requiring intermediate certificates. A single site certificate can be used with the explicit VM name [sqladapter] used during install.


5. Open a **new** elevated (administrative) PowerShell console and change to the directory where you extracted the files. Use a new window to avoid problems that may arise from incorrect PowerShell modules already loaded on the system.

6. [Install Azure PowerShell version 1.2.11](azure-stack-powershell-install.md).

7. Run the DeploySqlProvider.ps1 script with the parameters listed below.

The script performs these steps:

- Upload the certificates and other artifacts to a storage account on your Azure Stack.
- Publish gallery packages so that you can deploy SQL databases through the gallery.
- Publish a gallery package for deploying Hosting Servers
- Deploy a VM using the Windows Server 2016 image created in step 1 and install the resource provider.
- Register a local DNS record that maps to your resource provider VM.
- Register your resource provider with the local Azure Resource Manager (user and admin).

> [!NOTE]
> If the installation takes more than 90 minutes, it may fail and you see a failure message on the screen and in the log file, but the deployment is retried from the failing step. Systems that do not meet the recommended memory and vCPU specifications may not be able to deploy the SQL RP.
>

Here's an example you can run from the PowerShell prompt (but change the account information and passwords as needed):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### DeploySqlProvider.ps1 parameters
You can specify these parameters in the command line. If you do not, or any parameter validation fails, you are prompted to provide the required ones.

| Parameter Name | Description | Comment or Default Value |
| --- | --- | --- |
| **CloudAdminCredential** | The credential for the cloud administrator, necessary for accessing the Privileged Endpoint. | _required_ |
| **AzCredential** | Provide the credentials for the Azure Stack Service Admin account. Use the same credentials as you used for deploying Azure Stack). | _required_ |
| **VMLocalCredential** | Define the credentials for the local administrator account of the SQL resource provider VM. | _required_ |
| **PrivilegedEndpoint** | Provide the IP address or DNS Name of the Privleged Endpoint. |  _required_ |
| **DependencyFilesLocalPath** | Your certificate PFX file must be placed in this directory as well. | _optional_ (_mandatory_ for multi-node) |
| **DefaultSSLCertificatePassword** | The password for the .pfx certificate | _required_ |
| **MaxRetryCount** | Define how many times you want to retry each operation if there is a failure.| 2 |
| **RetryDuration** | Define the timeout between retries, in seconds. | 120 |
| **Uninstall** | Remove the resource provider and all associated resources (see notes below) | No |
| **DebugMode** | Prevents automatic cleanup on failure | No |


## Verify the deployment using the Azure Stack portal

> [!NOTE]
>  After the installation script completes, you will need to refresh the portal to see the admin blade.


1. Sign in to the admin portal as the service administrator.

2. Verify that the deployment succeeded. Browse for **Resource Groups** &gt;, click on the **system.\<location\>.sqladapter** resource group and verify that all four deployments succeeded.

      ![Verify Deployment of the SQL RP](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## Remove the SQL Resource Provider Adapter

In order to remove the resource provider, it is essential to first remove any dependencies.

1. Ensure you have the original deployment package that you downloaded for this version of the SQL Resource Provider Adapter.

2. All user databases must be deleted from the resource provider (this doesn't delete the data). This should be performed by the users themselves.

3. Administrator must delete the hosting servers from the SQL Resource Provider Adapter

4. Administrator must delete any plans that reference the SQL Resource Provider Adapter.

5. Administrator must delete any SKUs and quotas associated to the SQL Resource Provider Adapter.

6. Rerun the deployment script with the -Uninstall parameter, Azure Resource Manager endpoints, DirectoryTenantID, and credentials for the service administrator account.


## Next steps

[Add Hosting Servers](azure-stack-sql-resource-provider-hosting-servers.md) and [Create databases](azure-stack-sql-resource-provider-databases.md).

Try other [PaaS services](azure-stack-tools-paas-services.md) like the [MySQL Server resource provider](azure-stack-mysql-resource-provider-deploy.md) and the [App Services resource provider](azure-stack-app-service-overview.md).
