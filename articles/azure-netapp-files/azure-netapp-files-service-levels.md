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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017068"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files のサービス レベル
サービス レベルは容量プールの属性の 1 つです。 サービス レベルは、容量プール内のボリュームに割り当てられたクォータに基づいて許可された、ボリュームの最大スループットによって定義および区別されます。

## <a name="supported-service-levels"></a>サポートされているサービス レベル

Azure NetApp Files では、次の 3 つのサービス レベルがサポートされています。*Ultra*、*Premium*、*Standard*。 

* <a name="Ultra"></a>Ultra ストレージ

    Ultra ストレージ層は、プロビジョニングされた 1 TiB の容量あたり、最大で 128 MiB/s のスループットを提供します。 

* <a name="Premium"></a>Premium Storage

    Premium ストレージ層は、プロビジョニングされた 1 TiB の容量あたり、最大で 64 MiB/s のスループットを提供します。 

* <a name="Standard"></a>Standard Storage

    Standard ストレージ層は、プロビジョニングされた 1 TiB の容量あたり、最大で 16 MiB/s のスループットを提供します。

## <a name="throughput-limits"></a>スループットの制限

ボリュームに対するスループットの制限は、次の要素の組み合わせで決定されます。
* ボリュームが属する容量プールのサービス レベル
* ボリュームに割り当てられたクォータ  
* 容量プールの QoS の種類 ("*自動*" または "*手動*")  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>自動 QoS 容量プールのボリュームにおけるスループット制限の例

次の図は、自動 QoS 容量プールにおけるボリュームのスループット制限の例を示しています。

![サービス レベルの図](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* 例 1 では、2 TiB のクォータが割り当てられている Premium ストレージ層の QoS 容量プールのボリュームに、128 MiB/s (2 TiB * 64 MiB/s) のスループット制限が割り当てられます。 このシナリオは、容量プールのサイズや実際のボリューム消費量に関係なく適用されます。

* 例 2 では、100 GiB のクォータが割り当てられている Premium ストレージ層の QoS 容量プールのボリュームに、6.25 MiB/s (0.09765625 TiB * 64 MiB/s) のスループット制限が割り当てられます。 このシナリオは、容量プールのサイズや実際のボリューム消費量に関係なく適用されます。

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>手動 QoS 容量プールのボリュームにおけるスループット制限の例 

手動 QoS 容量プールを使用する場合、ボリュームに対して容量とスループットを別々に割り当てることができます。 手動 QoS 容量プールにボリュームを作成する場合は、スループット (MiB/s) の値を指定できます。 手動 QoS 容量プールのボリュームに割り当てられる合計スループットは、プールのサイズとサービス レベルによって異なります。 上限は、(容量プールのサイズ (TiB) x サービス レベルのスループット/TiB) になっています。 たとえば、Ultra サービス レベルの 10 TiB の容量プールで、ボリュームに対して使用可能な合計スループット容量は 1280 MiB/s (10 TiB x 128 MiB/s/TiB) です。

たとえば、SAP HANA システムでは、この容量プールを使用して次のボリュームを作成できます。 各ボリュームには、アプリケーションの要件を満たすサイズとスループットが個別に用意されています。

* SAP HANA データ ボリューム: サイズ 4 TiB で、最大 704 MiB/s
* SAP HANA ログ ボリューム: サイズ 0.5 TiB で、最大 256 MiB/s
* SAP HANA 共有ボリューム: サイズ 1 TiB で、最大 64 MiB/s
* SAP HANA バックアップ ボリューム: サイズ 4.5 TiB で最大 256 MiB/s

次の図は、SAP HANA のボリュームのシナリオを示しています。

![SAP HANA のボリュームの QoS シナリオ](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>次のステップ

- [Azure NetApp Files 価格ページ](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Azure NetApp Files のコスト モデル](azure-netapp-files-cost-model.md) 
- [容量プールの作成](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files のサービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/netapp/)
- [ボリュームのサービス レベルを動的に変更する](dynamic-change-volume-service-level.md) 
- [リージョン間レプリケーションのサービスレベル目標](cross-region-replication-introduction.md#service-level-objectives)
