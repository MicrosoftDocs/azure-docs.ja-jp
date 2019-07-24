---
title: Linux 上の VM ディスクのスケーラビリティおよびパフォーマンスの目標 - Microsoft Azure | Microsoft Docs
description: Linux を実行している VM に接続された仮想マシン ディスクのスケーラビリティおよびパフォーマンスの目標について説明します。
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 11/15/2017
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 39e0a36b50a9e222e6016140967336ccda167514
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330054"
---
# <a name="scalability-and-performance-targets-for-vm-disks-on-linux"></a>Linux 上の VM ディスクのスケーラビリティおよびパフォーマンスの目標

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

詳細については、[Linux VM のサイズ](../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

## <a name="managed-virtual-machine-disks"></a>管理対象の仮想マシン ディスク

アスタリスクで示されるサイズはプレビュー中です。 使用できるリージョンについては、[FAQ](faq-for-disks.md#new-disk-sizes-managed-and-unmanaged) を参照してください。

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

## <a name="unmanaged-virtual-machine-disks"></a>非管理対象の仮想マシン ディスク
[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="see-also"></a>関連項目

[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits)