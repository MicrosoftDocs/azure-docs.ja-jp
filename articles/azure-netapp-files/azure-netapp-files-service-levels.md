---
title: Azure NetApp Files のサービス レベル | Microsoft Docs
description: Azure NetApp Files のサービス レベルのスループット パフォーマンスについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533123"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files のサービス レベル
サービス レベルは容量プールの属性の 1 つです。 サービス レベルは、容量プール内のボリュームに割り当てられたクォータに基づいて許可された、ボリュームの最大スループットによって定義および区別されます。

## <a name="supported-service-levels"></a>サポートされているサービス レベル

Azure NetApp Files では、次の 3 つのサービス レベルがサポートされています。*Ultra*、*Premium*、*Standard*。 

* <a name="Ultra"></a>Ultra ストレージ

    Ultra ストレージ層は割り当てられた 1 TiB のボリューム クォータあたり、最大で 128 MiB/s のスループットを提供します。 

* <a name="Premium"></a>Premium Storage

    Premium ストレージ層は割り当てられた 1 TiB のボリューム クォータあたり、最大で 64 MiB/s のスループットを提供します。 

* <a name="Standard"></a>Standard Storage

    Standard ストレージ層は割り当てられた 1 TiB のボリューム クォータあたり、最大で 16 MiB/s のスループットを提供します。

## <a name="throughput-limits"></a>スループットの制限

ボリュームに対するスループットの制限は、次の要素の組み合わせで決定されます。
* ボリュームが属する容量プールのサービス レベル
* ボリュームに割り当てられたクォータ  

この概念を次の図に示します。

![サービス レベルの図](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

上記の例 1 では、2 TiB のクォータが割り当てられている Premium ストレージ層の容量プールからのボリュームに、128 MiB/s (2 TiB * 64 MiB/s) のスループットの制限が割り当てられます。 このシナリオは、容量プールのサイズや実際のボリューム消費量に関係なく適用されます。

上記の例 2 では、100 GiB のクォータが割り当てられている Premium ストレージ層の容量プールからのボリュームに、6.25 MiB/s (0.09765625 TiB * 64 MiB/s) のスループットの制限が割り当てられます。 このシナリオは、容量プールのサイズや実際のボリューム消費量に関係なく適用されます。

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md) 
- [容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md) 