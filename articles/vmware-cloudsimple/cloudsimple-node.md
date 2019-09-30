---
title: Azure VMware Solution by CloudSimple - ノードの概要
description: CloudSimple ノードと概念について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 58af69b401400c7b2f663a91de8bf38bc9a296a3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877696"
---
# <a name="cloudsimple-nodes-overview"></a>CloudSimple ノードの概要

ノードは、プライベート クラウドの構成要素です。 ノードは、以下のようになっています。

* VMware ESXi ハイパーバイザーがインストールされている専用ベア メタル コンピューティング ホスト  
* プライベート クラウドを作成するために購入または予約できるコンピューティング単位
* CloudSimple サービスが使用可能な領域で購入または保予約が可能

購入済みのノードからプライベート クラウドを作成します。 プライベート クラウドを作成するには、少なくとも同じ SKU のノードが 3 つ必要です。 プライベート クラウドを拡張するには、ノードを追加します。  既存のクラスターにノードを追加するか、Azure portal でノードを購入して CloudSimple サービスに関連付けることで、新しいクラスターを作成できます。  購入済みのすべてのノードが、CloudSimple サービスの下に表示されます。  

## <a name="purchased-nodes"></a>購入済みのノード

購入済みのノードの容量は、従量課金制です。 ノードを購入すると、オンデマンドで、VMware クラスターをすばやく拡張できます。 必要に応じて、ノードを追加したり、購入済みのノードを削除して VMware クラスターをスケールダウンしたりできます。 購入済みのノードは月単位で課金され、それらを購入したサブスクリプションに対して請求されます。

* Azure サブスクリプションをクレジット カードで支払う場合、ただちにカードに課金されます。
* 請求書による課金の場合、次回の請求書に料金が反映されます。

## <a name="vmware-solution-by-cloudsimple-nodes-sku"></a>CloudSimple ノード SKU による VMware ソリューション

次の種類のノードを、購入または予約できます。

| SKU | CS28 - ノード | CS36 - ノード |
|-----|-------------|-------------|
| CPU | 2x2.2 GHz、28 のコア (56 HT) | 2x2.3 GHz、36 のコア (72 HT) |
| RAM | 256 GB | 512 GB |
| キャッシュ ディスク |  1.6 TB NVMe | 3.2-TB NVMe |
| 容量ディスク | 5.625 TB Raw | 11.25 TB Raw |
| ストレージの種類 | All Flash | All Flash |

## <a name="limits"></a>制限

プライベート クラウドには、次のノード制限が適用されます。

| Resource | 制限 |
|----------|-------|
| プライベート クラウドを作成するためのノードの最小数 | 3 |
| プライベート クラウド上のクラスター内のノードの最大数 | 16 |
| プライベート クラウド内のノードの最大数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次の手順

* [ノードの購入](create-nodes.md)方法を確認します。
* [プライベート クラウド](cloudsimple-private-cloud.md)について確認します。
