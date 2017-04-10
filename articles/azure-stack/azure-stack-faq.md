---
title: Frequently asked questions for Azure Stack | Microsoft Docs
description: Azure Stack frequently asked questions.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 028479e4-a17e-43c7-885c-cb2130f850d2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2017
ms.author: helaw
translationtype: Human Translation
ms.sourcegitcommit: fee25fc1c84a9a32c4c313e06056bb409927f475
ms.openlocfilehash: 7e84323168705b8900521834c42b358eb34d8afa
ms.lasthandoff: 03/02/2017


---
# <a name="frequently-asked-questions-for-azure-stack"></a>Frequently asked questions for Azure Stack
## <a name="deployment"></a>Deployment
### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>Do I need to format my data disks before starting or restarting an installation?
Disks should be in raw format. If you reinstall the operating system, you may need to check if the old storage pool is still present and delete using the following steps:

1. Open Server Manager.
2. Select Storage Pools.
3. See if a storage pool is listed.
4. Right-click **storage pool** if listed and enable read / write.
5. Right-click **Virtual Hard Disk** (Lower left corner) and select delete.
6. Right-click **Storage Pool** and click delete.
7. Launch Azure Stack script again and verify that the disk verification passes.

Optionally, the following script can be used:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Can I use all SSD disks for the storage pool in the POC installation?
For more information on storage configurations, see the [requirements guide](azure-stack-deploy.md).

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Can I use NVMe data disks for the Microsoft Azure Stack POC?
While Storage Spaces Direct supports NVMe disks, Azure Stack only supports a subset of the possible drive types and combinations possible for Storage Spaces Direct.  See the [requirements guide](azure-stack-deploy.md) for more information. 

### <a name="how-can-i-reinstall-azure-stack"></a>How can I reinstall Azure Stack?
You can follow the steps in the [redeployment guide](azure-stack-redeploy.md).  

## <a name="tenant"></a>Tenant
### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Can I deploy my own images as a tenant?
Yes, just like in Azure, a tenant can upload images in Azure Stack, in addition to using the images from the service administrator. For an overview, see the [Adding a VM Image](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Testing
### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Can I use Nested Virtualization to test the Microsoft Azure Stack POC?
Nested virtualization is not supported or tested with Azure Stack Technical Preview 3.

## <a name="virtual-machines"></a>Virtual machines
### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Does Azure Stack support dynamic disks for virtual machines?
Azure Stack does not support dynamic disks.


## <a name="next-steps"></a>Next steps
[Troubleshooting](azure-stack-troubleshooting.md)


