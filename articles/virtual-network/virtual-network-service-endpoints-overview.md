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
ms.date: 08/15/2018
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: e36e1ca17b5106c79076d1c62e737ba60907ab19
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666474"
---
# <a name="virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイント

仮想ネットワーク (VNet) サービス エンドポイントは、直接接続によって仮想ネットワークのプライベート アドレス空間を拡張し、VNet の ID を Azure サービスに提供します。 エンドポイントを使用することで、重要な Azure サービス リソースへのアクセスを仮想ネットワークのみに限定することができます。 VNet から Azure サービスへのトラフィックは常に、Microsoft Azure のバックボーン ネットワーク上に残ります。

この機能は、次の Azure サービスとリージョンで提供されています。

**一般公開**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** :一般公開 (全 Azure リージョン)。
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (全 Azure リージョン)。
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (全 Azure リージョン)。
- **[Azure Database for PostgreSQL サーバー](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Database for MySQL サーバー](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** : 一般公開 (データベース サービスを利用できる Azure リージョン)。
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (全 Azure リージョン)。
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** :一般公開 (全 Azure リージョン)。
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (全 Azure リージョン)。
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (全 Azure リージョン)。
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** :一般公開 (ADLS Gen1 を利用できる全 Azure リージョン)。
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : 一般公開 (App Service を利用できる全 Azure リージョン)。

**パブリック プレビュー**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** :プレビュー公開 (Azure Container Registry を利用できる全 Azure リージョン)。
。

最新情報については、[Azure 仮想ネットワークの更新情報](https://azure.microsoft.com/updates/?product=virtual-network)ページをご覧ください。

## <a name="key-benefits"></a>主な利点

サービス エンドポイントには次の利点があります。

- **Azure サービス リソースのセキュリティ強化**:VNet のプライベート アドレス空間は重複することがあるため、VNet からのトラフィックを一意に識別するためには使用できません。 サービス エンドポイントは、VNet の ID をサービスまで拡張することで、仮想ネットワークに対する Azure サービス リソースをセキュリティで保護する機能を提供します。 ご利用の仮想ネットワークでサービス エンドポイントが有効になったら、Azure サービス リソースに仮想ネットワーク規則を追加することで、このリソースへのアクセスを仮想ネットワークに限定することができます。 こうして、リソースへのパブリック インターネット アクセスを完全に排除し、仮想ネットワークからのトラフィックのみを許可することにより、セキュリティが強化されます。
- **ご利用の仮想ネットワークから Azure サービスへの最適なトラフィック ルーティング**:現在、お客様の構内および 仮想アプライアンスの両方、またはそのいずれかへのインターネット トラフィックが、ご利用の仮想ネットワーク内のすべてのルートにおいて強制されている場合 (強制トンネリング)、Azure サービス へのトラフィックにもそのインターネット トラフィックと同じルートが強制されます。 サービス エンドポイントを使用することで、Azure トラフィックのルーティングを最適化することができます。 

  エンドポイントは常に、ご利用の仮想ネットワークから Microsoft Azure のバックボーン ネットワーク上のサービスへのサービス トラフィックを直接受け取ります。 Azure のバックボーン ネットワーク上でトラフィックを維持することで、サービス トラフィックに影響を与えることなく、強制トンネリングを通じて、ご利用の仮想ネットワークからの送信インターネット トラフィックの監査と監視を続けることができます。 詳細については、[ユーザー定義のルートと強制トンネリング](virtual-networks-udr-overview.md)に関するページをご覧ください。
- **管理の手間がかからないシンプルな設定**:ご利用の仮想ネットワークで予約済みのパブリック IP アドレスを使用することなく、IP ファイアウォールを経由して Azure リソースにアクセスできるようになりました。 サービス エンドポイントの設定に NAT やゲートウェイ用のデバイスは必要ありません。 サービス エンドポイントは、サブネット上での単純なクリック操作で構成されます。 エンドポイントを維持するために余分なコストはかかりません。

## <a name="limitations"></a>制限事項

- この機能は、Azure Resource Manager デプロイ モデルを使ってデプロイされた仮想ネットワークでのみ使用できます。
- エンドポイントは、Azure 仮想ネットワークに構成されたサブネット上で有効になります。 エンドポイントは、お客様の構内から Azure サービスへのトラフィックに対しては使用できません。 詳細については、「[オンプレミスから Azure サービスへのアクセスの保護](#securing-azure-services-to-virtual-networks)」をご覧ください。
- Azure SQL の場合、サービス エンドポイントは、仮想ネットワークのリージョン内の Azure サービス トラフィックにのみ適用されます。 Azure Storage の場合、RA-GRS および GRS のトラフィックをサポートするには、仮想ネットワークのデプロイ先のリージョンとペアになっているリージョンを含めるようにエンドポイントも拡張します。 [Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)の詳細をご覧ください。
- ADLS Gen 1 の場合、VNET 統合機能は、同じリージョン内の仮想ネットワークでのみ利用できます。 また、Azure Data Lake Storage Gen1 の仮想ネットワーク統合では、仮想ネットワークと Azure Active Directory (Azure AD) との間で仮想ネットワーク サービス エンドポイント セキュリティを利用して、アクセス トークン内に追加のセキュリティ要求が生成されることに注意してください。 これらの要求は、ご利用の Data Lake Storage Gen1 アカウントに対して仮想ネットワークを認証し、アクセスを許可するために使用されます。 サービス エンドポイントをサポートするサービスの下にリストされている "Microsoft.AzureActiveDirectory" タグは、ADLS Gen 1 へのサービス エンドポイントをサポートするためだけに使用されます。 サービス エンドポイントは Azure Active Directory (Azure AD) によってネイティブにサポートされていません。 Azure Data Lake Store Gin 1 の VNet 統合の詳細については、[こちら](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。

## <a name="securing-azure-services-to-virtual-networks"></a>Azure サービスへのアクセスを仮想ネットワークに限定する

- 仮想ネットワーク サービス エンドポイントは、ご利用の仮想ネットワークの ID を Azure サービスに提供します。 ご利用の仮想ネットワークでサービス エンドポイントが有効になったら、Azure サービス リソースに仮想ネットワーク規則を追加することで、このリソースへのアクセスを仮想ネットワークに限定することができます。
- 現在、仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして使用します。 サービス エンドポイントを使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するよう切り替えます。 この切り替えにより、IP ファイアウォールで使用される予約済みのパブリック IP アドレスを使用することなく、サービスにアクセスすることができます。

>[!NOTE]
> サービス エンドポイントを使用すると、サービス トラフィックのサブネット内にある仮想マシンの発信元 IP アドレスは、パブリック IPv4 アドレスの使用からプライベート IPv4 アドレスの使用に切り替わります。 この切り換えにより、Azure のパブリック IP アドレスを使用している既存の Azure サービスのファイアウォール規則が動作を停止します。 サービス エンドポイントを設定する前に、Azure サービスのファイアウォール規則でこの切り替えが考慮されていることを確認してください。 サービス エンドポイントの構成中にこのサブネットからのサービス トラフィックが一時的に中断される場合もあります。 
 
- __オンプレミスから Azure サービスへのアクセスの保護__:

  既定では、仮想ネットワークからのアクセスに限定された Azure サービス リソースは、オンプレミスのネットワークからはアクセスできません。 オンプレミスからのトラフィックを許可する場合は、オンプレミスまたは ExpressRoute からのパブリック IP アドレス (通常は NAT) を許可する必要もあります。 これらの IP アドレスは、Azure サービス リソースの IP ファイアウォール構成を通じて追加できます。

  ExpressRoute:パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用している場合、使用されている NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。

![Azure サービスへのアクセスを仮想ネットワークに限定する](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>構成

- サービス エンドポイントは仮想ネットワーク内のサブネット上で構成されます。 エンドポイントは、そのサブネット内で実行されているどの種類のコンピューティング インスタンスでも動作します。
- サブネット上のサポートされているすべての Azure サービス (Azure Storage や Azure SQL Database など) に対して複数のサービス エンドポイントを構成できます。
- Azure SQL Database の場合、仮想ネットワークは、Azure サービス リソースと同じリージョンにある必要があります。 GRS および RA-GRS Azure Storage アカウントを使用している場合、プライマリ アカウントは仮想ネットワークと同じリージョンにある必要があります。 その他のすべてのサービスでは、任意のリージョンで、仮想ネットワークに対する Azure サービス リソースをセキュリティで保護できます。 
- エンドポイントが構成されている仮想ネットワークのサブスクリプションは、Azure サービス リソースのサブスクリプションと同じでも違っていてもかまいません。 エンドポイントを設定して Azure サービスのセキュリティを保護するために必要なアクセス許可の詳細については、[プロビジョニング](#provisioning)に関するページを参照してください。
- サポートされているサービスについては、サービス エンドポイントを使って新規または既存のリソースへのアクセスを仮想ネットワークに限定することができます。

### <a name="considerations"></a>考慮事項

- サービス エンドポイントを有効にすると、サブネット内の仮想マシンの発信元 IP アドレスは、そのサブネットからサービスへの通信時に、パブリック IPv4 アドレスからプライベート IPv4 アドレスを使用するように切り替わります。 この切り替え中に、サービスに接続中の既存の TCP 接続はすべて閉じられます。 サービスに接続するサブネットのサービス エンドポイントを有効または無効にするときは、重要なタスクが実行されていないことを確認してください。 また、この IP アドレスの切り替えの後に、ご利用のアプリケーションが自動的に Azure サービスに接続できることを確認してください。

  IP アドレスの切り替えが影響するのは、ご利用の仮想ネットワークからのサービス トラフィックのみです。 ご利用の仮想マシンに割り当てられたパブリック IPv4 アドレスとの間でやりとりされる他のトラフィックへの影響はありません。 Azure サービスについては、Azure のパブリック IP アドレスを使用する既存のファイアウォール ルールがある場合、これらのルールは仮想ネットワークのプライベート アドレスへの切り替え時に停止します。
- サービス エンドポイントを使用しても、Azure サービスの DNS エントリは変わらず、引き続き Azure サービスに割り当てられているパブリック IP アドレスに解決されます。

- ネットワーク セキュリティ グループ (NSG) でのサービス エンドポイントの使用:
  - 既定では、NSG は送信インターネット トラフィックを許可するので、VNet から Azure サービスへのトラフィックも許可します。 これは、サービス エンドポイントを使用しても変わりません。 
  - すべての送信インターネット トラフィックを拒否して特定の Azure サービスへのトラフィックだけを許可する場合は、NSG の[サービス タグ](security-overview.md#service-tags)を使います。 NSG のルールで、サポートされている Azure サービスを接続先として指定することができ、各タグの基になる IP アドレスのメンテナンスは Azure によって提供されます。 詳細については、[NSG の Azure サービス タグ](security-overview.md#service-tags)に関するページをご覧ください。 

### <a name="scenarios"></a>シナリオ

- **ピアリングされた仮想ネットワーク、接続された仮想ネットワーク、または複数の仮想ネットワーク**:Azure サービスへのアクセスを 1 つの仮想ネットワーク内または複数の仮想ネットワークにまたがる複数のサブネットに限定するには、サブネットごとに個別にサービス エンドポイントを有効にして、Azure サービス リソースへのアクセスをこれらのサブネットに限定します。
- **仮想ネットワークから Azure サービスへの送信トラフィックのフィルタリング**:仮想ネットワークから Azure サービスへのトラフィックを検査またはフィルター処理する場合は、仮想ネットワーク内にネットワーク仮想アプライアンスをデプロイします。 その後、ネットワーク仮想アプライアンスがデプロイされているサブネットにサービス エンドポイントを適用し、このサブネットのみに Azure サービス リソースへのアクセスを限定します。 このシナリオは、ネットワーク仮想アプライアンス フィルタリングを使用して、ご利用の仮想ネットワークから Azure サービスへのアクセスを特定の Azure リソースにのみ制限する場合に役立ちます。 詳細については、[ネットワーク仮想アプライアンスを持つエグレス](/azure/architecture/reference-architectures/dmz/nva-ha)に関するページを参照してください。
- **Azure リソースへのアクセスを仮想ネットワークに直接デプロイされたサービスに限定する**:さまざまな Azure サービスを仮想ネットワーク内の特定のサブネットに直接デプロイできます。 管理されたサービスのサブネット上にサービス エンドポイントを設定することで、Azure サービス リソースへのアクセスを[管理されたサービス](virtual-network-for-azure-services.md)のサブネットに限定することができます。
- **Azure の仮想マシンからのディスク トラフィック**:マネージド/アンマネージド ディスクの仮想マシン ディスク トラフィック (マウント、マウント解除、diskIO を含みます) は、Azure Storage のサービス エンドポイントのルーティング変更の影響を受けません。 サービス エンドポイントと [Azure Storage ネットワーク ルール](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を介して、ページ BLOB への REST アクセスを選択したネットワークに制限することができます。 

### <a name="logging-and-troubleshooting"></a>ロギングおよびトラブルシューティング

特定のサービスに対してサービス エンドポイントを構成したら、そのサービス エンドポイントのルートが有効であることを次の方法で検証します。 
 
- サービスの診断で任意のサービス要求の発信元 IP アドレスを検証します。 サービス エンドポイントを使った新しい要求では、ご利用の仮想ネットワークから要求元のクライアントに割り当てられた仮想ネットワークのプライベート IP アドレスとして、要求の発信元 IP アドレスが表示されます。 エンドポイントを使用しない場合、このアドレスは Azure のパブリック IP アドレスになります。
- サブネット内の任意のネットワーク インターフェイス上に有効なルートを表示する。 サービスへのルートでは次のことを確認できます。
  - 各サービスのアドレス プレフィックス範囲へのより詳細な既定のルート表示。
  - *VirtualNetworkServiceEndpoint* に存在する nextHopType
  - 強制トンネリングのルートに比べ、より直接的なサービスへの接続が実施されていること。

>[!NOTE]
> サービス エンドポイントのルートは、アドレス プレフィックスが Azure サービスのものと一致するすべての BGP または UDR のルートをオーバーライドします。 [有効なルートを使用したトラブルシューティング](diagnose-network-routing-problem.md)の詳細をご覧ください。

## <a name="provisioning"></a>プロビジョニング

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。 Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "*Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action*" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)と、特定のアクセス許可を[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に割り当てる方法の詳細をご覧ください。

仮想ネットワークと Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 仮想ネットワークと Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントの下に置かれている必要があります。 

## <a name="pricing-and-limits"></a>料金と制限

サービス エンドポイントの使用に追加料金はかかりません。 現時点では、Azure サービス (Azure Storage、Azure SQL Database など) の現在の価格設定モデルが適用されます。

仮想ネットワーク内のサービス エンドポイントの合計数に制限はありません。

特定の Azure サービス (Azure Storage アカウントなど) では、リソースのセキュリティ保護に使用されるサブネットの数に制限が適用される場合があります。 詳細については、「[次のステップ](#next-steps)」の各種サービスに関するドキュメントを参照してください。

## <a name="virtual-network-service-endpoint-policies"></a>仮想ネットワーク サービス エンドポイント ポリシー 

仮想ネットワーク サービス エンドポイント ポリシーでは、サービス エンドポイント経由での Azure サービスへの仮想ネットワーク トラフィックをフィルター処理し、特定の Azure サービス リソースのみを許可することができます。 サービス エンドポイント ポリシーでは、Azure サービスへの仮想ネットワーク トラフィックに対する詳細なアクセス制御が提供されます。 詳細情報:[仮想ネットワーク サービス エンドポイント ポリシー](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>FAQ

FAQ については、[仮想ネットワーク サービス エンドポイントの FAQ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints) に関するページをご覧ください

## <a name="next-steps"></a>次の手順

- [仮想ネットワーク サービス エンドポイントを構成する](tutorial-restrict-network-access-to-resources.md)方法を学習する
- [Azure Storage アカウントを仮想ネットワークに限定する](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [Azure SQL Database を仮想ネットワークに限定する](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)方法を学習する
- [Azure SQL Data Warehouse を仮想ネットワークに限定する](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)方法を学習する
- [仮想ネットワーク内の Azure サービス統合](virtual-network-for-azure-services.md)について学習する
- [仮想ネットワーク サービス エンドポイント ポリシー](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)について学習する
-  クイック スタート:VNet のサブネットにサービス エンドポイントを設定し、そのサブネットの Azure Storage アカウントにセキュリティを確保する [Azure Resource Manager テンプレート](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)。

