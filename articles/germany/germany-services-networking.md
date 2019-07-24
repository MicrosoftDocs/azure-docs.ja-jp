---
title: Azure Germany のネットワーク サービス | Microsoft Docs
description: Azure Germany へのプライベート接続に関する機能の比較とガイダンスを提供します
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: 82d9be403b5dc8711d86a65ad84477419549f8df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "58408413"
---
# <a name="azure-germany-networking-services"></a>Azure Germany のネットワーク サービス
## <a name="expressroute-private-connectivity"></a>ExpressRoute (プライベート接続)
ExpressRoute は、Azure Germany で一般提供されています。 詳細については (パートナーとピアリングの場所を含む)、[ExpressRoute グローバル ドキュメント](../expressroute/index.yml)をご覧ください。

### <a name="variations"></a>バリエーション

* Azure Germany のお客様は、専用 Azure Germany ExpressRoute 接続経由で物理的に分離された容量に接続します。
* Azure Germany は、少なくとも 400 km 離れた場所にある複数のリージョンのペアを使用することで、可用性と持続性の向上を実現します。 
* Azure Germany ExpressRoute 接続は、既定ではすべてアクティブ-アクティブの冗長構成でバースティング サポートを備えており、最大 10 GB の回線容量を提供します。
* Azure Germany ExpressRoute の配置場所により、お客様と Azure Germany の geo 冗長リージョンにとって最適な経路 (最短のホップ、低待機時間、高パフォーマンスなど) を提供します。
* Azure Germany ExpressRoute のプライベート接続では、インターネットを使用したりインターネット上を移動することはなく、インターネットに一切依存していません。
* Azure Germany の物理的および論理的なインフラストラクチャは、国際的な Microsoft クラウド ネットワークから物理的に分離された専用のインフラストラクチャです。
* Azure Germany ExpressRoute は、Microsoft Azure クラウド サービスへのプライベート接続を提供しますが、Office 365 または Dynamics 365 クラウド サービスへのプライベート接続は提供しません。

### <a name="considerations"></a>考慮事項
次の 2 つの基本的なサービスで、Azure Germany にプライベート ネットワーク接続を提供します。ExpressRoute と VPN (一般的な組織の場合サイト間) です。

ExpressRoute を使用して、Azure Germany データセンターとオンプレミス インフラストラクチャ間やコロケーション環境内でプライベート接続を作成できます。 ExpressRoute 接続では、公共のインターネットを利用できません。 それらは一般的なインターネット接続よりも信頼性が高く、高速で、待機時間が短い接続を提供します。 オンプレミスのシステムと Azure 間のデータ転送に ExpressRoute 接続を使用することで、コスト上の大きなメリットが得られることがあります。   

ExpressRoute によって、Exchange プロバイダーの施設などの ExpressRoute の場所にある Azure への接続を確立します。 または、ネットワーク サービス プロバイダーが提供する、MPLS (マルチプロトコル ラベル スイッチング) VPN などの既存の WAN ネットワークから Azure に直接接続します。

ネットワーク サービスで Azure Germany を利用するお客様のアプリケーションとソリューションをサポートするために、Azure Germany への接続には ExpressRoute (プライベート接続) を実装することを強くお勧めします。 VPN 接続を使用する場合は、次のことを考慮してください。

* 運用認可責任者 (機関) に問い合わせて、プライベート接続やその他の安全な接続メカニズムが必要であるかどうかを判断し、その他の制限事項を見極めます。
* プライベート接続ゾーンを経由するようにサイト間 VPN をルーティングするよう義務付けるかどうかを判断します。
* ライセンスされたプライベート接続アクセス プロバイダーとの MPLS 回線または VPN を確保します。

プライベート接続アーキテクチャを使用する場合、Azure Germany の Gateway Network/Internet (GN/I) エッジ ルーターの責任分界点に対する接続に関して、適切な実装が確立され、維持されていることを確認します。 同様に、Azure Germany に使用される Gateway Network/Customer (GN/C) エッジ ルーターの責任分界点とオンプレミス環境との間のネットワーク接続を組織で確立する必要があります。

Azure Germany リージョン内の任意の 1 つのピアリングの場所で ExpressRoute を介して Microsoft に接続する場合、ドイツ国境内のすべてのリージョンですべての Microsoft Azure クラウド サービスにアクセスできます。 たとえば、ExpressRoute を介してベルリンの Microsoft に接続している場合、Azure Germany でホストされているすべての Microsoft クラウド サービスにアクセスできます。

場所とパートナーの詳細、および Azure Germany のピアリングの場所に対応する ExpressRoute の詳細な一覧については、[ExpressRoute グローバル ドキュメント](../expressroute/index.yml)の「**概要**」タブを参照してください。

複数の ExpressRoute 回線を購入できます。 複数の接続を持つことで、geo 冗長性による高可用性が確保される大きなメリットがあります。 複数の ExpressRoute 回線がある場合、パブリック ピアリング パスで Microsoft からアドバタイズされたプレフィックスの同じセットを受け取ります。 これは、ネットワークから Microsoft へのパスが複数あることを意味します。 この状況は、ネットワーク内で最適でないルーティングの決定が行われる可能性があることを示します。 その結果、さまざまなサービスへの接続エクスペリエンスが最適でなくなる可能性があります。 詳細については、[ExpressRoute グローバル ドキュメント](../expressroute/index.yml)の **「ハウツー ガイド」>「ベスト プラクティス」** タブで、「**ルーティングを最適化する**」を選択して参照してください。

## <a name="support-for-load-balancer"></a>Load Balancer のサポート
Azure Load Balancer は Azure Germany で一般提供されています。 詳細については、[Load Balancer グローバル ドキュメント](../load-balancer/load-balancer-overview.md)をご覧ください。 

## <a name="support-for-traffic-manager"></a>Traffic Manager のサポート
Azure Traffic Manager は、Azure Germany で一般提供されています。 詳細については、[Traffic Manager グローバル ドキュメント](../traffic-manager/traffic-manager-overview.md)をご覧ください。 

## <a name="support-for-virtual-network-peering"></a>仮想ネットワーク ピアリングのサポート 
仮想ネットワーク ピアリングは、Azure Germany で一般提供されています。 詳細については、[仮想ネットワーク ピアリング グローバル ドキュメント](../virtual-network/virtual-network-peering-overview.md)をご覧ください。 

## <a name="support-for-vpn-gateway"></a>VPN Gateway のサポート 
Azure VPN Gateway は、Azure Germany で一般提供されています。 詳細については、[VPN Gateway グローバル ドキュメント](../vpn-gateway/vpn-gateway-about-vpngateways.md)をご覧ください。 

## <a name="next-steps"></a>次の手順
補足情報と最新情報については、[Azure Germany ブログ](https://blogs.msdn.microsoft.com/azuregermany/)をご覧ください。
