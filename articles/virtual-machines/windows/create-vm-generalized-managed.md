---
title: Create VM from a managed VM image in Azure | Microsoft Docs
description: Create a Windows virtual machine from a generalized managed VM image using Azure PowerShell, in the Resource Manager deployment model.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: cynthn

---
# Create a VM from a managed image

You can create multiple VMs from a managed VM image in Azure. A managed VM image contains the information necessary to create a VM, including the OS and data disks. The VHDs that make up the image, including both the OS disks and any data disks, are stored as managed disks. 


## Prerequisites

You need to have already [created a managed VM image](capture-image-resource.md) to use for creating the new VM. 

Make sure that you have the latest versions of the AzureRM.Compute and AzureRM.Network PowerShell modules. Open a PowerShell prompt as an Administrator and run the following command to install them.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
For more information, see [Azure PowerShell Versioning](/powershell/azure/overview).



## Collect information about the image

First we need to gather basic information about the image and create a variable for the image. This example uses a managed VM image named **myImage** that is in the **myResourceGroup** resource group in the **West Central US** location. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## Create a virtual network
Create the vNet and subnet of the [virtual network](../../virtual-network/virtual-networks-overview.md).

1. Create the subnet. This example creates a subnet named **mySubnet** with the address prefix of **10.0.0.0/24**.  
   
    ```azurepowershell-interactive
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Create the virtual network. This example creates a virtual network named **myVnet** with the address prefix of **10.0.0.0/16**.  
   
    ```azurepowershell-interactive
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## Create a public IP address and network interface

To enable communication with the virtual machine in the virtual network, you need a [public IP address](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) and a network interface.

1. Create a public IP address. This example creates a public IP address named **myPip**. 
   
    ```azurepowershell-interactive
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Create the NIC. This example creates a NIC named **myNic**. 
   
    ```azurepowershell-interactive
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## Create the network security group and an RDP rule

To be able to log in to your VM using RDP, you need to have a network security rule (NSG) that allows RDP access on port 3389. 

This example creates an NSG named **myNsg** that contains a rule called **myRdpRule** that allows RDP traffic over port 3389. For more information about NSGs, see [Opening ports to a VM in Azure using PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## Create a variable for the virtual network

Create a variable for the completed virtual network. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## Get the credentials for the VM

The following cmdlet will open a window where you will enter a new user name and password to use as the local administrator account for remotely accessing the VM. 

```azurepowershell-interactive
$cred = Get-Credential
```

## Set variables for the VM name, computer name and the size of the VM

1. Create variables for the VM name and computer name. This example sets the VM name as **myVM** and the computer name as **myComputer**.

    ```azurepowershell-interactive
    $vmName = "myVM"
	$computerName = "myComputer"
    ```
2. Set the size of the virtual machine. This example creates **Standard_DS1_v2** sized VM. See the [VM sizes](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) documentation for more information.

    ```azurepowershell-interactive
    $vmSize = "Standard_DS1_v2"
	```

3. Add the VM name and size to the VM configuration.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## Set the VM image as source image for the new VM

Set the source image using the ID of the managed VM image.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## Set the OS configuration and add the NIC.

Enter the storage type (PremiumLRS or StandardLRS) and the size of the OS disk. This example sets the account type to **PremiumLRS**, the disk size to **128 GB** and disk caching to **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## Create the VM

Create the new Vm using the configuration that we have built and stored in the **$vm** variable.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## Verify that the VM was created
When complete, you should see the newly created VM in the [Azure portal](https://portal.azure.com) under **Browse** > **Virtual machines**, or by using the following PowerShell commands:

```azurepowershell-interactive
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## Next steps
To manage your new virtual machine with Azure PowerShell, see [Create and manage Windows VMs with the Azure PowerShell module](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

