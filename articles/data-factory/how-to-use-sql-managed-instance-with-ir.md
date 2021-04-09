---
title: Azure Data Factory 内で Azure SQL Server Integration Services (SSIS) と共に Azure SQL Managed Instance を使用する
description: Azure Data Factory 内で SQL Server Integration Services (SSIS) と共に Azure SQL Managed Instance を使用する方法について説明します。
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 62bd5d2e70d3a66998907305fecee4dcc87cdb23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451729"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Azure Data Factory 内で SQL Server Integration Services (SSIS) と共に Azure SQL Managed Instance を使用する

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

SQL Server Integration Services (SSIS) プロジェクト、パッケージ、ワークロードを Azure クラウドに移動できるようになりました。 SQL Server Management Studio (SSMS) などのよく使われるツールを利用して、SSIS プロジェクトとパッケージを Azure SQL Database または SQL Managed Instance でデプロイ、実行、管理します。 この記事では、Azure SQL Managed Instance を Azure-SSIS Integration Runtime (IR) と共に使用するときの、次の特定の領域を取り上げます。

- [Azure SQL Managed Instance によってホストされている SSIS カタログ (SSISDB) を使用して Azure-SSIS IR をプロビジョニングする](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Azure SQL Managed Instance エージェント ジョブによって SSIS パッケージを実行する](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Azure SQL Managed Instance エージェント ジョブによって SSISDB ログをクリーンアップする](#clean-up-ssisdb-logs)
- [Azure SQL Managed Instance による Azure-SSIS IR フェールオーバー](configure-bcdr-azure-ssis-integration-runtime.md)
- [データベース ワークロードの宛先として Azure SQL Managed Instance を使用して、ADF の SSIS にオンプレミスの SSIS ワークロードを移行する](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Azure SQL Managed Instance によってホストされている SSISDB を使用して Azure-SSIS IR をプロビジョニングする

### <a name="prerequisites"></a>前提条件

1. Azure Active Directory 認証を選択する場合は、[Azure SQL Managed Instance に対して Azure Active Directory (Azure AD) を有効にします](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)。

1. SQL Managed Instance を接続する方法を、プライベート エンドポイント経由かパブリック エンドポイント経由か選択します。

    - プライベート エンドポイント経由 (優先)

        1. Azure-SSIS IR が参加する仮想ネットワークを選択します。
            - **サブネットが異なる**、マネージド インスタンスと同じ仮想ネットワーク内部。
            - 仮想ネットワーク ピアリング経由 (グローバル VNet ピアリング制約のために同じリージョンに制限)、または仮想ネットワークと仮想ネットワークの間の接続を介した、マネージド インスタンスとは異なる仮想ネットワーク内部。

            SQL Managed Instance 接続の詳細については、[Azure SQL Managed Instance へのご自分のアプリケーションの接続](/azure/sql-database/sql-database-managed-instance-connect-app)に関するページをご覧ください。

        1. [仮想ネットワークを構成します](#configure-virtual-network)。

    - パブリック エンドポイント経由

        Azure SQL Managed Instance は、[パブリック エンドポイント](../azure-sql/managed-instance/public-endpoint-configure.md)経由で接続を提供できます。 SQL Managed Instance と Azure-SSIS IR の間のトラフィックを許可するには、受信と送信の要件を満たす必要があります。

        - Azure-SSIS IR が仮想ネットワーク内にない場合 (優先)

            Azure-SSIS IR からの受信トラフィックを許可するための **SQL Managed Instance の受信要件**。

            | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 | Destination port range |
            |---|---|---|---|---|
            |TCP|Azure クラウド サービス タグ|*|VirtualNetwork|3342|

            詳細については、「[ネットワーク セキュリティ グループでパブリック エンドポイント トラフィックを許可する](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)」をご覧ください。

        - Azure-SSIS IR が仮想ネットワーク内部にある場合

            SQL Managed Instance が Azure-SSIS IR でサポートされていないリージョンにあり、グローバル VNet ピアリングの制限のために VNet ピアリングを使用しない Azure-SSIS IR が仮想ネットワーク内部にあるという特別なシナリオがあります。 このシナリオでは、**仮想ネットワーク内の Azure-SSIS IR** は、**パブリック エンドポイント経由** で SQL Managed Instance に接続します。 以下のネットワーク セキュリティ グループ (NSG) ルールを使用して、SQL Managed Instance と Azure-SSIS IR 間のトラフィックを許可します。

            1. Azure-SSIS IR からの受信トラフィックを許可するための **SQL Managed Instance の受信要件**。

                | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 |Destination port range |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR の静的 IP アドレス <br> 詳細については、[Azure-SSIS IR への独自のパブリック IP の使用](join-azure-ssis-integration-runtime-virtual-network.md#publicIP)に関するページをご覧ください。|*|VirtualNetwork|3342|

             1. SQL Managed Instance への送信トラフィックを許可するための **Azure-SSIS IR の送信要件**。

                | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 |Destination port range |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL Managed Instance のパブリック エンドポイント IP アドレス](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>仮想ネットワークを構成する

1. **ユーザーのアクセス許可**: Azure-SSIS IR を作成するユーザーには、少なくとも Azure Data Factory リソースに対する[ロールを割り当てる](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)必要があります。次のオプションがあります。

    - 組み込みのネットワーク共同作成者ロールを使用します。 このロールには、必要なスコープよりずっと大きなスコープを持つ _Microsoft.Network/\*_ アクセス許可が備わっています。
    - 必要な _Microsoft.Network/virtualNetworks/\*/join/action_ アクセス許可のみを含むカスタム ロールを作成してください。 また、Azure-SSIS IR を Azure Resource Manager 仮想ネットワークに参加させるときに独自のパブリック IP アドレスを使用する場合は、_Microsoft.Network/publicIPAddresses/*/join/action_ アクセス許可もロールに含めてください。

1. **Virtual network**。

    1. 仮想ネットワークのリソース グループが特定の Azure ネットワーク リソースを作成および削除できることを確認します。

        Azure SSIS IR では、仮想ネットワークと同じリソース グループ下に特定のネットワーク リソースを作成する必要があります。 これらのリソースには次が含まれます。
        - *\<Guid>-azurebatch-cloudserviceloadbalancer* という名前の Azure ロード バランサー
        - *\<Guid>-azurebatch-cloudservicenetworksecuritygroup という名前のネットワーク セキュリティ グループ
        - -azurebatch-cloudservicepublicip という名前の Azure パブリック IP アドレス

        これらのリソースは、Azure-SSIS IR の開始時に作成されます。 これらは、Azure-SSIS IR の停止時に削除されます。 Azure-SSIS IR の停止がブロックされないように、他のリソースでこれらのネットワーク リソースを再利用しないでください。

    1. 仮想ネットワークが属するリソース グループまたはサブスクリプション上で[リソースのロック](../azure-resource-manager/management/lock-resources.md)がないことを確認します。 読み取り専用または削除ロックを構成した場合、Azure-SSIS IR の開始と停止が失敗するか、応答しなくなります。

    1. 仮想ネットワークが属するリソース グループまたはサブスクリプション下に次のリソースが作成されるのを妨げる Azure ポリシーがないことを確認します。
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. ネットワーク セキュリティ グループ (NSG) ルールに基づいてトラフィックを許可して、SQL Managed Instance と Azure-SSIS IR 間のトラフィックと、Azure-SSIS IR が必要とするトラフィックを許可します。
        1. Azure-SSIS IR からの受信トラフィックを許可するための **SQL Managed Instance の受信要件**。

            | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 | Destination port range | 説明 |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433、11000 ～ 11999|SQL Database のサーバー接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。|

        1. SQL Managed Instance への送信トラフィックと、Azure-SSIS IR で必要とされる他のトラフィックを許可するための **Azure-SSIS IR の送信要件**。

        | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 | Destination port range | 説明 |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433、11000 ～ 11999 |SQL Managed Instance への送信トラフィックを許可します。 接続ポリシーが **[リダイレクト]** ではなく **[プロキシ]** に設定されている場合、ポート 1433 のみが必要です。 |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | 仮想ネットワークの Azure-SSIS IR のノードはこのポートを使って、Azure Storage や Azure Event Hubs などの Azure サービスにアクセスします。 |
        | TCP | VirtualNetwork | * | インターネット | 80 | (省略可能) 仮想ネットワーク内の Azure-SSIS IR のノードでは、このポートを使用して、インターネットから証明書失効リストをダウンロードします。 このトラフィックをブロックすると、IR の開始時にパフォーマンスが低下し、証明書の使用状況について証明書失効リストを確認する機能が失われる可能性があります。 送信先を特定の FQDN にさらに絞り込む場合は、[Azure ExpressRoute またはユーザー定義ルート (UDR) の使用](./join-azure-ssis-integration-runtime-virtual-network.md#route)に関するページをご覧ください。|
        | TCP | VirtualNetwork | * | ストレージ | 445 | (省略可能) この規則は、Azure Files に格納されている SSIS パッケージを実行する場合にのみ必要です。 |
        |||||||

        1. Azure-SSIS IR に必要なトラフィックを許可するための、**Azure-SSIS IR の受信要件**。

        | トランスポート プロトコル | source | 発信元ポート範囲 | 宛先 | Destination port range | 説明 |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876、29877 (IR を Resource Manager 仮想ネットワークに参加させる場合) <br/><br/>10100、20100、30100 (IR をクラシック仮想ネットワークに参加させる場合)| Data Factory サービスはこれらのポートを使って、仮想ネットワークの Azure-SSIS IR のノードと通信します。 <br/><br/> サブネットレベルの NSG を作成するかどうかにかかわらず、Azure-SSIS IR をホストする仮想マシンにアタッチされているネットワーク インターフェイス カード (NIC) のレベルで、Data Factory は NSG を常に構成します。 Data Factory の IP アドレスから指定したポートで受信したトラフィックのみが、その NIC レベルの NSG によって許可されます。 サブネット レベルでインターネット トラフィックに対してこれらのポートを開いている場合でも、Data Factory の IP アドレスではない IP アドレスからのトラフィックは NIC レベルでブロックされます。 |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (省略可能) このルールは、Microsoft サポーターがお客様に対して、高度なトラブルシューティングのために開くように依頼した場合にのみ必要になり、トラブルシューティングの直後に閉じることができます。 **CorpNetSaw** サービス タグでは、Microsoft 企業ネットワーク上のセキュリティで保護されたアクセス ワークステーションでのみ、リモート デスクトップの使用が許可されます。 このサービス タグはポータルから選択することはできず、Azure PowerShell または Azure CLI 経由でのみ使用できます。 <br/><br/> NIC レベルの NSG では、ポート 3389 が既定で開かれ、サブネット レベルの NSG ではポート 3389 を制御できます。一方、保護のために各 IR ノードの Windows ファイアウォール規則では既定で、Azure-SSIS IR によってポート 3389 の送信が禁止されています。 |
        |||||||

    1. 詳細については、[仮想ネットワークの構成](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)に関するページをご覧ください。
        - Azure-SSIS IR に独自のパブリック IP アドレスを使用する
        - 独自のドメイン ネーム システム (DNS) サーバーを使用する
        - Azure ExpressRoute またはユーザー定義ルート (UDR) を使用する
        - カスタマイズされた Azure-SSIS IR を使用する

### <a name="provision-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime をプロビジョニングする

1. SQL Managed Instance のプライベート エンドポイントまたはパブリック エンドポイントを選択します。

    Azure portal/ADF アプリの [SQL の設定] ページで [Azure-SSIS IR をプロビジョニング](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)するとき、SSIS カタログ (SSISDB) の作成時に、SQL Managed Instance の **プライベート エンドポイント** または **パブリック エンドポイント** を使用します。

    パブリック エンドポイントのホスト名が <mi_name>.public.<dns_zone>.database.windows.net 形式になっており、接続に使用されるポートが 3342 です。  

    ![統合ランタイム セットアップのスクリーンショット。SSIS カタログの作成が選択されており、Catalog データベース サーバー エンドポイントが入力されています。](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. 適用する場合は、[Azure AD 認証] を選択します。

    ![カタログ-パブリック-エンドポイント](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Azure AD 認証を有効にする方法の詳細については、[Azure SQL Database Managed Instance に対する Azure AD の有効化](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)に関するセクションをご覧ください。

1. 適用する場合は、Azure-SSIS IR を仮想ネットワークに参加させます。

    [詳細設定] ページで、参加する仮想ネットワークとサブネットを選択します。
    
    SQL Managed Instance と同じ仮想ネットワーク内の場合、SQL Managed Instance とは **異なるサブネット** を選択します。 

    Azure-SSIS IR を仮想ネットワークに参加させる方法の詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」をご覧ください。

    ![統合ランタイム セットアップの詳細設定のスクリーンショット。ここで、参加するランタイムに仮想ネットワークを選択できます。](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Azure-SSIS IR を作成する方法の詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)」を参照してください。

## <a name="clean-up-ssisdb-logs"></a>SSISDB ログのクリーンアップ

SSISDB ログの保持ポリシーは [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database) の以下のプロパティで定義されています。

- OPERATION_CLEANUP_ENABLED

    値が TRUE の場合、RETENTION_WINDOW (日) より古い操作の詳細および操作のメッセージは、カタログから削除されます。 値が FALSE の場合は、すべての操作の詳細と操作のメッセージがカタログに格納されます。 注: SQL Server ジョブでは操作のクリーンアップを実行します。

- RETENTION_WINDOW

    操作の詳細と操作のメッセージがカタログに格納される日数。 値が -1 の場合、リテンション期間は無期限です。 注:クリーンアップが必要ない場合は、OPERATION_CLEANUP_ENABLED を FALSE に設定します。

管理者によって設定された保持期間が経過した SSISDB のログを削除するには、ストアド プロシージャ `[internal].[cleanup_server_retention_window_exclusive]` をトリガーできます。 必要に応じて、ストアド プロシージャをトリガーするように、SQL Managed Instance エージェント ジョブの実行をスケジュールできます。

## <a name="next-steps"></a>次のステップ

- [Azure SQL Managed Instance エージェント ジョブによって SSIS パッケージを実行する](how-to-invoke-ssis-package-managed-instance-agent.md)
- [ビジネス継続性とディザスター リカバリー (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [ADF でオンプレミスの SSIS ワークロードを SSIS に移行する](scenario-ssis-migration-overview.md)