---
title: CloudSimple による VMware ソリューションのノード概要 -　Azure
description: CloudSimple ノードと概念について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fb82e31d58d9955efc3b147eccf2b82b8768aeee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165793"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple ノードの概要

ノードは、以下のようになっています。

* 専用のベア メタル コンピューティングホスト。 VMware ESXi ハイパーバイザーがインストール済み。  
* プライベート クラウドを作成するためにプロビジョニングまたは予約できるコンピューティングの単位です。  
* CloudSimple サービスが使用可能なリージョンでプロビジョニングまたは予約できます。

ノードは、プライベート クラウドの構成要素です。  プライベート クラウドを作成するには、少なくとも同じ SKU のノードが 3 つ必要です。  プライベート クラウドを展開するには、他のノードを追加します。  既存のクラスターにノードを追加することができます。 または、Azure portal でノードをプロビジョニングし、それを CloudSimple サービスに関連付けることによって新しいクラスターを作成できます。  プロビジョニングされたすべてのノードが CloudSimple サービスの下に表示されます。  CloudSimple ポータル上のプロビジョニングされたノードからプライベート クラウドを作成します。

## <a name="provisioned-nodes"></a>プロビジョニングされたノード

プロビジョニングされたノードでは、従量課金制の容量が提供されます。 ノードのプロビジョニングは、VMware クラスターを必要に応じて迅速にスケーリングするのに役立ちます。 必要に応じてノードを追加したり、プロビジョニングされたノードを削除して VMware クラスターをスケールダウンしたりできます。 プロビジョニングされたノードは月単位で課金され、それらがプロビジョニングされているサブスクリプションに対して請求されます。

* Azure サブスクリプションをクレジット カードで支払う場合、ただちにカードに課金されます。
* 請求書による課金の場合、次回の請求書に料金が反映されます。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>CloudSimple ノード SKU による VMware ソリューション

次の種類のノードをプロビジョニングまたは予約できます。

| SKU | CS28 - ノード | CS36 - ノード |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz、28 のコア (56 HT) | 2x2.3 GHz、36 のコア (72 HT) |
| RAM | 256 GB | 512 GB |
| キャッシュ ディスク |  1.6 TB NVMe | 3.2-TB NVMe |
| 容量ディスク | 5.625 TB Raw | 11.25 TB Raw |
| ストレージの種類 | All Flash | All Flash |

## <a name="limits"></a>制限

プライベート クラウドに次のノード制限が適用されます。

| Resource | 制限 |
|----------|-------|
| プライベート クラウドを作成するための最小数のノード | 3 |
| プライベート クラウド上のクラスタ内の 最大ノード数 | 16 |
| プライベート クラウド内の最大ノード数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次の手順

* [ノードをプロビジョニングする](create-nodes.md)方法を学習する
* [プライベート クラウド](cloudsimple-private-cloud.md)について説明します。