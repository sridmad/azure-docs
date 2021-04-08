---
title: Reprotect from Azure to an on-premises site | Microsoft Docs
description: After failover of VMs to Azure, you can initiate a failback to bring VMs back to on-premises. Learn how to reprotect before a failback.
services: site-recovery
documentationcenter: ''
author: ruturaj
manager: gauravd
editor: ''

ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd

---
# Reprotect from Azure to an on-premises site



## Overview
This article describes how to reprotect Azure virtual machines from Azure to an on-premises site. Follow the instructions in this article when you're ready to fail back your VMware virtual machines or Windows/Linux physical servers after they've failed over from the on-premises site to Azure (as described in [Replicate VMware virtual machines and physical servers to Azure with Azure Site Recovery](site-recovery-failover.md)).

> [!WARNING]
> You cannot failback after you have either [completed migration](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), moved a virtual machine to another resource group, or deleted the Azure virtual machine. If you disable protection of the virtual machine, you cannot failback.


After reprotection finishes and the protected virtual machines are replicating, you can initiate a failback on the virtual machines to bring them to the on-premises site.

> [!NOTE]
> You can only reprotect and failback to an ESXi host. You cannot failback the virtual machine to Hyper-v hosts, or VMware workstations, or any other virtualization platform.

Post comments or questions at the end of this article or on the [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

For a quick overview, watch the following video about how to fail over from Azure to an on-premises site.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## Prerequisites

> [!IMPORTANT]
> During failover to Azure, the on-premises site may not be accessible and hence the configuration server may be either un-available or shutdown. During reprotect and failback, the on-premises configuration server should be running and in a connected OK state.


When you prepare to reprotect virtual machines, take or consider the following prerequisite actions:

* If a vCenter server manages the virtual machines that you want to fail back to, make sure that you have the [required permissions](site-recovery-vmware-to-azure-classic.md) for discovery of virtual machines on vCenter servers.

  > [!WARNING]
  > If snapshots are present on the on-premises master target or the virtual machine, reprotection fails. You can delete the snapshots on the master target before you proceed to reprotect. The snapshots on the virtual machine are automatically merged during a reprotect job.

* Before you fail back, create two additional components:

  * **Process server**: The [process server](site-recovery-vmware-setup-azure-ps-resource-manager.md) receives data from the protected virtual machine in Azure and sends data to the on-premises site. A low-latency network is required between the process server and the protected virtual machine. Thus, you can have an on-premises process server if you're using an Azure ExpressRoute connection or an Azure-based process server and a VPN.
  
  * **Master target server**: The master target server receives failback data. The on-premises management server that you created has a master target server installed by default. However, depending on the volume of failed-back traffic, you might need to create a separate master target server for failback.
    * [A Linux virtual machine needs a Linux master target server](site-recovery-how-to-install-linux-master-target.md).
    * A Windows virtual machine needs a Windows master target server. You can use the on-premises process server and master target machines again.

    The master target has other prerequisites that are listed in [Common things to check on a master target before reprotect](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).

* A configuration server is required on-premises when you fail back. During failback, the virtual machine must exist in the configuration server database. Otherwise, failback is unsuccessful. 

> [!IMPORTANT]
> Make sure that you take regularly scheduled backups of your configuration server. If there's a disaster, restore the server with the same IP address so that failback works.

* Set the `disk.EnableUUID=true` setting in the configuration parameters of the master target virtual machine in VMware. If this row does not exist, add it. This setting is required to provide a consistent UUID to the virtual machine disk (VMDK) so that it mounts correctly.

* *You cannot use Storage vMotion on the master target server*. This can cause the failback to fail. The virtual machine can't start because the disks are not available to it. To prevent this problem, exclude the master target servers from your vMotion list.

* Add a new drive to the master target server: a retention drive. Add a new disk and format the drive.


### Frequently asked questions

#### Why do I need a S2S VPN or an ExpressRoute connection to replicate data back to the on-premises site?
Whereas replication from on-premises to Azure can happen over the Internet or an ExpressRoute connection that has public peering, reprotection and failback require a site-to-site (S2S) VPN to replicate data. Provide the network so that the failed-over virtual machines in Azure can reach (ping) the on-premises configuration server. You might also deploy a process server in the Azure network of the failed-over virtual machine. This process server should also be able to communicate with the on-premises configuration server.

#### When should I install a process server in Azure?


The virtual machines on Azure that you want to reprotect send replication data to a process server. Set up your network so that the virtual machines on Azure can reach the process server.

You can deploy a process server in Azure or use the existing process server that you used during failover. The important point to consider is the latency to send the data from the virtual machines on Azure to the process server.

If you have an ExpressRoute connection set up, you can use an on-premises process server to send data because the latency between the virtual machine and the process server is low.

 ![Architecture diagram for ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



However, if you have only an S2S VPN, we recommend deploying the process server in Azure.

 ![Architecture diagram for VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


Remember that replication from Azure to on-premises can happen only over the S2S VPN or the private peering of your ExpressRoute network. Ensure that enough bandwidth is available over that network channel.

For information about installing an Azure-based process server, see [Manage a process server running in Azure](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> We recommend using an Azure-based process server during failback. The replication performance is higher if the process server is closer to the replicating virtual machine (the failed-over machine in Azure). However, during your proof of concept (POC) or demonstration, you can use the on-premises process server along with ExpressRoute with private peering to complete the POC faster.

> [!NOTE]
> Replication from on-premises to Azure can happen only over internet or ExpressRoute with public peering. Replication from Azure to on-premises can happen only over S2S VPN or ExpressRoute with private peering


#### What ports should I open on different components so that reprotection can work?

![Ports for failover and failback](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

#### Which master target server should I use for reprotection?
An on-premises master target server is required to receive data from the process server and then write to the on-premises virtual machine's VMDK. If you are protecting Windows virtual machines, you need a Windows master target server. You can reuse the on-premises process server and master target<!-- !todo component -->. For Linux virtual machines, you need to set up an additional on-premises Linux master target.


For information about installing a master target server, see:

* [How to install Windows master target server](site-recovery-vmware-to-azure.md)
* [How to install Linux master target server](site-recovery-how-to-install-linux-master-target.md)


#### What datastore types are supported on the on-premises ESXi host during failback?

Currently, Azure Site Recovery supports failing back only to a virtual machine file system (VMFS) or vSAN datastore. A NFS datastore is not supported. Due to this limitation, the datastore selection input on the reprotect screen is empty in the case of NFS datastores, or it shows the vSAN datastore but fails during the job. If you intend to fail back, you can create a VMFS datastore on-premises and fail back to it. This failback will cause a full download of the VMDK.

### Common things to check after completing installation of the master target server

* If the virtual machine is present on-premises on the vCenter server, the master target server needs access to the on-premises virtual machine's VMDK. Access is needed to write the replicated data to the virtual machine's disks. Ensure that the on-premises virtual machine's datastore is mounted on the master target's host with read/write access.

* If the virtual machine is not present on-premises on the vCenter server, the Site Recovery service needs to create a new virtual machine during reprotection. This virtual machine is created on the ESX host on which you create the master target. Choose the ESX host carefully, so that the failback virtual machine is created on the host that you want.

* *You cannot use Storage vMotion for the master target server*. This can cause the failback to fail. The virtual machine can't start because the disks are not available to it.

  > [!WARNING]
  > If a master target undergoes a Storage vMotion task after reprotection, the protected virtual machines disks that are attached to the master target migrate to the target of the vMotion task. If you try to fail back after this, detachment of the disk fails because the disks are not found. It then becomes hard to find the disks in your storage accounts. You need to find them manually and attach them to the virtual machine. After that, the on-premises virtual machine can be booted.

* Add a retention drive to your existing Windows master target server. Add a new disk and format the drive. The retention drive is used to stop the points in time when the virtual machine replicates back to the on-premises site. Following are some criteria of a retention drive. Without these criteria, the drive will not be listed for the master target server.

   * The volume is not used for any other purpose, such as a target of replication.

   * The volume is not in lock mode.

   * The volume is not a cache volume. The master target installation shouldn't exist on that volume. The custom installation volume for the process server and master target is not eligible for a retention volume. When the process server and master target are installed on a volume, the volume is a cache volume of the master target.

   * The file system type of the volume is not FAT or FAT32.

   * The volume capacity is nonzero.

   * The default retention volume for Windows is the R volume.

   * The default retention volume for Linux is /mnt/retention.

  > [!IMPORTANT]
  > You need to add a new drive if you're using an existing process server/configuration server machine or a scale or a process server/master target server machine. The new drive should meet the preceding requirements. If the retention drive is not present, it doesn't appear in the selection drop-down list on the portal. After you add a drive to the on-premises master target, it takes up to 15 minutes for the drive to appear in the selection on the portal. You can also refresh the configuration server if the drive does not appear after 15 minutes.

* A Linux failed-over virtual machine requires a Linux master target server. A Windows failed-over virtual machine requires a Windows master target server.

* Install VMware tools on the master target server. Without the VMware tools, the datastores on the master target's ESXi host cannot be detected.

* Enable the `disk.EnableUUID=true` parameter on the master target virtual machine by using the vCenter properties. <!-- !todo Needs link. -->

* The master target should have at least one VMFS datastore attached. If there is none, the **Datastore** input on the reprotect page will be empty, and you can't proceed.

* The master target server cannot have snapshots on the disks. If there are snapshots, reprotection and failback fail.

* The master target cannot have a Paravirtual SCSI controller. The controller can only be an LSI Logic controller. Without an LSI Logic controller, reprotection fails.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## Steps to reprotect

> [!NOTE]
> After a virtual machine boots in Azure, it takes some time for the agent to register back to the configuration server (up to 15 minutes). During this time, reprotection fails, and an error message indicates that the agent is not installed. Wait for a few minutes, and then try reprotection again.



1. In **Vault** > **Replicated items**, right-click the virtual machine that's been failed over, and then select **Re-Protect**. You can also click the machine and select **Re-Protect** from the command buttons.
2. In the blade, notice that the direction of protection, **Azure to On-premises**, is already selected.
3. In **Master Target Server** and **Process Server**, select the on-premises master target server and the process server.
4. For **Datastore**, select the datastore to which you want to recover the disks on-premises. This option is used when the on-premises virtual machine is deleted, and you need to create new disks. This option is ignored if the disks already exist, but you still need to specify a value.
5. Choose the retention drive.
6. The failback policy is automatically selected.
7. Click **OK** to begin reprotection. A job begins to replicate the virtual machine from Azure to the on-premises site. You can track the progress on the **Jobs** tab.

If you want to recover to an alternate location (when the on-premises virtual machine is deleted), select the retention drive and datastore that are configured for the master target server. When you fail back to the on-premises site, the VMware virtual machines in the failback protection plan use the same datastore as the master target server. A new virtual machine is then created in vCenter.

If you want to recover the virtual machine on Azure to an existing on-premises virtual machine, mount the on-premises virtual machine's datastores with read/write access on the master target server's ESXi host.
    ![Re-protect dialog box](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

You can also reprotect at the level of a recovery plan. A replication group can be reprotected only through a recovery plan. When you reprotect by using a recovery plan, you need to provide the values for every protected machine.

> [!NOTE]
> Use the same master target server to reprotect a replication group. If you use a different master target server to reprotect a replication group, the server cannot provide a common point in time.

> [!NOTE]
> The on-premises virtual machine is turned off during reprotection. This helps ensure data consistency during replication. Do not turn on the virtual machine after reprotection finishes.

After the reprotection succeeds, the virtual machine will enter a protected state.

## Next steps

After the virtual machine has entered a protected state, you can [initiate a failback](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back). 

The failback will shut down the virtual machine in Azure and boot the on-premises virtual machine. Expect some downtime for the application. Choose a time for failback when the application can tolerate downtime.

## Common problems

* If you used a template to create your virtual machines, ensure that each virtual machine has its own UUID for the disks. If the on-premises virtual machine's UUID clashes with that of the master target because both were created from the same template, reprotection fails. Deploy another master target that has not been created from the same template.

* If you perform a read-only user vCenter discovery and protect virtual machines, protection succeeds, and failover works. During reprotection, failover fails because the datastores cannot be discovered. A symptom is that the datastores aren't listed during reprotection. To resolve this problem, you can update the vCenter credential with an appropriate account that has permissions, and retry the job. For more information, see [Replicate VMware virtual machines and physical servers to Azure with Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

* When you fail back a Linux virtual machine and run it on-premises, you can see that the Network Manager package has been uninstalled from the machine. This uninstallation happens because the Network Manager package is removed when the virtual machine is recovered in Azure.

* When a Linux virtual machine is configured with a static IP address and is failed over to Azure, the IP address is acquired from DHCP. When you fail over to on-premises, the virtual machine continues to use DHCP to acquire the IP address. Manually sign in to the machine, and set the IP address back to a static address if necessary. A Windows virtual machine can acquire its static IP again.

* If you use either ESXi 5.5 free edition or vSphere 6 Hypervisor free edition, failover succeeds, but failback does not succeed. To enable failback, upgrade to either program's evaluation license.

* If you cannot reach the configuration server from the process server, use Telnet to check connectivity to the configuration server on port 443. You can also try to ping the configuration server from the process server. A process server should also have a heartbeat when it is connected to the configuration server.

* If you are trying to fail back to an alternate vCenter, make sure that your new vCenter and the master target server are discovered. A typical symptom is that the datastores are not accessible or visible in the **Reprotect** dialog box.

* A Windows Server 2008 R2 SP1 server that is protected as a physical on-premises server cannot be failed back from Azure to an on-premises site.

### Common Error codes

#### Error code 95226

*Reprotect failed as the Azure virtual machine was not able to reach the on-premises configuration server.*

This happens when 
1. The Azure virtual machine could not reach the on-premises configuration server and hence could not be discovered and registered to the configuration server. 
2. The InMage Scout Application service on the Azure virtual machine that needs to be running to communicate to the on-premises configuration server might not be running post failover.

To resolve this issue
1. You need to ensure that the network of the Azure virtual machine is configured such that the virtual machine can communicate with the on-premises configuration server. To do this, either set up a Site to Site VPN back to your on-premises datacenter or configure an ExpressRoute connection with private peering on the virtual network of the Azure virtual machine. 
2. If you already have a network configured such that the Azure virtual machine can communicate with the on-premises configuration server, then log into the virtual machine and check the 'InMage Scout Application Service'. If you observe that the InMage Scout Application Service is not running then start the service manually and ensure that the service start type is set to Automatic.

### Error code 78052
Reprotect fails with the error message: *Protection couldn't be completed for the virtual machine.*

This can happen due to two reasons
1. The virtual machine you are reprotecting is a Windows Server 2016. Curently this operating system is not supported for failback, but will be supported very soon.
2. There already exists a virtual machine with the same name on the Master target server you are failing back to.

To resolve this issue you can select a different master target server on a different host, so that the reprotect will create the machine on a different host, where the names do not collide. You can also vMotion the master target to a different host where the name collision will not happen. If the existing virtual machine is a stray machine, you can just rename it so that the new virtual machine can get created on the same ESXi host.

### Error code 78093

*The VM is not running, in a hung state or not accessible.*

To reprotect a failed over virtual machine back to on-premises, you need the Azure virtual machine running. This is so that the mobility service registers with the configuration server on-premises and can start replicating by communicating with the process server. If the machine is on an incorrect network or in not running (hung state or shutdown), then the configuration server cannot reach the mobility service in the virtual machine to begin the reprotect. You can restart the virtual machine so that it can start communicating back on-premises. Restart the reprotect job after starting the Azure virtual machine

### Error code 8061

*The datastore is not accessible from ESXi host.*

Refer to the [master target pre-requisites](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) and the [support datastores](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) for failback

