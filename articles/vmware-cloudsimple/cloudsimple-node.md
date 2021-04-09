---
title: Azure VMware Solution by CloudSimple - ノードの概要
description: ノード、プロビジョニングされたノード、プライベート クラウド、VMware ソリューションなどの CloudSimple の概念について、CloudSimple のノード SKU 別に説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7de4b5faa997d909089daedab7e48e5d5a6de2e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140838"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple ノードの概要

ノードは、プライベート クラウドの構成要素です。 ノードは、以下のようになっています。

* VMware ESXi ハイパーバイザーがインストールされている専用ベア メタル コンピューティング ホスト  
* プライベート クラウドを作成するためにプロビジョニングまたは予約できるコンピューティングの単位
* CloudSimple サービスが使用可能なリージョンでプロビジョニングまたは予約できます。

プロビジョニングされたノードからプライベート クラウドを作成します。 プライベート クラウドを作成するには、少なくとも同じ SKU のノードが 3 つ必要です。 プライベート クラウドを拡張するには、ノードを追加します。  既存のクラスターにノードを追加するか、Azure portal でノードをプロビジョニングして CloudSimple サービスに関連付けることで、新しいクラスターを作成できます。  プロビジョニングされたすべてのノードが CloudSimple サービスの下に表示されます。  

## <a name="provisioned-nodes"></a>プロビジョニングされたノード

プロビジョニングされたノードでは、従量課金制の容量が提供されます。 ノードのプロビジョニングは、VMware クラスターを必要に応じて迅速にスケーリングするのに役立ちます。 必要に応じてノードを追加したり、プロビジョニングされたノードを削除して VMware クラスターをスケールダウンしたりできます。 プロビジョニングされたノードは月単位で課金され、それらがプロビジョニングされているサブスクリプションに対して請求されます。

* Azure サブスクリプションをクレジット カードで支払う場合、ただちにカードに課金されます。
* 請求書による課金の場合、次回の請求書に料金が反映されます。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>VMware Solution by CloudSimple ノード SKU

次の種類のノードをプロビジョニングまたは予約できます。

| SKU           | CS28 - ノード                 | CS36 - ノード                 | CS36m - ノード                |
|---------------|-----------------------------|-----------------------------|-----------------------------|
| リージョン        | 米国東部、米国西部            | 米国東部、米国西部            | 西ヨーロッパ                 |
| CPU           | 2x2.2 GHz、28 のコア (56 HT) | 2x2.3 GHz、36 のコア (72 HT) | 2x2.3 GHz、36 のコア (72 HT) |
| RAM           | 256 GB                      | 512 GB                      | 576 GB                      |
| キャッシュ ディスク    | 1.6 TB NVMe                 | 3.2-TB NVMe                 | 3.2-TB NVMe                 |
| 容量ディスク | 5.625 TB Raw                | 11.25 TB Raw                | 15.36 TB Raw                |
| ストレージ型  | All Flash                   | All Flash                   | All Flash                   |

## <a name="limits"></a>制限

プライベート クラウドには、次のノード制限が適用されます。

| リソース | 制限 |
|----------|-------|
| プライベート クラウドを作成するためのノードの最小数 | 3 |
| プライベート クラウド上のクラスター内のノードの最大数 | 16 |
| プライベート クラウド内のノードの最大数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次のステップ

* [ノードをプロビジョニングする](create-nodes.md)方法を学習する
* [プライベート クラウド](cloudsimple-private-cloud.md)について確認します。
