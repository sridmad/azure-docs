---
title: Azure Linux VM sizes - GPU | Microsoft Docs
description: Lists the different GPU optimized sizes available for Linux virtual machines in Azure. Lists information about the number of vCPUs, data disks and NICs as well as storage throughput and network bandwidth for sizes in this series.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management

ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2017
ms.author: jonbeck

---

# GPU optimized virtual machine sizes

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

For driver installation and verification steps, see [N-series driver setup for Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* You shouldn't install X server or other systems that use the `Nouveau` driver on Ubuntu NC VMs. Before installing NVIDIA GPU drivers, you need to disable the `Nouveau` driver.  

## Other sizes
- [General purpose](sizes-general.md)
- [Compute optimized](sizes-compute.md)
- [Memory optimized](sizes-memory.md)
- [Storage optimized](sizes-storage.md)
- [High performance compute](sizes-hpc.md)

## Next steps
Learn more about how [Azure compute units (ACU)](acu.md) can help you compare compute performance across Azure SKUs.