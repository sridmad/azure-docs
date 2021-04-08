
The following limits apply to Azure Backup.

| Limit Identifier | Default Limit |
| --- | --- |
| Number of servers/machines that can be registered against each vault |50 for Windows Server/Client/SCDPM <br/> 200 for IaaS VMs |
| Size of a data source for data stored in Azure vault storage |54400 GB max<sup>1</sup> |
| Number of backup vaults that can be created in each Azure subscription |25 (Backup vaults) <br/> 25 Recovery Services vaults per region |
| Number of times backup can be scheduled per day |3 per day for Windows Server/Client <br/> 2 per day for SCDPM <br/> Once a day for IaaS VMs |
| Data disks attached to an Azure virtual machine for backup |16 |
| Size of individual data disk attached to an Azure virtual machine for backup| 1023 GB <sup>2</sup>|

* <sup>1</sup>The 54400 GB limit does not apply to IaaS VM backup.
* <sup>2</sup> We have a [private preview](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a?redir=0) for supporting unmanaged disks upto 4TB. 

