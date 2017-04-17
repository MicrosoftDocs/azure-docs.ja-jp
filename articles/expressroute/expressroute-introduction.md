---
title: "ExpressRoute の概要: 専用プライベート接続でオンプレミスのネットワークを Azure に拡張する | Microsoft Docs"
description: "この ExpressRoute の技術概要では、ExpressRoute 接続が専用プライベート接続を介してオンプレミスのネットワークを Azure に拡張するしくみについて説明します。"
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/28/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 280a17677714a522ca978a6eb6baf3e70f853d7a
ms.openlocfilehash: 5b0865310076557c8f7ce6f9d9b553950854407c
ms.lasthandoff: 03/01/2017


---
# <a name="expressroute-technical-overview"></a>ExpressRoute の技術概要
Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 

接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。 ExpressRoute 接続では、公共のインターネットを利用できません。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 ExpressRoute を使用してネットワークを Microsoft に接続する方法については、「[ExpressRoute connectivity models](expressroute-connectivity-models.md)」(ExpressRoute 接続モデル) をご覧ください。

![](./media/expressroute-introduction/expressroute-connection-overview-diagram.png)

## <a name="key-benefits"></a>主な利点

* 接続プロバイダーによるオンプレミス ネットワークと Microsoft Cloud 間のレイヤー 3 接続。 接続には、任意の環境間 (IP VPN) 接続、ポイント ツー ポイントのイーサネット接続、イーサネット交換による仮想交差接続があります。
* 地政学的領域のすべての地域にわたる Microsoft のクラウド サービスへの接続。
* ExpressRoute プレミアム アドオンを持つすべての領域にわたる Microsoft サービスへのグローバル接続。
* 業界標準プロトコル (BGP) でのネットワークと Microsoft 間の動的ルーティング。
* あらゆるピアリングの場所に冗長性を組み込むという高い信頼性。
* 接続アップタイム [SLA](https://azure.microsoft.com/support/legal/sla/)。
* Skype for Business の QoS サポート。

詳しくは、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

## <a name="features"></a>機能

### <a name="layer-3-connectivity"></a>レイヤー 3 接続
Microsoft は業界標準の動的ルーティング プロトコル (BGP) を利用し、オンプレミス ネットワーク、Azure のインスタンス、および Microsoft パブリック アドレスの間のルートを交換します。  さまざまなトラフィック プロファイルに合わせ、ネットワークとさまざまな BGP セッションを確立します。 詳しくは、 [ExpressRoute の回線とルーティング ドメイン](expressroute-circuit-peerings.md) に関する記事を参照してください。

### <a name="redundancy"></a>冗長性
各 ExpressRoute 回線は、接続プロバイダー/ネットワーク エッジから 2 つの Microsoft Enterprise エッジ ルーター (WSEE) への&2; つの接続で構成されます。 Microsoft は接続プロバイダー/あなたの側から 2 つの BGP 接続を必要とします。MSEE につき 1 つです。 あなたの側で冗長デバイス/イーサネット回線をデプロイしないことを選択できます。 ただし、接続プロバイダーは冗長デバイスを利用することで冗長性が与えられる方法で接続を Microsoft に渡します。 冗長レイヤー 3 接続構成は [SLA](https://azure.microsoft.com/support/legal/sla/) を有効にするための必須条件です。 

### <a name="connectivity-to-microsoft-cloud-services"></a>Microsoft クラウド サービスへの接続
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

ExpressRoute 接続によって、次のようなサービスにアクセスできます。

* Microsoft Azure サービス
* Microsoft Office 365 サービス
* Microsoft CRM Online サービス 

[ExpressRoute FAQ](expressroute-faqs.md) ページには、ExpressRoute でサポートされているサービスの詳しい一覧があります。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>地政学的な地域内のすべてのリージョンへの接続
[ピアリングの場所](expressroute-locations.md) の&1; つで Microsoft に接続し、地政学的な領域内のすべてのリージョンにアクセスできます。 

たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、北ヨーロッパと西ヨーロッパでホストされているすべての Microsoft クラウド サービスにアクセスできます。 地政学的領域、関連付けられている Microsoft クラウド リージョン、対応する ExpressRoute のピアリングの場所の概要については、「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」という記事をご覧ください。

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>ExpressRoute Premium アドオンによるグローバル接続
ExpressRoute Premium アドオン機能を有効にすると、地政学的境界を越えて接続を拡張できます。 たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、世界中のあらゆるリージョンでホストされているすべての Microsoft クラウド サービスにアクセスできます (国内クラウドは除外します)。 北ヨーロッパ リージョンと西ヨーロッパ リージョンにアクセスする場合と同じ方法で南アメリカやオーストラリアにデプロイされているサービスにアクセスできます。

### <a name="rich-connectivity-partner-ecosystem"></a>大勢の接続パートナーが属するエコシステム
ExpressRoute のエコシステムには継続的に新しい接続パートナーと SI パートナーが加わり、拡大します。 最新の情報については、 [ExpressRoute のプロバイダーと場所](expressroute-locations.md) に関する記事を参照してください。

### <a name="connectivity-to-national-clouds"></a>国内クラウドへの接続
Microsoft は特殊な地政学的領域と顧客区分のために隔離されたクラウド環境を運営しています。 国内のクラウドとプロバイダーの一覧については、 [ExpressRoute のプロバイダーと場所](expressroute-locations.md) に関するページを参照してください。

### <a name="bandwidth-options"></a>帯域幅のオプション
さまざまな帯域幅に合った ExpressRoute 回線を購入できます。 サポートされる帯域幅の一覧は次のとおりです。 サポートされる帯域幅の一覧は接続パートナーに必ず確認してください。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>帯域幅の動的スケーリング
接続を壊さずに (ベスト エフォート基準で) ExpressRoute 回線の帯域幅を増やすことができます。 

### <a name="flexible-billing-models"></a>柔軟な課金モデル
最適な請求モデルを選択できます。 次に示す請求モデルから選択します。 詳しくは、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

* **無制限のデータ**。 ExpressRoute 回線は月額料金に基づいて課金されます。すべての受信または送信データ転送が無料で含まれます。 
* **従量制課金データ**。 ExpressRoute 回線は月額料金に基づいて課金されます。 すべての受信データ転送は無料で含まれます。 送信データ転送は&1; GB のデータ転送単位で課金されます。 データ転送料金は地域によって異なります。
* **ExpressRoute Premium アドオン**。 ExpressRoute Premiumは ExpressRoute 回線のアドオンです。 ExpressRoute Premium アドオンには次の機能があります。 
  * Azure パブリックと Azure プライベートのピアリングのルート上限の増加 (4,000 ルートから 10,000 ルートに)。
  * サービスのグローバル接続。 (国内のクラウドを除く) あらゆるリージョンで作成された ExpressRoute 回線に与えられる世界中の他のリージョンにあるリソースへのアクセス。 たとえば、西ヨーロッパで作成された仮想ネットワークにシリコン バレーでプロビジョニングされた ExpressRoute 回線からアクセスできます。
  * ExpressRoute ごとの VNet リンクの増加 (回線の帯域幅にもよりますが、10 からそれ以上に)。

## <a name="next-steps"></a>次のステップ

* [ExpressRoute 接続モデル](expressroute-connectivity-models.md)について説明します。
* ExpressRoute 接続とルーティング ドメインについて説明します。 「 [ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
* サービス プロバイダーを検索します。 「 [ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
* [ルーティング](expressroute-routing.md)、[NAT](expressroute-nat.md)、[QoS](expressroute-qos.md) の要件をご覧ください。
* ExpressRoute 接続を構成します。
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-portal-resource-manager.md)
  * [ルーティングの構成](expressroute-howto-routing-portal-resource-manager.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-portal-resource-manager.md)

