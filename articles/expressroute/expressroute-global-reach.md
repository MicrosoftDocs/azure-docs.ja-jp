---
title: 'Azure ExpressRoute: Global Reach を使用して Microsoft Cloud に接続する'
description: Azure ExpressRoute Global Reach を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築できます。その方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2020
ms.author: duau
ms.custom: references_regions
ms.openlocfilehash: 50679e11697a4227af69b8568c5f3cd23fe26cbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662799"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute は、複数のオンプレミス ネットワークを Microsoft Cloud に接続することができるプライベートで回復性がある方法です。 プライベート データ センターまたは企業ネットワークから、Azure や Microsoft 365 などの多くの Microsoft クラウド サービスにアクセスできます。 たとえば、シリコン バレーに ExpressRoute 回線があるサンフランシスコのブランチ オフィスと、市内に ExpressRoute 回線があるロンドンのブランチ オフィスがあるとします。 どちらのブランチ オフィスも、米国西部と英国南部で Azure リソースへの高速の接続を利用しています。 ただし、ブランチ オフィスは相互に直接接続してデータを送信することはできません。 つまり、10.0.1.0/24 から 10.0.3.0/24 および 10.0.4.0/24 ネットワークにデータを送信することはできますが、10.0.2.0/24 ネットワークには送信できません。

![Express Route Global Reach とリンクされていない回線を示す図。][1]

**ExpressRoute Global Reach** を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築できます。 上記の例では、ExpressRoute Global Reach を追加することで、サンフランシスコ オフィス (10.0.1.0/24) が、既存の ExpressRoute 回線と Microsoft のグローバル ネットワークを介してロンドン オフィス (10.0.2.0/24) とデータを直接交換できます。 

![Express Route Global Reach とリンクされている回線を示す図。][2]

## <a name="use-case"></a>使用事例
ExpressRoute Global Reach は、サービス プロバイダーの WAN 実装を補完し、世界各地のブランチ オフィスを接続するために設計されています。 たとえば、お使いのサービス プロバイダーが主に米国で業務を行っていて、米国内のすべてのブランチはリンクしていたが、日本や香港特別行政区では業務を行っていない場合、ExpressRoute Global Reach によって現地のサービス プロバイダーを利用できます。そうすると、Microsoft が ExpressRoute と自社のグローバル ネットワークを使用して、現地のユーザーのブランチを米国内のブランチに接続します。

![Express Route Global Reach のユース ケースを示す図。][3]

## <a name="availability"></a>可用性 
ExpressRoute Global Reach は以下の場所でサポートされています。 

> [!NOTE] 
> [異なる地政学的地域](expressroute-locations-providers.md#locations)間で ExpressRoute Global Reach を有効にするには、**Premium SKU** の回線を使用する必要があります。

* オーストラリア
* Canada
* フランス
* ドイツ
* 香港特別行政区
* アイルランド
* 日本
* 韓国
* オランダ
* ニュージーランド
* ノルウェー
* シンガポール
* 南アフリカ (ヨハネスブルグのみ)
* スウェーデン
* スイス
* イギリス
* United States

## <a name="next-steps"></a>次のステップ
- [Global Reach の FAQ](expressroute-faqs.md#globalreach) を確認します。
- [Global Reach を有効にする](expressroute-howto-set-global-reach.md)方法を確認します。
- [ExpressRoute 回線を仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)方法を確認します。

<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Global Reach なしの図"
[2]: ./media/expressroute-global-reach/2.png "Global Reach ありの図"
[3]: ./media/expressroute-global-reach/3.png "Global Reach のユース ケース"
