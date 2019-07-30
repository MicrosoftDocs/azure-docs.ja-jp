---
title: Azure Virtual Network | Microsoft Docs
description: Azure Virtual Network の概念と機能について説明します。
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 22c1e3050915fc697a62862620ef492ef22f80b8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542831"
---
# <a name="what-is-azure-virtual-network"></a>Azure Virtual Network とは

Azure Virtual Network (VNet) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 VNet により、Azure Virtual Machines (VM) などのさまざまな種類の Azure リソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。 VNet は、自社のデータ センターで運用する従来のネットワークと似ていますが、スケール、可用性、分離性など、Azure のインフラストラクチャのさらなる利点を提供します。

## <a name="vnet-concepts"></a>VNet の概念

- **[アドレス空間]:** VNet を作成する場合は、パブリック アドレスとプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する必要があります。 Azure は、ユーザーが割り当てたアドレス空間のプライベート IP アドレスを仮想ネットワーク内のリソースに割り当てます。 たとえば、アドレス空間 10.0.0.0/16 を指定した VNet で VM をデプロイする場合、VM には 10.0.0.4 などのプライベート IP が割り当てられます。
- **サブネット:** サブネットにより、仮想ネットワークを 1 つ以上のサブネットワークにセグメント分割して、仮想ネットワークのアドレス空間の一部を各サブネットに割り当てることができます。 その後、特定のサブネット内に Azure リソースをデプロイできます。 従来のネットワークのように、サブネットでは、組織の内部ネットワークに適したセグメントに VNet アドレス空間をセグメント分割することができます。 これにより、アドレスの割り当ての効率も向上します。 ネットワーク セキュリティ グループを使用して、サブネット内のリソースを保護できます。 詳細については、「[セキュリティ グループ](security-overview.md)」を参照してください。
- **リージョン**:VNet は 1 つのリージョン/場所内に制限されますが、仮想ネットワーク ピアリングを使用して、異なるリージョンの複数の仮想ネットワークを相互に接続できます。
- **サブスクリプション:** VNet は、サブスクリプション内に制限されます。 各 Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)と Azure [リージョン](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)内に複数の仮想ネットワークを実装できます。

## <a name="best-practices"></a>ベスト プラクティス

Azure でネットワークを構築するときに、次の汎用的な設計原則に留意することが重要です。

- アドレス空間が重複しないように徹底します。 VNet アドレス空間 (CIDR ブロック) が組織の他のネットワークの範囲と重複しないようにします。
- サブネットは、VNet のアドレス空間全体を有効範囲に含めてはなりません。 事前に計画し、将来に備えてアドレス空間の一部を予約します。
- 小規模な VNet を複数持つよりも、大規模な VNet を少数持つことをお勧めします。 これによって、管理の負担を抑えられます。
- ネットワーク セキュリティ グループ (NSG) を使用して VNet を保護します。

## <a name="communicate-with-the-internet"></a>インターネットとの通信

VNet 内のすべてのリソースにおいて、既定でインターネットへの送信方向の通信が可能です。 リソースへの受信通信は、リソースにパブリック IP アドレスまたはパブリック ロード バランサーを割り当てることによって可能になります。 パブリック IP またはパブリック ロード バランサーを使用して、送信接続を管理することもできます。  Azure での送信接続の詳細については、[送信接続](../load-balancer/load-balancer-outbound-connections.md)、[パブリック IP アドレス](virtual-network-public-ip-address.md)、[ロード バランサー](../load-balancer/load-balancer-overview.md)に関する各ページを参照してください。

>[!NOTE]
>内部 [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) のみを使用している場合、[送信接続](../load-balancer/load-balancer-outbound-connections.md)でインスタンスレベルの IP アドレスまたはパブリック ロード バランサーがどのように処理されるかを定義するまで、送信接続は使用できません。

## <a name="communicate-between-azure-resources"></a>Azure リソース間の通信

次のいずれかの方法により、Azure のリソースは互いに安全に通信することができます。

- **仮想ネットワーク経由**:仮想ネットワークに、VM や他のいくつかの種類の Azure リソース (Azure App Service Environment、Azure Kubernetes Service (AKS)、Azure Virtual Machine Scale Sets など) をデプロイできます。 仮想ネットワークにデプロイできる Azure リソースの詳細な一覧については、[仮想ネットワーク サービスの統合](virtual-network-for-azure-services.md)に関するページを参照してください。
- **仮想ネットワーク サービス エンドポイント経由**:直接接続を使用して、仮想ネットワークのプライベート アドレス空間と仮想ネットワークの ID を Azure Storage アカウントや Azure SQL データベースなどの Azure サービス リソースに拡張します。 サービス エンドポイントを使用することで、重要な Azure サービス リソースを仮想ネットワークのみに固定することができます。 詳細については、[仮想ネットワーク サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)に関するページを参照してください。
- **VNet ピアリング経由**:仮想ネットワークを相互に接続することで、任意の仮想ネットワークのリソースが仮想ネットワーク ピアリングを使用して相互に通信できるようになります。 接続する仮想ネットワークが属している Azure リージョンは、同じであっても異なっていてもかまいません。 詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。

## <a name="communicate-with-on-premises-resources"></a>オンプレミス リソースとの通信

オンプレミスのコンピューターおよびネットワークを仮想ネットワークに接続するには、次のオプションを組み合わせて使用します。

- **ポイント対サイト仮想プライベート ネットワーク (VPN)** :仮想ネットワークとネットワーク内の 1 台のコンピューターの間で確立されます。 仮想ネットワークとの接続を確立する各コンピューターで、接続を構成する必要があります。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 コンピューターと仮想ネットワーク間の通信は、インターネット上の暗号化されたトンネルを通じて送信されます。 詳細については、[ポイント対サイト VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S) に関するページを参照してください。
- **サイト間 VPN**:オンプレミス VPN デバイスと仮想ネットワークにデプロイされた Azure VPN ゲートウェイの間で確立されます。 この接続の種類を使用すると、承認した任意のオンプレミス リソースが仮想ネットワークにアクセスできます。 オンプレミス VPN デバイスと Azure VPN ゲートウェイ間の通信は、インターネット上の暗号化されたトンネルを通じて送信されます。 詳細については、[サイト間 VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) に関するページを参照してください。
- **Azure ExpressRoute**:ExpressRoute のパートナーを介して、ネットワークと Azure の間で確立されます。 この接続はプライベート接続です。 トラフィックはインターネットを経由しません。 詳細については、[ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute) に関するページを参照してください。

## <a name="filter-network-traffic"></a>ネットワーク トラフィックのフィルター処理

次のオプションのいずれかまたは両方を使用して、サブネット間のネットワーク トラフィックをフィルター処理できます。

- **セキュリティ グループ**:ネットワーク セキュリティ グループとアプリケーション セキュリティ グループには、受信と送信のセキュリティ規則を複数含めることができます。これらの規則を使用すると、送信元と送信先の IP アドレス、ポート、およびプロトコルに基づいて、リソースとの間で送受信されるトラフィックをフィルター処理できます。 詳しくは、「[ネットワーク セキュリティ グループ](security-overview.md#network-security-groups)」または「[アプリケーション セキュリティ グループ](security-overview.md#application-security-groups)」をご覧ください。
- **ネットワーク仮想アプライアンス**:ネットワーク仮想アプライアンスとは、ファイアウォール、WAN 最適化などのネットワーク機能を実行する VM です。 仮想ネットワークにデプロイできる使用可能なネットワーク仮想アプライアンスの一覧については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) で確認してください。

## <a name="route-network-traffic"></a>ネットワーク トラフィックのルーティング

Azure では、既定で、サブネット、接続されている仮想ネットワーク、オンプレミス ネットワーク、およびインターネット間でトラフィックがルーティングされます。 次のオプションのいずれかまたは両方を実装して、Azure によって作成される既定のルートをオーバーライドできます。

- **ルート テーブル**:サブネットごとにトラフィックのルーティング先を制御するルートを含む、カスタム ルート テーブルを作成できます。 [ルート テーブル](virtual-networks-udr-overview.md#user-defined)の詳細を確認してください。
- **ボーダー ゲートウェイ プロトコル (BGP) のルート**:Azure VPN ゲートウェイまたは ExpressRoute 接続を使用して仮想ネットワークをオンプレミス ネットワークに接続する場合、オンプレミス BGP ルートを仮想ネットワークに伝達できます。 [Azure VPN ゲートウェイ](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) と [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange) で BGP を使用する方法の詳細を確認してください。

## <a name="azure-vnet-limits"></a>Azure VNet の制限

デプロイできる Azure リソースの数について特定の制限があります。 Azure のネットワークの制限のほとんどは、最大値です。 ただし、[VNet の制限に関するページ](../azure-subscription-service-limits.md#networking-limits)に記載されているように、[特定のネットワークの上限を引き上げる](../azure-supportability/networking-quota-requests.md)ことができます。 

## <a name="pricing"></a>価格

Azure VNet は無料でご利用いただけます。コストはかかりません。 仮想マシン (VM) やその他の製品などのリソースには、Standard の料金が適用されます。 詳細については、[VNet の価格](https://azure.microsoft.com/pricing/details/virtual-network/)に関するページと Azure の「[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)」を参照してください。

## <a name="next-steps"></a>次の手順

 仮想ネットワークの使用を開始するには、仮想ネットワークを作成し、いくつかの VM をそこにデプロイし、VM 間で通信します。 その方法については、[仮想ネットワークの作成](quick-create-portal.md)に関するクイック スタートを参照してください。
