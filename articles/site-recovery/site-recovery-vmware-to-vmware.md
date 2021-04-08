---
title: Set up disaster recovery of VMware VMs or physical servers to a secondary site | Microsoft Docs
description: This article explains how to replicate on-premises VMware VMs or Windows/Linux physical servers to a secondary site, with the Azure Site Recovery service.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: jwhit
editor: ''

ms.assetid: b2cba944-d3b4-473c-8d97-9945c7eabf63
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2017
ms.author: raynew

---
# Set up disaster recovery of VMware virtual machines or physical servers to a secondary site


InMage Scout in Azure Site Recovery provides real-time replication between on-premises VMware sites. InMage Scout is included in Azure Site Recovery service subscriptions.

If you don't have an Azure subscription, [create a free account](https://azure.microsoft.com/pricing/free-trial/) before you begin.


## Create a vault
1. Sign in to the [Azure portal](https://portal.azure.com/) > **Recovery Services**.
2. Click New > Management > Backup and Site Recovery (OMS). Alternatively, you can click Browse > Recovery Services Vault > Add.
3. In **Name** specify a friendly name to identify the vault. If you have more than one subscription, select one of them.
4. In **Resource group** Create a new resource group or select an existing one. Specify an Azure region to complete required fields.
5. In **Location**, select the geographic region for the vault. To check supported regions, see [Azure Site Recovery
   Pricing](https://azure.microsoft.com/pricing/details/site-recovery/).
6. If you want to quickly access the vault from the Dashboard click Pin to dashboard and then click Create.
7. The new vault will appear on the Dashboard > All resources, and on the main Recovery Services vaults page.

## Configure the vault and download InMage Scout components
1. In the Recovery Services vaults page, select your vault and click **Settings**.
2. In **Settings** > **Getting Started** click **Site Recovery** > Step 1: **Prepare Infrastructure** > **Protection goal**.
3. In **Protection goal** select To recovery site, and select Yes, with VMware vSphere Hypervisor. Then click OK.
4. In **Scout setup**, click download to download InMage Scout 8.0.1 GA software and registration key. The setup files for all of the required components are in the downloaded .zip file.

## Step 3: Install component updates
Read about the latest [updates](#updates). You'll install the update files on servers in the following order:

1. RX server if relevant
2. Configuration servers
3. Process servers
4. Master target servers
5. vContinuum servers
6. Source server (both Windows and Linux Server)

Install the updates as follows:

1. Download the [update](https://aka.ms/asr-scout-update5) .zip file. This .zip file contains the following files:

   * RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
   * CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
   * UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
   * UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
   * vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe
   * UA update4 bits for RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
2. Extract the .zip files.<br>
3. **For the RX server**: Copy **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** to the RX server and extract it. In the extracted folder, run **/Install**.<br>
4. **For the configuration server/process server**: Copy **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** to the configuration server and process server. Double-click to run it.<br>
5. **For the Windows master target server**: To update the unified agent, copy **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** to the master target server. Double-click it to run it. Note that the unified agent is also applicable to the source server if source is not updated till Update4. You should install it on the source server as well, as mentioned later in this list.<br>
6. **For the vContinuum server**:  Copy **vCon_Windows_8.0.5.0_GA_Update_5_11525767_20Apr17.exe** to the vContinuum server.  Make sure that you've closed the vContinuum wizard. Double-click on the file to run it.<br>
7. **For the Linux master target server**: To update the unified agent, copy **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** to the master target server and extract it. In the extracted folder, run **/Install**.<br>
8. **For the Windows source server**: You do not need to install Update 5 agent on the source if it's already running Update 4. If it is running less than Update 4, apply the Update 5 agent.
To update the unified agent, copy **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** to the source server. Double-click it to run it. <br>
9. **For the Linux source server**: To update the unified agent, copy corresponding  version of UA file to the Linux server and extract it. In the extracted folder, run **/Install**.  Example: For RHEL 6.7 64 bit server,  copy **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz**  to the server and extract it. In the extracted folder, run **/Install**.

## Step 4: Set up replication
1. Set up replication between the source and target VMware sites.
2. For guidance, use the InMage Scout documentation that's downloaded with the product. Alternatively, you can access the documentation as follows:

   * [Release notes](https://aka.ms/asr-scout-release-notes)
   * [Compatibility matrix](https://aka.ms/asr-scout-cm)
   * [User guide](https://aka.ms/asr-scout-user-guide)
   * [RX user guide](https://aka.ms/asr-scout-rx-user-guide)
   * [Quick installation guide](https://aka.ms/asr-scout-quick-install-guide)

## Updates
### Azure Site Recovery Scout 8.0.1 Update 5
Scout Update 5 is a cumulative update. It has all the fixes of update1 till update4 and following new bug fixes and enhancements.
Fixes that are added from Site Recovery Scout update4 to update5 are specific to Master Target and vContinuum components. If all your source servers, Master Target, Configuration Server, Process Server and RX are already on Site Recovery Scout update4 then you need to apply update 5 only on Master Target server. 

**New platform support**
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)

> [!NOTE]
> SLES 11 SP4 64 bit  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** is packaged with base Scout GA package **InMage_Scout_Standard_8.0.1 GA.zip**. Download Scout GA package from portal as mentioned in [step 1](#step-1-create-a-vault).
>

**Bug fixes and enhancements**

* Increase Windows Cluster support reliability
	* Fixed- Sometime some of the P2V MSCS cluster disks become RAW after recovery
	* Fixed- P2V MSCS cluster recovery fails due to disk order mismatch
	* Fixed- MSCS cluster add disks operation fails with disk size mismatch
	* Fixed- Source MSCS cluster with RDM LUNs mapping readiness check fails in size verification
	* Fixed- Single node cluster protection fails due to SCSI mismatch issue 
	* Fixed- Re-protect of the P2V Windows cluster server fails if target cluster disks are present. 
	
* During failback protection, if selected MT is not on the same ESXi server as that of the protected source machine (during forward protection), then vContinuum picks up the wrong MT during Failback Recovery and subsequently recovery operation fails.

> [!NOTE]
> 
> * Above P2V cluster fixes are applicable to only those physical MSCS clusters that are  freshly protected with Site Recovery Scout update5. To avail the cluster fixes on the already protected P2V MSCS cluster with older updates, you need to follow the upgrade steps that are mentioned in the section 12, Upgrade protected P2V MSCS clusters to Scout Update 5 of the [release notes](https://aka.ms/asr-scout-release-notes).
> 
> * Re-protect of physical MSCS cluster can reuse existing target disks only if
at the time of re-protection, the same set of disks are active on each of the cluster nodes as they were when initially protected. If not, then there are manual steps as mentioned in section 12 of the [release notes](https://aka.ms/asr-scout-release-notes) to  move the target side disks to the correct datastore path to re-use them during re-protection. If you reprotect the MSCS cluster in P2V mode without following upgrade steps then it will create new disk on the target ESXi server. You need to manually delete the old disks from  the datastore.
> 
> * Whenever source SLES11 or SLES11 with any service pack server is rebooted gracefully, then one should manually mark the **root** disk replication pairs for re-sync as it will not be notified in CX UI. If you don't mark the root disk for resync, you may see data integrity (DI) issues.
> 

### Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 is a cumulative update. It has all the fixes of update1 till update3 and following new bug fixes and enhancements.

**New platform support**

* Support has been added for vCenter/vSphere 6.0, 6.1 and 6.2
* Support has been added for following Linux operating systems
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 and 7.2
  * CentOS 7.0, 7.1 and 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64 bit  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** is packaged with base Scout GA package **InMage_Scout_Standard_8.0.1 GA.zip**. Download Scout GA package from portal as mentioned in [step 1](#step-1-create-a-vault).
>
>

**Bug fixes and enhancements**

* Improved shutdown handling for following Linux OSes and clones to prevent unwanted re-sync issues.
  * Red Hat Enterprise Linux (RHEL) 6.x
  * Oracle Linux (OL) 6.x
* For Linux, complete folder access permissions in unified agent installation directory are now restricted only to the local user.
* On Windows timing out issue while issuing  common distributed consistency book mark on heavily loaded distributed applications like SQL and Share Point clusters.
* Added log related fix in CX base installer.
* VMware vCLI 6.0 download link is added to Windows Master Target base installer.
* Added more checks and logs for network configurations changes during failover and DR drills.
* Sometime retention information is not reported to the CX.  
* For physical cluster, volume Re-size operation through vContinuum wizard fails when the source volume shrink occurs.
* Cluster protection failed with error "Failed to find the disk signature" when cluster disk is PRDM disk.
* cxps transport server crash because of out-of-range exception.
* Server name and IP columns are now resizable in push install page of vContinuum wizard.
* RX API Enhancements
  * Provides Five latest available common consistency points (Only Guaranteed tags).
  * Provides capacity and free space details for all the protected devices.
  * Provides Scout driver state on source server.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** base package now has updated CX base installer **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**  and Windows Master Target  base installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. For all new installation use new CX and Windows Master Target GA bits.
> * Update 4 can be directly applied on 8.0.1 GA.
> * The configuration server and RX updates can’t be rolled back after they're applied on the system.
>
>

### Azure Site Recovery Scout 8.0.1 Update 3
Update 3 includes the following bug fixes and enhancements:

* The configuration server and RX fail to register to the Site Recovery vault when they're behind the proxy.
* The number of hours that the recovery point objective (RPO) hasn't been met isn't updated in the health report.
* The configuration server is not syncing with RX when the ESX hardware details or network details contain any UTF-8 characters.
* Windows Server 2008 R2 domain controllers fail to boot after recovery.
* Offline sync is not working as expected.
* After virtual machine (VM) failover, replication-pair deletion gets stuck in the CX UI for a long time, and users cannot complete the failback or resume operation.
* Overall snapshot operations that are done by the consistency job have been optimized to help reduce application disconnects like SQL clients.
* The performance of the consistency tool (VACP.exe) has been improved by reducing the memory usage that is required for creating snapshots on Windows.
* The push install service crashes when the password is greater than 16 characters.
* vContinuum is not checking and prompting for new vCenter credentials when the credentials are changed.
* On Linux, the master target cache manager (cachemgr) is not downloading files from the process server, which results in replication pair throttling.
* When the physical failover cluster (MSCS) disk order is not the same on all the nodes, replication is not set for some of the cluster volumes.
  <br/>Note that the cluster needs to be reprotected to take advantage of this fix.  
* SMTP functionality is not working as expected after RX is upgraded from Scout 7.1 to Scout 8.0.1.
* More stats have been added in the log for the rollback operation to track the time it has taken to complete it.
* Support has been added for Linux operating systems on the source server:
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* The CX and RX UI can now show the notification for the pair, which goes into bitmap mode.
* The following security fixes have been added in RX:

| **Issue description** | **Implementation procedures** |
| --- | --- |
| Authorization bypass via parameter tampering |Restricted access to non-applicable users. |
| Cross-site request forgery |Implemented the page-token concept, which generates randomly for every page. <br/>With this, you will see: <li> There is only a single sign-in instance for the same user.</li><li>Page refresh does not work--it will redirect to the dashboard.</li> |
| Malicious file upload |Restricted files to certain extensions. Allowed extensions are: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, and zip. |
| Persistent cross-site scripting |Added input validations. |

> [!NOTE]
> * All Site Recovery updates are cumulative. Update 3 has all the fixes of Update 1 and Update 2. Update 3 can be directly applied on 8.0.1 GA.
> * The configuration server and RX updates can’t be rolled back after they're applied on the system.
>
>

### Azure Site Recovery Scout 8.0.1 Update 2 (Update 03Dec15)
Fixes in Update 2 include:

* **Configuration server**: Fix for an issue that prevented the 31-day free metering feature from working as expected when the configuration server was registered in Site Recovery.
* **Unified agent**: Fix for an issue in Update 1 that resulted in the update not being installed on the master target server when it was upgraded from version 8.0 to 8.0.1.

### Azure Site Recovery Scout 8.0.1 Update 1
Update 1 includes the following bug fixes and new features:

* 31 days of free protection per server instance. This enables you to test functionality or set up a proof-of-concept.
  * All operations on the server, including failover and failback, are free for the first 31 days, starting from the time that a server is first protected with Site Recovery Scout.
  * From the 32nd day onwards, each protected server will be charged at the standard instance rate for Azure Site Recovery protection to a customer-owned site.
  * At any time, the number of protected servers that are currently being charged is available on the Dashboard page of the Azure Site Recovery vault.
* Support added for vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Support added for Linux operating systems on the source server:
  * RHEL 6 Update 6
  * RHEL 5 Update 11
  * CentOS 6 Update 6
  * CentOS 5 Update 11
* Bug fixes to address the following issues:
  * Vault registration fails for the configuration server or RX server.
  * Cluster volumes don't appear as expected when clustered virtual machines are reprotected when they resume.
  * Failback fails when the master target server is hosted on a different ESXi server from the on-premises production virtual machines.
  * Configuration file permissions are changed when you upgrade to 8.0.1, which affects protection and operations.
  * The resynchronization threshold isn't enforced as expected, which leads to inconsistent replication behavior.
  * The RPO settings are not appearing correctly in the configuration server interface. The uncompressed data value incorrectly shows the compressed value.
  * The Remove operation doesn't delete as expected in the vContinuum wizard, and replication isn't deleted from the configuration server interface.
  * In the vContinuum wizard, the disk is automatically unselected when you click **Details** in the disk view during protection of MSCS virtual machines.
  * During the physical-to-virtual (P2V) scenario, required HP services, such as CIMnotify and CqMgHost, aren't moved to manual in virtual machine recovery. This results in additional boot time.
  * Linux virtual machine protection fails when there are more than 26 disks on the master target server.

## Next steps
Post any questions that you have on the [Azure Recovery Services forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
