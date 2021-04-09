---
title: 'Azure ExpressRoute: BFD の構成'
description: この記事では、ExpressRoute 回線のプライベート ピアリング経由で BFD (Bidirectional Forwarding Detection) を構成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 254f5909e7ed8db4dc18ade2677a3213b268cf41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511265"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute 経由の BFD の構成

ExpressRoute は、プライベート ピアリングと Microsoft ピアリングの両方で BFD (Bidirectional Forwarding Detection) をサポートしています。 ExpressRoute 経由で BFD を有効にすると、Microsoft Enterprise edge (MSEE) デバイスと、ExpressRoute 回線が構成されているルーター (CE/PE) との間のリンク障害の検出速度が向上します。 ご使用のエッジ ルーティング デバイスまたはパートナーのエッジ ルーティング デバイス経由で ExpressRoute を構成できます (マネージド レイヤー 3 接続サービスを選択した場合)。 このドキュメントでは、BFD の必要性と、ExpressRoute 経由の BFD を有効にする方法について説明します。

## <a name="need-for-bfd"></a>BFD の必要性

次の図は、ExpressRoute 回線経由の BFD を有効にする利点を示しています。[![1]][1]

ExpressRoute 回線は、レイヤー 2 接続またはマネージド レイヤー 3 接続のいずれかで有効にできます。 どちらの場合も、ExpressRoute 接続パスに複数のレイヤー 2 デバイスが存在するときは、上にある BGP セッションに、パスのリンク障害を検出する責任があります。

MSEE デバイスでは、通常、BGP のキープアライブとホールド時間はそれぞれ 60 秒と 180 秒に設定されています。 このため、リンクの障害が発生した場合、リンク障害を検出し、トラフィックが別の接続に切り替わるまで最大で 3 分かかる場合があります。

BGP タイマーを制御するには、エッジ ピアリング デバイスで BGP のキープアライブとホールド時間を低く設定します。 2 つのピアリング デバイス間で BGP タイマーが同じでない場合は、短い方の時間値を使用して BGP セッションが確立されます。 設定できる BGP キープアライブの最短時間は 3 秒で、ホールド時間は 10 秒です。 ただし、プロトコルで大量のプロセスが集中的に消費されるため、BGP タイマーをあまりに低く設定することはお勧めできません。

このシナリオでは、BFD が役立ちます。 BFD では、1 秒未満の時間間隔で低オーバーヘッドのリンク障害検出が可能です。 


## <a name="enabling-bfd"></a>BFD の有効化

MSEE では、新しく作成されたすべての ExpressRoute プライベート ピアリング インターフェイスで、BFD が既定で構成されています。 そのため、BFD を有効にするには、プライマリおよびセカンダリ デバイスの両方で BFD を構成するだけで済みます。 BFD は 2 段階のプロセスで構成します。 インターフェイス上で BFD を構成し、次に、それを BGP セッションにリンクします。

CE/PE (Cisco IOS XE を使用) の構成の例を次に示します。 

```console
interface TenGigabitEthernet2/0/0.150
   description private peering to Azure
   encapsulation dot1Q 15 second-dot1q 150
   ip vrf forwarding 15
   ip address 192.168.15.17 255.255.255.252
   bfd interval 300 min_rx 300 multiplier 3


router bgp 65020
   address-family ipv4 vrf 15
      network 10.1.15.0 mask 255.255.255.128
      neighbor 192.168.15.18 remote-as 12076
      neighbor 192.168.15.18 fall-over bfd
      neighbor 192.168.15.18 activate
      neighbor 192.168.15.18 soft-reconfiguration inbound
   exit-address-family
```

>[!NOTE]
>既にある既存のプライベート ピアリングで BFD を有効にするには、そのピアリングをリセットする必要があります。 「[ExpressRoute 回線のピアリングをリセットする][ResetPeering]」を参照してください。
>

## <a name="bfd-timer-negotiation"></a>BFD タイマー ネゴシエーション

BFD ピア間では、2 つのピアのうち遅い方のピアによって送信レートが決まります。 MSEE の BFD 送信/受信間隔は、300 ミリ秒に設定されています。 特定のシナリオでは、間隔に 750 ミリ秒という大きな値を設定できます。 大きな値を設定することで、これらの間隔を強制的に長くすることはできますが、短くすることはできません。

>[!NOTE]
>geo 冗長 ExpressRoute 回線を構成しているか、またはサイト間 IPSec VPN 接続をバックアップとして使用している場合、 BFD を有効にすると、ExpressRoute 接続障害の後のフェールオーバーがより迅速になります。 
>

## <a name="next-steps"></a>次の手順

詳細やヘルプについては、次のリンク先を確認してください。

- [ExpressRoute 回線の作成と変更][CreateCircuit]
- [ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD によりリンク障害で差し引かれる時間が短縮"

<!--Link References-->
[CreateCircuit]: ./expressroute-howto-circuit-portal-resource-manager.md
[CreatePeering]: ./expressroute-howto-routing-portal-resource-manager.md
[ResetPeering]: ./expressroute-howto-reset-peering.md