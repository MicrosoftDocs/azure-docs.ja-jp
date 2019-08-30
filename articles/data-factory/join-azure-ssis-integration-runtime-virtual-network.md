---
title: Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる | Microsoft Docs
description: Azure-SSIS 統合ランタイムを Azure 仮想ネットワークに参加させる方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3a1e272fa332c0bf0ee4e5ececa3edd83aec1d46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543145"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
Azure Data Factory (ADF) で SQL Server Integration Services (SSIS) を使用する場合、次のシナリオでは、Azure-SSIS Integration Runtime (IR) を Azure 仮想ネットワークに参加させる必要があります。 

- セルフホステッド IR をプロキシとして構成/管理することなく、Azure-SSIS IR 上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。 

- Azure-SSIS IR 上で実行される SSIS パッケージから、仮想ネットワーク サービス エンドポイントでサポートされている Azure サービス リソースに接続したい。

- 仮想ネットワーク サービス エンドポイントがあるか､または仮想ネットワーク内にマネージ インスタンスがある Azure SQL Database で SSIS カタログ データベース (SSISDB) をホストしている｡ 

ADF では、クラシック デプロイ モデルまたは Azure Resource Manager デプロイ モデルで作成された仮想ネットワークに Azure-SSIS IR を参加させることができます。 

> [!IMPORTANT]
> クラシック仮想ネットワークは現在非推奨とされているため、代わりに Azure Resource Manager 仮想ネットワークを使用してください。  既にクラシック仮想ネットワークを使っている場合は、できるだけ早期に Azure Resource Manager 仮想ネットワークを使用するよう切り替えてください。

## <a name="access-to-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする
SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合、オンプレミス ネットワークに接続されている仮想ネットワークに Azure-SSIS IR を参加させるか、Azure-SSIS IR のプロキシとしてセルフホステッド IR を構成/管理することができます。[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)方法に関する記事を参照してください。 Azure-SSIS IR を仮想ネットワークに参加させる場合、注意すべき重要な点がいくつかあります。 

- オンプレミスのネットワークに接続された既存の仮想ネットワークがない場合は、最初に、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)または[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、その仮想ネットワークからオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)または[ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。 

- Azure-SSIS IR と同じ場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager またはクラシック仮想ネットワークがある場合は、その仮想ネットワークに IR を参加させることができます。 

- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存のクラシック仮想ネットワークがある場合は、最初に、Azure-SSIS IR を参加させるための[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、[クラシック間の仮想ネットワーク](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md)接続を構成します。 または、Azure-SSIS 統合ランタイムを参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。 
 
- Azure-SSIS IR とは異なる場所にオンプレミス ネットワークに接続された既存の Azure Resource Manager 仮想ネットワークがある場合は、最初に、Azure-SSIS IR を参加させるための [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md##create-a-virtual-network)を作成します。 次に、Azure Resource Manager 間の仮想ネットワーク接続を構成します。 または、Azure-SSIS IR を参加させるための[クラシック仮想ネットワーク](../virtual-network/virtual-networks-create-vnet-classic-pportal.md)を作成します。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。 

## <a name="access-to-azure-services-with-virtual-network-service-endpoints"></a>仮想ネットワーク サービス エンドポイントを使用して Azure サービスにアクセスする
SSIS パッケージで[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)でサポートされている Azure サービス リソースにアクセスするときに、それらのリソースを Azure-SSIS IR に対してセキュリティで保護する場合は、仮想ネットワーク サービス エンドポイントが構成された仮想ネットワーク サブネットに Azure-SSIS IR を参加させると同時に、同じサブネットからのアクセスを許可する仮想ネットワーク規則を Azure サービス リソースに追加できます。

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Azure SQL Database と仮想ネットワーク サービス エンドポイント/Managed Instance で SSIS のカタログ データベースをホストする
仮想ネットワーク サービス エンドポイントがある Azure SQL Database で SSIS カタログをホストする場合は、Azure SSIS IR を、必ず同じ仮想ネットワークとサブネットに参加させるようにします。

Managed Instance と同じ仮想ネットワークに Azure-SSIS IR を参加させる場合は、Azure-SSIS IR を、必ず Managed Instance とは異なるサブネットに配置します。 Managed Instance とは異なる仮想ネットワークに Azure-SSIS IR を参加させる場合、仮想ネットワーク ピアリング (同じリージョンに限定される) または仮想ネットワーク接続への 1 つの仮想ネットワークのどちらかをお勧めします。 「[Azure SQL Database Managed Instance にアプリケーションを接続する](../sql-database/sql-database-managed-instance-connect-app.md)」を参照してください。

すべての場合に、仮想ネットワークは Azure Resource Manager デプロイ モデルでのみデプロイすることができます。

以降のセクションではさらに詳しく説明します。 

## <a name="requirements-for-virtual-network-configuration"></a>仮想ネットワーク構成の要件
-   `Microsoft.Batch` が、Azure-SSIS IR をホストする仮想ネットワーク サブネットのサブスクリプションの登録済みプロバイダーであることを確認します。 Classic 仮想ネットワーク (従来の仮想ネットワーク) を使用している場合、その仮想ネットワークの Classic Virtual Machine Contributor (従来の仮想マシン共同作成者) ロールにも `MicrosoftAzureBatch` を参加させます。 

-   必要なアクセス許可を持っていることを確認します。 「[必要なアクセス許可](#perms)」を参照してください。

-   Azure-SSIS IR をホストするための適切なサブネットを選択します。 「[サブネットを選択する](#subnet)」をご覧ください。 

-   仮想ネットワークで独自のドメイン ネーム サービス (DNS) サーバーを使用している場合は、「[ドメイン ネーム サービス サーバー](#dns_server)」をご覧ください。 

-   サブネットでネットワーク セキュリティ グループ (NSG) を使用している場合は、「[ネットワーク セキュリティ グループ](#nsg)」をご覧ください。 

-   Azure Express Route を使用している場合、またはユーザー定義ルートを構成する場合は、「[Azure ExpressRoute またはユーザー定義ルートを使用する](#route)」をご覧ください。 

-   仮想ネットワークのリソース グループが、特定の Azure ネットワーク リソースを作成および削除できることを確認します。 「[リソース グループの要件](#resource-group)」をご覧ください。 

-   [Azure-SSIS IR のカスタム設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)に関する記事で説明しているように Azure-SSIS IR をカスタマイズする場合、Azure-SSIS IR ノードには、172.16.0.0 ～ 172.31.255.255 の事前定義された範囲のプライベート IP アドレスが割り当てられます。したがって、仮想/オンプレミス ネットワークのプライベート IP アドレス範囲がこの範囲と競合しないようにしてください。

Azure-SSIS IR に必要な接続を示す図を次に示します。

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> 必要なアクセス許可

Azure-SSIS 統合ランタイムを作成するユーザーは、次のアクセス許可を持っている必要があります。

- SSIS IR を Azure Resource Manager 仮想ネットワークに参加させようとしている場合は、次の 2 つのオプションがあります。

  - 組み込みの*ネットワーク共同作成者*ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。

  - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 

- SSIS IR をクラシック仮想ネットワークに参加させようとしている場合は、組み込みの*従来の仮想マシン共同作成者*ロールを使用することをお勧めします。 そうしない場合は、仮想ネットワークに参加するためのアクセス許可を含むカスタム ロールを定義する必要があります。

### <a name="subnet"></a> サブネットを選択する
-   仮想ネットワーク ゲートウェイ専用であるため、Azure SSIS Integration Runtime をデプロイするために GatewaySubnet を選択しないでください。 

-   選択したサブネットに、Azure SSIS IR が使用するための利用可能なアドレス領域が十分にあることを確認してください。 利用可能な IP アドレスで少なくとも 2 * の IR ノード番号をそのまま残しておきます。 Azure は、各サブネット内で一部の IP アドレスを予約し、これらのアドレスを使用することはできません。 サブネットの最初と最後の IP アドレスは、Azure サービスで使用される 3 つ以上のアドレスと共に、プロトコル準拠に予約されます。 詳細については、「[これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)」をご覧ください。 

-   (SQL Database Managed Instance、App Service など) 他の Azure サービスによって排他的に専有されているサブネットを使用しないでください。 

### <a name="dns_server"></a> ドメイン ネーム サービス サーバー 
Azure-SSIS 統合ランタイムによって参加した仮想ネットワークで、独自のドメイン ネーム サービス (DNS) サーバーを使用する必要がある場合、そのサーバーが、(Azure Storage BLOB 名、`<your storage account>.blob.core.windows.net` などの) グローバル Azure ホスト名を解決できることを確認してください。 

次の手順が推奨されます。 

-   要求を Azure DNS に転送するようにカスタム DNS を構成します。 独自の DNS サーバー上で、未解決の DNS レコードを Azure の再帰的リゾルバーの IP アドレス (168.63.129.16) に転送することができます。 

-   カスタム DNS を仮想ネットワークのプライマリとして、Azure DNS をセカンダリとして設定します。 独自の DNS サーバーが利用できない場合に備えて、Azure の再帰的リゾルバーの IP アドレス (168.63.129.16) をセカンダリ DNS サーバーとして登録します。 

詳細については、「[独自の DNS サーバーを使用する名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。 

### <a name="nsg"></a> ネットワーク セキュリティ グループ
Azure-SSIS 統合ランタイムが使用するサブネットにネットワーク セキュリティ グループ (NSG) を実装する必要がある場合は、次のポートを受信/送信トラフィックが通過できるようにします。 

| Direction | トランスポート プロトコル | Source | 送信元ポート範囲 | Destination | 送信先ポート範囲 | 説明 |
|---|---|---|---|---|---|---|
| 受信 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (IR を Azure Resource Manager 仮想ネットワークに参加させる場合) <br/><br/>10100、20100、30100 (IR をクラシック仮想ネットワークに参加させる場合)| Data Factory サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS 統合ランタイムのノードと通信します。 <br/><br/> サブネットレベルの NSG を作成するかどうかにかかわらず、Azure-SSIS IR をホストする仮想マシンにアタッチされているネットワーク インターフェイス カード (NIC) のレベルで、Data Factory は NSG を常に構成します。 Data Factory の IP アドレスから指定したポートで受信したトラフィックのみが、その NIC レベルの NSG によって許可されます。 サブネット レベルでインターネット トラフィックに対してこれらのポートを開いている場合でも、Data Factory の IP アドレスではない IP アドレスからのトラフィックは NIC レベルでブロックされます。 |
| 送信 | TCP | VirtualNetwork | * | AzureCloud | 443 | 仮想ネットワークの Azure-SSIS 統合ランタイムのノードはこのポートを使って、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスします。 |
| 送信 | TCP | VirtualNetwork | * | インターネット | 80 | 仮想ネットワーク内の Azure-SSIS 統合ランタイムのノードはこのポートを使用して、インターネットから証明書失効リストをダウンロードします。 |
| 送信 | TCP | VirtualNetwork | * | SQL | 1433、11000 ～ 11999 | 仮想ネットワークの Azure-SSIS 統合ランタイムのノードはこれらのポートを使って、Azure SQL Database サーバーによってホストされている SSISDB にアクセスします。 Azure SQL Database のサーバー接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。 この送信セキュリティ規則は、仮想ネットワーク内の Managed Instance によってホストされる SSISDB には適用されません。 |
||||||||

### <a name="route"></a> Azure ExpressRoute またはユーザー定義ルートを使用する
[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線を仮想ネットワーク インフラストラクチャに接続してオンプレミス ネットワークを Azure に拡張する際の一般的な構成では、仮想ネットワーク フローからオンプレミス ネットワーク アプライアンスへの送信インターネット トラフィックを強制して検査とログ記録を行う、強制トネリング (BGP ルート、0.0.0.0/0 を仮想ネットワークにアドバタイズする) を使用します。 
 
または、[ユーザー定義ルート (UDR)](../virtual-network/virtual-networks-udr-overview.md) を定義して、Azure-SSIS IR をホストするサブネットから、検査とログ記録のためのファイアウォールとしての仮想ネットワークアプライアンス (NVA) または Azure Firewall をホストする別のサブネットへの送信インターネット トラフィックを強制することができます。
 
どちらの場合も、トラフィック ルートで、依存している Azure Data Factory サービス (具体的には Azure Batch 管理サービス) から仮想ネットワーク内の Azure-SSIS IR への接続で必要な受信接続が切断されます。 
 
解決策は、Azure SSIS IR が含まれるサブネット上に 1 つ (以上) のユーザー定義ルート (UDR) を定義することです。 

- Azure ExpressRoute シナリオでは、Azure-SSIS IR をホストするサブネットに次ホップの種類が **[インターネット]** である 0.0.0.0/0 ルートを適用するか、NVA シナリオでは、既存の 0.0.0.0/0 ルートの次ホップの種類を **[仮想アプライアンス]** から **[インターネット]** に変更できます。

![ルートを追加する](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)
- サブネットからの送信インターネット トラフィックを検査する機能が失われることを心配している場合。 Azure Batch 管理サービスと Azure-SSIS IR 間で次ホップの種類が **[インターネット]** であるトラフィックのみをルーティングする UDR を定義できます。
例: Azure-SSIS IR が `UK South` にある場合は、[サービス タグの IP 範囲のダウンロード リンク](https://www.microsoft.com/en-us/download/details.aspx?id=56519)から、または [Service Tag Discovery API](https://aka.ms/discoveryapi) を使用して、サービス タグ `BatchNodeManagement.UKSouth` の IP 範囲の一覧を取得する必要があります。 その後、次ホップの種類が **[インターネット]** である次の関連する IP 範囲のルートの UDR を適用します。

![AzureBatch UDR の設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)
> [!NOTE]
> この方法では、Azure-SSIS IR の切断を避けるために定期的に IP 範囲を確認し、新しい IP 範囲を UDR に追加する必要があるという追加のメンテナンス コストが発生します。 サービス タグ内に出現した新しい IP は、有効になるまでに 1 か月かかります。 このため、IP 範囲を毎月確認することをお勧めします。 

### <a name="resource-group"></a> リソース グループの要件
-   Azure SSIS IR では、仮想ネットワークと同じリソース グループ下に特定のネットワーク リソースを作成する必要があります。 これらのリソースとして、次が挙げられます。
    -   Azure ロード バランサー。 *\<Guid>-azurebatch-cloudserviceloadbalancer* という名前で使用される。
    -   Azure パブリック IP アドレス。 *\<Guid>-azurebatch-cloudservicepublicip* という名前で使用される。
    -   ネットワーク ワーク セキュリティ グループ。 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup* という名前で使用される。 

    これらのリソースは、IR の開始時に作成され、IR の停止時に削除されます。 他のリソースでそれらを再利用しないでください。再利用すると、IR の停止がブロックされます。 

-   仮想ネットワークが属するリソース グループまたはサブスクリプションでリソース ロックがないことを確認します。 読み取り専用ロックまたは削除ロックのどちらかを構成した場合、IR の開始と停止は失敗または応答停止するおそれがあります。 

-   仮想ネットワークが属するリソース グループまたはサブスクリプション下に次のリソースが作成されるのを妨げる Azure ポリシーがないことを確認します。 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 受信接続用に Azure-SSIS IR で公開されるパブリック IP アドレスを保護するにはどうすればよいですか。 パブリック IP アドレスを削除することはできますか。
 
    現時点では、パブリック IP アドレスは、Azure-SSIS IR の VNet への参加時に自動的に作成されます。 NIC レベルの NSG では、Azure Batch 管理サービスに対してのみ、Azure-SSIS IR への着信接続が許可されています。ご自分で受信保護用のサブネット レベルの NSG を指定することもできます。

    パブリック IP アドレスの公開を望まないときに、シナリオに適用できるのであれば、VNet ではなく [Azure-SSIS IR 用のプロキシとしてセルフホステッド IR を構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)という方法を検討できます。
 
- データ ソースにアクセスするためのファイアウォールの許可リストに配置できる Azure-SSIS IR の静的 IP アドレスはありますか。
 
    - データ ソースがオンプレミスの場合、仮想ネットワークをオンプレミス ネットワークに接続し、その仮想ネットワーク サブネットに Azure-SSIS IR を参加させた後、そのサブネットの IP 範囲を許可リストに追加できます。
    - データ ソースが仮想ネットワーク サービス エンドポイントでサポートされている Azure サービスの場合は、仮想ネットワーク上に仮想ネットワーク サービス ポイントを構成し、その仮想ネットワーク サブネットに Azure-SSIS IR を参加させることができます。その後、アクセスを許可する IP 範囲の代わりに、Azure サービスの仮想ネットワーク規則を使用できます。
    - データ ソースが他のクラウド データ ソースの場合は、UDR を使用して、Azure-SSIS IR から NVA または Azure Firewall への送信トラフィックを静的パブリック IP アドレスを使用してルーティングできるため、NVA または Azure Firewall のパブリック IP アドレスを許可リストに配置できます。
    - 上記のいずれもご自分の要件を満たしていない場合は、[Azure-SSIS IR 用のプロキシとしてのセルフホステッド IR の構成](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)によってデータ ソースへのアクセスが可能かどうかを評価した後、Azure-SSIS IR を VNet に参加させる代わりに、セルフホステッド IR をホストするコンピューターの IP アドレスを許可リストに配置できます。

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (データ ファクトリ UI)
このセクションでは、Azure Portal とデータ ファクトリ UI を使って、既存の Azure-SSIS ランタイムを仮想ネットワーク (クラシックまたは Azure Resource Manager) に参加させる方法について説明します。 Azure-SSIS IR を仮想ネットワークに参加させる前に、まず仮想ネットワークを適切に構成する必要があります。 仮想ネットワークの種類 (クラシックまたは Azure Resource Manager) に基づいて次の 2 つのセクションのいずれかを実行します。 次に 3 番目のセクションに進み、Azure-SSIS IR を仮想ネットワークに参加させます。 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>ポータルを使ってAzure Resource Manager 仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 

1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク]** ページで、 **[プロパティ]** を選びます。 

1. **[リソース ID]** のコピー ボタンを選び、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選びます。 **[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。 

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   a. Azure Portal で、左メニューの **[サブスクリプション]** を選びます。 

   b. サブスクリプションを選択します。 

   c. 左側の **[リソース プロバイダー]** を選び、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>ポータルを使ってクラシック仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 

1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 **[仮想ネットワーク (クラシック)]** を選びます。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク (クラシック)]** ページで、 **[プロパティ]** を選びます。 

   ![クラシック仮想ネットワークのリソース ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. **[リソース ID]** のコピー ボタンを選び、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選びます。 **[使用可能なアドレス]** の数が Azure-SSIS 統合ランタイムのノード数より多いことを確認します。 

   ![仮想ネットワークで使用可能なアドレスの数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. **MicrosoftAzureBatch** を仮想ネットワークの**従来の仮想マシン共同作成者**ロールに参加させます。 

    a. 左側のメニューの **[アクセス制御 (IAM)]** を選択し、 **[ロール割り当て]** タブを選択します。 

    ![[アクセス制御] ボタンと [追加] ボタン](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. **[ロールの割り当ての追加]** を選択します。

    c. **[ロールの割り当ての追加]** ページで、 **[ロール]** に **[従来の仮想マシン共同作成者]** を選択します。 **[選択]** ボックスに **ddbf3205-c6bd-46ae-8127-60eb93363864** を貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選びます。 

    ![[ロールの割り当ての追加] ページでの検索結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. **[保存]** を選んで設定を保存し、ページを閉じます。 

    ![アクセス設定の保存](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 共同作成者の一覧に **Microsoft Azure Batch** があることを確認します。 

    ![Azure Batch アクセスの確認](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 登録されていない場合は、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   a. Azure Portal で、左メニューの **[サブスクリプション]** を選びます。 

   b. サブスクリプションを選択します。 

   c. 左側の **[リソース プロバイダー]** を選び、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR を仮想ネットワークに参加させる
1. Microsoft Edge または Google Chrome を起動します。 現在、Data Factory の UI はこれらの Web ブラウザーでのみサポートされています。 

1. [Azure Portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、 **[その他のサービス]** を選び、 **[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選びます。 

   ![データ ファクトリの一覧](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. リストで Azure-SSIS 統合ランタイムを使うデータ ファクトリを選びます。 データ ファクトリのホーム ページが表示されます。 **[作成およびデプロイ]** タイルを選びます。 Data Factory の UI が別のタブに表示されます。 

   ![データ ファクトリのホーム ページ](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. データ ファクトリの UI で、 **[編集]** タブに切り替え、 **[接続]** を選択し、 **[統合ランタイム]** タブに切り替えます。 

   ![[統合ランタイム] タブ](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR が実行されている場合、統合ランタイム リストで、Azure-SSIS IR の **[アクション]** 列の **[停止]** ボタンを選びます。 IR は、停止するまで編集できません。 

   ![IR を停止する](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 統合ランタイム リストで、Azure-SSIS IR の **[アクション]** 列の **[編集]** ボタンを選びます。 

   ![統合ランタイムを編集する](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. **[Integration Runtime Setup]\(Integration Runtime 設定\)** パネルで、 **[General Settings]\(全般設定\)** ページと **[SQL 設定]** ページの **[次へ]** ボタンをクリックして先に進みます。 

1. **[詳細設定]** ページで、次の操作を実行します。 

   a. **[Select a VNet...]\(VNet を選択...\)** チェック ボックスをオンにします。 

   b. **[サブスクリプション]** で、ご利用の Azure サブスクリプションを選択すると、続けて既存の仮想ネットワークを選択できます。 
  
   c. **[VNET 名]** で仮想ネットワークを選びます。 

   d. **[サブネット名]** で、仮想ネットワークのサブネットを選びます。 

   e. セルフホステッド IR を Azure-SSIS IR のプロキシとして構成/管理する場合は、 **[Set up Self-Hosted...]\(セルフホステッドとして設定...\)** チェック ボックスをオンにして、「[セルフホステッド IR を ADF で Azure-SSIS IR のプロキシとして構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)」の記事を参照してください。

   f. **[VNet Validation]\(VNet の検証\)** ボタンをクリックし、成功した場合は **[次へ]** ボタンをクリックします。 

   ![IR セットアップの詳細設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. **[Summary]\(概要\)** ページで、Azure-SSIS IR のすべての設定を確認して **[更新]** ボタンをクリックします。

1. これで、Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンをクリックして Azure-SSIS IR を開始できます。 仮想ネットワークに参加する Azure-SSIS IR を開始するには、約 20 ～ 30 分かかります。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 Azure-SSIS 統合ランタイムが仮想ネットワークを参加させるための仮想ネットワークのアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Azure-SSIS IR を作成して仮想ネットワークに参加させる
Azure-SSIS IR を作成し、作成と同時に仮想ネットワークに参加させることができます。 完全なスクリプトと手順については、[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md#azure-powershell)に関するページをご覧ください。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>既存の Azure-SSIS IR を仮想ネットワークに参加させる
[Azure-SSIS 統合ランタイムの作成](create-azure-ssis-integration-runtime.md)に関する記事に示されているスクリプトは、Azure-SSIS IR の作成と仮想ネットワークへの参加を同じスクリプトで実行する方法を示しています。 既存の Azure-SSIS IR がある場合に、それを仮想ネットワークに参加させるには、次の手順を実行します。 
1. Azure-SSIS IR を停止します。 
1. 仮想ネットワークに参加するように Azure-SSIS IR を構成します。 
1. Azure-SSIS IR を開始します。 

### <a name="define-the-variables"></a>変数を定義する
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Azure-SSIS IR を停止する
仮想ネットワークに参加させる前に、Azure-SSIS 統合ランタイムを停止します。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR が参加するように仮想ネットワーク設定を構成する
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Azure-SSIS IR を構成する
仮想ネットワークに参加するように Azure-SSIS 統合ランタイムを構成するのには、`Set-AzDataFactoryV2IntegrationRuntime` コマンドを実行します。 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR を開始する
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

このコマンドは、完了するまでに 20 ～ 30 分かかります。

## <a name="next-steps"></a>次の手順
Azure-SSIS 統合ランタイムについて詳しくは、以下のトピックをご覧ください。 
- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md): このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- [Azure-SSIS 統合ランタイムを作成](create-azure-ssis-integration-runtime.md)します。 この記事では、チュートリアルを基に、Azure SQL Database と仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance を使って SSIS カタログをホストする方法と、Azure-SSIS IR を仮想ネットワークに参加させる方法が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法を示し、返された情報に含まれる状態について説明しています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
