---
title: 'Azure ExpressRoute: 回線の NAT 要件'
description: このページでは、ExpressRoute 回線の NAT を構成および管理するための詳細な要件について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: a2c322c765d39a3afe4974ed88bf4dc18fd467a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89569647"
---
# <a name="expressroute-nat-requirements"></a>ExpressRoute NAT の要件
ExpressRoute を使用して Microsoft クラウド サービスに接続するには、NAT をセットアップして管理する必要があります。 一部の接続プロバイダーでは、NAT のセットアップと管理が管理されたサービスとして提供されています。 このようなサービスが提供されているかどうか、接続プロバイダーに問い合わせてください。 このようなサービスが提供されていない場合は、次に示す要件に従う必要があります。 

[ExpressRoute の回線とルーティング ドメイン](expressroute-circuit-peerings.md) のページをお読みになり、さまざまなルーティング ドメインの概要をご確認ください。 Azure パブリックと Microsoft ピアリングのパブリック IP アドレス要件を満たすために、ネットワークと Microsoft の間に NAT をセットアップすることをお勧めします。 このセクションでは、セットアップする NAT インフラストラクチャに関して説明します。

## <a name="nat-requirements-for-microsoft-peering"></a>Microsoft ピアリングの NAT 要件
Microsoft ピアリング パスにより、Azure パブリック ピアリング パスでサポートされていない Microsoft クラウド サービスに接続できます。 対象となるサービスには、Exchange Online、SharePoint Online、Skype for Business などの Microsoft 365 サービスが含まれます。 Microsoft は今後、Microsoft ピアリングで双方向の接続をサポートする予定です。 Microsoft クラウド サービスに向かうトラフィックは有効な IPv4 アドレスに SNAT 変換しないと、Microsoft ネットワークに入れません。 Microsoft クラウド サービスからご利用のネットワークに送信されるトラフィックは、[非対称ルーティング](expressroute-asymmetric-routing.md)を回避するためにインターネット エッジで SNAT 変換する必要があります。 下の図に、Microsoft ピアリングのために NAT を設定する方法の概要を示します。

![Microsoft ピアリング用に NAT を設定する方法の概要図。](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>ご利用のネットワークから Microsoft に送信されるトラフィック
* トラフィックが有効なパブリック IPv4 アドレスで Microsoft ピアリング パスに入っていることを確認する必要があります。 Microsoft はリージョンのルーティング インターネット レジストリ (RIR) またはインターネット ルーティング レジストリ (IRR) に対して IPv4 NAT アドレスの所有者を検証する必要があります。 ピアリングされている AS 番号と NAT に使用されている IP アドレスに基づいて確認されます。 ルーティング レジストリに関する情報については、 [ExpressRoute のルーティングの要件](expressroute-routing.md) のページを参照してください。
* Azure パブリック ピアリング セットアップと他の ExpressRoute 回線に使用する IP アドレスは BGP セッションで Microsoft にアドバタイズしないでください。 このピアリングでアドバタイズされる NAT IP プレフィックスの長さには制約がありません。
  
  > [!IMPORTANT]
  > Microsoft にアドバタイズされる NAT IP プールはインターネットにアドバタイズしないでください。 他の Microsoft サービスへの接続が切断されます。
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>Microsoft からあなたのネットワークに送信されるトラフィック
* 一部のシナリオでは、あなたのネットワーク内でホストされているサービス エンドポイントへの接続を Microsoft が開始する必要があります。 そのようなシナリオの典型的な例は、Microsoft 365 からあなたのネットワークでホストされている ADFS サービスに接続する場合です。 そのような場合は、ネットワークから Microsoft ピアリングに適切なプレフィックスをリークする必要があります。 
* [非対称ルーティング](expressroute-asymmetric-routing.md)を回避するために、ご利用のネットワーク内のサービス エンドポイント向けのインターネット エッジで Microsoft トラフィックを SNAT 変換する必要があります。 ExpressRoute 経由で受信したルートと一致する宛先 IP を持つ要求 **と応答** は、常に ExpressRoute 経由で送信されます。 要求がインターネット経由で受信され、応答が ExpressRoute 経由で送信される場合に、非対称ルーティングが見られます。 インターネット エッジで受信した Microsoft トラフィックを SNAT 変換すると、応答トラフィックは強制的にインターネット エッジに返されるため、問題は解決します。

![ExpressRoute を使用した非対称ルーティング](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Azure パブリック ピアリングの NAT 要件

> [!NOTE]
> Azure パブリック ピアリングは新しい回線では使用できません。
> 

Azure パブリック ピアリング パスを利用すれば、パブリック IP アドレスで Azure にホストされているすべてのサービスに接続できます。 たとえば、 [ExpessRoute FAQ](expressroute-faqs.md) の一覧にあるサービスや Microsoft Azure で ISV によりホストされているサービスです。 

> [!IMPORTANT]
> パブリック ピアリングでの Microsoft Azure への接続は常にネットワークから Microsoft ネットワークに対して開始されます。 そのため、ExpressRoute 経由で Microsoft Azure サービスからネットワークへのセッションを開始することはできません。 開始しようとすると、これらのアドバタイズされた IP に送信されるパケットでは、ExpressRoute ではなく、インターネットが使用されます。
> 

パブリック ピアリングで Microsoft Azure に向かうトラフィックは有効な IPv4 アドレスに SNAT 変換しないと、Microsoft ネットワークに入れません。 下の図は、上記の要件を満たすように NAT をセットアップするしくみを上のレベルで示しています。

![Microsoft ネットワークに入る前に、NAT を有効なパブリック IPv4 アドレスに SNAT 変換するよう設定する方法の概要図。](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP プールとルート アドバタイズ
トラフィックが有効なパブリック IPv4 アドレスで Azure パブリック ピアリング パスに入っていることを確認する必要があります。 Microsoft はリージョンのルーティング インターネット レジストリ (RIR) またはインターネット ルーティング レジストリ (IRR) に対して IPv4 NAT アドレスの所有権を検証する必要があります。 ピアリングされている AS 番号と NAT に使用されている IP アドレスに基づいて確認されます。 ルーティング レジストリに関する情報については、 [ExpressRoute のルーティングの要件](expressroute-routing.md) のページを参照してください。

このピアリングでアドバタイズされる NAT IP プレフィックスの長さには制約がありません。 NAT プールを監視し、NAT セッションが不足していないことを確認する必要があります。

> [!IMPORTANT]
> Microsoft にアドバタイズされる NAT IP プールはインターネットにアドバタイズしないでください。 他の Microsoft サービスへの接続が切断されます。
> 
> 

## <a name="next-steps"></a>次のステップ
* [ルーティング](expressroute-routing.md)と [QoS](expressroute-qos.md) の要件を参照してください。
* ワークフロー情報については、「 [ExpressRoute 回線のプロビジョニング ワークフローと回線の状態](expressroute-workflows.md)」を参照してください。
* ExpressRoute 接続を構成します。
  
  * [ExpressRoute 回線の作成](expressroute-howto-circuit-portal-resource-manager.md)
  * [ルーティングの構成](expressroute-howto-routing-portal-resource-manager.md)
  * [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-portal-resource-manager.md)

