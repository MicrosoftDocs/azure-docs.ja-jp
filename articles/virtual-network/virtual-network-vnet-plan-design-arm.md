---
title: Azure 仮想ネットワークを計画する | Microsoft Docs
description: 分離、接続、場所の要件に基づいて仮想ネットワークを計画する方法について説明します。
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 9ed060c1f3fc47aa7050c0f9b25c757953fa22fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98220339"
---
# <a name="plan-virtual-networks"></a>仮想ネットワークを計画する

実験的に仮想ネットワークを作成することは簡単ですが、おそらく、組織の運用ニーズに対応するために、時間の経過とともに複数の仮想ネットワークをデプロイすることになります。 ある程度の計画を立てることで、より効率的に仮想ネットワークをデプロイし、必要なリソースを接続することができます。 この記事の情報は、既に仮想ネットワークに精通し、それらの使用経験がある場合に最も役に立ちます。 仮想ネットワークに慣れていない場合は、[仮想ネットワークの概要](virtual-networks-overview.md)に関する記事を読むことをお勧めします。

## <a name="naming"></a>名前を付ける

Azure のすべてのリソースには名前があります。 名前はスコープ内で一意でなければならず、スコープはリソースの種類によって異なる場合があります。 たとえば、仮想ネットワークの名前は、[リソース グループ](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)内では一意である必要がありますが、[サブスクリプション](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription)内または Azure [リージョン](https://azure.microsoft.com/regions/#services)内では重複していてもかまいません。 リソースの名前を付けるときに一貫して使用できる名前付け規則を定義することは、複数のネットワーク リソースの管理が長期間に及ぶときは有効なことです。 推奨事項については、「[名前付け規則](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork)」を参照してください。

## <a name="regions"></a>リージョン

すべての Azure リソースは、Azure リージョンと Azure サブスクリプションの内部に作成されます。 リソースを作成できるのは、リソースと同じリージョンおよびサブスクリプションに存在する仮想ネットワーク内だけです。 ただし、異なるサブスクリプションおよびリージョンに存在する仮想ネットワークに接続することはできます。 詳しくは、「[接続](#connectivity)」をご覧ください。 リソースをデプロイするリージョンを決めるときは、リソースの使用者の物理的な居場所を考慮します。

- リソースの使用者は、通常、リソースに対するネットワーク待機時間が最短になることを望みます。 特定の場所と Azure リージョンの間の相対的な待機時間を確認する方法については、[相対待機時間の確認](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。
- データの保存場所、管轄、コンプライアンス、回復性に関する要件はありますか? ある場合は、要件に合ったリージョンを選ぶことが大事です。 詳しくは、「[Azure の地域](https://azure.microsoft.com/global-infrastructure/geographies/)」をご覧ください。
- デプロイするリソースに対して同じ Azure リージョン内にある Azure の可用性ゾーン全体での回復性は必要ですか? 同じ仮想ネットワーク内の複数の異なる可用性ゾーンに、仮想マシン (VM) などのリソースをデプロイできます。 ただし、可用性ゾーンをサポートしていない Azure リージョンもあります。 可用性ゾーンとそれをサポートするリージョンについて詳しくは、[可用性ゾーン](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

## <a name="subscriptions"></a>サブスクリプション

[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の範囲内であれば、各サブスクリプション内に必要なだけいくつでも仮想ネットワークをデプロイできます。 たとえば、部門ごとに異なるサブスクリプションを使用している組織もあります。 サブスクリプションに関する詳細と考慮事項については、[サブスクリプションのガバナンス](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy)に関するページをご覧ください。

## <a name="segmentation"></a>セグメント化

サブスクリプションごとおよびリージョンごとに、複数の仮想ネットワークを作成できます。 各仮想ネットワーク内には複数のサブネットを作成することができます。 必要な仮想ネットワークとサブネットの数を決めるときは、以下のことを考慮すると役に立ちます。

### <a name="virtual-networks"></a>仮想ネットワーク

仮想ネットワークは、仮想的で分離された、Azure のパブリック ネットワークの一部です。 各仮想ネットワークは、特定のサブスクリプション専用です。 サブスクリプションに作成する仮想ネットワークが 1 つでよいか、または複数必要かを決めるときは、次のことを考慮します。

- 組織にはトラフィックを異なる仮想ネットワークに分離するセキュリティ要件がありますか? 仮想ネットワークを接続するかどうかを選択できます。 仮想ネットワークを接続する場合は、ファイアウォールなどのネットワーク仮想アプライアンスを実装して、仮想ネットワーク間のトラフィックのフローを制御できます。 詳しくは、「[セキュリティ](#security)」および「[接続](#connectivity)」をご覧ください。
- 組織には、仮想ネットワークを異なる[サブスクリプション](#subscriptions)または[リージョン](#regions)に分離する要件がありますか?
- [ネットワーク インターフェイス](virtual-network-network-interface.md)を介して、VM と他のリソースの通信ができます。 各ネットワーク インターフェイスには、1 つ以上のプライベート IP アドレスが割り当てられています。 仮想ネットワークでは、ネットワーク インターフェイスと[プライベート IP アドレス](./private-ip-addresses.md)がいくつ必要ですか? 1 つの仮想ネットワークに設定できるネットワーク インターフェイスとプライベート IP アドレスの数には[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)があります。
- 仮想ネットワークを別の仮想ネットワークまたはオンプレミス ネットワークに接続しますか? 仮想ネットワークを相互に、またはオンプレミスのネットワークに接続することはできますが、それ以外には接続できません。 詳しくは、「[接続](#connectivity)」をご覧ください。 別の仮想ネットワークまたはオンプレミスのネットワークに接続する各仮想ネットワークは、一意のアドレス空間を持っている必要があります。 各仮想ネットワークのアドレス空間には、1 つまたは複数のパブリック アドレス範囲またはプライベート アドレス範囲が割り当てられます。 アドレス範囲は、クラスレス ドメイン間ルーティング (CIDR) 形式 (10.0.0.0/16 など) で指定されます。 詳しくは、仮想ネットワークの[アドレス範囲](manage-virtual-network.md#add-or-remove-an-address-range)に関するページをご覧ください。
- 組織には、異なる仮想ネットワークのリソースに対する管理要件がありますか? ある場合は、異なる仮想ネットワークにリソースを分離することによって、組織内のユーザーに対する[アクセス許可の割り当て](#permissions)を簡素化したり、異なる仮想ネットワークに異なるポリシーを割り当てたりすることができます。
- Azure サービス リソースを仮想ネットワークにデプロイすると、独自の仮想ネットワークが作成されます。 Azure サービスが独自の仮想ネットワークを作成するかどうかを確認するには、「[仮想ネットワークにデプロイできるサービス](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)」をご覧ください。

### <a name="subnets"></a>サブネット

仮想ネットワークは、[制限](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の範囲内で複数のサブネットにセグメント化できます。 サブスクリプションに作成するサブネットが 1 つでよいか、または複数必要かを決めるときは、次のことを考慮します。

- 各サブネットには、仮想ネットワークのアドレス空間内に、CIDR 形式で指定された一意のアドレス範囲が必要です。 このアドレス範囲は、仮想ネットワーク内の他のサブネットと重複することはできません。
- 仮想ネットワークに Azure サービス リソースをデプロイする場合、独自のサブネットがリソースによって必要とされる、または作成される可能性があるので、そのための十分な未割り当て領域が必要です。 Azure サービスが独自のサブネットを作成するかどうかを確認するには、「[仮想ネットワークにデプロイできるサービス](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)」をご覧ください。 たとえば、Azure VPN Gateway を使ってオンプレミスのネットワークに仮想ネットワークを接続する場合、仮想ネットワークにはゲートウェイに専用のサブネットが必要です。 詳しくは、「[ゲートウェイ サブネット](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub)」をご覧ください。
- 既定では、ネットワーク トラフィックは仮想ネットワーク内のすべてのサブネット間でルーティングされます。 Azure の既定のルーティングをオーバーライドして、たとえば、Azure のサブネット間のルーティングを禁止したり、ネットワーク仮想アプライアンスを通してサブネット間でトラフィックをルーティングしたりできます。 同じ仮想ネットワーク内のリソース間のトラフィックを、ネットワーク仮想アプライアンス (NVA) を通してフローさせる必要がある場合は、異なるサブネットにリソースをデプロイします。 詳しくは、「[セキュリティ](#security)」をご覧ください。
- Azure ストレージ アカウントや Azure SQL Database などの Azure リソースへのアクセスを、仮想ネットワーク サービス エンドポイントのある特定のサブネットに制限することができます。 さらに、インターネットからのリソースへのアクセスを拒否することができます。 複数のサブネットを作成し、一部のサブネットではサービス エンドポイントを有効にして、それ以外では無効にすることができます。 詳しくは、[サービス エンドポイント](virtual-network-service-endpoints-overview.md)およびサービス エンドポイントを有効にできる Azure リソースに関するページをご覧ください。
- 仮想ネットワーク内の各サブネットには、0 個または 1 個のネットワーク セキュリティ グループを関連付けることができます。 各サブネットに関連付けるネットワーク セキュリティ グループは、同じでも、異なっていてもかまいません。 各ネットワーク セキュリティ グループには規則が含まれ、送信元と送信先の間でやり取りされるトラフィックを許可または拒否できます。 [ネットワーク セキュリティ グループ](#traffic-filtering)の詳細を確認する。

## <a name="security"></a>Security

仮想ネットワーク内のリソースが送受信するネットワーク トラフィックを、ネットワーク セキュリティ グループおよびネットワーク仮想アプライアンスを使ってフィルター処理できます。 サブネットからのトラフィックを Azure がルーティングする方法を制御できます。 また、仮想ネットワーク内のリソースを使用できる組織内のユーザーを制限することもできます。

### <a name="traffic-filtering"></a>トラフィックのフィルター処理

- ネットワーク セキュリティ グループと、ネットワーク トラフィックをフィルター処理する NVA のどちらか一方または両方を使って、仮想ネットワーク内のリソース間のネットワーク トラフィックをフィルター処理することができます。 ファイアウォールなどの NVA をデプロイしてネットワーク トラフィックをフィルター処理するには、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1) をご覧ください。 NVA を使うときは、サブネットから NVA にトラフィックをルーティングするためのカスタム ルートも作成します。 詳しくは、「[トラフィックのルーティング](#traffic-routing)」をご覧ください。
- ネットワーク セキュリティ グループには、リソースが送受信するトラフィックを許可または拒否する、複数の既定のセキュリティ規則が含まれます。 ネットワーク セキュリティ グループは、ネットワーク インターフェイスと、ネットワーク インターフェイスが含まれるサブネットの、どちらか一方または両方に関連付けることができます。 セキュリティ規則の管理を簡単にするため、可能な場合は常に、サブネット内の個々のネットワーク インターフェイスではなく、個別のサブネットにネットワーク セキュリティ グループを関連付けることをお勧めします。
- サブネット内の VM ごとに異なるセキュリティ規則を適用する必要がある場合は、VM 内のネットワーク インターフェイスを、1 つまたは複数のアプリケーション セキュリティ グループに関連付けることができます。 セキュリティ規則では、送信元と送信先のどちらか一方または両方のアプリケーション セキュリティ グループを指定できます。 その規則は、アプリケーション セキュリティ グループのメンバーであるネットワーク インターフェイスにのみ適用されます。 詳しくは、[ネットワーク セキュリティ グループ](./network-security-groups-overview.md)および[アプリケーション セキュリティ グループ](./network-security-groups-overview.md#application-security-groups)に関するページをご覧ください。
- Azure では、各ネットワーク セキュリティ グループ内に複数のセキュリティ規則が既定で作成されます。 既定の規則の 1 つでは、仮想ネットワーク内のすべてのリソース間で、すべてのトラフィックのフローが許可されます。 この動作をオーバーライドするには、ネットワーク セキュリティ グループと、NVA にトラフィックをルーティングするためのカスタム ルーティングの、どちらか一方または両方を使います。 Azure の[既定のセキュリティ規則](./network-security-groups-overview.md#default-security-rules)のすべてと、ネットワーク セキュリティ グループの規則がリソースに適用される方法を、よく理解することをお勧めします。

[NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) を使用して Azure とインターネットの間に境界ネットワーク (DMZ とも呼ばれます) を実装するためのサンプル設計を表示できます。

### <a name="traffic-routing"></a>トラフィックのルーティング

Azure では、サブネットからの送信トラフィックに対して複数のルートが既定で作成されます。 ルート テーブルを作成してサブネットに関連付けることにより、Azure の既定のルーティングをオーバーライドできます。 Azure の既定のルーティングをオーバーライドする一般的な理由としては、次のようなものがあります。
- サブネット間のトラフィックに NVA を経由させる必要がある場合。 詳しくは、[トラフィックを強制的に NVA 経由にするようルート テーブルを構成する方法](tutorial-create-route-table-portal.md)に関するページをご覧ください。
- インターネットにバインドされたすべてのトラフィックを、強制的に Azure VPN Gateway を介して NVA 経由 (つまりオンプレミス) にする必要がある場合。 検査とログのためにインターネット トラフィックを強制的にオンプレミスにすることを、強制トンネリングと呼ぶことがよくあります。 詳しくは、[強制トンネリングの構成方法](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)に関するページをご覧ください。

カスタム ルーティングを実装する必要がある場合は、[Azure でのルーティング](virtual-networks-udr-overview.md)についてよく理解しておくことをお勧めします。

## <a name="connectivity"></a>接続

仮想ネットワークは、仮想ネットワーク ピアリングを使って他の仮想ネットワークに、または Azure VPN Gateway を使ってオンプレミスのネットワークに接続できます。

### <a name="peering"></a>ピアリング

[仮想ネットワーク ピアリング](virtual-network-peering-overview.md)で接続される仮想ネットワークが存在するサポートされる Azure リージョンは、同じであっても、異なっていてもかまいません。 仮想ネットワークが含まれる Azure サブスクリプションは同じでも異なっていてもかまいません (サブスクリプションが異なる Azure Active Directory テナントに属していてもかまいません)。 ピアを作成する前に、ピアリングのすべての[要件と制約](virtual-network-manage-peering.md#requirements-and-constraints)をよく理解しておくことをお勧めします。 同じリージョン内でピアリングされる仮想ネットワーク内のリソース間の帯域幅は、リソースが同じ仮想ネットワーク内にある場合と同じです。

### <a name="vpn-gateway"></a>VPN Gateway

Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を利用すると、[サイト間 VPN](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使って、または Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) による専用接続を使って、オンプレミスのネットワークに仮想ネットワークを接続できます。

ピアリングと VPN Gateway を組み合わせて、[ハブとスポークのネットワーク](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成できます。この場合、たとえば、スポークの仮想ネットワークはハブの仮想ネットワークに接続し、ハブはオンプレミスのネットワークに接続します。

### <a name="name-resolution"></a>名前解決

ある仮想ネットワーク内のリソースは、Azure の[組み込み DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) を使って、ピアリングされた仮想ネットワーク内のリソースの名前を解決することはできません。 ピアリングされた仮想ネットワーク内の名前を解決するには、[独自の DNS サーバーをデプロイ](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)するか、Azure DNS [プライベート ドメイン](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使います。 仮想ネットワーク内のリソースとオンプレミス ネットワークのリソースの間で名前を解決するには、独自の DNS サーバーをデプロイする必要もありします。

## <a name="permissions"></a>アクセス許可

Azure では、リソースに対して、[Azure ロール ベースのアクセス制御 (Azure RBAC)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を利用します。 アクセス許可は、管理グループ、サブスクリプション、リソース グループ、および個々のリソースという階層内の[スコープ](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope)に割り当てられます。 階層について詳しくは、[リソースの整理](../governance/management-groups/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。 Azure 仮想ネットワークと、ピアリング、ネットワーク セキュリティ グループ、サービス エンドポイント、ルート テーブルなどの関連するすべての機能を使用するには、組織のメンバーを組み込みの[所有者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner)、[共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)、または[ネットワーク共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロールに割り当ててから、ロールを適切なスコープに割り当てることができます。 仮想ネットワークの機能のサブセットに対して特定のアクセス許可を割り当てる場合は、[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を作成し、[仮想ネットワーク](manage-virtual-network.md#permissions)、[サブネットとサービス エンドポイント](virtual-network-manage-subnet.md#permissions)、[ネットワーク インターフェイス](virtual-network-network-interface.md#permissions)、[ピアリング](virtual-network-manage-peering.md#permissions)、[ネットワークおよびアプリケーション セキュリティ グループ](manage-network-security-group.md#permissions)、または[ルート テーブル](manage-route-table.md#permissions)に必要な特定のアクセス許可を、ロールに割り当てます。

## <a name="policy"></a>ポリシー

Azure Policy を使って、ポリシーの定義の作成、割り当て、管理を行うことができます。 ポリシー定義は、リソースにさまざまな規則を適用し、組織の標準とサービス レベル アグリーメントへのリソースの準拠が維持されるようにします。 Azure Policy によって、リソースの評価が実行され、ユーザーのポリシー定義に準拠していないリソースがスキャンされます。 たとえば、特定のリソース グループまたはリージョン内でのみ仮想ネットワークの作成を許可するようなポリシーを定義して適用することができます。 または、すべてのサブネットにネットワーク セキュリティ グループが関連付けられていることを要求するポリシーを作成できます。 ポリシーは、リソースを作成および更新するときに評価されます。

ポリシーは、管理グループ、サブスクリプション、およびリソース グループという階層に適用されます。 詳しくは、[Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) に関するページを参照するか、仮想ネットワークの [Azure Policy 定義](./policy-reference.md)をデプロイしてください。

## <a name="next-steps"></a>次のステップ

[仮想ネットワーク](manage-virtual-network.md)、[サブネットおよびサービス エンドポイント](virtual-network-manage-subnet.md)、[ネットワーク インターフェイス](virtual-network-network-interface.md)、[ピアリング](virtual-network-manage-peering.md)、[ネットワークおよびアプリケーション セキュリティ グループ](manage-network-security-group.md)、[ルート テーブル](manage-route-table.md)のすべてのタスク、設定、およびオプションについて説明します。
