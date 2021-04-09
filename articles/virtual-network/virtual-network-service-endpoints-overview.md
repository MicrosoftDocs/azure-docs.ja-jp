---
title: Azure 仮想ネットワーク サービス エンドポイント
titlesuffix: Azure Virtual Network
description: サービス エンドポイントを使って仮想ネットワークから Azure リソースへの直接アクセスを有効にする方法を説明します。
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8926e99db926fc8182e98509c3deff0ccc3d1612
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576669"
---
# <a name="virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイント

仮想ネットワーク (VNet) サービス エンドポイントでは、Azure のバックボーン ネットワーク上で最適化されたルートを介して、Azure サービスに安全に直接接続できます。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 サービス エンドポイントを使用すると、VNet 内のプライベート IP アドレスは、VNet 上のパブリック IP アドレスを必要とせずに、Azure サービスのエンドポイントに接続できます。

この機能は、次の Azure サービスとリージョンで提供されています。 *Microsoft.\** リソースはかっこ内に示されています。 お使いのサービスに対してサービス エンドポイントを構成する際に、サブネット側でこのリソースを有効にしてください。

**一般公開**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*):一般公開 (全 Azure リージョン)。
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*):一般公開 (全 Azure リージョン)。
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): 専用 SQL プール (以前の SQL DW) のすべての Azure リージョンで一般公開されています。
- **[Azure Database for PostgreSQL サーバー](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*):一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Database for MySQL サーバー](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*):一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft.Sql*):一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*):一般公開 (全 Azure リージョン)。
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*):一般公開 (全 Azure リージョン)。
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*):一般公開 (全 Azure リージョン)。
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*):一般公開 (全 Azure リージョン)。
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*):一般公開 (ADLS Gen1 を利用できる全 Azure リージョン)。
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft.Web*):一般公開 (App Service を利用できる全 Azure リージョン)。
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft.CognitiveServices*): 一般公開 (Cognitive Services を利用できる全 Azure リージョン)。

**パブリック プレビュー**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*):プレビュー公開 (Azure Container Registry を利用できる限られた Azure リージョン)。

最新情報については、[Azure Virtual Network の更新情報](https://azure.microsoft.com/updates/?product=virtual-network)ページをご覧ください。

## <a name="key-benefits"></a>主な利点

サービス エンドポイントには次の利点があります。

- **Azure サービス リソースのセキュリティ強化**:VNet プライベート アドレス空間は重複することがあります。 重複する空間を使用して、VNet を起点とするトラフィックを一意に特定することはできません。 サービス エンドポイントは、VNet の ID をサービスまで拡張することで、Azure サービス リソースへのアクセスを仮想ネットワークに限定する機能を提供します。 ご利用の仮想ネットワークでサービス エンドポイントを有効にしたら、仮想ネットワーク規則を追加して、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。 この規則を追加すれば、リソースへのパブリック インターネット アクセスを完全に排除し、仮想ネットワークからのトラフィックのみを許可することにより、セキュリティが強化されます。
- **ご利用の仮想ネットワークから Azure サービスへの最適なトラフィック ルーティング**:現在、オンプレミス、あるいは仮想アプライアンスにインターネット トラフィックを強制する仮想ネットワーク内のすべてのルートは、Azure サービスのトラフィックにもそのインターネット トラフィックと同じルートを強制します。 サービス エンドポイントを使用することで、Azure トラフィックのルーティングを最適化することができます。 

  エンドポイントは常に、ご利用の仮想ネットワークから Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックを直接受け取ります。 Azure のバックボーン ネットワーク上でトラフィックを維持することで、サービス トラフィックに影響を与えることなく、強制トンネリングを通じて、ご利用の仮想ネットワークからの送信インターネット トラフィックの監査と監視を続けることができます。 ユーザー定義のルートと強制トンネリングの詳細については、[Azure 仮想ネットワーク トラフィックのルーティング](virtual-networks-udr-overview.md)に関するページを参照してください。
- **管理の手間がかからないシンプルな設定**:IP ファイアウォールを使用して Azure リソースを保護するために、仮想ネットワークで予約済みのパブリック IP アドレスを使用する必要はなくなりました。 サービス エンドポイントの設定には、ネットワーク アドレス変換 (NAT) またはゲートウェイ デバイスは必要ありません。 サービス エンドポイントは、サブネット上での単純なクリック操作で構成できます。 エンドポイントを維持するために余分な手間がかかることはなくなりました。

## <a name="limitations"></a>制限事項

- この機能は、Azure Resource Manager デプロイ モデルを使ってデプロイされた仮想ネットワークでのみ使用できます。
- エンドポイントは、Azure 仮想ネットワークに構成されたサブネット上で有効になります。 エンドポイントは、オンプレミスから Azure サービスへのトラフィックには使用できません。 詳細については、「[オンプレミスから Azure サービスへのアクセスの保護](#secure-azure-services-to-virtual-networks)」を参照してください。
- Azure SQL の場合、サービス エンドポイントは、仮想ネットワークのリージョン内の Azure サービス トラフィックにのみ適用されます。 Azure Storage では、仮想マシンをデプロイするペアになっているリージョンを含むようにエンドポイントを拡張して、読み取りアクセス geo 冗長ストレージ (RA-GRS) および geo 冗長ストレージ (GRS) のトラフィックをサポートできます。 詳細については、「[Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)」をご覧ください。
- Azure Data Lake Storage (ADLS) Gen 1 では、VNet 統合機能は同じリージョン内の仮想ネットワークでのみ利用できます。 また、ADLS Gen1 の仮想ネットワーク統合では、仮想ネットワークと Azure Active Directory (Azure AD) との間で仮想ネットワーク サービス エンドポイント セキュリティを利用して、アクセス トークン内に追加のセキュリティ要求が生成されることにご注意ください。 これらの要求は、ご利用の Data Lake Storage Gen1 アカウントに対して仮想ネットワークを認証し、アクセスを許可するために使用されます。 サービス エンドポイントをサポートするサービスの下にリストされている *Microsoft.AzureActiveDirectory* タグは、ADLS Gen 1 へのサービス エンドポイントをサポートするためだけに使用されます。 サービス エンドポイントは Azure AD によってネイティブにサポートされていません。 Azure Data Lake Store Gen 1 の VNet 統合の詳細については、[Azure Data Lake Storage Gen1 のネットワーク セキュリティ](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

## <a name="secure-azure-services-to-virtual-networks"></a>Azure サービスへのアクセスを仮想ネットワークに限定する

- 仮想ネットワーク サービス エンドポイントは、ご利用の仮想ネットワークの ID を Azure サービスに提供します。 ご利用の仮想ネットワークでサービス エンドポイントを有効にしたら、仮想ネットワーク規則を追加して、Azure サービス リソースへのアクセスを仮想ネットワークに限定することができます。
- 現在、仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして使用します。 サービス エンドポイントを使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するよう切り替えます。 この切り替えにより、IP ファイアウォールで使用される予約済みのパブリック IP アドレスを使用することなく、サービスにアクセスすることができます。

   >[!NOTE]
   > サービス エンドポイントを使用すると、サービス トラフィックのサブネット内にある仮想マシンの発信元 IP アドレスは、パブリック IPv4 アドレスの使用からプライベート IPv4 アドレスの使用に切り替わります。 この切り換えにより、Azure のパブリック IP アドレスを使用している既存の Azure サービスのファイアウォール規則が動作を停止します。 サービス エンドポイントを設定する前に、Azure サービスのファイアウォール規則でこの切り替えが考慮されていることを確認してください。 サービス エンドポイントの構成中にこのサブネットからのサービス トラフィックが一時的に中断される場合もあります。 
 
## <a name="secure-azure-service-access-from-on-premises"></a>オンプレミスから Azure サービスへのアクセスの保護

  既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute からのパブリック IP アドレス (通常は NAT) を許可する必要もあります。 これらの IP アドレスは、Azure サービス リソースの IP ファイアウォール構成を使用して追加できます。

  ExpressRoute:パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用している場合、使用している NAT IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、NAT IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。  サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 ExpressRoute のパブリック ピアリングおよび Microsoft ピアリングの NAT の詳細については、[ExpressRoute の NAT 要件](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)に関するページを参照してください。

![Azure サービスへのアクセスを仮想ネットワークに限定する](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>構成

- 仮想ネットワーク内のサブネット上でサービス エンドポイントを構成します。 エンドポイントは、そのサブネット内で実行されているどの種類のコンピューティング インスタンスでも動作します。
- サブネット上のサポートされているすべての Azure サービス (Azure Storage や Azure SQL Database など) に対して複数のサービス エンドポイントを構成できます。
- Azure SQL Database の場合、仮想ネットワークは、Azure サービス リソースと同じリージョンにある必要があります。 GRS および RA-GRS Azure Storage アカウントを使用している場合、プライマリ アカウントは仮想ネットワークと同じリージョンにある必要があります。 その他のすべてのサービスでは、任意のリージョンで、Azure サービス リソースへのアクセスを仮想ネットワークに限定できます。 
- エンドポイントが構成されている仮想ネットワークのサブスクリプションは、Azure サービス リソースのサブスクリプションと同じでも違っていてもかまいません。 エンドポイントを設定して Azure サービスのセキュリティを保護するために必要なアクセス許可の詳細については、[プロビジョニング](#provisioning)に関するページを参照してください。
- サポートされているサービスについては、サービス エンドポイントを使って新規または既存のリソースへのアクセスを仮想ネットワークに限定することができます。

### <a name="considerations"></a>考慮事項

- サービス エンドポイントを有効にすると、発信元 IP アドレスは、そのサブネットからサービスへの通信時に、パブリック IPv4 アドレスからプライベート IPv4 アドレスを使用するように切り替わります。 この切り替え中に、サービスに接続中の既存の TCP 接続はすべて閉じられます。 サービスに接続するサブネットのサービス エンドポイントを有効または無効にするときは、重要なタスクが実行されていないことを確認してください。 また、この IP アドレスの切り替えの後に、ご利用のアプリケーションが自動的に Azure サービスに接続できることを確認してください。

  IP アドレスの切り替えが影響するのは、ご利用の仮想ネットワークからのサービス トラフィックのみです。 仮想マシンに割り当てられたパブリック IPv4 アドレスとの間でやりとりされる他のトラフィックへの影響はありません。 Azure サービスについては、Azure のパブリック IP アドレスを使用する既存のファイアウォール ルールがある場合、これらのルールは仮想ネットワークのプライベート アドレスへの切り替え時に停止します。
- サービス エンドポイントを使用しても、Azure サービスの DNS エントリは変わらず、引き続き Azure サービスに割り当てられているパブリック IP アドレスに解決されます。

- ネットワーク セキュリティ グループ (NSG) でのサービス エンドポイントの使用:
  - 既定では、NSG は送信インターネット トラフィックを許可し、VNet から Azure サービスへのトラフィックも許可します。 このトラフィックは、サービス エンドポイントでも同じように機能します。 
  - すべての送信インターネット トラフィックを拒否して特定の Azure サービスへのトラフィックだけを許可する場合は、NSG の[サービス タグ](./network-security-groups-overview.md#service-tags)を使います。 NSG の規則で、サポートされている Azure サービスを接続先として指定することができます。また、各タグの基になる IP アドレスのメンテナンスは Azure によって提供されます。 詳細については、[NSG の Azure サービス タグ](./network-security-groups-overview.md#service-tags)に関するページをご覧ください。 

### <a name="scenarios"></a>シナリオ

- **ピアリングされた仮想ネットワーク、接続された仮想ネットワーク、または複数の仮想ネットワーク**:Azure サービスへのアクセスを 1 つの仮想ネットワーク内または複数の仮想ネットワークにまたがる複数のサブネットに限定するには、サブネットごとに個別にサービス エンドポイントを有効にして、Azure サービス リソースへのアクセスをこれらのサブネットに限定します。
- **仮想ネットワークから Azure サービスへの送信トラフィックのフィルタリング**:仮想ネットワークから Azure サービスに送信されるトラフィックを検査またはフィルター処理する場合は、仮想ネットワーク内にネットワーク仮想アプライアンスをデプロイします。 その後、ネットワーク仮想アプライアンスがデプロイされているサブネットにサービス エンドポイントを適用し、このサブネットのみに Azure サービス リソースへのアクセスを限定します。 このシナリオは、ネットワーク仮想アプライアンス フィルタリングを使用して、ご利用の仮想ネットワークから Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[ネットワーク仮想アプライアンスを持つエグレス](/azure/architecture/reference-architectures/dmz/nva-ha)に関するページを参照してください。
- **Azure リソースへのアクセスを仮想ネットワークに直接デプロイされたサービスに限定する**:さまざまな Azure サービスを仮想ネットワーク内の特定のサブネットに直接デプロイできます。 管理されたサービスのサブネット上にサービス エンドポイントを設定することで、Azure サービス リソースへのアクセスを[管理されたサービス](virtual-network-for-azure-services.md)のサブネットに限定することができます。
- **Azure の仮想マシンからのディスク トラフィック**:マネージドおよびアンマネージド ディスクの仮想マシン ディスク トラフィックは、Azure Storage のサービス エンドポイントのルーティング変更の影響を受けません。 このトラフィックには diskIO と、マウントおよびマウント解除が含まれます。 サービス エンドポイントと [Azure Storage ネットワーク規則](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を使用して、ページ BLOB への REST アクセスを選択したネットワークに制限することができます。 

### <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング

特定のサービスに対してサービス エンドポイントを構成したら、そのサービス エンドポイントのルートが有効であることを次の方法で検証します。 
 
- サービスの診断で任意のサービス要求の発信元 IP アドレスを検証します。 サービス エンドポイントを使った新しい要求では、ご利用の仮想ネットワークから要求元のクライアントに割り当てられた仮想ネットワークのプライベート IP アドレスとして、要求の発信元 IP アドレスが表示されます。 エンドポイントを使用しない場合、このアドレスは Azure のパブリック IP アドレスになります。
- サブネット内の任意のネットワーク インターフェイス上に有効なルートを表示します。 サービスへのルートでは次のことを確認できます。
  - 各サービスのアドレス プレフィックス範囲へのより詳細な既定のルート表示。
  - *VirtualNetworkServiceEndpoint* の nextHopType が存在すること。
  - 任意の強制トンネリングのルートに比べ、より直接的なサービスへの接続が実施されていること。

>[!NOTE]
> サービス エンドポイントのルートは、アドレス プレフィックスが Azure サービスのものと一致するすべての BGP または UDR のルートをオーバーライドします。 詳細については、[有効なルートについてのトラブルシューティング](diagnose-network-routing-problem.md)に関するページを参照してください。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。 Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されたサブネットの *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* へのアクセス許可を持っている必要があります。 組み込みのサービス管理者ロールには、既定でこのアクセス許可が含まれています。 アクセス許可を変更するには、カスタム ロールを作成します。

組み込みロールの詳細については、「[Azure 組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 特定のアクセス許可をカスタム ロールに割り当てる方法の詳細については、「[Azure カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。

仮想ネットワークと Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 Azure Storage や Azure Key Vault など、(すべてではなく) 特定の Azure サービスでも、異なる Active Directory (AD) テナントにまたがるサービス エンドポイントがサポートされています。つまり、仮想ネットワークと Azure サービス リソースを異なる Active Directory (AD) テナントに配置できます。 詳細については、個々のサービスのドキュメントを参照してください。  

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイントの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage、Azure SQL Database など) の現在の価格モデルが適用されます。

仮想ネットワーク内のサービス エンドポイントの合計数に制限はありません。

特定の Azure サービス (Azure Storage アカウントなど) では、リソースのセキュリティ保護に使用されるサブネットの数に制限が適用される場合があります。 詳細については、「[次のステップ](#next-steps)」セクションの各種サービスに関するドキュメントを参照してください。

## <a name="vnet-service-endpoint-policies"></a>VNet サービス エンドポイント ポリシー 

VNet サービス エンドポイント ポリシーを使用すると、Azure サービスへの仮想ネットワーク トラフィックをフィルター処理できます。 このフィルターでは、サービス エンドポイント経由の特定の Azure サービス リソースだけが許可されます。 サービス エンドポイント ポリシーでは、Azure サービスへの仮想ネットワーク トラフィックに対する詳細なアクセス制御が提供されます。 詳細については、「[仮想ネットワーク サービス エンドポイント ポリシー](./virtual-network-service-endpoint-policies-overview.md)」を参照してください。

## <a name="faqs"></a>FAQ

FAQ については、[仮想ネットワーク サービス エンドポイントの FAQ](./virtual-networks-faq.md#virtual-network-service-endpoints) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [仮想ネットワーク サービス エンドポイントの構成](tutorial-restrict-network-access-to-resources.md)
- [Azure Storage アカウントを仮想ネットワークに限定する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure SQL Database を仮想ネットワークに限定する](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Azure Synapse Analytics を仮想ネットワークに限定する](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [仮想ネットワーク内の Azure サービス統合](virtual-network-for-azure-services.md)
- [仮想ネットワーク サービス エンドポイント ポリシー](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
