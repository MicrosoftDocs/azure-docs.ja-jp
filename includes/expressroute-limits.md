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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622108"
---
| Resource | 既定/上限 |
| --- | --- |
| サブスクリプションあたりの ExpressRoute 回線数 |10 |
| サブスクリプションあたりのリージョンごとの ExpressRoute 回線数 (Azure Resource Manager) |10 |
| ExpressRoute Standard の Azure プライベート ピアリングにアドバタイズされる最大ルート数 |4,000 |
| ExpressRoute Premium アドオンの Azure プライベート ピアリングにアドバタイズされる最大ルート数 |10,000 |
| ExpressRoute 接続に使用する VNet アドレス空間からの Azure プライベート ピアリングからアドバタイズされる最大ルート数 |200 |
| ExpressRoute Standard の Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| ExpressRoute Premium アドオンの Azure Microsoft ピアリングにアドバタイズされる最大ルート数 |200 |
| 同じピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute 回線の最大数 |4 |
| さまざまなピアリング場所で同じ仮想ネットワークにリンクされる ExpressRoute の最大数 |4 |
| ExpressRoute 回線あたりで許可される仮想ネットワークのリンク数 |「[ExpressRoute 回線あたりの仮想ネットワーク数](#vnetpercircuit)」の表を参照してください。  |

#### <a name="vnetpercircuit"></a>ExpressRoute 回線あたりの仮想ネットワーク数
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
