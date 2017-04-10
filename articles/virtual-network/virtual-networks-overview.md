---
title: Azure Virtual Network | Microsoft Docs
description: "Azure Virtual Network の概念と機能について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 186b8331d2fcfc16bd41eb08badb200e2abf9e30
ms.lasthandoff: 03/25/2017


---
# <a name="azure-virtual-network"></a>Azure Virtual Network

Azure Virtual Network サービスでは、仮想ネットワーク (VNet) を使用して Azure リソースを安全に相互接続することができます。 VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 サブスクリプション専用に Azure クラウドが論理的に分離されています。 VNet は、オンプレミス ネットワークに接続することもできます。 次の図は、Azure Virtual Network サービスの機能の一部を示しています。

![ネットワーク図](./media/virtual-networks-overview/virtual-network-overview.png)

次の Azure Virtual Network の機能の詳細については、各機能をクリックしてください。
- **[分離:](#isolation)** VNet は相互に分離されています。 同じ CIDR アドレス ブロックを使用する開発、テスト、運用環境に個別の VNet を作成できます。 逆に、異なる CIDR アドレス ブロックを使用する複数の VNet を作成し、ネットワークをまとめて接続することもできます。 VNet は、複数のサブネットに分割することができます。 Azure では、VNet に接続されている VM ロール インスタンスと Cloud Services ロール インスタンス用に内部名前解決が用意されています。 必要に応じて、Azure の内部名前解決を使用する代わりに、独自の DNS サーバーを使用するよう VNet を構成できます。
- **[インターネット接続:](#internet)** VNet に接続されているすべての Azure Virtual Machines (VM) ロール インスタンスと Cloud Services ロール インスタンスは、既定でインターネットにアクセスできます。 また、必要に応じて、特定のリソースへの着信アクセスを有効にすることもできます。
- **[Azure リソース接続:](#within-vnet)** クラウド サービスや VM などの Azure リソースを同じ VNet に接続できます。 リソースは、異なるサブネットに存在する場合でも、プライベート IP アドレスを使用して相互に接続できます。 Azure では、サブネット、VNet、オンプレミス ネットワークの間に既定のルーティングを提供しているため、ルートの構成と管理は必要ありません。
- **[VNet 接続:](#connect-vnets)** VNet は相互接続が可能なため、任意の VNet に接続されているリソースは他の VNet 上の任意のリソースと通信できます。
- **[オンプレミス接続:](#connect-on-premises)** VNet は、自身のネットワークと Azure の間のプライベート ネットワーク接続、またはインターネットを介したサイト間 VPN 接続を使用して、オンプレミス ネットワークに接続できます。
- **[トラフィック フィルタリング:](#filtering)** VM ロール インスタンスと Cloud Services ロール インスタンスのネットワーク トラフィックは、着信、送信方向ともに、送信元の IP アドレスとポート、送信先の IP アドレスとポート、およびプロトコルでフィルター処理できます。
- **[ルーティング:](#routing)** 必要に応じて、独自のルートを構成するかネットワーク ゲートウェイ経由で BGP ルートを使用することで、Azure の既定のルーティングを上書きできます。

## <a name = "isolation"></a>ネットワークの分離とセグメント化

各 Azure サブスクリプションと Azure リージョン内に複数の VNet を実装できます。 VNet どうしは分離されています。 各 VNet では、次のことを実行できます。
- パブリックおよびプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する。 Azure は、VNet に接続されているリソースに、割り当てたアドレス空間のプライベート IP アドレスを割り当てます。
- VNet を 1 つ以上のサブネットに分割し、各サブネットに VNet のアドレス空間の一部を割り当てる。
- Azure で提供される名前解決を使用するか、VNet に接続されたリソースで使用するために独自の DNS サーバーを指定する。 VNet での名前解決の詳細については、[VM と Cloud Services の名前解決](virtual-networks-name-resolution-for-vms-and-role-instances.md)に関する記事を参照してください。

## <a name = "internet"></a>インターネットへの接続
VNet に接続されているすべてのリソースは、既定で、インターネットに送信接続されています。 リソースのプライベート IP アドレスは、Azure インフラストラクチャによりプライベート IP アドレスへの送信元ネットワーク アドレス変換 (SNAT) が行われています。 インターネットへの送信接続の詳細については、「[Azure の送信用接続の詳細](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address)」を参照してください。 カスタム ルーティングとトラフィック フィルタリングを実装することで、既定の接続を変更できます。

インターネットから Azure リソースへの着信接続、または SNAT なしでインターネットへの送信接続を行うには、リソースにパブリック IP アドレスを割り当てる必要があります。 パブリック IP アドレスの詳細については、「[パブリック IP アドレス](virtual-network-public-ip-address.md)」を参照してください。

## <a name="within-vnet"></a>Azure リソースの接続
VNet には、仮想マシン (VM)、クラウド サービス、App Service 環境、仮想マシン スケール セットなど、複数の Azure リソースを接続できます。 VM は、ネットワーク インターフェイス (NIC) を経由して VNet 内のサブネットに接続します。 NIC の詳細については、「[ネットワーク インターフェイス](virtual-network-network-interface.md)」を参照してください。

## <a name="connect-vnets"></a>仮想ネットワークの接続

VNet を相互に接続できるため、一方の VNet に接続されているリソースは、VNet をまたいで相互に通信できます。 次のオプションのいずれかまたは両方を使用して、複数の VNet を相互に接続できます。
- **ピアリング:** 同じ Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を可能にします。 複数の VNet の間での帯域幅と待ち時間は、リソースが同じ VNet に接続されている場合と同じです。 ピアリングの詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。
- **VNet 間接続:** 同じまたは異なる Azure の場所内の別の Azure VNet に接続されているリソースの相互接続を有効にします。 ピアリングとは異なり、VNet 間での帯域幅は制限されます。これは、トラフィックが Azure VPN ゲートウェイを通過する必要があるためです。 VNet 間接続を使用した VNet 接続の詳細については、[VNet 間接続の構成](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事を参照してください。

## <a name="connect-on-premises"></a>オンプレミス ネットワークへの接続

オンプレミス ネットワークを VNet に接続するには、次のオプションを組み合わせて使用します。
- **ポイント対サイト仮想プライベート ネットワーク (VPN):** ネットワークに接続されている 1 台の PC と VNet の間で確立されます。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 この接続は、SSTP プロトコルを使用して、PC と VNet の間にインターネット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、ポイント対サイト VPN の待ち時間は予測できません。これは暗号化されています。
- **サイト間 VPN:** VPN デバイスと Azure VPN ゲートウェイの間で確立されます。 この接続の種類を使用すると、承認した任意のオンプレミス リソースが VNet にアクセスできます。 この接続は IPSec/IKE VPN で、オンプレミスのデバイスと Azure VPN ゲートウェイの間にインターケット経由の暗号化された通信を提供します。 トラフィックがインターネットを経由するため、サイト間接続の待ち時間は予測できません。
- **Azure ExpressRoute:** ExpressRoute のパートナーを介して、ネットワークと Azure の間で確立されます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 ExpressRoute 接続の待ち時間は予測可能です。これは、トラフィックがインターネットを経由せず、暗号化されていないためです。

ここまでに説明したすべての接続オプションの詳細については、「[接続トポロジの図](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namediagramsaconnection-topology-diagrams)」を参照してください。

## <a name="filtering"></a>ネットワーク トラフィックのフィルター処理
<!---Get confirmation that a UDR on the gateway subnet is supported. Need to provide some additional info as to the key differences between the two options.--->

次のオプションのいずれかまたは両方を使用して、サブネット間のネットワーク トラフィックをフィルター処理できます。
- **ネットワーク セキュリティ グループ (NSG):** 各 NSG には、送信元と送信先の IP アドレス、ポート、およびプロトコルでトラフィックをフィルター処理できるようにする受信と送信のセキュリティ規則を複数含めることができます。 NSG は VM の各 NIC に適用できます。 NSG は、NIC または他の Azure リソースが接続されているサブネットに適用することもできます。 NSG について詳しくは、[ネットワーク セキュリティ グループ](virtual-networks-nsg.md)に関する記事をご覧ください。
- **ネットワーク仮想アプライアンス (NVA):** NVA とは、ファイアウォールなどのネットワーク機能を実行するソフトウェアが動作している VM です。 入手可能な NVA の一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) を参照してください。 WAN の最適化やその他のネットワーク トラフィック機能を提供する NVA も入手できます。 通常、NVA はユーザー定義ルートまたは BGP ルートで使用されます。 また、NVA を使用して、VNet 間のトラフィックをフィルター処理することもできます。

## <a name="routing"></a>ネットワーク トラフィックのルーティング

Azure では、VNet 内の任意のサブネットに接続されている複数のリソースが相互通信できるようにするルート テーブルが既定で作成されます。 次のオプションのいずれかまたは両方を実装して、Azure によって作成される既定のルートを上書きできます。
- **ユーザー定義ルート:** サブネットごとにトラフィックのルーティング先を制御するルートを含むカスタム ルート テーブルを作成できます。 ユーザー定義ルートについて詳しくは、[ユーザー定義のルート](virtual-networks-udr-overview.md)に関する記事をご覧ください。
- **BGP のルート:** Azure VPN ゲートウェイまたは ExpressRoute 接続を使用して VNet をオンプレミス ネットワークに接続する場合、BGP ルートを VNet に伝達できます。

## <a name="pricing"></a>価格

仮想ネットワーク、サブネット、ルート テーブル、ネットワーク セキュリティ グループには料金がかかりません。 送信インターネット帯域幅の使用量、パブリック IP アドレス、仮想ネットワーク ピアリング、VPN ゲートウェイ、ExpressRoute それぞれには、独自の料金体系が用意されています。 詳細については、[Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network)、[VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway)、[ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) の価格に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

- 「[最初の仮想ネットワークの作成](virtual-network-get-started-vnet-subnet.md)」の手順を実行して、最初の VNet を作成し、その VNet にいくつかの VM を接続します。
- [ポイント対サイト接続の構成](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事の手順を実行して、VNet に対するポイント対サイト接続を作成します。

