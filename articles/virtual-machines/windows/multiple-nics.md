﻿---
title: Create and manage Windows VMs in Azure that use multiple NICs | Microsoft Docs
description: Learn how to create and manage a Windows VM that has multiple NICs attached to it by using Azure PowerShell or Resource Manager templates.
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''

ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/26/2017
ms.author: iainfou

---
# Create and manage a Windows virtual machine that has multiple NICs
Virtual machines (VMs) in Azure can have multiple virtual network interface cards (NICs) attached to them. A common scenario is to have different subnets for front-end and back-end connectivity, or a network dedicated to a monitoring or backup solution. This article details how to create a VM that has multiple NICs attached to it. You also learn how to add or remove NICs from an existing VM. Different [VM sizes](sizes.md) support a varying number of NICs, so size your VM accordingly.

## Prerequisites
Make sure that you have the [latest Azure PowerShell version installed and configured](/powershell/azure/overview).

In the following examples, replace example parameter names with your own values. Example parameter names include *myResourceGroup*, *myVnet*, and *myVM*.


## Create a VM with multiple NICs
First, create a resource group. The following example creates a resource group named *myResourceGroup* in the *EastUs* location:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

### Create virtual network and subnets
A common scenario is for a virtual network to have two or more subnets. One subnet may be for front-end traffic, the other for back-end traffic. To connect to both subnets, you then use multiple NICs on your VM.

1. Define two virtual network subnets with [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). The following example defines the subnets for *mySubnetFrontEnd* and *mySubnetBackEnd*:

    ```powershell
    $mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
        -AddressPrefix "192.168.1.0/24"
    $mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
        -AddressPrefix "192.168.2.0/24"
    ```

2. Create your virtual network and subnets with [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). The following example creates a virtual network named *myVnet*:

    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
        -Location "EastUs" `
        -Name "myVnet" `
        -AddressPrefix "192.168.0.0/16" `
        -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
    ```


### Create multiple NICs
Create two NICs with [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). Attach one NIC to the front-end subnet and one NIC to the back-end subnet. The following example creates NICs named *myNic1* and *myNic2*:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic1" `
    -Location "EastUs" `
    -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Name "myNic2" `
    -Location "EastUs" `
    -SubnetId $backEnd.Id
```

Typically you also create a [network security group](../../virtual-network/virtual-networks-nsg.md) to filter network traffic to the VM and a [load balancer](../../load-balancer/load-balancer-overview.md) to distribute traffic across multiple VMs.

### Create the virtual machine
Now start to build your VM configuration. Each VM size has a limit for the total number of NICs that you can add to a VM. For more information, see [Windows VM sizes](sizes.md).

1. Set your VM credentials to the `$cred` variable as follows:

    ```powershell
    $cred = Get-Credential
    ```

2. Define your VM with [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). The following example defines a VM named *myVM* and uses a VM size that supports more than two NICs (*Standard_DS3_v2*):

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS3_v2"
    ```

3. Create the rest of your VM configuration with [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) and [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage). The following example creates a Windows Server 2016 VM:

    ```powershell
    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig `
        -Windows `
        -ComputerName "myVM" `
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig `
        -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" `
        -Skus "2016-Datacenter" `
        -Version "latest"
   ```

4. Attach the two NICs that you previously created with [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
    ```

5. Finally, create your VM with [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm):

    ```powershell
    New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "EastUs"
    ```

## Add a NIC to an existing VM
To add a virtual NIC to an existing VM, you deallocate the VM, add the virtual NIC, then start the VM. Different [VM sizes](sizes.md) support a varying number of NICs, so size your VM accordingly. If needed, you can [resize a VM](resize-vm.md).

1. Deallocate the VM with [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). The following example deallocates the VM named *myVM* in *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Get the existing configuration of the VM with [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). The following example gets information for the VM named *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. The following example creates a virtual NIC with [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) named *myNic3* that is attached to *mySubnetBackEnd*. The virtual NIC is then attached to the VM named *myVM* in *myResourceGroup* with [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

    ```powershell
    # Get info for the back end subnet
    $myVnet = Get-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName "myResourceGroup"
    $backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}

    # Create a virtual NIC
    $myNic3 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
        -Name "myNic3" `
        -Location "EastUs" `
        -SubnetId $backEnd.Id

    # Get the ID of the new virtual NIC and add to VM
    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "MyNic3").Id
    Add-AzureRmVMNetworkInterface -VM $vm -Id $nicId | Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```

    ### Primary virtual NICs
    One of the NICs on a multi-NIC VM needs to be primary. If one of the existing virtual NICs on the VM is already set as primary, you can skip this step. The following example assumes that two virtual NICs are now present on a VM and you wish to add the first NIC (`[0]`) as the primary:
        
    ```powershell
    # List existing NICs on the VM and find which one is primary
    $vm.NetworkProfile.NetworkInterfaces
    
    # Set NIC 0 to be primary
    $vm.NetworkProfile.NetworkInterfaces[0].Primary = $true
    $vm.NetworkProfile.NetworkInterfaces[1].Primary = $false
    
    # Update the VM state in Azure
    Update-AzureRmVM -VM $vm -ResourceGroupName "myResourceGroup"
    ```

4. Start the VM with [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

## Remove a NIC from an existing VM
To remove a virtual NIC from an existing VM, you deallocate the VM, remove the virtual NIC, then start the VM.

1. Deallocate the VM with [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). The following example deallocates the VM named *myVM* in *myResourceGroup*:

    ```powershell
    Stop-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

2. Get the existing configuration of the VM with [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm). The following example gets information for the VM named *myVM* in *myResourceGroup*:

    ```powershell
    $vm = Get-AzureRmVm -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```

3. Get information about the NIC remove with [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface). The following example gets information about *myNic3*:

    ```powershell
    # List existing NICs on the VM if you need to determine NIC name
    $vm.NetworkProfile.NetworkInterfaces

    $nicId = (Get-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" -Name "myNic3").Id   
    ```

4. Remove the NIC with [Remove-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface) and then update the VM with [Update-AzureRmVm](/powershell/module/azurerm.compute/update-azurermvm). The following example removes *myNic3* as obtained by `$nicId` in the preceding step:

    ```powershell
    Remove-AzureRmVMNetworkInterface -VM $vm -NetworkInterfaceIDs $nicId | `
        Update-AzureRmVm -ResourceGroupName "myResourceGroup"
    ```   

5. Start the VM with [Start-AzureRmVm](/powershell/module/azurerm.compute/start-azurermvm):

    ```powershell
    Start-AzureRmVM -Name "myVM" -ResourceGroupName "myResourceGroup"
    ```   

## Create multiple NICs with templates
Azure Resource Manager templates provide a way to create multiple instances of a resource during deployment, such as creating multiple NICs. Resource Manager templates use declarative JSON files to define your environment. For more information, see [overview of Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md). You can use *copy* to specify the number of instances to create:

```json
"copy": {
    "name": "multiplenics",
    "count": "[parameters('count')]"
}
```

For more information, see [creating multiple instances by using *copy*](../../resource-group-create-multiple.md). 

You can also use `copyIndex()` to append a number to a resource name. You can then create *myNic1*, *MyNic2* and so on. The following code shows an example of appending the index value:

```json
"name": "[concat('myNic', copyIndex())]", 
```

You can read a complete example of [creating multiple NICs by using Resource Manager templates](../../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Next steps
Review [Windows VM sizes](sizes.md) when you're trying to create a VM that has multiple NICs. Pay attention to the maximum number of NICs that each VM size supports. 


