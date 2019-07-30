---
title: 'Azure Premium Storage: Windows VM におけるパフォーマンスのための設計 | Microsoft Docs'
description: Azure Premium Storage を使用する高パフォーマンスのアプリケーションを設計します。 Premium Storage は、Azure Virtual Machines で実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c6de3144a87a5bfad38e1b33148f292b26c0f181
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658244"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> ときには、ディスク パフォーマンスの問題のように見えるものが、実際にはネットワークのボトルネックであることもあります。 このような場合は、[ネットワーク パフォーマンス](../../virtual-network/virtual-network-optimize-network-bandwidth.md)を最適化する必要があります。
>
> ディスクのベンチマークを実行する場合は、「[ディスクのベンチマーク](disks-benchmarks.md)」に関する記事を参照してください。
>
> VM で高速ネットワークがサポートされる場合は、それが有効になっていることを確認する必要があります。 有効になっていない場合は、[Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) と [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) の両方で、既にデプロイされている VM 上で有効にすることができます。

Premium Storage を初めてご使用になる場合は、作業を始める前に、まず [IaaS VM 用の Azure ディスクの種類の選択](disks-types.md)に関する記事と 「[ストレージ アカウントでの Azure Storage のスケーラビリティとパフォーマンスのターゲット](../../storage/common/storage-scalability-targets.md)」をお読みください。


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
