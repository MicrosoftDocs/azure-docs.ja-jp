---
title: 'Azure ExpressRoute: Global Reach を使用して Microsoft Cloud に接続する'
description: この記事では、ExpressRoute Global Reach について説明します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: cherylmc
ms.openlocfilehash: 7e98f4a401a9fcab12bd7a276c47df67c7936b1d
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853414"
---
# <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
ExpressRoute は、複数のオンプレミス ネットワークを Microsoft Cloud に接続することができるプライベートで回復性がある方法です。 プライベート データ センターまたは企業ネットワークから、Azure や Office 365 などの多くの Microsoft クラウド サービスにアクセスできます。 たとえば、シリコン バレーに ExpressRoute 回線があるサンフランシスコのブランチ オフィスと、市内に ExpressRoute 回線があるロンドンのブランチ オフィスがあるとします。 どちらのブランチ オフィスも、米国西部と英国南部で Azure リソースへの高速の接続を利用できます。 ただし、ブランチ オフィスは相互にデータを直接交換することはできません。 言い換えれば、10.0.1.0/24 はデータを、10.0.3.0/24 と 10.0.4.0/24 には送信できますが、10.0.2.0/24 には送信できません。

![ない場合][1]

**ExpressRoute Global Reach** を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築できます。 上記の例では、ExpressRoute Global Reach を追加することで、サンフランシスコ オフィス (10.0.1.0/24) が、既存の ExpressRoute 回線と Microsoft のグローバル ネットワークを介してロンドン オフィス (10.0.2.0/24) とデータを直接交換できます。 

![ある場合][2]

## <a name="use-case"></a>使用事例
ExpressRoute Global Reach は、サービス プロバイダーの WAN 実装を補完し、世界各地のブランチ オフィスを接続するために設計されています。 たとえば、お使いのサービス プロバイダーが主に米国で業務を行っていて、米国内のすべてのブランチはリンクしていたが、日本や香港特別行政区では業務を行っていない場合、ExpressRoute Global Reach によって現地のサービス プロバイダーを利用できます。そうすると、Microsoft が ExpressRoute と自社のグローバル ネットワークを使用して、現地のユーザーのブランチを米国内のブランチに接続します。

![ユース ケース][3]

## <a name="availability"></a>可用性 
現在、ExpressRoute Global Reach は以下の場所でサポートされています。

* オーストラリア
* カナダ
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
* スイス
* イギリス
* 米国

ExpressRoute 回線は、上記の国または地域の [ExpressRoute ピアリング拠点](expressroute-locations.md)に作成する必要があります。 [異なる地政学的地域](expressroute-locations.md)間で ExpressRoute Global Reach を有効にするには、Premium SKU の回線を使用する必要があります。

## <a name="next-steps"></a>次のステップ
1. [Global Reach の FAQ を表示する](expressroute-faqs.md#globalreach)
2. [Global Reach を有効にする方法を学習する](expressroute-howto-set-global-reach.md)
3. [ExpressRoute 回線を仮想ネットワークにリンクする方法を学習する](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Global Reach なしの図"
[2]: ./media/expressroute-global-reach/2.png "Global Reach ありの図"
[3]: ./media/expressroute-global-reach/3.png "Global Reach のユース ケース"
