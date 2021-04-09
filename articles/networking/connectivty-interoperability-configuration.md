---
title: 'Azure バックエンド接続機能の相互運用性: 構成の詳細 |Microsoft Docs'
description: この記事では、Azure における ExpressRoute、サイト間 VPN、仮想ネットワーク ピアリングの相互運用性を分析する際に使用できるテスト セットアップの構成情報について説明します。
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 50fd5641c61d08939eca1f2cbafb1d077254e37d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233272"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Azure バックエンド接続機能の相互運用性: テスト構成の詳細

この記事では､[テスト セットアップ][Setup]の構成の詳細について説明します。 テスト セットアップは、Azure のネットワーク サービスがコントロール プレーン レベルとデータ プレーン レベルでどのように相互運用されるかを分析するのに役立ちます。

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet ピアリングを使用したスポーク VNet 接続

次の図は、スポーク仮想ネットワーク (VNet) の Azure Virtual Network ピアリングの詳細を示したものです。 2 つの VNet 間のピアリングを設定する方法については、[VNet ピアリングの管理][VNet-Config]に関する記事をご覧ください。 スポーク VNet で、ハブ VNet に接続されたゲートウェイを使用する場合は､ **[リモート ゲートウェイを使用する]** を選択します。

[![1]][1]

次の図は､ハブ VNet の VNet ピアリングの詳細を示したものです。 ハブ VNet で、スポーク VNet でのハブのゲートウェイの使用を許可するには、 **[ゲートウェイ転送を許可する]** を選択します。

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>サイト間 VPN を使用したブランチ VNet 接続

Azure VPN Gateway で VPN ゲートウェイを使用して、ハブ VNet とブランチ VNet の間のサイト間 VPN 接続を設定します。 既定では、VPN ゲートウェイと Azure ExpressRoute ゲートウェイでは、プライベート自律システム番号 (ASN) の値として **65515** が使用されます。 VPN Gateway の ASN 値は変更できます。 テスト セットアップでは、ハブ VNet とブランチ VNet 間の eBGP ルーティングをサポートするために、ブランチ VNet VPN ゲートウェイの ASN 値が **65516** に変更されています。


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>ExpressRoute とサイト間 VPN を使用したオンプレミスの Location 1 接続

### <a name="expressroute-1-configuration-details"></a>ExpressRoute 1 の構成の詳細

次の図は､オンプレミスの Location 1 カスタマー エッジ (CE) ルーターに対する Azure Region 1 ExpressRoute 回線の構成を示したものです。

[![4]][4]

次の図は､ExpressRoute 1 回線とハブ VNet 間の接続の構成を示したものです。

[![5]][5]

次に示すのは、ExpressRoute プライベート ピアリング接続用のプライマリ CE ルーターの構成です (テスト セットアップでは、Cisco ASR1000 ルーターが CE ルーターとして使用されています)。オンプレミス ネットワークを Azure に接続するために、サイト間 VPN と ExpressRoute 回線が並列に構成されている場合､Azure の既定では、ExpressRoute 回線が優先されます。 非対称ルーティングを回避するため、オンプレミス ネットワークでも、サイト間 VPN 接続より ExpressRoute 接続を優先するようにしてください。 次の構成では、BGP の **local-preference** 属性を使用して優先度を設定しています。

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>サイト間 VPN 構成の詳細

次に示すのは､サイト間 VPN 接続用のプライマリ CE ルーターの構成です。

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>ExpressRoute を使用したオンプレミスの Location 2 接続

オンプレミスの Location 2 に近接した場所にある 2 つ目の ExpressRoute 回線では､オンプレミスの Location 2 をハブ VNet に接続します。 次の図は、2 つ目の ExpressRoute の構成を示したものです。

[![6]][6]

次の図は､2 つ目の ExpressRoute 回線とハブ VNet 間の接続の構成を示したものです。

[![7]][7]

ExpressRoute 1 では､ハブ VNet とオンプレミスの Location 1 の両方を、別の Azure リージョンにあるリモート VNet に接続します。

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute とサイト間 VPN 接続の併用

###  <a name="site-to-site-vpn-over-expressroute"></a>ExpressRoute 上のサイト間 VPN

ExpressRoute Microsoft ピアリングを使用してサイト間 VPN を構成することにより、オンプレミス ネットワークと Azure VNet の間でプライベートにデータを交換することができます。 この構成を使用すれば、機密性、信頼性、整合性が確保されたデータ交換を実現できます。 また、このデータ交換には、アンチリプレイ対策も講じられています。 ExpressRoute Microsoft ピアリングを使用してトンネル モードでサイト間 IPsec VPN を構成する方法の詳細については、[ExpressRoute Microsoft ピアリングでのサイト間 VPN][S2S-Over-ExR] に関するページを参照してください。 

Microsoft ピアリングを使用するサイト間 VPN 構成に伴う主な制限はスループットです。 IPsec トンネル上では、VPN ゲートウェイの容量によりスループットが制限されます。 VPN ゲートウェイのスループットは、ExpressRoute のスループットよりも低くなります。 このシナリオでは、高いセキュリティが要求されるトラフィックには IPsec トンネルを使用し、それ以外のすべてのトラフィックにはプライベート ピアリングを使用すると、ExpressRoute の帯域幅使用率の最適化に役立ちます。

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>ExpressRoute の安全なフェールオーバー パスとしてのサイト間 VPN

ExpressRoute は、高可用性を確保する冗長回線ペアの役割を果たします。 異なる Azure リージョンで geo 冗長 ExpressRoute 接続を構成することができます。 また、このテスト セットアップで紹介したとおり、Azure リージョン内でサイト間 VPN を使用して、ExpressRoute 接続のフェールオーバー パスを作成することができます。 ExpressRoute とサイト間 VPN の両方に同じプレフィックスがアドバタイズされた場合、Azure では ExpressRoute が優先されます。 ExpressRoute とサイト間 VPN の間で非対称なルーティングを回避するために、オンプレミス ネットワークも、ExpressRoute 接続をサイト間 VPN 接続よりも優先して使用するように構成する必要があります。

ExpressRoute とサイト間 VPN が共存する接続を構成する方法の詳細については、[ExpressRoute とサイト間の共存][ExR-S2S-CoEx]に関するページを参照してください。

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>バックエンド接続をスポーク VNet とブランチの場所に拡張する

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>VNet ピアリングを使用したスポーク VNet 接続

ハブ アンド スポークの VNet アーキテクチャは、広く利用されています。 ハブは Azure 内の VNet であり、スポーク VNet とオンプレミス ネットワークの間の接続の中央拠点として機能します。 スポークはハブとピアリングする VNet であり、ワークロードの分離に利用できます。 オンプレミスのデータセンターとハブとの間のトラフィックは、ExpressRoute または VPN 接続を経由して送信されます。 このアーキテクチャの詳細については、「[Azure にハブスポーク ネットワーク トポロジを実装する][Hub-n-Spoke]」を参照してください。

リージョン内の VNet ピアリングでは、スポーク VNet はハブ VNet ゲートウェイ (VPN ゲートウェイと ExpressRoute ゲートウェイの両方) を使用して、リモート ネットワークと通信を行うことができます。

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>サイト間 VPN を使用したブランチ VNet 接続

異なるリージョンにあるブランチ VNet と、オンプレミス ネットワークを、ハブ VNet を介して相互に通信させたい場合があります。 この構成に対する Azure のネイティブ ソリューションは、VPN を使用したサイト間 VPN 接続です。 その他に、ネットワーク仮想アプライアンス (NVA) をハブでのルーティングに使用する方法もあります。

詳細については、「[VPN ゲートウェイとは][VPN]」および[高可用性 NVA のデプロイ][Deploy-NVA]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

テスト セットアップの[コントロール プレーン分析][Control-Analysis]と、トポロジ内のさまざまな VNet や VLAN のビューを確認します。

テスト セットアップの[データ プレーン分析][Data-Analysis]と Azure ネットワーク監視機能のビューを確認します。

「[ExpressRoute の FAQ][ExR-FAQ]」を参照して以下を行います。
-   ExpressRoute ゲートウェイに接続できる ExpressRoute 回線の数について学習する。
-   ExpressRoute 回線に接続できる ExpressRoute ゲートウェイの数について学習する。
-   ExpressRoute のその他のスケールの制限について学習する。


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "スポーク VNet の VNet ピアリング"
[2]: ./media/backend-interoperability/HubVNet-peering.png "ハブ VNet の VNet ピアリング"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "ブランチ VNet の VPN Gateway の構成"
[4]: ./media/backend-interoperability/ExR1.png "ExpressRoute 1 の構成"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "ExpressRoute 1 からハブ VNet ExR ゲートウェイへの接続の構成"
[6]: ./media/backend-interoperability/ExR2.png "ExpressRoute 2 の構成"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "ExpressRoute 2 からハブ VNet ExR ゲートウェイへの接続の構成"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "ExpressRoute 2 からリモート VNet ExR ゲートウェイへの接続の構成"

<!--Link References-->
[Setup]: ./connectivty-interoperability-preface.md
[ExpressRoute]: ../expressroute/expressroute-introduction.md
[VPN]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[VNet]: ../virtual-network/tutorial-connect-virtual-networks-portal.md
[Control-Analysis]: ./connectivty-interoperability-control-plane.md
[Data-Analysis]: ./connectivty-interoperability-data-plane.md
[ExR-FAQ]: ../expressroute/expressroute-faqs.md
[S2S-Over-ExR]: ../expressroute/site-to-site-vpn-over-microsoft-peering.md
[ExR-S2S-CoEx]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[Hub-n-Spoke]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: /azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: ../virtual-network/virtual-network-manage-peering.md