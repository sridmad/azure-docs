
---
title: Support matrix for replication to a secondary site with Azure Site Recovery | Microsoft Docs
description: Summarizes the supported operating systems and components for Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''

ms.assetid:
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2017
ms.author: raynew

---
# Support matrix for replication to a secondary site with Azure Site Recovery

This article summarizes what's supported when you use the [Azure Site Recovery](site-recovery-overview.md) service to replicate to a secondary on-premises site.

## Supported scenarios

**Deployment** | **Details** 
--- | ---
**VMware to VMware** | Disaster recovery of on-premises VMware VMs to secondary VMware site.<br/><br/> Download the [InMage Scout user guide](https://aka.ms/asr-scout-user-guide)
**Hyper-V to Hyper-V** | Disaster recovery of on-premises Hyper-V VMs in VMM clouds to a secondary VMM cloud.<br></br> Not supported without VMM.



  

## Host servers

**Deployment** | **Support**
--- | ---
**VMware VM/physical server** | vCenter 5.5, 6.0 and 6.5 (support for 5.5 features only)
**Hyper-V with VMM** | Windows Server 2016 and Windows Server 2012 R2 with the latest updates.<br/><br/> Windows Server 2016 hosts should be managed by VMM 2016.<br/><br/> VMM 2016 clouds with a mixture of Windows Server 2016 and 2012 R2 hosts aren't currently supported.<br/><br/> Deployment that include an upgrade of an existing VMM 2012 R2 to System Center 2016 aren't currently supported.


## Support for replicated machine OS versions

The following table summarizes operating system support for machines replicated with Site Recovery. Any workload can be running on the supported operating system.

**VMware/physical server** | **Hyper-V (with VMM)**
--- | ---
64-bit Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 with at least SP1<br/><br/> Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1, 7.2 <br/><br/> Centos 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1, 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5, 6.8 running the Red Hat compatible kernel, or Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4  | Any guest operating system [supported by Hyper-V](https://technet.microsoft.com/library/mt126277.aspx)

## Linux machine storage

Only Linux machines with the following storage can be replicated:

- File system (EXT3, ETX4, ReiserFS, XFS).
- Multipath software-device Mapper.
- Volume manager (LVM2).
- Physical servers with HP CCISS controller storage are not supported.
- The ReiserFS file system is supported only on SUSE Linux Enterprise Server 11 SP3.

## Network configuration

### Hosts

**Configuration** | **VMware/physical server** | **Hyper-V (with VMM)**
--- | --- | ---
NIC teaming | Yes | Yes
VLAN | Yes | Yes
IPv4 | Yes | Yes
IPv6 | No | No

### Guest VMs

**Configuration** | **VMware/physical server** | **Hyper-V (with VMM)**
--- | --- | ---
NIC teaming | No | No
IPv4 | Yes | Yes
IPv6 | No | No
Static IP (Windows) | Yes | Yes
Static IP (Linux) | Yes | Yes
Multi-NIC | Yes | Yes


## Storage

### Host storage

**Storage (host)** | **VMware/physical server** | **Hyper-V (with VMM)**
--- | --- | ---
NFS | Yes | N/A
SMB 3.0 | N/A | Yes
SAN (ISCSI) | Yes | Yes
Multi-path (MPIO) | Yes | Yes

### Guest or physical server storage

**Configuration** | **VMware/physical server** | **Hyper-V (with VMM)**
--- | --- | ---
VMDK | Yes | N/A
VHD/VHDX | N/A | Yes (up to 16 disks)
Gen 2 VM | N/A | Yes
Shared cluster disk | Yes  | No
Encrypted disk | No | No
UEFI| Yes | N/A
NFS | No | No
SMB 3.0 | No | No
RDM | Yes | N/A
Disk > 1 TB | Yes | Yes
Volume with striped disk > 1 TB<br/><br/> LVM | Yes | Yes
Storage Spaces | No | Yes
Hot add/remove disk | Yes | No
Exclude disk | Yes | Yes
Multi-path (MPIO) | N/A | Yes

## Vaults

**Action** | **VMware/physical server** | **Hyper-V (with VMM)**
--- | --- | ---
Move vaults across resource groups (within or across subscriptions) | No | No
Move storage, network, Azure VMs across resource groups (within or across subscriptions) | No | No

## Provider and agent

**Name** | **Description** | **Latest version** | **Details**
--- | --- | --- | --- | ---
**Azure Site Recovery Provider** | Coordinates communications between on-premises servers and Azure <br/><br/> Installed on on-premises VMM servers, or on Hyper-V servers if there's no VMM server | 5.1.19 ([available from portal](http://aka.ms/downloaddra)) | [Latest features and fixes](https://support.microsoft.com/kb/3155002)
**Mobility service** | Coordinates replication between on-premises VMware servers or physical servers, and the secondary site<br/><br/> Installed on VMware VM or physical servers that you want to replicate  | N/A (available from portal) | N/A


## Next steps

- [Replicate Hyper-V VMs in VMM clouds to a secondary site](tutorial-vmm-to-vmm.md)
- [Replicate VMware VMs and physical servers to a secondary site](tutorial-vmware-to-vmware.md)
