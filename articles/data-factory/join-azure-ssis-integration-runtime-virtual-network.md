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
ms.openlocfilehash: 92687b7cb8cdad8612f5a44833efcca351c45a43
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114755"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる
Azure Data Factory で SQL Server Integration Services (SSIS) を使用する場合、次のシナリオでは、Azure-SSIS 統合ランタイム (IR) を Azure 仮想ネットワークに参加させる必要があります。 

- セルフホステッド IR をプロキシとして構成/管理することなく、Azure-SSIS IR 上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。 

- Azure-SSIS IR 上で実行される SSIS パッケージから、仮想ネットワーク サービス エンドポイントでサポートされている Azure サービス リソースに接続したい。

- 仮想ネットワーク サービス エンドポイントがあるか､または仮想ネットワーク内にマネージ インスタンスがある Azure SQL Database で SSIS カタログ データベース (SSISDB) をホストしている｡ 

Data Factory では、クラシック デプロイ モデルまたは Azure Resource Manager デプロイ モデルで作成された仮想ネットワークに Azure-SSIS IR を参加させることができます。 

> [!IMPORTANT]
> 従来の仮想ネットワークは非推奨になるため、代わりに Azure Resource Manager 仮想ネットワークを使用してください。  既に従来の仮想ネットワークを使っている場合は、できるだけ早期に Azure Resource Manager 仮想ネットワークに切り替えます。

## <a name="access-to-on-premises-data-stores"></a>オンプレミスのデータ ストアにアクセスする
SSIS パッケージがオンプレミスのデータ ストアにアクセスする場合は、オンプレミスのネットワークに接続されている仮想ネットワークに Azure-SSIS IR を参加させることができます。 または、セルフホステッド IR を Azure-SSIS IR のプロキシとして構成または管理することができます。 詳細については、[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)方法に関する記事を参照してください。 

Azure-SSIS IR を仮想ネットワークに参加させる場合は、次の重要な点に注意してください。 

- 仮想ネットワークがオンプレミス ネットワークに接続されていない場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成します。 次に、その仮想ネットワークからオンプレミス ネットワークへのサイト間 [VPN ゲートウェイ接続](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)または [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 接続を構成します。 

- Azure Resource Manager 仮想ネットワークが既に Azure-SSIS IR と同じ場所にあるオンプレミス ネットワークに接続されている場合は、その仮想ネットワークに IR を参加させることができます。 

- 従来の仮想ネットワークが、Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md#create-a-virtual-network)を作成できます。 次に、[クラシックと Azure Resource Manager の間の仮想ネットワーク](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)接続を構成します。 
 
- Azure Resource Manager 仮想ネットワークが Azure-SSIS IR とは異なる場所にあるオンプレミス ネットワークに既に接続されている場合は、まず Azure-SSIS IR を参加させる [Azure Resource Manager 仮想ネットワーク](../virtual-network/quick-create-portal.md##create-a-virtual-network)を作成できます。 次に、Azure Resource Management と Azure Resource Manager 間の仮想ネットワーク接続を構成します。 

## <a name="access-to-azure-services"></a>Azure サービスへのアクセス
SSIS パッケージから、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)でサポートされている Azure サービス リソースにアクセスし、Azure-SSIS IR に対するそれらのリソースをセキュリティで保護する場合は、仮想ネットワーク サービス エンドポイントを使用して構成された仮想ネットワークサブネットに Azure-SSIS IR を参加させることができます。 また、同じサブネットからのアクセスを許可する仮想ネットワーク規則を Azure サービス リソースに追加します。

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL Database での SSIS カタログのホスト
仮想ネットワーク サービス エンドポイントがある Azure SQL Database で SSIS カタログをホストする場合は、Azure SSIS IR を、必ず同じ仮想ネットワークとサブネットに参加させるようにします。

マネージド インスタンスと同じ仮想ネットワークに Azure-SSIS IR を参加させるには、必ずマネージド インスタンスとは異なるサブネットに Azure-SSIS IR を配置します。 マネージド インスタンスとは異なる仮想ネットワークに Azure-SSIS IR を参加させるには、仮想ネットワーク ピアリング (同じリージョンに限定される)、または仮想ネットワークから仮想ネットワークへの接続のいずれかをお勧めします。 詳細については、「[Azure SQL Database Managed Instance にアプリケーションを接続する](../sql-database/sql-database-managed-instance-connect-app.md)」を参照してください。

すべての場合に、仮想ネットワークは Azure Resource Manager デプロイ モデルでのみデプロイすることができます。

以降のセクションではさらに詳しく説明します。 

## <a name="virtual-network-configuration"></a>Virtual Network の構成

次の要件を満たすように仮想ネットワークを設定します。 

-   `Microsoft.Batch` が、Azure-SSIS IR をホストする仮想ネットワーク サブネットのサブスクリプションの登録済みプロバイダーであることを確認します。 従来の仮想ネットワークを使用している場合は、その仮想ネットワークの従来の仮想マシン共同作成者ロールにも `MicrosoftAzureBatch` を参加させます。 

-   必要なアクセス許可を持っていることを確認します。 詳細については、「[アクセス許可を設定する](#perms)」を参照してください。

-   Azure-SSIS IR をホストするための適切なサブネットを選択します。 詳細については、「[サブネットを選択する](#subnet)」を参照してください。 

-   仮想ネットワーク上で独自のドメイン ネーム システム (DNS) サーバーを使用する場合は、「[DNS サーバーを設定する](#dns_server)」を参照してください。 

-   サブネット上でネットワーク セキュリティ グループ (NSG) を使用する場合は、「[NSG を設定する](#nsg)」を参照してください。 

-   Azure ExpressRoute またはユーザー定義ルート (UDR) を使用する場合は、「[Azure ExpressRoute または UDR を使用する](#route)」を参照してください。 

-   仮想ネットワークのリソース グループが特定の Azure ネットワーク リソースを作成および削除できることを確認します。 詳細については、「[リソース グループを設定する](#resource-group)」を参照してください。 

-   [Azure-SSIS IR のカスタムの設定](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)に関する記事で説明されているように Azure-SSIS IR をカスタマイズすると、Azure-SSIS IR ノードは、事前に定義された範囲の 172.16.0.0 から 172.31.255.255 のプライベート IP アドレスを取得します。 そのため、仮想またはオンプレミス ネットワークのプライベート IP アドレスの範囲がこの範囲と競合しないようにしてください。

次の図は、Azure-SSIS IR に必要な接続を示しています。

![Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a> アクセス許可を設定する

Azure-SSIS IR を作成するユーザーは、次のアクセス許可を持っている必要があります。

- SSIS IR を Azure Resource Manager 仮想ネットワークに参加させようとしている場合は、次の 2 つのオプションがあります。

  - 組み込みのネットワーク共同作成者ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。

  - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 

- SSIS IR を従来の仮想ネットワークに参加させる場合は、組み込みの従来の仮想マシン共同作成者ロールを使用することをお勧めします。 そうしない場合は、仮想ネットワークに参加するためのアクセス許可を含むカスタム ロールを定義する必要があります。

### <a name="subnet"></a> サブネットを選択する

サブネットを選択するとき: 

-   Azure-SSIS IR をデプロイするには、GatewaySubnet を選択しないでください。 これは仮想ネットワーク ゲートウェイ専用です。 

-   Azure-SSIS IR が使用するための利用可能なアドレス領域が選択したサブネットに十分にあることを確認してください。 IR ノード番号の 2 倍以上の使用可能な IP アドレスを残しておきます。 Azure は、各サブネット内で一部の IP アドレスを予約します。 これらのアドレスは使用できません。 サブネットの最初と最後の IP アドレスはプロトコル準拠用に予約され、3 つ以上のアドレスが Azure サービスに使用されます。 詳細については、「 [これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

-   (SQL Database マネージド インスタンス、App Service など) 他の Azure サービスによって排他的に専有されているサブネットを使用しないでください。 

### <a name="dns_server"></a> DNS サーバーを設定する 
Azure-SSIS IR が参加する仮想ネットワークで独自の DNS サーバーを使用する必要がある場合は、グローバルな Azure ホスト名 (たとえば、`<your storage account>.blob.core.windows.net` という名前の Azure Storage BLOB) を解決できることを確認してください。 

次の手順が推奨されます。 

-   要求を Azure DNS に転送するようにカスタム DNS を構成します。 独自の DNS サーバー上で、未解決の DNS レコードを Azure の再帰的リゾルバーの IP アドレス (168.63.129.16) に転送することができます。 

-   カスタム DNS を仮想ネットワークのプライマリ DNS サーバーとして設定します。 セカンダリ DNS サーバーとして Azure DNS を設定します。 独自の DNS サーバーが利用できない場合に備えて、Azure の再帰的リゾルバーの IP アドレス (168.63.129.16) をセカンダリ DNS サーバーとして登録します。 

詳細については、「[独自の DNS サーバーを使用する名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。 

### <a name="nsg"></a> NSG を設定する
Azure-SSIS IR によって使用されるサブネットに NSG を実装する必要がある場合は、次のポートを経由する受信および送信トラフィックを許可します。 

| Direction | トランスポート プロトコル | source | 発信元ポート範囲 | Destination | Destination port range | 説明 |
|---|---|---|---|---|---|---|
| 受信 | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (IR を Resource Manager 仮想ネットワークに参加させる場合) <br/><br/>10100、20100、30100 (IR をクラシック仮想ネットワークに参加させる場合)| Data Factory サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS IR のノードと通信します。 <br/><br/> サブネットレベルの NSG を作成するかどうかにかかわらず、Azure-SSIS IR をホストする仮想マシンにアタッチされているネットワーク インターフェイス カード (NIC) のレベルで、Data Factory は NSG を常に構成します。 Data Factory の IP アドレスから指定したポートで受信したトラフィックのみが、その NIC レベルの NSG によって許可されます。 サブネット レベルでインターネット トラフィックに対してこれらのポートを開いている場合でも、Data Factory の IP アドレスではない IP アドレスからのトラフィックは NIC レベルでブロックされます。 |
| 送信 | TCP | VirtualNetwork | * | AzureCloud | 443 | 仮想ネットワークの Azure-SSIS IR のノードはこのポートを使って、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスします。 |
| 送信 | TCP | VirtualNetwork | * | インターネット | 80 | 仮想ネットワーク内の Azure-SSIS IR のノードでは、このポートを使用して、インターネットから証明書失効リストをダウンロードします。 |
| 送信 | TCP | VirtualNetwork | * | SQL | 1433、11000 ～ 11999 | 仮想ネットワーク内の Azure-SSIS IR のノードでは、これらのポートを使用して、SQL Database サーバーによってホストされている SSISDB にアクセスします。 SQL Database のサーバー接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。 この送信セキュリティ規則は、仮想ネットワーク内のマネージド インスタンスによってホストされる SSISDB には適用されません。 |
||||||||

### <a name="route"></a> Azure ExpressRoute または UDR を使用する
[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線を仮想ネットワーク インフラストラクチャに接続してオンプレミス ネットワークを Azure に拡張する場合、一般的な構成では強制トンネリングを使用します (BGP ルート 0.0.0.0/0 を仮想ネットワークにアドバタイズします)。 このトンネリングによって、検査およびログ記録のために、仮想ネットワーク フローからオンプレミス ネットワーク アプライアンスへの送信インターネット トラフィックが強制されます。 
 
または、[UDR](../virtual-network/virtual-networks-udr-overview.md) を定義して、Azure-SSIS IR をホストするサブネットから、検査およびログ記録用のファイアウォールまたは Azure Firewall としてネットワーク仮想アプライアンス (NVA) をホストする別のサブネットへの送信インターネット トラフィックを強制することができます。 

どちらの場合も、トラフィック ルートで、依存している Azure Data Factory サービス (具体的には Azure Batch 管理サービス) から仮想ネットワーク内の Azure-SSIS IR への接続で必要な受信接続が切断されます。 これを回避するには、Azure-SSIS IR を含むサブネット上に 1 つ以上の UDR を定義します。 

Azure ExpressRoute のシナリオで Azure-SSIS IR をホストするサブネット上で、次ホップの種類が **[インターネット]** の 0.0.0.0/0 ルートを適用できます。 または、NVA のシナリオで、次ホップの種類の既存の 0.0.0.0/0 ルートを **[仮想アプライアンス]** から **[インターネット]** に変更することもできます。

![ルートを追加する](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

そのサブネットからの送信インターネット トラフィックを検査する機能が失われることが懸念される場合は、次ホップの種類が **[インターネット]** の Azure Batch 管理サービスと Azure-SSIS IR 間のトラフィックのみをルーティングする特定の UDR を定義できます。

たとえば、Azure-SSIS IR が `UK South` にある場合は、[サービス タグの IP 範囲のダウンロード リンク](https://www.microsoft.com/en-us/download/details.aspx?id=56519)または [Service Tag Discovery API](https://aka.ms/discoveryapi) を使用して、サービス タグ `BatchNodeManagement.UKSouth` の IP 範囲一覧を取得します。 次ホップの種類が **[インターネット]** である関連する IP 範囲のルートの次の UDR を適用します。

![Azure Batch UDR の設定](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> この方法では、追加のメンテナンス コストが発生します。 Azure-SSIS IR が中断されないように、IP 範囲を定期的に確認し、UDR に新しい IP 範囲を追加します。 IP 範囲は月単位で確認することをお勧めします。これは、新しい IP がサービス タグに出現した場合、IP が有効になるまでに 1 か間かかるためです。 

### <a name="resource-group"></a> リソース グループを設定する
Azure SSIS IR では、仮想ネットワークと同じリソース グループ下に特定のネットワーク リソースを作成する必要があります。 これらのリソースには次が含まれます。
   -   Azure ロード バランサー。 *\<Guid>-azurebatch-cloudserviceloadbalancer* という名前で使用される。
   -   Azure パブリック IP アドレス。 *\<Guid>-azurebatch-cloudservicepublicip* という名前で使用される。
   -   ネットワーク ワーク セキュリティ グループ。 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup* という名前で使用される。 

これらのリソースは、IR の起動時に作成されます。 これらは、IR の停止時に削除されます。 IR の停止をブロックしないように、他のリソースでこれらのネットワーク リソースを再利用しないでください。 

仮想ネットワークが属するリソース グループまたはサブスクリプション上でリソースのロックがないことを確認します。 読み取り専用ロックまたは削除ロックを構成すると、IR の起動と停止が失敗したり、IR が応答しなくなったりする可能性があります。 

仮想ネットワークが属するリソース グループまたはサブスクリプション下に次のリソースが作成されるのを妨げる Azure ポリシーがないことを確認します。 
   -   Microsoft.Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a> FAQ

- 受信接続用に Azure-SSIS IR で公開されるパブリック IP アドレスを保護するにはどうすればよいですか。 パブリック IP アドレスを削除することはできますか。
 
    現時点では、Azure-SSIS IR が仮想ネットワークに参加すると、パブリック IP アドレスが自動的に作成されます。 NIC レベルの NSG を使用して、Azure-SSIS IR への受信接続のみを Azure Batch 管理サービスに許可します。 受信保護のために、サブネットレベルの NSG を指定することもできます。

    このシナリオに該当する場合、パブリック IP アドレスが公開されないようにするには、仮想ネットワークではなく、[Azure-SSIS IR のプロキシとしてセルフホステッド IR を構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)ことを検討してください。
 
- データ ソースのファイアウォールの許可リストに Azure-SSIS IR の静的 IP アドレスを追加できますか。
 
    - データ ソースがオンプレミスの場合、仮想ネットワークをオンプレミス ネットワークに接続し、その仮想ネットワーク サブネットに Azure-SSIS IR を参加させた後、そのサブネットの IP 範囲を許可リストに追加できます。
    - データ ソースが仮想ネットワーク サービス エンドポイントでサポートされている Azure サービスである場合は、仮想ネットワーク上で仮想ネットワーク サービス ポイントを構成し、その仮想ネットワーク サブネットに Azure-SSIS IR を参加させることができます。 これで、IP 範囲ではなく、Azure サービスの仮想ネットワーク規則を使用してアクセスを許可できるようになります。
    - データ ソースが異なる種類のクラウド データ ソースである場合は、UDR を使用して Azure-SSIS IR から NVA に送信トラフィックをルーティングするか、静的パブリック IP アドレスを使用して Azure Firewall にルーティングすることができます。 NVA または Azure Firewall のパブリック IP アドレスを許可リストに追加できます。
    - 前の答えがニーズに合わない場合は、[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成して](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)データ ソースへのアクセスを提供することを検討してください。 これで、Azure-SSIS IR を仮想ネットワークに参加させるのではなく、セルフホステッド IR をホストするマシンの IP アドレスを許可リストに追加することができるようになります。

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (データ ファクトリ UI)
このセクションでは、Azure portal とデータ ファクトリ UI を使って、既存の Azure-SSIS IR を仮想ネットワーク (クラシックまたは Azure Resource Manager) に参加させる方法について説明します。 

Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを適切に構成する必要があります。 仮想ネットワークの種類 (クラシックまたは Azure Resource Manager) に該当するセクションの手順を実行してください。 次に、3 番目のセクションの手順に従って、Azure-SSIS IR を仮想ネットワークに参加させます。 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 仮想ネットワークを構成する

Azure-SSIS IR を参加させる前に、ポータルを使用して Azure Resource Manager 仮想ネットワークを構成します。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 フィルターを適用して **[仮想ネットワーク]** を選択します。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク]** ページで、 **[プロパティ]** を選びます。 

1. **[リソース ID]** のコピー ボタンを選び、仮想ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選択します。 使用可能なアドレスの数が Azure-SSIS IR のノード数より多いことを確認します。 

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 または、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   a. Azure portal の左側のメニューで、 **[サブスクリプション]** を選択します。 

   b. サブスクリプションを選択します。 

   c. 左側の **[リソース プロバイダー]** を選択し、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="configure-a-classic-virtual-network"></a>従来の仮想ネットワークを構成する
Azure-SSIS IR を参加させる前に、ポータルを使用して従来の仮想ネットワークを構成します。 

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 

1. **[そのほかのサービス]** を選択します。 **[仮想ネットワーク (クラシック)]** を選びます。 

1. 一覧で目的の仮想ネットワークを選びます。 

1. **[仮想ネットワーク (クラシック)]** ページで、 **[プロパティ]** を選びます。 

   ![クラシック仮想ネットワークのリソース ID](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. **[リソース ID]** のコピー ボタンを選び、クラシック ネットワークのリソース ID をクリップボードにコピーします。 クリップボードから OneNote またはファイルに ID を保存します。 

1. 左側のメニューの **[サブネット]** を選択します。 使用可能なアドレスの数が Azure-SSIS IR のノード数より多いことを確認します。 

   ![仮想ネットワークで使用可能なアドレスの数](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. **MicrosoftAzureBatch** を仮想ネットワークの**従来の仮想マシン共同作成者**ロールに参加させます。 

    a. 左側のメニューの **[アクセス制御 (IAM)]** を選択し、 **[ロール割り当て]** タブを選択します。 

    ![[アクセス制御] ボタンと [追加] ボタン](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. **[ロールの割り当ての追加]** を選択します。

    c. **[ロールの割り当ての追加]** ページで、 **[ロール]** に **[従来の仮想マシン共同作成者]** を選択します。 **[選択]** ボックスに「**ddbf3205-c6bd-46ae-8127-60eb93363864**」を貼り付け、検索結果の一覧から **[Microsoft Azure Batch]** を選択します。 

    ![[ロールの割り当ての追加] ページでの検索結果](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. **[保存]** を選択して設定を保存し、ページを閉じます。 

    ![アクセス設定の保存](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. 共同作成者の一覧に **Microsoft Azure Batch** があることを確認します。 

    ![Azure Batch アクセスの確認](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. 仮想ネットワークが含まれる Azure サブスクリプションに Azure Batch プロバイダーが登録されていることを確認します。 または、Azure Batch プロバイダーを登録します。 Azure Batch アカウントがサブスクリプションに既にある場合は、サブスクリプションは Azure Batch に登録されています。 (Data Factory ポータルで Azure-SSIS IR を作成した場合、Azure Batch プロバイダーが自動的に登録されます。) 

   a. Azure portal の左側のメニューで、 **[サブスクリプション]** を選択します。 

   b. サブスクリプションを選択します。 

   c. 左側の **[リソース プロバイダー]** を選択し、**Microsoft.Batch** が登録済みのプロバイダーであることを確認します。 

   !["登録済み" 状態の確認](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   **Microsoft.Batch** が一覧に表示されない場合は、サブスクリプションに[空の Azure Batch アカウントを作成](../batch/batch-account-create-portal.md)します。 これは後で削除することができます。 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR を仮想ネットワークに参加させる

Azure Resource Manager 仮想ネットワークまたは従来の仮想ネットワークを構成した後は、Azure-SSIS IR を仮想ネットワークに参加させることができます。

1. Microsoft Edge または Google Chrome を起動します。 現時点では、Data Factory UI をサポートしているのはこれらの Web ブラウザーのみです。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[データ ファクトリ]** を選択します。 メニューに **[データ ファクトリ]** が表示されない場合は、 **[その他のサービス]** を選択し、 **[インテリジェンス + 分析]** セクションで **[データ ファクトリ]** を選択します。 

   ![データ ファクトリの一覧](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 一覧で Azure-SSIS IR を使用するデータ ファクトリを選択します。 データ ファクトリのホーム ページが表示されます。 **[作成およびデプロイ]** タイルを選びます。 Data Factory の UI が別のタブに表示されます。 

   ![データ ファクトリのホーム ページ](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. データ ファクトリの UI で、 **[編集]** タブに切り替え、 **[接続]** を選択し、 **[統合ランタイム]** タブに切り替えます。 

   ![[統合ランタイム] タブ](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR が実行されている場合、 **[統合ランタイム]** 一覧の **[アクション]** 列で Azure-SSIS IR の **[停止]** ボタンを選択します。 IR は、停止するまで編集できません。 

   ![IR を停止する](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. **[統合ランタイム]** 一覧の **[アクション]** 列で、Azure-SSIS IR の **[編集]** ボタンを選択します。 

   ![統合ランタイムを編集する](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. **[Integration Runtime Setup]\(統合ランタイムの設定\)** パネルで、 **[次へ]** ボタンを選択して **[全般設定]** ページと **[SQL 設定]** ページで先に進みます。 

1. **[詳細設定]** ページ: 

   a. **[Select a VNet]\(VNet の選択\)** の横にあるチェック ボックスをオンにします。 

   b. **[サブスクリプション]** で、Azure サブスクリプションを選択します。 サブスクリプションで、既存の仮想ネットワークを選択できます。 
  
   c. **[VNET 名]** で仮想ネットワークを選びます。 

   d. **[サブネット名]** で、仮想ネットワークのサブネットを選びます。 

   e. また、セルフホステッド IR を Azure-SSIS IR のプロキシとして構成または管理する場合は、 **[Set-up Self-Hosted]\(セルフホステッドの設定\)** チェック ボックスをオンにします。 詳細については、[セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)方法に関する記事を参照してください。

   f. **[VNet Validation]\(VNet の検証\)** ボタンを選択します。 検証が成功した場合は、 **[次へ]** ボタンを選択します。 

   ![IR セットアップの詳細設定](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. **[概要]** ページで、Azure-SSIS IR のすべての設定を確認します。 次に、 **[更新]** ボタンを選択します。

1. Azure-SSIS IR の **[アクション]** 列の **[開始]** ボタンを選択して、Azure-SSIS IR を開始します。 仮想ネットワークに参加する Azure-SSIS IR が開始されるまでに約 20 から 30 分かかります。 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する
Azure-SSIS IR を仮想ネットワークに参加させる前に、仮想ネットワークを構成する必要があります。 Azure-SSIS IR が仮想ネットワークを参加させるための仮想ネットワークのアクセス許可および設定を自動的に構成するには、次のスクリプトを追加します。

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
Azure-SSIS IR を作成し、作成と同時に仮想ネットワークに参加させることができます。 完全なスクリプトと手順については、[Azure-SSIS IR の作成](create-azure-ssis-integration-runtime.md#azure-powershell)に関する記事を参照してください。

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>既存の Azure-SSIS IR を仮想ネットワークに参加させる
[Azure-SSISIR の作成](create-azure-ssis-integration-runtime.md)に関する記事では、Azure-SSIS IR を作成し、同じスクリプト内の仮想ネットワークに参加させる方法が説明されています。 既に Azure-SSIS IR がある場合は、次の手順に従って仮想ネットワークに参加させます。 
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
Azure-SSIS IR を仮想ネットワークに参加させるには、先に停止する必要があります。 このコマンドは、そのすべてのノードを解放し、課金を停止します。

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Azure-SSIS IR が参加するように仮想ネットワーク設定を構成する

Azure SSIS が参加する仮想ネットワークの設定を構成するには、次のスクリプトを使用します。 

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
仮想ネットワークに参加するように Azure-SSIS IR を構成するには、`Set-AzDataFactoryV2IntegrationRuntime` コマンドを実行します。 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Azure-SSIS IR を開始する
Azure-SSIS IR を開始するには、次のコマンドを実行します。 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

このコマンドは、完了するまでに 20 ～ 30 分かかります。

## <a name="next-steps"></a>次の手順
Azure-SSIS IR の詳細については、次の記事を参照してください。 
- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [チュートリアル:Azure への SSIS パッケージのデプロイに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従って作成します。 このチュートリアルでは、Azure-SSIS IR の作成手順を示しています。 Azure SQL Database を使って SSIS カタログをホストします。 
- 「[Azure-SSIS IR を作成する](create-azure-ssis-integration-runtime.md)」。 この記事は、このチュートリアルを拡張しています。 仮想ネットワーク サービス エンドポイントまたは仮想ネットワーク内のマネージド インスタンスを含む Azure SQL Database を使用して、SSIS カタログをホストする手順について説明されます。 Azure-SSIS IR を仮想ネットワークに参加させる方法が示されます。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR に関する情報を取得する方法が説明されています。 返された情報の状態の説明が提供されます。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
