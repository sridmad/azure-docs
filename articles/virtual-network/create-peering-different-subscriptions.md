---
title: Create an Azure virtual network peering - Resource Manager - different subscriptions | Microsoft Docs
description: Learn how to create a virtual network peering between virtual networks created through Resource Manager that exist in different Azure subscriptions.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin

---
# Create a virtual network peering - Resource Manager, different subscriptions 

In this tutorial, you learn to create a virtual network peering between virtual networks created through Resource Manager. The virtual networks exist in different subscriptions. Peering two virtual networks enables resources in different virtual networks to communicate with each other with the same bandwidth and latency as though the resources were in the same virtual network. Learn more about [Virtual network peering](virtual-network-peering-overview.md). 

The steps to create a virtual network peering are different, depending on whether the virtual networks are in the same, or different, subscriptions, and which [Azure deployment model](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) the virtual networks are created through. Learn how to create a virtual network peering in other scenarios by clicking the scenario from the following table:

|Azure deployment model  | Azure subscription  |
|--------- |---------|
|[Both Resource Manager](virtual-network-create-peering.md) |Same|
|[One Resource Manager, one classic](create-peering-different-deployment-models.md) |Same|
|[One Resource Manager, one classic](create-peering-different-deployment-models-subscriptions.md) |Different|

A virtual network peering cannot be created between two virtual networks deployed through the classic deployment model. If you need to connect virtual networks that were both created through the classic deployment model, you can use an Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) to connect the virtual networks. 

This tutorial peers virtual networks in the same region. The ability to peer virtual networks in different regions is currently in preview. Complete the steps in [Register for global virtual network peering](#register) before attempting to peer virtual networks in different regions, or the peering fails. The ability to connect virtual networks in different regions with an Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) is generally available, and does not require registration.

You can use the [Azure portal](#portal), the Azure [command-line interface](#cli) (CLI), Azure [PowerShell](#powershell), or an [Azure Resource Manager template](#template) to create a virtual network peering. Click any of the previous tool links to go directly to the steps for creating a virtual network peering using your tool of choice.

## <a name="portal"></a>Create peering - Azure portal

This tutorial uses different accounts for each subscription. If you're using an account that has permissions to both subscriptions, you can use the same account for all steps, skip the steps for logging out of the portal, and skip the steps for assigning another user permissions to the virtual networks.

1. Log in to the [Azure portal](https://portal.azure.com) as UserA. The account you log in with must have the necessary permissions to create a virtual network peering. See the [Permissions](#permissions) section of this article for details.
2. Click **+ New**, click **Networking**, then click **Virtual network**.
3. In the **Create virtual network** blade, enter, or select values for the following settings, then click **Create**:
    - **Name**: *myVnetA*
    - **Address space**: *10.0.0.0/16*
    - **Subnet name**: *default*
    - **Subnet address range**: *10.0.0.0/24*
    - **Subscription**: Select subscription A.
    - **Resource group**: Select **Create new** and enter *myResourceGroupA*
    - **Location**: *East US*
4. In the **Search resources** box at the top of the portal, type *myVnetA*. Click **myVnetA** when it appears in the search results. A blade appears for the **myVnetA** virtual network.
5. In the **myVnetA** blade that appears, click **Access control (IAM)** from the vertical list of options on the left side of the blade.
6. In the **myVnetA - Access control (IAM)** blade that appears, click **+ Add**.
7. In the **Add permissions** blade that appears, select **Network contributor** in the **Role** box.
8. In the **Select** box, select a UserB, or type UserB's email address to search for it. The list of users shown is from the same Azure Active Directory tenant as the virtual network you're setting up the peering for.
9. Click **Save**.
10. In the **myVnetA - Access control (IAM)** blade, click **Properties** from the vertical list of options on the left side of the blade. Copy the **RESOURCE ID**, which is used in a later step. The resource ID is similar to the following example: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Log out of the portal as UserA, then log in as UserB.
12. Complete steps 2-3, entering or selecting the following values in step 3:

    - **Name**: *myVnetB*
    - **Address space**: *10.1.0.0/16*
    - **Subnet name**: *default*
    - **Subnet address range**: *10.1.0.0/24*
    - **Subscription**: Select subscription B.
    - **Resource group**: Select **Create new** and enter *myResourceGroupB*
    - **Location**: *East US*

13. In the **Search resources** box at the top of the portal, type *myVnetB*. Click **myVnetB** when it appears in the search results. A blade appears for the **myVnetB** virtual network.
14. In the **myVnetB** blade that appears, click **Properties** from the vertical list of options on the left side of the blade. Copy the **RESOURCE ID**, which is used in a later step. The resource ID is similar to the following example: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Click **Access control (IAM)** in the **myVnetB** blade and then complete steps 5-10 for myVnetB, entering **UserA** in step 8.
16. Log out of the portal as UserB and log in as UserA.
17. In the **Search resources** box at the top of the portal, type *myVnetA*. Click **myVnetA** when it appears in the search results. A blade appears for the **myVnet** virtual network.
18. Click **myVnetA**.
19. In the **myVnetA** blade that appears, click **Peerings** from the vertical list of options on the left side of the blade.
20. In the **myVnetA - Peerings** blade that appeared, click **+ Add**
21. In the **Add peering** blade that appears, enter, or select the following options, then click **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Virtual network deployment model**:  Select **Resource Manager**.
     - **I know my resource ID**: Check this box.
     - **Resource ID**: Enter the resource ID from step 14.
     - **Allow virtual network access:** Ensure that **Enabled** is selected.
    No other settings are used in this tutorial. To learn about all peering settings, read [Manage virtual network peerings](virtual-network-manage-peering.md#create-a-peering).
22. After clicking **OK** in the previous step, the **Add peering** blade closes and you see the **myVnetA - Peerings** blade again. After a few seconds, the peering you created appears in the blade. **Initiated** is listed in the **PEERING STATUS** column for the **myVnetAToMyVnetB** peering you created. You've peered myVnetA to myVnetB, but now you must peer myVnetB to myVnetA. The peering must be created in both directions to enable resources in the virtual networks to communicate with each other.
23. Log out of the portal as UserA and log in as UserB.
24. Complete steps 17-21 again for myVnetB. In step 21, name the peering *myVnetBToMyVnetA*, select *myVnetA* for **Virtual network**, and enter the ID from step 10 in the **Resource ID** box.
25. A few seconds after clicking **OK** to create the peering for myVnetB, the **myVnetBToMyVnetA** peering you just created is listed with **Connected** in the **PEERING STATUS** column.
26. Log out of the portal as UserB and log in as UserA.
27. Complete steps 17-19 again. The **PEERING STATUS** for the **myVnetAToVNetB** peering is now also **Connected**. The peering is successfully established after you see **Connected** in the **PEERING STATUS** column for both virtual networks in the peering. Any Azure resources you create in either virtual network are now able to communicate with each other through their IP addresses. If you're using default Azure name resolution for the virtual networks, the resources in the virtual networks are not able to resolve names across the virtual networks. If you want to resolve names across virtual networks in a peering, you must create your own DNS server. Learn how to set up [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
28. **Optional**: Though creating virtual machines is not covered in this tutorial, you can create a virtual machine in each virtual network and connect from one virtual machine to the other, to validate connectivity.
29. **Optional**: To delete the resources that you create in this tutorial, complete the steps in the [Delete resources](#delete-portal) section of this article.

## <a name="cli"></a>Create peering - Azure CLI

This tutorial uses different accounts for each subscription. If you're using an account that has permissions to both subscriptions, you can use the same account for all steps, skip the steps for logging out of Azure, and remove the lines of script that create user role assignments. Replace UserA@azure.com and UserB@azure.com in all of the following scripts with the usernames you're using for UserA and UserB.

The following script:

- Requires the Azure CLI version 2.0.4 or later. To find the version, run `az --version`. If you need to upgrade, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Works in a Bash shell. For options on running Azure CLI scripts on Windows client, see [Running the Azure CLI in Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Instead of installing the CLI and its dependencies, you can use the Azure Cloud Shell. The Azure Cloud Shell is a free Bash shell that you can run directly within the Azure portal. It has the Azure CLI preinstalled and configured to use with your account. Click the **Try it** button in the script that follows, which invokes a Cloud Shell that you can log in to your Azure account with. 

1. Open a CLI session and log in to Azure as UserA using the `azure login` command. The account you log in with must have the necessary permissions to create a virtual network peering. See the [Permissions](#permissions) section of this article for details.
2. Copy the following script to a text editor on your PC, replace `<SubscriptionA-Id>` with the ID of SubscriptionA, then copy the modified script, paste it in your CLI session, and press `Enter`. If you don't know your subscription Id, enter the 'az account show` command. The value for **id** in the output is your subscription Id.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```
    
3. Log out of Azure as UserA using the `az logout` command, then log in to Azure as UserB. The account you log in with must have the necessary permissions to create a virtual network peering. See the [Permissions](#permissions) section of this article for details.
4. Create myVnetB. Copy the script contents in step 2 to a text editor on your PC. Replace `<SubscriptionA-Id>` with the ID of SubscriptionB. Change 10.0.0.0/16 to 10.1.0.0/16, change all As to B, and all Bs to A. Copy the modified script, paste it in to your CLI session, and press `Enter`. 
5. Log out of Azure as UserB and log in to Azure as UserA.
6. Create a virtual network peering from myVnetA to myVnetB. Copy the following script contents to a text editor on your PC. Replace `<SubscriptionB-Id>` with the ID of SubscriptionB. To execute the script, copy the modified script, paste it into your CLI session, and press Enter.
 
    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)
    
        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. View the peering state of myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    The state is **Initiated**. It changes to **Connected** once you create the peering to myVnetA from myVnetB.

8. Log out UserA from Azure and log in to Azure as UserB.
9. Create the peering from myVnetB to myVnetA. Copy the script contents in step 6 to a text editor on your PC. Replace `<SubscriptionB-Id>` with the ID for SubscriptionA and change all As to B and all Bs to A. Once you've made the changes, copy the modified script, paste it into your CLI session, and press `Enter`.
10. View the peering state of myVnetB. Copy the script contents in step 7 to a text editor on your PC. Change A to B for the resource group and virtual network names, copy the script, paste the modified script in to your CLI session, and then press `Enter`. The peering state is **Connected**. The peering state of myVnetA changes to **Connected** after you've created the peering from myVnetB to myVnetA. You can log UserA back in to Azure and complete step 7 again to verify the peering state of myVnetA. 

    > [!NOTE]
    > The peering is not established until the peering state is **Connected** for both virtual networks.

11. **Optional**: Though creating virtual machines is not covered in this tutorial, you can create a virtual machine in each virtual network and connect from one virtual machine to the other, to validate connectivity.
12. **Optional**: To delete the resources that you create in this tutorial, complete the steps in [Delete resources](#delete-cli) in this article.

Any Azure resources you create in either virtual network are now able to communicate with each other through their IP addresses. If you're using default Azure name resolution for the virtual networks, the resources in the virtual networks are not able to resolve names across the virtual networks. If you want to resolve names across virtual networks in a peering, you must create your own DNS server. Learn how to set up [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).
 
## <a name="powershell"></a>Create peering - PowerShell

This tutorial uses different accounts for each subscription. If you're using an account that has permissions to both subscriptions, you can use the same account for all steps, skip the steps for logging out of Azure, and remove the lines of script that create user role assignments. Replace UserA@azure.com and UserB@azure.com in all of the following scripts with the usernames you're using for UserA and UserB.

1. Install the latest version of the PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) module. If you're new to Azure PowerShell, see [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Start a PowerShell session.
3. In PowerShell, log in to Azure as UserA by entering the `login-azurermaccount` command. The account you log in with must have the necessary permissions to create a virtual network peering. See the [Permissions](#permissions) section of this article for details.
4. Create a resource group and virtual network A. Copy the following script to a text editor on your PC. Replace `<SubscriptionA-Id>` with the ID of SubscriptionA. If you don't know your subscription Id, enter the `Get-AzureRmSubscription` command to view it. The value for **Id** in the returned output is your subscription ID. To execute the script, copy the modified script, paste it in to PowerShell, and then press `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
   	```

5. Log out UserA from Azure and log in UserB. The account you log in with must have the necessary permissions to create a virtual network peering. See the [Permissions](#permissions) section of this article for details.
6. Copy the script contents in step 4 to a text editor on your PC. Replace `<SubscriptionA-Id>` with the ID for subscription B. Change 10.0.0.0/16 to 10.1.0.0/16. Change all As to B and all Bs to A. To execute the script, copy the modified script, paste into PowerShell, and then press `Enter`.
7. Log out UserB from Azure and log in UserA.
8. Create the peering from myVnetA to myVnetB. Copy the following script to a text editor on your PC. Replace `<SubscriptionB-Id>` with the ID of subscription B. To execute the script, copy the modified script, paste in to PowerShell, and then press `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. View the peering state of myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    The state is **Initiated**. It changes to **Connected** once you set up the peering to myVnetA from myVnetB.

10. Log out UserA from Azure and log in UserB.
11. Create the peering from myVnetB to myVnetA. Copy the script contents in step 8 to a text editor on your PC. Replace `<SubscriptionB-Id>` with the ID of subscription A and change all As to B and all Bs to A. To execute the script, copy the modified script, paste it in to PowerShell, and then press `Enter`.
12. View the peering state of myVnetB. Copy the script contents in step 9 to a text editor on your PC. Change A to B for the resource group and virtual network names. To execute the script, paste the modified script into PowerShell, and then press `Enter`. The state is **Connected**. The peering state of **myVnetA** changes to **Connected** after you've created the peering from **myVnetB** to **myVnetA**. You can log UserA back in to Azure and complete step 9 again to verify the peering state of myVnetA. 

    > [!NOTE]
    > The peering is not established until the peering state is **Connected** for both virtual networks.

    Any Azure resources you create in either virtual network are now able to communicate with each other through their IP addresses. If you're using default Azure name resolution for the virtual networks, the resources in the virtual networks are not able to resolve names across the virtual networks. If you want to resolve names across virtual networks in a peering, you must create your own DNS server. Learn how to set up [Name resolution using your own DNS server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

13. **Optional**: Though creating virtual machines is not covered in this tutorial, you can create a virtual machine in each virtual network and connect from one virtual machine to the other, to validate connectivity.
14. **Optional**: To delete the resources that you create in this tutorial, complete the steps in [Delete resources](#delete-powershell) in this article.

## <a name="template"></a>Create peering - Resource Manager template

1. To create a virtual network and assign the appropriate [permissions](#permissions) to the account in each subscription, complete the steps in the [Portal](#portal), [Azure CLI](#cli), or [PowerShell](#powershell) sections of this article.
2. Save the text that follows to a file on your local computer. Replace `<subscription ID>` with UserA's subscription ID. You might save the file as vnetpeeringA.json, for example.

	```json
	{
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
	"resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
 	}
	```

3. Log in to Azure as UserA and deploy the template using the [portal](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), or the [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Specify the file name you saved the example json text in step 2 to.
4. Copy the example json from step 2 to a file on your computer and make changes to the lines that begin with:
	- **name**: Change *myVnetA/myVnetAToMyVnetB* to *myVnetB/myVnetBToMyVnetA*.
	- **id**: Replace `<subscription ID>` with UserB's subscription ID and change *myVnetB* to *myVnetA*.
5. Complete step 3 again, logged in to Azure as UserB.
6. **Optional**: Though creating virtual machines is not covered in this tutorial, you can create a virtual machine in each virtual network and connect from one virtual machine to the other, to validate connectivity.
7. **Optional**: To delete the resources that you create in this tutorial, complete the steps in the [Delete resources](#delete) section of this article, using either the Azure portal, PowerShell, or the Azure CLI.

## <a name="permissions"></a>Permissions

The accounts you use to create a virtual network peering must have the necessary role or permissions. For example, if you were peering two virtual networks named **myVnetA** and **myVnetB**, your account must be assigned the following minimum role or permissions for each virtual network:
    
|Virtual network|Role|Permissions|
|---|---|---|
|myVnetA|[Network Contributor](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write|
|myVnetB|[Network Contributor](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)|Microsoft.Network/virtualNetworks/peer|

Learn more about [built-in roles](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) and assigning specific permissions to [custom roles](../active-directory/role-based-access-control-custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Resource Manager only).

## <a name="delete"></a>Delete resources
When you've finished this tutorial, you might want to delete the resources you created in the tutorial, so you don't incur usage charges. Deleting a resource group also deletes all resources that are in the resource group.

### <a name="delete-portal"></a>Azure portal

1. Log in to the Azure portal as UserA.
2. In the portal search box, enter **myResourceGroupA**. In the search results, click **myResourceGroupA**.
3. On the **myResourceGroupA** blade, click the **Delete** icon.
4. To confirm the deletion, in the **TYPE THE RESOURCE GROUP NAME** box, enter **myResourceGroupA**, and then click **Delete**.
5. Log out of the portal as UserA and log in as UserB.
6. Complete steps 2-4 for myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Log in to Azure as UserA and execute the following command:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Log out of Azure as UserA and log in as UserB.
3. Execute the following command:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Log in to Azure as UserA and execute the following command:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Log out of Azure as UserA and log in as UserB.
3. Execute the following command:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="register"></a>Register for the global virtual network peering preview

The ability to peer virtual networks in different regions is currently in preview. The capability is available in a limited set of regions (initially, US West Central, Canada Central, and US West 2). Virtual network peerings created between virtual networks in different regions may not have the same level of availability and reliability as a peering between virtual networks in the same region. For the most up-to-date notifications on availability and status of this feature, check the [Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network) page.

To peer virtual networks across regions, you must first register for the preview, by completing the following steps (within the subscription each virtual network you want to peer is in) using Azure PowerShell or the Azure CLI:

### PowerShell

1. Install the latest version of the PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) module. If you're new to Azure PowerShell, see [Azure PowerShell overview](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Start a PowerShell session and log in to Azure using the `Login-AzureRmAccount` command.
3. Register the subscription that each virtual network you want to peer is in for the preview by entering the following commands:

    ```powershell
    Register-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    
    Register-AzureRmResourceProvider `
      -ProviderNamespace Microsoft.Network
    ```
4. Confirm that you are registered for the preview by entering the following command:

    ```powershell    
    Get-AzureRmProviderFeature `
      -FeatureName AllowGlobalVnetPeering `
      -ProviderNamespace Microsoft.Network
    ```

    Do not complete the steps in the Portal, Azure CLI, PowerShell, or Resource Manager template sections of this article until the **RegistrationState** output you receive after entering the previous command is **Registered** for both subscriptions.

### Azure CLI

1. [Install and configure the Azure CLI](/cli/azure/install-azure-cli?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).
2. Ensure you are using version 2.0.18 or higher of the Azure CLI by entering the `az --version` command. If you are not, install the most recent version.
3. Log in to Azure with the `az login` command.
4. Register for the preview by entering the following commands:

   ```azurecli-interactive
   az feature register --name AllowGlobalVnetPeering --namespace Microsoft.Network
   az provider register --name Microsoft.Network
   ```

5. Confirm that you are registered for the preview by entering the following command:

    ```azurecli-interactive
    az feature show --name AllowGlobalVnetPeering --namespace Microsoft.Network
    ```

    Do not complete the steps in the Portal, Azure CLI, PowerShell, or Resource Manager template sections of this article until the **RegistrationState** output you receive after entering the previous command is **Registered** for both subscriptions.

## Next steps

- Thoroughly familiarize yourself with important [virtual network peering constraints and behaviors](virtual-network-manage-peering.md#requirements-and-constraints) before creating a virtual network peering for production use.
- Learn about all [virtual network peering settings](virtual-network-manage-peering.md#create-a-peering).
- Learn how to [create a hub and spoke network topology](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) with virtual network peering.
