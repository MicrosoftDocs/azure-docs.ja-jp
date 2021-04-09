---
title: Azure ExpressRoute プライベート ピアリングのバックアップとしてのサイト間 VPN の使用 |Microsoft Docs
description: このページでは、サイト間 VPN を使用して Azure ExpressRoute プライベート ピアリングをバックアップするためのアーキテクチャ上の推奨事項を示します。
services: networking
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/05/2020
ms.author: duau
ms.openlocfilehash: 752edea8078cf55fc3965bdc7aa9e1b4269dee34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92207922"
---
# <a name="using-s2s-vpn-as-a-backup-for-expressroute-private-peering"></a>ExpressRoute プライベート ピアリングのバックアップとしてのサイト間 VPN の使用

「[ExpressRoute プライベート ピアリングを使用したディザスター リカバリーの設計][DR-PP]」という記事では、ExpressRoute プライベート ピアリング接続のためのバックアップ接続ソリューションの必要性と、その目的で geo 冗長 ExpressRoute 回線を使用する方法について説明しました。 この記事では、ExpressRoute プライベート ピアリングのバックアップとしてサイト間 (S2S) VPN を活用および維持する方法について説明します。 

geo 冗長 ExpressRoute 回線とは異なり、ExpressRoute と VPN のディザスター リカバリーの組み合わせはアクティブ/パッシブ モードでのみ使用できます。 パッシブ モードでバックアップ ネットワーク接続を使用することの主な課題は、パッシブ接続がプライマリ接続と共に失敗することが多いことです。 パッシブ接続のエラーの一般的な原因は、アクティブなメンテナンスが不足していることです。 したがって、この記事では、ExpressRoute プライベート ピアリングをバックアップするサイト間 VPN 接続を検証し、アクティブに維持する方法に焦点を当てます。

>[!NOTE] 
>ExpressRoute と VPN の両方を介して特定のルートをアドバタイズする場合、Azure は ExpressRoute 経由のルーティングを優先します。  
>

この記事では、Azure の観点と顧客側のネットワーク エッジの観点の両方から接続を検証する方法について説明します。 どちらの側からも検証できるということは、Microsoft ネットワーク エンティティとピアリングする顧客側ネットワーク デバイスを管理するかどうかに関係なく役立ちます。 

## <a name="example-topology"></a>トポロジの例

このセットアップでは、オンプレミス ネットワークが ExpressRoute 回線とサイト間 VPN 接続の両方を介して Azure のハブ VNet に接続されています。 次の図に示すように、Azure のハブ VNet はスポーク VNet にピアリングされています。

![1][1]

このセットアップでは、ExpressRoute 回線は、オンプレミスにある "顧客のエッジ" (CE) ルーターのペアで終端されています。 オンプレミス LAN は、リーダーフォロワー モードで動作するファイアウォールのペアを介して CE ルーターに接続されています。 サイト間 VPN は、ファイアウォールで直接終端されています。

次の表に、トポロジの主要な IP プレフィックスを示します。

| **エンティティ** | **プレフィックス** |
| --- | --- |
| オンプレミス LAN | 10.1.11.0/25 |
| Azure のハブ VNet | 10.17.11.0/25 |
| Azure のスポーク VNet | 10.17.11.128/26 |
| オンプレミスのテスト サーバー | 10.1.11.10 |
| スポーク VNet のテスト VM | 10.17.11.132 |
| ExpressRoute プライマリ接続 p2p サブネット | 192.168.11.16/30 |
| ExpressRoute セカンダリ接続 p2p サブネット | 192.168.11.20/30 |
| VPN ゲートウェイ プライマリ BGP ピア IP | 10.17.11.76 |
| VPN ゲートウェイ セカンダリ BGP ピア IP | 10.17.11.77 |
| オンプレミス ファイアウォール VPN BGP ピア IP | 192.168.11.88 |
| ファイアウォールに向かうプライマリ CE ルーター i/f の IP | 192.168.11.0/31 |
| プライマリ CE ルーターに向かうファイアウォール i/f の IP | 192.168.11.1/31 |
| ファイアウォールに向かうセカンダリ CE ルーター i/f の IP | 192.168.11.2/31 |
| セカンダリ CE ルーターに向かうファイアウォール i/f の IP | 192.168.11.3/31 |


次の表に、トポロジの ASN を示します。

| **自律システム** | **ASN** |
| --- | --- |
| オンプレミス | 65020 |
| Microsoft Enterprise Edge | 12076 |
| 仮想ネットワーク GW (ExR) | 65515 |
| 仮想ネットワーク GW (VPN) | 65515 |

## <a name="high-availability-without-asymmetricity"></a>非対称性のない高可用性

### <a name="configuring-for-high-availability"></a>高可用性を実現するための構成

[ExpressRoute およびサイト間の共存接続を構成する][Conf-CoExist]方法に関する記事では、共存する ExpressRoute 回線とサイト間 VPN 接続を構成する方法について説明しています。 「[ExpressRoute を使用した高可用性のための設計][HA]」で説明したように、ExpressRoute の高可用性を向上させるために、このセットアップではエンドポイントまでのすべてのネットワーク冗長性を維持します (単一障害点を回避します)。 また、ExpressRoute 回線のプライマリとセカンダリの両方の接続は、両方の接続を介してオンプレミスのプレフィックスを同じようにアドバタイズすることで、アクティブ/アクティブ モードで動作するように構成されます。 

ExpressRoute 回線のプライマリ接続を介したプライマリ CE ルーターのオンプレミス ルートのアドバタイズを以下に示します (Junos コマンド):

```console
user@SEA-MX03-01> show route advertising-protocol bgp 192.168.11.18 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

ExpressRoute 回線のセカンダリ接続を介したセカンダリ CE ルーターのオンプレミス ルートのアドバタイズを以下に示します (Junos コマンド):

```console
user@SEA-MX03-02> show route advertising-protocol bgp 192.168.11.22 

Cust11.inet.0: 8 destinations, 8 routes (7 active, 0 holddown, 1 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

バックアップ接続の高可用性を向上させるために、サイト間 VPN もアクティブ/アクティブ モードで構成されます。 Azure VPN Gateway 構成を以下に示します。 VPN 構成の一部として、ゲートウェイの BGP ピア IP アドレス (10.17.11.76 と 10.17.11.77) も示されていることに注意してください。

![2][2]

オンプレミス ルートは、ファイアウォールによって、VPN ゲートウェイのプライマリおよびセカンダリ BGP ピアにアドバタイズされます。 ルート アドバタイズは次のようになります (Junos)。

```console
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.76 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I

{primary:node0}
user@SEA-SRX42-01> show route advertising-protocol bgp 10.17.11.77    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
* 10.1.11.0/25            Self                                    I
```

>[!NOTE] 
>サイト間 VPN をアクティブ/アクティブ モードで構成すると、ディザスター リカバリー バックアップ ネットワーク接続に高可用性が提供されるだけでなく、バックアップ接続のスループットも向上します。 言い換えると、アクティブ/アクティブ モードでのサイト間 VPN の構成は、基になる複数のトンネルが強制的に作成されるため、推奨されます。
>

### <a name="configuring-for-symmetric-traffic-flow"></a>対称トラフィック フローを実現するための構成

特定のオンプレミス ルートが ExpressRoute とサイト間 VPN の両方を介してアドバタイズされる場合、Azure が ExpressRoute パスを優先することは既に述べました。 共存する ExpressRoute よりもサイト間 VPN のパスを Azure が優先するように強制するには、VPN 接続経由でより具体的なルート (より大きなサブネット マスクを持つ、より長いプレフィックス) をアドバタイズする必要があります。 ここでの目的は、VPN 接続をバックアップ専用として使用することです。 したがって、Azure の既定のパス選択の動作はここでの目的に一致しています。 

オンプレミスから Azure へのトラフィックも、サイト間 VPN よりも ExpressRoute パスを優先するようにする必要があります。 このオンプレミス セットアップでの CE ルーターとファイアウォールの既定のローカル設定は 100 です。 そのため、ExpressRoute プライベート ピアリングを介して受信するルートのローカル設定を 100 より大きく (150 などに) 構成することにより、Azure 宛てのトラフィックが、定常状態で ExpressRoute 回線を優先するようにすることができます。

ExpressRoute 回線のプライマリ接続を終端させるプライマリ CE ルーターの BGP 構成を次に示します。 iBGP セッション経由でアドバタイズされたルートのローカル設定の値が 150 に構成されていることに注意してください。 同様に、ExpressRoute 回線のセカンダリ接続を終端させるセカンダリ CE ルーターのローカル設定も、150 に構成されていることを確認する必要があります。

```console
user@SEA-MX03-01> show configuration routing-instances Cust11
description "Customer 11 VRF";
instance-type virtual-router;
interface xe-0/0/0:0.110;
interface ae0.11;
protocols {
  bgp {
    group ibgp {
        type internal;
        local-preference 150;
        neighbor 192.168.11.1;
    }
    group ebgp {
        peer-as 12076;
        bfd-liveness-detection {
            minimum-interval 300;
            multiplier 3;
        }
        neighbor 192.168.11.18;
    }
  }
}
```

オンプレミス ファイアウォールのルーティング テーブルにより、Azure 宛てのオンプレミス トラフィックの優先パスが、定常状態で ExpressRoute 経由であることが裏付けられます (以下を参照)。

```console
user@SEA-SRX42-01> show route table Cust11.inet.0 10.17.11.0/24

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.17.11.0/25      *[BGP/170] 2d 00:34:04, localpref 150
                      AS path: 12076 I, validation-state: unverified
                    > to 192.168.11.0 via reth1.11
                      to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                    [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
10.17.11.76/32     *[Static/5] 2d 21:12:16
                     > via st0.118
10.17.11.77/32     *[Static/5] 2d 00:41:56
                    > via st0.119
10.17.11.128/26    *[BGP/170] 2d 00:34:04, localpref 150
                       AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.0 via reth1.11
                       to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 00:34:01, localpref 150
                      AS path: 12076 I, validation-state: unverified
                     > to 192.168.11.2 via reth2.11
                    [BGP/170] 2d 21:12:13, localpref 100, from 10.17.11.76
                       AS path: 65515 I, validation-state: unverified
                    > via st0.118
                     [BGP/170] 2d 00:41:51, localpref 100, from 10.17.11.77
                       AS path: 65515 I, validation-state: unverified
                     > via st0.119
```

上記のルーティング テーブルで、ハブとスポークの VNet ルート (10.17.11.0/25 と 10.17.11.128/26) では、VPN 接続よりも ExpressRoute 回線が優先されることがわかります。 192.168.11.0 と192.168.11.2 は、CE ルーターに向かうファイアウォール インターフェイスの IP です。

## <a name="validation-of-route-exchange-over-s2s-vpn"></a>サイト間 VPN 経由のルート交換の検証

この記事の前半で、VPN ゲートウェイのプライマリおよびセカンダリ BGP ピアに対するファイアウォールのオンプレミス ルート アドバタイズを検証しました。 ここではさらに、VPN ゲートウェイのプライマリおよびセカンダリ BGP ピアからファイアウォールが受信した Azure ルートを確認します。

```console
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.76 table Cust11.inet.0 

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.76                             65515 I
  10.17.11.128/26         10.17.11.76                             65515 I

{primary:node0}
user@SEA-SRX42-01> show route receive-protocol bgp 10.17.11.77 table Cust11.inet.0    

Cust11.inet.0: 14 destinations, 21 routes (14 active, 0 holddown, 0 hidden)
  Prefix                  Nexthop              MED     Lclpref    AS path
  10.17.11.0/25           10.17.11.77                             65515 I
  10.17.11.128/26         10.17.11.77                             65515 I
```

同様に、Azure VPN ゲートウェイが受信したオンプレミスのネットワーク ルート プレフィックスを確認します。 

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayLearnedRoute -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn | where {$_.Network -eq "10.1.11.0/25"} | select Network, NextHop, AsPath, Weight

Network      NextHop       AsPath      Weight
-------      -------       ------      ------
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.76   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 192.168.11.88 65020        32768
10.1.11.0/25 10.17.11.77   65020        32768
10.1.11.0/25 10.17.11.69   12076-65020  32769
10.1.11.0/25 10.17.11.69   12076-65020  32769
```

前述のように、VPN ゲートウェイには、VPN ゲートウェイのプライマリおよびセカンダリの両方の BGP ピアによって受信されたルートがあります。 また、プライマリおよびセカンダリの ExpressRoute 接続を介して受信されたルートも確認できます (AS-path の前に 12076 が付加されたもの)。 VPN 接続を介して受信されたルートを確認するには、接続のオンプレミス BGP ピア IP を把握している必要があります。 検討対象のこのセットアップでは、これは 192.168.11.88 であり、そこから受信されたルートが表示されています。

次に、Azure VPN ゲートウェイによってオンプレミスのファイアウォール BGP ピア (192.168.11.88) にアドバタイズされたルートを検証します。

```powershell
PS C:\Users\user> Get-AzVirtualNetworkGatewayAdvertisedRoute -Peer 192.168.11.88 -ResourceGroupName SEA-Cust11 -VirtualNetworkGatewayName SEA-Cust11-VNet01-gw-vpn |  select Network, NextHop, AsPath, Weight

Network         NextHop     AsPath Weight
-------         -------     ------ ------
10.17.11.0/25   10.17.11.76 65515       0
10.17.11.128/26 10.17.11.76 65515       0
10.17.11.0/25   10.17.11.77 65515       0
10.17.11.128/26 10.17.11.77 65515       0
```

ルート交換が表示されない場合は、接続エラーを示しています。 VPN 接続のトラブルシューティングのヘルプについては、「[トラブルシューティング:Azure サイト間 VPN が動作を停止して接続できない][VPN Troubleshoot]」を参照してください。

## <a name="testing-failover"></a>フェールオーバーのテスト

VPN 接続を介した正常なルート交換 (コントロール プレーン) を確認したので、ExpressRoute 接続から VPN 接続へのトラフィックの切り替え (データ プレーン) を行います。 

>[!NOTE] 
>運用環境でのフェールオーバーのテストは、サービスを停止させる可能性があるため、スケジュールされたネットワーク メンテナンスの作業期間中に行う必要があります。
>

トラフィックの切り替えを行う前に、オンプレミスのテスト サーバーからスポーク VNet 内のテスト VM に至るセットアップ内の現在のパスをトレース ルートしてみましょう。

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2    <1 ms    <1 ms    11 ms  192.168.11.0
  3    <1 ms    <1 ms    <1 ms  192.168.11.18
  4     *        *        *     Request timed out.
  5     6 ms     6 ms     5 ms  10.17.11.132

Trace complete.
```

このセットアップのプライマリおよびセカンダリの ExpressRoute ポイントツーポイント接続サブネットは、それぞれ 192.168.11.16/30 と 192.168.11.20/30 になります。 上記のトレース ルートでは、ステップ 3 で、プライマリ MSEE のインターフェイス IP である192.168.11.18 に到達していることがわかります。 MSEE インターフェイスが存在することにより、予想通り現在のパスが ExpressRoute 経由であることが裏付けられます。

「[ExpressRoute 回線のピアリングをリセットする][RST]」で報告されているように、次の PowerShell コマンドを使用して、ExpressRoute 回線のプライマリとセカンダリの両方のピアリングを無効にします。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Disabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

フェールオーバーの切り替え時間は BGP のコンバージェンス時間によって異なります。 このセットアップでは、フェールオーバーの切り替えにかかる時間は数秒 (10 秒未満) です。 切り替え後に traceroute を再び実行すると、次のパスが表示されます。

```console
C:\Users\PathLabUser>tracert 10.17.11.132

Tracing route to 10.17.11.132 over a maximum of 30 hops

  1    <1 ms    <1 ms    <1 ms  10.1.11.1
  2     *        *        *     Request timed out.
  3     6 ms     7 ms     9 ms  10.17.11.132

Trace complete.
```

traceroute の結果により、サイト間 VPN 経由のバックアップ接続がアクティブであり、プライマリとセカンダリの両方の ExpressRoute 接続に障害が発生した場合にサービスの継続性を確保できることが裏付けられます。 フェールオーバー テストを完了するには、次のコマンド セットを使用して、ExpressRoute 接続を有効に戻し、トラフィック フローを正常化します。

```powershell
$ckt = Get-AzExpressRouteCircuit -Name "expressroute name" -ResourceGroupName "SEA-Cust11"
$ckt.Peerings[0].State = "Enabled"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

トラフィックが ExpressRoute に戻されたことを確認するには、traceroute を再び実行し、トラフィックが ExpressRoute プライベート ピアリングを通過することを確認します。

## <a name="next-steps"></a>次のステップ

ExpressRoute は、Microsoft ネットワーク内で単一障害点のない高可用性を実現するように設計されています。 ただし、ExpressRoute 回線は、1 つの地理的リージョンおよび 1 つのサービス プロバイダーに限定されています。 サイト間 VPN は、ExpressRoute 回線に対する優れたディザスター リカバリー パッシブ バックアップ ソリューションです。 信頼できるパッシブ バックアップ接続ソリューションを実現するには、パッシブ構成の定期的なメンテナンスと接続の定期的な検証を行うことが重要です。 VPN 構成を陳腐化させないようにし、この記事に記載されている検証とフェールオーバーのテスト手順をメンテナンス期間中に定期的に (たとえば四半期ごとに) 繰り返すことが不可欠です。

VPN Gateway メトリックに基づいて監視とアラートを有効にするには、「[VPN Gateway メトリックにアラートを設定する][VPN-alerts]」を参照してください。

ExpressRoute の障害後に BGP コンバージェンスを迅速に行うには、[ExpressRoute 経由の BFD の構成][BFD]を行います。

<!--Image References-->
[1]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/topology.png "検討中のトポロジ"
[2]: ./media/use-s2s-vpn-as-backup-for-expressroute-privatepeering/vpn-gw-config.png "VPN GW 構成"

<!--Link References-->
[DR-PP]: ./designing-for-disaster-recovery-with-expressroute-privatepeering.md
[Conf-CoExist]: ./expressroute-howto-coexist-resource-manager.md
[HA]: ./designing-for-high-availability-with-expressroute.md
[VPN Troubleshoot]: ../vpn-gateway/vpn-gateway-troubleshoot-site-to-site-cannot-connect.md
[VPN-alerts]: ../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md
[BFD]: ./expressroute-bfd.md
[RST]: ./expressroute-howto-reset-peering.md