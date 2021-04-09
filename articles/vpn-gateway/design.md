---
title: Azure VPN Gateway の設計について
description: Azure 仮想ネットワークに接続するように VPN ゲートウェイ トポロジを設計する方法について説明します。
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91397217"
---
# <a name="vpn-gateway-design"></a>VPN Gateway の設計

VPN ゲートウェイ接続ではさまざまな構成が利用できることを理解しておくことが重要です。 また、どの構成が自分のニーズに最適かを判断する必要があります。 以下のセクションでは、次の VPN ゲートウェイ接続に関する設計情報とトポロジの図を確認できます。 図と説明を参考にして、要件を満たす接続トポロジを選択できます。 図は主要なベースライン トポロジを示していますが、図をガイドラインとして使用して、より複雑な構成を構築することもできます。

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>サイト間とマルチサイト (IPsec/IKE VPN トンネル)

### <a name="site-to-site"></a><a name="S2S"></a>サイト間

サイト間 (S2S) VPN ゲートウェイ接続とは、IPsec/IKE (IKEv1 または IKEv2) VPN トンネルを介した接続です。 S2S 接続は、クロスプレミスおよびハイブリッド構成に使用できます。 サイト間接続には、パブリック IP アドレスを割り当てたオンプレミスの VPN デバイスが必要です。 VPN デバイスの選択に関する詳細については、[VPN Gateway に関する FAQ の VPN デバイスに関する項目](vpn-gateway-vpn-faq.md#s2s)を参照してください。

![Azure VPN Gateway サイト間接続の例](./media/design/vpngateway-site-to-site-connection-diagram.png)

VPN Gateway は、1 つのパブリック IP を使用してアクティブ/スタンバイ モードで構成することも、2 つのパブリック IP を使用してアクティブ/アクティブ モードで構成することもできます。 アクティブ/スタンバイ モードでは、一方の IPsec トンネルがアクティブになり、もう一方のトンネルがスタンバイ状態になります。 このセットアップでは、トラフィックはアクティブ トンネルを通過します。このトンネルで何らかの問題が発生した場合、トラフィックはスタンバイ トンネルに切り替わります。 両方の IPsec トンネルが同時にアクティブになり、両方のトンネルを同時に通過するデータ フローがある場合は、アクティブ/アクティブ モードで VPN Gateway を設定することを "*お勧め*" します。 アクティブ/アクティブ モードのもう 1 つの利点は、顧客がより高いスループットを体験できることです。

### <a name="multi-site"></a><a name="Multi"></a>マルチサイト

この種類の接続は、サイト間接続の一種です。 仮想ネットワーク ゲートウェイから複数の VPN 接続を作成し、通常は複数のオンプレミスのサイトに接続します。 複数の接続を使用する場合は、(クラシック VNet を使用する際に動的ゲートウェイと呼ばれる) RouteBased という VPN の種類を使用する必要があります。 各仮想ネットワークに配置できる VPN ゲートウェイは 1 つのみであるため、ゲートウェイを経由するすべての接続は、使用可能な帯域幅を共有します。 この種類の接続は、一般に "マルチサイト" 接続と呼ばれます。

![Azure VPN Gateway マルチサイト接続の例](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>サイト間接続とマルチサイト接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>ポイント対サイト VPN

ポイント対サイト (P2S) VPN ゲートウェイ接続では、個々のクライアント コンピューターから仮想ネットワークへの、セキュリティで保護された接続を作成することができます。 P2S 接続は、クライアント コンピューターから接続を開始することによって確立されます。 このソリューションは、在宅勤務者が自宅や会議室など、遠隔地から Azure VNet に接続する場合に便利です。 P2S VPN は、VNet への接続を必要とするクライアントが数台である場合に、S2S VPN の代わりに使用するソリューションとしても便利です。

S2S 接続とは異なり、P2S 接続には、オンプレミスの公開 IP アドレスまたは VPN デバイスは必要ありません。 P2S 接続と S2S 接続は、両者の構成要件がすべて両立する場合に、同じ VPN ゲートウェイを使って併用することができます。 ポイント対サイト接続について詳しくは、「[ポイント対サイト VPN について](point-to-site-about.md)」を参照してください。

![Azure VPN Gateway ポイント対サイト接続の例](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>P2S で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>VNet 間接続 (IPsec/IKE VPN トンネル)

仮想ネットワークから別の仮想ネットワーク (VNet 対 Vnet) への接続は、VNet からオンプレミス サイトの場所への接続に似ています。 どちらの接続タイプでも、VPN ゲートウェイを使用して、IPsec/IKE を使った安全なトンネルが確保されます。 マルチサイト接続構成と VNet 間通信を組み合わせることもできます。 そのため、クロスプレミス接続と仮想ネットワーク間接続とを組み合わせたネットワーク トポロジを確立することができます。

以下のような VNet を接続できます。

* 同じリージョンまたは異なるリージョンにある
* 同じサブスクリプションまたは異なるサブスクリプションにある 
* 同じデプロイメント モデルまたは異なるデプロイメント モデルである

![Azure VPN Gateway VNet 間接続の例](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>デプロイメント モデル間の接続

Azure には現在、クラシックと Resource Manager という 2 つのデプロイメント モデルがあります。 これまで Azure を使用してきているユーザーであれば、おそらく Azure VM およびクラシック VNet で実行されているインスタンス ロールを利用されていることでしょう。 新しい VM とロール インスタンスが、Resource Manager で作成された VNet 上で実行されていることも考えられます。 VNet 間に接続を作成し、一方の VNet 内のリソースがもう一方の VNet 内のリソースと直接通信できるようにすることが可能です。

### <a name="vnet-peering"></a>VNET ピアリング

仮想ネットワークが特定の要件を満たしていれば、接続の作成に VNET ピアリングを使用することができます。 VNET ピアリングは、仮想ネットワーク ゲートウェイを使用しません。 詳細については、「 [VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)」を参照してください。

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>VNet 間接続で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (プライベート接続)

ExpressRoute を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Microsoft 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。 接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。

ExpressRoute 接続では、公共のインターネットを利用できません。 それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

ExpressRoute 接続では、仮想ネットワーク ゲートウェイが必要な構成の一部として使用されます。 ExpressRoute 接続では、仮想ネットワーク ゲートウェイは "Vpn" ではなく "ExpressRoute" というゲートウェイの種類で構成されます。 ExpressRoute 回線を経由するトラフィックは既定では暗号化されませんが、ExpressRoute 回線経由で暗号化されたトラフィックを送信できるソリューションを作成することができます。 ExpressRoute の詳細については、「 [ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>サイト間と ExpressRoute の共存接続

ExpressRoute は、パブリックなインターネットを経由せずに WAN から Azure などの Microsoft サービスに直接アクセスする、プライベート接続です。 サイト間 VPN トラフィックは、暗号化が施されたうえでパブリック インターネット経由で送受信されます。 同じ仮想ネットワークに対してサイト間 VPN と ExpressRoute 接続が構成可能な場合、いくつかの利点があります。

ExpressRoute 用にセキュリティで保護されたフェールオーバー パスとしてサイト間 VPN を構成したり、サイト間 VPN を使用して、ネットワークの一部ではないものの、ExpressRoute 経由で接続されているサイトに接続したりすることができます。 この構成では、同一の仮想ネットワークに 2 つの仮想ネットワーク ゲートウェイが必要なことに注意してください (1 つのゲートウェイの種類は "Vpn"、もう 1 つのゲートウェイの種類は "ExpressRoute")。

![ExpressRoute と VPN Gateway の共存接続の例](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>S2S と ExpressRoute の共存で使用できるデプロイメント モデルとデプロイ方法

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>高可用性接続

高可用性接続の計画と設計については、[高可用性接続](vpn-gateway-highlyavailable.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

* 詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」を参照してください。

* [VPN Gateway の構成設定](vpn-gateway-about-vpn-gateway-settings.md)について学習します。

* VPN Gateway BGP の考慮事項については、[BGP の概要](vpn-gateway-bgp-overview.md)に関するページをご覧ください。

* [サブスクリプションとサービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)に関するページを参照してください。

* Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
