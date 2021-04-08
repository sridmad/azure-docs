---
title: Azure Hybrid Benefit for Windows Server | Microsoft Docs
description: Learn how to maximize your Windows Software Assurance benefits to bring on-premises licenses to Azure
services: virtual-machines-windows
documentationcenter: ''
author: kmouss
manager: timlt
editor: ''

ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss

---
# Azure Hybrid Benefit for Windows Server
For customers with Software Assurance, Azure Hybrid Benefit for Windows Server allows you to use your on-premises Windows Server licenses and run Windows virtual machines on Azure at a reduced cost. You can use Azure Hybrid Benefit for Windows Server to deploy new virtual machines from any Azure supported platform Windows Server image or Windows custom images. This article goes over the steps on how to deploy new VMs with Azure Hybrid Benefit for Windows Server and how you can update existing running VMs. For more information about Azure Hybrid Benefit for Windows Server licensing and cost savings, see the [Azure Hybrid Benefit for Windows Server licensing page](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> The legacy '[HUB]' Windows Server images that were published for customers with Enterprise Agreement on Azure Marketplace has been retired as of 9/11/2017, use the standard Windows Server with the "Save Money" option on the portal for Azure Hybrid Benefit for Windows Server. For more information, please refer to this [article.](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)
>

> [!NOTE]
> Using Azure Hybrid Benefit for Windows Server with VMs that are charged for additional software such as SQL Server or any of the third-party marketplace images is being rolled out. If you get a 409 error such as: Changing property 'LicenseType' is not allowed; then you are trying to convert or deploy a new Windows Server VM that has additional software cost, which may not be supported in that region. Same if you try to look for the portal configuration option to do the conversion and you can't see it for that VM.
>


> [!NOTE]
> For classic VMs, only deploying new VM from on-prem custom images is supported. To take advantage of the capabilities supported in this article, you must first migrate classic VMs to Resource Manager model.
>


## Ways to use Azure Hybrid Benefit for Windows Server
There are few ways to use Windows virtual machines with the Azure Hybrid Benefit:

1. You can deploy VMs from one of the provided  [Windows Server images on the Azure Marketplace](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. You can  [upload a custom VM](#upload-a-windows-vhd) and [deploy using a Resource Manager template](#deploy-a-vm-via-resource-manager) or [Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. You can toggle and convert existing VM between running with Azure Hybrid Benefit or pay on-demand cost for Windows Server
4. You can also deploy a new virtual machine scale set with Azure Hybrid Benefit for Windows Server

> [!NOTE]
> Converting an existing virtual machine scale set to use Azure Hybrid Benefit for Windows Server isn't supported
>

## Deploy a VM from a Windows Server Marketplace Image
All Windows Server images that are available from the Azure Marketplace are enabled with Azure Hybrid Benefit for Windows Server. For example, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, and Windows Server 2008SP1 and more. You can use these images to deploy VMs directly from the Azure portal, Resource Manager templates, Azure PowerShell, or other SDKs.

You can deploy these images directly from the Azure portal. For use in Resource Manager templates and with Azure PowerShell, view the list of images as follows:

### Powershell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
You can follow the steps to [Create a Windows virtual machine with PowerShell](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) and pass LicenseType = "Windows_Server". This option allows you to use your existing Windows Server license on Azure.

### Portal
You can follow the steps to [Create a Windows virtual machine with the Azure portal](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal) and select the option to use your existing Windows Server license.

## Convert an existing VM using Azure Hybrid Benefit for Windows Server
If you have an existing VM that you would like to convert to take advantage of Azure Hybrid Benefit for Windows Server, you can update your VM's license type as follows:

### Convert to using Azure Hybrid Benefit for Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### Convert back to pay as you go
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### Portal
From portal VM blade, you can update the VM to use Azure Hybrid Benefit by selecting "Configuration" option and toggle the "Azure hybrid benefit" option

> [!NOTE]
> If you don't see the option to toggle "Azure hybrid benefit" under "Configuration", it is because the conversion isn't supported yet for the selected VM type (for example a VM built from custom image or from an image that has additional paid software like SQL Sever or Azure Marketplace third-party software).
>

## Upload a Windows Server VHD
To deploy a Windows Server VM in Azure, you first need to create a VHD that contains your base Windows build. This VHD must be appropriately prepared via Sysprep before you upload it to Azure. You can [read more about the VHD requirements and Sysprep process](upload-generalized-managed.md) and [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Back up the VM before running Sysprep. 

Once you have prepared your VHD, upload the VHD to your Azure Storage account as follows:

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server, and Dynamics can also utilize your Software Assurance licensing. You shall need to prepare the Windows Server image by installing your application components and providing license keys accordingly, then uploading the disk image to Azure. Review the appropriate documentation for running Sysprep with your application, such as [Considerations for Installing SQL Server using Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) or [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx).
>
>

You can also read more about [uploading the VHD to Azure process](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)

## Deploy a VM via Resource Manager Template
Within your Resource Manager templates, an additional parameter `licenseType` must be specified. You can read more about [authoring Azure Resource Manager templates](../../resource-group-authoring-templates.md). Once you have your VHD uploaded to Azure, edit you Resource Manager template to include the license type as part of the compute provider and deploy your template as normal:

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## Deploy a VM via PowerShell quickstart
When deploying your Windows Server VM via PowerShell, you have an additional parameter `-LicenseType`. Once you have your VHD uploaded to Azure, you create a VM using `New-AzureRmVM` and specify the licensing type as follows:

For Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

You can read a more descriptive guide on the different steps to [create a Windows VM using Resource Manager and PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## Verify your VM is utilizing the licensing benefit
Once you have deployed your VM through either PowerShell, Resource Manager template or portal, you can verify the license type with `Get-AzureRmVM` as follows:

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

The output is similar to the following example for Windows Server:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

This output contrasts with the following VM deployed without Azure Hybrid Benefit for Windows Server licensing:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## List all Azure Hybrid Benefit for Windows Server VMs in a subscription

To see and count all virtual machines deployed with Azure Hybrid Benefit for Windows Server, you can run the following command from your subscription:

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## Deploy a virtual machine scale set with Azure Hybrid Benefit for Windows Server
Within your virtual machine scale set Resource Manager templates, an additional parameter `licenseType` must be specified. You can read more about [authoring Azure Resource Manager templates](../../resource-group-authoring-templates.md). Edit your Resource Manager template to include the licenseType property as part of the scale set’s virtualMachineProfile and deploy your template as normal - see following example using 2016 Windows Server image:


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
You can also [Create and deploy a virtual machine scale set](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) and set the LicenseType property

## Next steps
Read more about [How to save money with the Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

Learn more about [Azure Hybrid Benefit for Windows Server licensing detailed guidance](https://docs.microsoft.com/en-us/windows-server/get-started/azure-hybrid-benefit)

Learn more about [Using Resource Manager templates](../../azure-resource-manager/resource-group-overview.md)

Learn more about [Azure Hybrid Benefit for Windows Server and Azure Site Recovery make migrating applications to Azure even more cost-effective](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)

Learn more about [Windows 10 on Azure with Multitenant Hosting Right](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)

Read more about [Frequently asked questions](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
