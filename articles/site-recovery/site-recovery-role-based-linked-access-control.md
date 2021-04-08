---
title: Using Role-Based Access Control to manage Azure Site Recovery | Microsoft Docs
description: This article describes how to apply and use Role-Based Access Control (RBAC) to manage your Azure Site Recovery deployments
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
editor: ''

ms.assetid: ''
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: manayar

---
# Use Role-Based Access Control to manage Azure Site Recovery deployments

Azure Role-Based Access Control (RBAC) enables fine-grained access management for Azure. Using RBAC, you can segregate responsibilities within your team and grant only specific access permissions to users as needed to perform specific jobs.

Azure Site Recovery provides 3 built-in roles to control Site Recovery management operations. Learn more on [Azure RBAC built-in roles](../active-directory/role-based-access-built-in-roles.md)

* [Site Recovery Contributor](../active-directory/role-based-access-built-in-roles.md#site-recovery-contributor) - This role has all permissions required to manage Azure Site Recovery operations in a Recovery Services vault. A user with this role, however, can't create or delete a Recovery Services vault or assign access rights to other users. This role is best suited for disaster recovery administrators who can enable and manage disaster recovery for applications or entire organizations, as the case may be.
* [Site Recovery Operator](../active-directory/role-based-access-built-in-roles.md#site-recovery-operator) - This role has permissions to execute and manager Failover and Failback operations. A user with this role can't enable or disable replication, create or delete vaults, register new infrastructure or assign access rights to other users. This role is best suited for a disaster recovery operator who can failover virtual machines or applications when instructed by application owners and IT administrators in an actual or simulated disaster situation such as a DR drill. Post resolution of the disaster, the DR operator can re-protect and failback the virtual machines.
* [Site Recovery Reader](../active-directory/role-based-access-built-in-roles.md#site-recovery-reader) - This role has permissions to view all Site Recovery management operations. This role is best suited for an IT monitoring executive who can monitor the current state of protection and raise support tickets if required.

If you're looking to define your own roles for even more control, see how to [build Custom roles](../active-directory/role-based-access-control-custom-roles.md) in Azure.

## Permissions required to Enable Replication for new Virtual Machines
When a new Virtual Machine is replicated to Azure using Azure Site Recovery, the associated user's access levels are validated to ensure that the user has the required permissions to use the Azure resources provided to Site Recovery.

To enable replication for a new virtual machine, a user must have:
* Permission to create a virtual machine in the selected resource group
* Permission to create a virtual machine in the selected virtual network
* Permission to write to the selected Storage account

A user needs the following permissions to complete replication of a new virtual machine.

> [!IMPORTANT]
>Ensure that relevant permissions are added per the deployment model (Resource Manager/ Classic) used for resource deployment.

| **Resource Type** | **Deployment Model** | **Permission** |
| --- | --- | --- |
| Compute | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Classic | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Network | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Classic | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Classic | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Resource Group | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Consider using the 'Virtual Machine Contributor' and 'Classic Virtual Machine Contributor' [built-in roles](../active-directory/role-based-access-built-in-roles.md) for Resource Manager and Classic deployment models respectively.

## Next steps
* [Role-Based Access Control](../active-directory/role-based-access-control-configure.md): Get started with RBAC in the Azure portal.
* Learn how to manage access with:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [Role-Based Access Control troubleshooting](../active-directory/role-based-access-control-troubleshooting.md): Get suggestions for fixing common issues.
