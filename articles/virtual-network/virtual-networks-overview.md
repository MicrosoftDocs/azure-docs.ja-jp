---
title: Azure Virtual Network | Microsoft Docs
description: Azure Virtual Network の概念と機能について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: 9fb6aa0c2bf585862f61d7c78bd09b340ff8a3ce
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015901"
---
# <a name="what-is-azure-virtual-network"></a>Azure Virtual Network とは

Azure Virtual Network では、Azure Virtual Machines (VM) などのさまざまな種類の Azure リソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。 仮想ネットワークは 1 つのリージョン内に制限されますが、仮想ネットワーク ピアリングを使用すれば、異なるリージョンの複数の仮想ネットワークを相互に接続することができます。

Azure Virtual Network では、次に示す重要な機能が提供されます。

## <a name="isolation-and-segmentation"></a>分離とセグメント化

各 Azure [サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)と Azure [リージョン](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)内に複数の仮想ネットワークを実装できます。 各仮想ネットワークは、他の仮想ネットワークから分離されています。 各仮想ネットワークに対して、次の操作を実行できます。
- パブリックおよびプライベート (RFC 1918) アドレスを使用して、カスタム プライベート IP アドレス空間を指定する。 Azure は、ユーザーが割り当てたアドレス空間のプライベート IP アドレスを仮想ネットワーク内のリソースに割り当てます。
- 仮想ネットワークを 1 つ以上のサブネットに分割し、各サブネットに仮想ネットワークのアドレス空間の一部を割り当てる。
- Azure で提供される名前解決を使用するか、独自の DNS サーバーを指定して、仮想ネットワーク内のリソースで使用する。

## <a name="communicate-with-the-internet"></a>インターネットとの通信

仮想ネットワーク内のすべてのリソースにおいて、既定で、インターネットへの送信方向の通信が可能です。 リソースへの受信通信は、リソースにパブリック IP アドレスまたはパブリック ロード バランサーを割り当てることによって可能になります。 パブリック IP またはパブリック ロード バランサーを使用して、送信接続を管理することもできます。  Azure での送信接続の詳細については、[送信接続](../load-balancer/load-balancer-outbound-connections.md)、[パブリック IP アドレス](virtual-network-public-ip-address.md)、[ロード バランサー](../load-balancer/load-balancer-overview.md)に関する各ページを参照してください。

>[!NOTE]
>内部 [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) のみを使用している場合、[送信接続](../load-balancer/load-balancer-outbound-connections.md)でインスタンスレベルの IP アドレスまたはパブリック ロード バランサーがどのように処理されるかを定義するまで、送信接続は使用できません。

## <a name="communicate-between-azure-resources"></a>Azure リソース間の通信

次のいずれかの方法により、Azure のリソースは互いに安全に通信することができます。

- **仮想ネットワーク経由**:仮想ネットワークに、VM や他のいくつかの種類の Azure リソース (Azure App Service Environment、Azure Kubernetes Service (AKS)、Azure Virtual Machine Scale Sets など) をデプロイできます。 仮想ネットワークにデプロイできる Azure リソースの詳細な一覧については、[仮想ネットワーク サービスの統合](virtual-network-for-azure-services.md)に関するページを参照してください。 
- **仮想ネットワーク サービス エンドポイント経由**:直接接続を使用して、仮想ネットワークのプライベート アドレス空間と仮想ネットワークの ID を Azure Storage アカウントや Azure SQL Database などの Azure サービス リソースに拡張します。 サービス エンドポイントを使用することで、重要な Azure サービス リソースを仮想ネットワークのみに固定することができます。 詳細については、[仮想ネットワーク サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)に関するページを参照してください。
 
## <a name="communicate-with-on-premises-resources"></a>オンプレミス リソースとの通信

オンプレミスのコンピューターおよびネットワークを仮想ネットワークに接続するには、次のオプションを組み合わせて使用します。

- **ポイント対サイト仮想プライベート ネットワーク (VPN)**:仮想ネットワークとネットワーク内の 1 台のコンピューターの間で確立されます。 仮想ネットワークとの接続を確立する各コンピューターで、接続を構成する必要があります。 この接続の種類は、既存のネットワークへの変更をほとんどまたはまったく必要としないため、Azure を使い始めたばかりのユーザーまたは開発者に適しています。 コンピューターと仮想ネットワーク間の通信は、インターネット上の暗号化されたトンネルを通じて送信されます。 詳細については、[ポイント対サイト VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S) に関するページを参照してください。
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

## <a name="connect-virtual-networks"></a>仮想ネットワークの接続

仮想ネットワークを相互に接続することで、任意の仮想ネットワークのリソースが仮想ネットワーク ピアリングを使用して相互に通信できるようになります。 接続する仮想ネットワークが属している Azure リージョンは、同じであっても異なっていてもかまいません。 詳細については、「[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Virtual Network の概要については以上です。 仮想ネットワークの使用を開始するには、仮想ネットワークを作成し、いくつかの VM をそこにデプロイし、VM 間で通信します。 その方法については、[仮想ネットワークの作成](quick-create-portal.md)に関するクイック スタートを参照してください。
