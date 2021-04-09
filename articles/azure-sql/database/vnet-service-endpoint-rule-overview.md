---
title: Azure SQL Database 内のデータベース用の仮想ネットワーク エンドポイントおよび規則
description: サブネットを仮想ネットワーク サービス エンドポイントとしてマークします。 その後、データベースの ACL に仮想ネットワーク規則としてそのエインドポイントを追加します。 データベースでは、すべての仮想マシンとサブネット上の他のノードからの通信を許可します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 11/14/2019
ms.openlocfilehash: 0dcffe6731c177d1d45c569361fcb200f23af86c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095360"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-servers-in-azure-sql-database"></a>Azure SQL Database のサーバー用の仮想ネットワーク サービス エンドポイントと規則の使用

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

"*仮想ネットワーク規則*" はファイアウォール セキュリティ機能であり、[Azure SQL Database](sql-database-paas-overview.md) 内のデータベースおよびエラスティック プール用、または [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 内の専用 SQL プール (旧称 SQL DW) データベース用のサーバーが、仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御します。 この記事では、仮想ネットワーク規則が、場合によっては SQL Database および Azure Synapse Analytics のデータベースへの通信を安全に許可するための最適な選択肢となる理由について説明します。

> [!NOTE]
> この記事は、SQL Database と Azure Synapse Analytics の両方に適用されます。 簡潔にするために、"*データベース*" という用語で、SQL Database と Azure Synapse Analytics の両方のデータベースを表します。 同様に、"*サーバー*" という言葉は、SQL Database と Azure Synapse Analytics をホストする [論理 SQL サーバー](logical-servers.md)を表します。

仮想ネットワーク規則を作成するには、まず、参照する規則の[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。

## <a name="create-a-virtual-network-rule"></a>仮想ネットワーク規則を作成する

仮想ネットワーク規則の作成のみが必要であれば、途中を読み飛ばして、[この記事で後述](#anchor-how-to-by-using-firewall-portal-59j)している手順と説明に進んでください。

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各仮想ネットワーク サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 そのエンドポイントにより、他のリージョンでサブネットからの通信の受け入れが可能になることはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク規則は、サーバー上の 1 つの特定のデータベースだけではなく、お使いのサーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

対照的に、IP 規則はどちらのレベルにも適用できます。

### <a name="security-administration-roles"></a>セキュリティ管理ロール

仮想ネットワーク サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理者 ([ネットワーク共同作成者](../../role-based-access-control/built-in-roles.md#network-contributor)ロール):** &nbsp;エンドポイントを有効にします。
- **データベース管理者 ([SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)ロール):** &nbsp;アクセス制御リスト (ACL) を更新して、指定されたサブネットをサーバーに追加します。

#### <a name="azure-rbac-alternative"></a>Azure RBAC による代替

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブネットのみを保持する単一のカスタム ロールを作成するために、Azure には[ロールベースのアクセス制御 (RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム役割にユーザーを追加する場合と、他の 2 つの主要な管理者の役割にユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

> [!NOTE]
> SQL Database 内のデータベースと仮想ネットワーク サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
>
> - 両方のサブスクリプションが同じ Azure Active Directory (Azure AD) テナントに存在する必要がある。
> - ユーザーに操作 (サービス エンドポイントの有効化や、特定のサーバーへの仮想ネットワーク サブネットの追加など) を開始するために必要な権限がある。
> - 両方のサブスクリプションに Microsoft.Sql プロバイダーが登録されている。

## <a name="limitations"></a>制限事項

SQL Database の場合、仮想ネットワーク規則機能には以下のような制限事項があります。

- SQL Database 内のデータベースのファイアウォールでは、各仮想ネットワーク規則はサブネットを参照します。 これらの参照されるサブネットはすべて、データベースがホストされているのと同じ地理的リージョンでホストされている必要があります。
- 各サーバーは、任意の仮想ネットワークに対して ACL エントリを 128 個まで保持できます。
- 仮想ネットワーク規則は[クラシック デプロイ モデル][arm-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークのみに適用されます。
- SQL Database に対する仮想ネットワーク サービス エンドポイントを有効にすると、Azure Database for MySQL と Azure Database for PostgreSQL に対してもエンドポイントが有効になります。 エンドポイントが **[オン]** に設定されている場合、エンドポイントから Azure Database for MySQL または Azure Database for PostgreSQL のインスタンスに接続しようとすると、失敗する場合があります。
  - 根本的な理由は、Azure Database for MySQL と Azure Database for PostgreSQL に仮想ネットワーク規則が構成されていない可能性があるためです。 接続を成功させるには、Azure Database for MySQL と Azure Database for PostgreSQL の仮想ネットワーク規則を構成する必要があります。
  - プライベート エンドポイントを使用して既に構成されている SQL 論理サーバー上で仮想ネットワークのファイアウォール規則を定義するには、 **[Deny public network access]\(パブリック ネットワーク アクセスの拒否\)** を **[いいえ]** に設定します。
- ファイアウォールでは、IP アドレスの範囲は以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
  - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
  - [Azure ExpressRoute](../../expressroute/index.yml) 経由のオンプレミス

### <a name="considerations-when-you-use-service-endpoints"></a>サービス エンドポイントを使用する場合の考慮事項

SQL Database に対してサービス エンドポイントを使用する場合は、次の考慮事項を確認してください。

- **Azure SQL Database のパブリック IP へのアウトバウンドが必要です。** ネットワーク セキュリティ グループ (NSG) は、接続を許可するために SQL Database IP に対して開かれている必要があります。 これは、SQL Database 用の NSG [サービス タグ](../../virtual-network/network-security-groups-overview.md#service-tags)を使用して行うことができます。

### <a name="expressroute"></a>ExpressRoute

パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用する場合、使用される NAT の IP アドレスを特定する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、お客様またはサービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 ExpressRoute のパブリックおよび Microsoft ピアリングの NAT の詳細については、「[Azure パブリック ピアリングの NAT 要件](../../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)」を参照してください。

回線から SQL Database への通信を許可するには、NAT のパブリック IP アドレスに対する IP ネットワーク規則を作成する必要があります。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-using-virtual-network-service-endpoints-with-azure-storage"></a>Azure Storage で仮想ネットワーク サービス エンドポイントを使用した場合の影響

Azure Storage は、Azure ストレージ アカウントへの接続を制限できる同じ機能を実装しています。 SQL Database で使用されている Azure Storage アカウントでこの機能を使用することにした場合は、問題が発生する可能性があります。 この影響を受ける SQL Database と Azure Synapse Analytics の機能の一覧と説明を次に示します。

### <a name="azure-synapse-analytics-polybase-and-copy-statement"></a>Azure Synapse Analytics PolyBase と COPY ステートメント

PolyBase と COPY ステートメントは、高スループットのデータ インジェストのために、Azure Storage アカウントから Azure Synapse Analytics にデータを読み込むために一般的に使用されます。 データの読み込み元の Azure Storage アカウントでアクセスが一連の仮想ネットワーク サブネットだけに制限されている場合は、そのストレージ アカウントへの PolyBase と COPY ステートメントを使用した接続は切断されます。 仮想ネットワークに対してセキュリティ保護された Azure Storage に接続している Azure Synapse Analytics で COPY と PolyBase を使用して、インポートおよびエクスポートのシナリオを有効にするには、このセクションの手順に従います。

#### <a name="prerequisites"></a>前提条件

- [こちらのガイド](/powershell/azure/install-az-ps)を使用して、Azure PowerShell をインストールします。
- 汎用 v1 または Azure Blob Storage アカウントが存在する場合は、「[汎用 v2 ストレージ アカウントにアップグレードする](../../storage/common/storage-account-upgrade.md)」の手順に従って、最初に汎用 v2 にアップグレードする必要があります。
- Azure Storage アカウントの **[ファイアウォールと仮想ネットワーク]** 設定メニューで、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** をオンにする必要があります。 この構成を有効にすると、PolyBase と COPY ステートメントは、ネットワーク トラフィックが Azure バックボーン上に残る強力な認証を使用してこのストレージ アカウントに接続できるようになります。 詳細については、[こちらのガイド](../../storage/common/storage-network-security.md#exceptions)を参照してください。

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは、SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 AzureRM モジュールのバグ修正は、少なくとも 2020 年 12 月までは引き続き受け取ることができます。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。 その互換性の詳細については、「[新しい Azure PowerShell Az モジュールの概要](/powershell/azure/new-azureps-module-az)」を参照してください。

#### <a name="steps"></a>手順

1. スタンドアロンの専用 SQL プールがある場合は、PowerShell を使用して SQL サーバーを Azure AD に登録します。

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```

   Azure Synapse Analytics ワークスペース内の専用 SQL プールでは、この手順は必要ありません。

1. Azure Synapse Analytics ワークスペースがある場合は、ワークスペースのシステム マネージド ID を登録します。

   1. Azure portal で Azure Synapse Analytics ワークスペースに移動します。
   2. **[マネージド ID]** ペインに移動します。
   3. **[パイプラインの許可]** オプションが有効になっていることを確認します。
   
1. 「[ストレージ アカウントを作成する](../../storage/common/storage-account-create.md)」の手順に従って、**汎用 v2 ストレージ アカウント** を作成します。

   > [!NOTE]
   >
   > - 汎用 v1 または Blob Storage アカウントが存在する場合は、「[汎用 v2 ストレージ アカウントにアップグレードする](../../storage/common/storage-account-upgrade.md)」の手順に従って、"*最初に v2 にアップグレードする*" 必要があります。
   > - Azure Data Lake Storage Gen2 に関する既知の問題については、「[Azure Data Lake Storage Gen2 に関する既知の問題](../../storage/blobs/data-lake-storage-known-issues.md)」を参照してください。

1. お使いのストレージ アカウントで、 **[アクセス制御 (IAM)]** に移動し、 **[ロールの割り当ての追加]** を選択します。 Azure AD に登録した、専用 SQL プールをホストしているサーバーまたはワークスペースに、**ストレージ BLOB データ共同作成者** Azure ロールを割り当てます。

   > [!NOTE]
   > ストレージ アカウントの所有者特権を持つメンバーのみが、この手順を実行できます。 さまざまな Azure の組み込みロールについては、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。
  
1. Azure Storage アカウントへの PolyBase 接続を有効にするには、次のようにします。

   1. データベースの[マスター キー](/sql/t-sql/statements/create-master-key-transact-sql)をまだ作成していない場合は、作成します。

       ```sql
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```

   1. **IDENTITY = 'Managed Service Identity'** を使用して、データベース スコープ資格情報を作成します。

       ```sql
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```

       > [!NOTE]
       >
       > - このメカニズムは内部的に[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を使用するため、Azure Storage アクセス キーを使用して SECRET を指定する必要はありません。
       > - 仮想ネットワークに対してセキュリティ保護された Azure Storage アカウントを操作するには、PolyBase 接続用の IDENTITY の名前を **"Managed Service Identity"** にする必要があります。

   1. PolyBase を使用して汎用 v2 ストレージ アカウントに接続するための `abfss://` スキームを使用して外部データ ソースを作成します。

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```

       > [!NOTE]
       >
       > - 汎用 v1 または Blob Storage アカウントに外部テーブルが既に関連付けられている場合は、まずそれらの外部テーブルを削除する必要があります。 その後、対応する外部データ ソースを削除します。 次に、前に示したように、汎用 v2 ストレージ アカウントに接続する `abfss://` スキームを使用して外部データ ソースを作成します。 そして、この新しい外部データ ソースを使用して、すべての外部テーブルを再作成します。 [[スクリプトの生成とパブリッシュ] ウィザード](/sql/ssms/scripting/generate-and-publish-scripts-wizard)を使用して、すべての外部テーブルを簡単に作成するスクリプトを生成できます。
       > - `abfss://` スキームの詳細については、「[Azure Data Lake Storage Gen2 URI を使用する](../../storage/blobs/data-lake-storage-introduction-abfs-uri.md)」を参照してください。
       > - CREATE EXTERNAL DATA SOURCE の詳細については、[こちらのガイド](/sql/t-sql/statements/create-external-data-source-transact-sql)を参照してください。

   1. [外部テーブル](/sql/t-sql/statements/create-external-table-transact-sql)を使用して、通常どおりクエリを実行します。

### <a name="sql-database-blob-auditing"></a>SQL Database BLOB の監査

BLOB 監査では、監査ログをユーザー独自のストレージ アカウントにプッシュします。 このストレージ アカウントで仮想ネットワーク サービス エンドポイント機能が使用される場合、SQL Database からストレージ アカウントへの接続は切断されます。

## <a name="add-a-virtual-network-firewall-rule-to-your-server"></a>仮想ネットワーク ファイアウォール規則をサーバーに追加する

この機能が強化される前は、ライブ仮想ネットワーク規則をファイアウォールに実装するには、仮想ネットワーク サービス エンドポイントを有効にする必要がありました。 それらのエンドポイントが特定の仮想ネットワーク サブネットを SQL Database 内のデータベースに関連付けていました。 2018 年 1 月以降、**IgnoreMissingVNetServiceEndpoint** フラグを設定することでこの要件を回避できます。 仮想ネットワーク サービス エンドポイントを有効にすることなく、仮想ネットワーク ファイアウォール規則をサーバーに追加できるようになりました。

単にファイアウォール規則を設定するだけでは、サーバーのセキュリティ保護には役立ちません。 セキュリティを有効にするには、仮想ネットワーク サービス エンドポイントを有効にする必要もあります。 サービス エンドポイントをオンにすると、オフからオンへの切り替えが完了するまで仮想ネットワーク サブネットでダウンタイムが発生します。 このダウンタイムの期間は、特に大規模な仮想ネットワークの状況で当てはまります。 **IgnoreMissingVNetServiceEndpoint** フラグを使用すると、切り替え中のダウンタイムを軽減するか、なくすことができます。

PowerShell を使用して、**IgnoreMissingVNetServiceEndpoint** フラグを設定できます。 詳細については、[PowerShell での仮想ネットワーク サービス エンドポイントと SQL Database の規則の作成][sql-db-vnet-service-endpoint-rule-powershell-md-52d]に関する記事を参照してください。

## <a name="errors-40914-and-40615"></a>エラー 40914 および 40615

接続エラー 40914 は、Azure portal の **[ファイアウォール]** ペインで指定されている "*仮想ネットワーク規則*" に関係があります。 エラー 40615 も同様ですが、ファイアウォールでの "*IP アドレス規則*" に関係している点が異なります。

### <a name="error-40914"></a>エラー 40914

**メッセージ テキスト:** "Cannot open server '[server-name]' requested by the login. (ログインにより要求されたサーバー ' *[server-name]* ' を開くことができません。) Client is not allowed to access the server. (クライアントはサーバーへのアクセスを許可されていません。)"

**エラーの説明:** クライアントは、仮想ネットワーク サーバーのエンドポイントを持つサブネット内にあります。 しかし、サーバーには、データベースと通信する権限をサブネットに付与する仮想ネットワーク規則がありません。

**エラーの解決策:** Azure portal の **[ファイアウォール]** ペインで、仮想ネットワーク規則コントロールを使用して、サブネットの [仮想ネットワーク規則を追加](#anchor-how-to-by-using-firewall-portal-59j)します。

### <a name="error-40615"></a>エラー 40615

**メッセージ テキスト:** "Cannot open server '{0}' requested by the login. (ログインにより要求されたサーバー '{0}' を開くことができません。) Client with IP address '{1}' is not allowed to access the server. (IP アドレス '{1}' のクライアントはこのサーバーへのアクセスを許可されていません。)"

**エラーの説明:** サーバーへの接続を許可されていない IP アドレスからクライアントが接続しようとしています。 サーバーのファイアウォールには、指定された IP アドレスからデータベースへの通信をクライアントに許可する IP アドレス規則がありません。

**エラーの解決策:** IP ルールとしてクライアントの IP アドレスを入力します。 この手順を実行するには、Azure portal の **[ファイアウォール]** ペインを使用します。

<a name="anchor-how-to-by-using-firewall-portal-59j"></a>

## <a name="use-the-portal-to-create-a-virtual-network-rule"></a>ポータルを使用して仮想ネットワーク規則を作成する

このセクションでは、[Azure portal][http-azure-portal-link-ref-477t] を使用して、SQL Database 内のデータベースに "*仮想ネットワーク規則*" を作成する方法について説明します。 この規則は、"*仮想ネットワーク サービス エンドポイント*" としてタグ付けされた特定のサブネットからの通信を許可するようにデータベースに指示します。

> [!NOTE]
> サーバーの仮想ネットワーク ファイアウォール規則にサービス エンドポイントを追加する場合は、まず、サービス エンドポイントがそのサブネットに対して有効になっていることを確認します。
>
> サービス エンドポイントがサブネットに対して有効になっていない場合、それらを有効にするようポータルから求められます。 規則を追加する場合と同じペインの **[有効化]** ボタンを選択してください。

## <a name="powershell-alternative"></a>PowerShell による代替

スクリプトでは、PowerShell コマンドレット **New-AzSqlServerVirtualNetworkRule** または [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) を使用して仮想ネットワーク規則を作成することもできます。 関心をお持ちの場合は、[PowerShell での仮想ネットワーク サービス エンドポイントと SQL Database の規則の作成][sql-db-vnet-service-endpoint-rule-powershell-md-52d]に関する記事を参照してください。

## <a name="rest-api-alternative"></a>REST API の代替手段

SQL 仮想ネットワーク アクション用の PowerShell コマンドレットは、内部的に REST API を呼び出します。 REST API を直接呼び出すことができます。

- [仮想ネットワーク規則:操作][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>前提条件

保持している 1 つのサブネットが、SQL Database に関連する特定の仮想ネットワーク サービス エンドポイントの "*種類名*" で既にタグ付けされている必要があります。

- 関連するエンドポイントの種類名は **Microsoft.Sql** です。
- サブネットが種類名でタグ付けされていない場合は、「[自分のサブネットがエンドポイントであることを確認する][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]」をご覧ください。

<a name="a-portal-steps-for-vnet-rule-200"></a>

## <a name="azure-portal-steps"></a>Azure Portal の手順

1. [Azure portal][http-azure-portal-link-ref-477t] にサインインします。

1. **SQL サーバー** を検索して選択してから、使用するサーバーを選択します。 **[セキュリティ]** で、 **[ファイアウォールと仮想ネットワーク]** を選択します。

1. **[Azure サービスへのアクセスを許可]** を **[オフ]** に設定します。

    > [!IMPORTANT]
    > この制御を **[オン]** に設定したままにすると、サーバーは Azure 境界内のすべてのサブネットからの通信を受け入れます。 これは、Azure データセンター用に定義された範囲内として認識された IP アドレスのいずれかから発信された通信です。 この制御を **[オン]** に設定したままにすると、セキュリティの観点から見れば過度なアクセスになる可能性があります。 Microsoft Azure Virtual Network サービス エンドポイント機能は、SQL Database の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

1. **[仮想ネットワーク]** セクションにある **[+ 既存を追加]** を選択します。

    ![[+ 既存を追加] (SQL 規則としてのサブネット エンドポイント) の選択を示すスクリーンショット。][image-portal-firewall-vnet-add-existing-10-png]

1. 新しい **[作成/更新]** ペインで、お使いの Azure リソースの名前をボックスに入力します。

    > [!TIP]
    > お使いのサブネットの正しいアドレス プレフィックスを含める必要があります。 **[アドレス プレフィックス]** 値はポータルで確認できます。 **[すべてのリソース]** &gt; **[すべての種類]** &gt; **[仮想ネットワーク]** の順に移動します。 フィルターにお使いの仮想ネットワークが表示されます。 仮想ネットワークを選択し、 **[サブネット]** を選択します。 **[アドレス範囲]** 列に、必要なアドレス プレフィックスが含まれています。

    ![新しい規則のボックスへの入力を示すスクリーンショット。][image-portal-firewall-create-update-vnet-rule-20-png]

1. ペインの下部付近にある **[OK]** ボタンを選択します。

1. **[ファイアウォール]** ペインで結果の仮想ネットワーク規則を確認します。

    ![[ファイアウォール] ペインの新しい規則を示すスクリーンショット。][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 規則には次の状態が適用されます。
>
> - **準備完了**:開始した操作が成功したことを示します。
> - **[失敗]** : 開始した操作が失敗したことを示します。
> - **削除済み**:削除操作にのみ適用されます。規則が削除され、適用されなくなったことを示します。
> - **進行中**:操作が進行中であることを示します。 操作がこの状態にある間は、古い規則が適用されます。

<a name="anchor-how-to-links-60h"></a>

## <a name="related-articles"></a>関連記事

- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]
- [サーバー レベルとデータベース レベルのファイアウォール規則][sql-db-firewall-rules-config-715d]

## <a name="next-steps"></a>次のステップ

- [PowerShell を使用して、仮想ネットワーク サービス エンドポイントと、SQL Database の仮想ネットワーク規則を作成する][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [仮想ネットワーク規則:REST API を使用した操作][rest-api-virtual-network-rules-operations-862r]

<!-- Link references, to images. -->
[image-portal-firewall-vnet-add-existing-10-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png
[image-portal-firewall-create-update-vnet-rule-20-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png
[image-portal-firewall-vnet-result-rule-30-png]: ../../sql-database/media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../../azure-resource-manager/management/deployment-models.md
[expressroute-indexmd-744v]:../index.yml
[rbac-what-is-813s]:../../role-based-access-control/overview.md
[sql-db-firewall-rules-config-715d]:firewall-configure.md
[sql-db-vnet-service-endpoint-rule-powershell-md-52d]:scripts/vnet-service-endpoint-rule-powershell-create.md
[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]:scripts/vnet-service-endpoint-rule-powershell-create.md#a-verify-subnet-is-endpoint-ps-100
[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[vm-virtual-network-service-endpoints-overview-649d]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[vpn-gateway-indexmd-608y]: ../../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->
[http-azure-portal-link-ref-477t]: https://portal.azure.com/
[rest-api-virtual-network-rules-operations-862r]: /rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON
- Azure CLI
- ARM templates
-->
