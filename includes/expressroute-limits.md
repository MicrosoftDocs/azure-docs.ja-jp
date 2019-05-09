---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ee987448f9f6d95e6f97317bec2f696e734cba20
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632761"
---
#### <a name="expressroute-limits"></a>ExpressRoute の制限
各サブスクリプションの Azure ExpressRoute リソースには、次の制限が適用されます。

| Resource | 既定/上限 |
| --- | --- |
| サブスクリプションあたりの ExpressRoute 回線数 |10 |
| サブスクリプションあたりのリージョンごとの ExpressRoute 回線数 (Azure Resource Manager) |10 |
| ExpressRoute Standard の Azure プライベート ピアリング用の最大ルート数 |4,000 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリング用の最大ルート数 |10,000 |
| ExpressRoute 接続に使用する VNet アドレス空間からの Azure プライベート ピアリング用の最大ルート数 |200 | 
| ExpressRoute Standard の Microsoft Azure ピアリング用の最大ルート数 |200 |
| ExpressRoute Premium アドオンの Microsoft Azure ピアリング用の最大ルート数 |200 |
| さまざまなピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute の最大数 |4 |
| ExpressRoute 回線あたりで許可される仮想ネットワークのリンク数 |次の表を参照してください。 |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>ExpressRoute 回線あたりの仮想ネットワーク数
| **回線のサイズ** | **Standard での仮想ネットワークのリンク数** | **Premium アドオンでの仮想ネットワークのリンク数** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

