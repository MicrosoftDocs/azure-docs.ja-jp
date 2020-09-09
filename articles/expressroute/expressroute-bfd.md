---
title: 'Azure ExpressRoute: BFD の構成'
description: この記事では、ExpressRoute 回線のプライベート ピアリング経由で BFD (Bidirectional Forwarding Detection) を構成する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: duau
ms.openlocfilehash: db2f45da0193ac648d58c0be9773f36e542ed917
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397612"
---
# <a name="configure-bfd-over-expressroute"></a>ExpressRoute 経由の BFD の構成

ExpressRoute は、プライベート ピアリングと Microsoft ピアリングの両方で BFD (Bidirectional Forwarding Detection) をサポートしています。 ExpressRoute 経由の BFD を有効にすることにより、Microsoft Enterprise Edge (MSEE) デバイスと ExpressRoute 回線を終端するルーター (CE/PE) の間のリンク障害の検出を速めることができます。 顧客のエッジ ルーティング デバイスまたはパートナーのエッジ ルーティング デバイス経由の ExpressRoute を終端できます (マネージド レイヤー 3 接続サービスを選択した場合)。 このドキュメントでは、BFD の必要性と、ExpressRoute 経由の BFD を有効にする方法について説明します。

## <a name="need-for-bfd"></a>BFD の必要性

次の図は、ExpressRoute 回線経由の BFD を有効にする利点を示しています。[![1]][1]

ExpressRoute 回線は、レイヤー 2 接続またはマネージド レイヤー 3 接続のいずれかで有効にできます。 いずれの場合も、ExpressRoute 接続パスに 1 つ以上のレイヤー 2 デバイスが存在するときは、上にある BGP に、パスのリンク障害を検出する責任があります。

MSEE デバイスでは、通常、BGP のキープアライブとホールド時間はそれぞれ 60 秒と 180 秒に設定されています。 したがって、リンク障害が発生してから、リンク障害が検出され、トラフィックが別の接続に切り替わるまでの時間は最大で 3 分です。

BGP タイマーを制御するには、顧客のエッジ ピアリング デバイスで BGP のキープアライブとホールド時間を低く設定します。 2 つのピアリング デバイス間で BGP タイマーが一致しない場合、ピア間の BGP セッションでは、低い方のタイマー値が使用されます。 設定できる BGP キープアライブの最短時間は 3 秒で、ホールド時間は数十秒です。 ただし、プロトコルの処理負荷が高くなるため、BGP タイマーを積極的に設定することはお勧めできません。

このシナリオでは、BFD が役立ちます。 BFD では、1 秒未満の時間間隔で低オーバーヘッドのリンク障害検出が可能です。 


## <a name="enabling-bfd"></a>BFD の有効化

MSEE では、新しく作成されたすべての ExpressRoute プライベート ピアリング インターフェイスで、BFD が既定で構成されています。 そのため、BFD を有効にするには、CE/PE (両方がプライマリおよびセカンダリ デバイスにあります) で BFD を構成するだけで済みます。 BFD の構成は、2 つの手順から成るプロセスです。インターフェイス上で BFD を構成した後、それを BGP セッションにリンクする必要があります。

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

BFD ピア間では、2 つのピアのうち遅い方のピアによって送信レートが決まります。 MSEE の BFD 送信/受信間隔は、300 ミリ秒に設定されています。 特定のシナリオでは、間隔に 750 ミリ秒という大きな値を設定できます。 大きな値を設定すると、これらの間隔を強制的に大きくできますが、短くすることはできません。

>[!NOTE]
>geo 冗長 ExpressRoute 回線を構成しているか、またはサイト間 IPSec VPN 接続をバックアップとして使用している場合は、BFD を有効にすると ExpressRoute 接続障害の後のフェールオーバーがより迅速になります。 
>

## <a name="next-steps"></a>次の手順

詳細やヘルプについては、次のリンク先を確認してください。

- [ExpressRoute 回線の作成と変更][CreateCircuit]
- [ExpressRoute 回線のルーティングの作成と変更を行う][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD によりリンク障害で差し引かれる時間が短縮"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






