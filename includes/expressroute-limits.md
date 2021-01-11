---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 146a257a9b8f31e79283d71ede1e1c5c0355765c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147784"
---
| リソース | 制限 |
| --- | --- |
| サブスクリプションあたりの ExpressRoute 回線数 |10 |
| サブスクリプションあたりのリージョンごとの ExpressRoute 回線数 (Azure Resource Manager) |10 |
| ExpressRoute Standard の Azure プライベート ピアリングにアドバタイズされる最大ルート数 |4,000 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリングにアドバタイズされる最大ルート数 |10,000 |
| ExpressRoute 接続に使用する VNet アドレス空間からの Azure プライベート ピアリングからアドバタイズされる最大ルート数 |200 |
| ExpressRoute Standard の Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| ExpressRoute Premium アドオンの Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| Standard Performance/ErGw1AZ ゲートウェイにリンクされる ExpressRoute 回線の最大数 |4 | 
| High Performance/ErGw1AZ ゲートウェイにリンクされる ExpressRoute 回線の最大数 |8 | 
| Ultra Performance/ErGw1AZ ゲートウェイにリンクされる ExpressRoute 回線の最大数 |16 | 
| さまざまなピアリング場所 (すべての SKU) で同じ仮想ネットワークにリンクされる ExpressRoute の最大数 |4 |
| ExpressRoute 回線あたりで許可される仮想ネットワークのリンク数 |「[ExpressRoute 回線あたりの仮想ネットワーク数](#vnetpercircuit)」の表を参照してください。  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>ExpressRoute 回線あたりの仮想ネットワーク数
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
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**100 Gbps ExpressRoute Direct のみ*

> [!NOTE]
> Global Reach の接続数は、ExpressRoute 回線あたりの仮想ネットワーク接続数の上限にカウントされます。 たとえば、10 Gbps Premium 回線であれば、Global Reach 接続が 5 本と ExpressRoute ゲートウェイへの接続が 95 本、または Global Reach 接続が 95 本と ExpressRoute ゲートウェイへの接続が 5 本許容されます。回線に対して 100 接続という上限を超えなければ、他のどのような組み合わせでもかまいません。
