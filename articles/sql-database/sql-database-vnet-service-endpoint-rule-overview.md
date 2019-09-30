---
title: Azure SQL の単一およびプールされたデータベース用の VNet エンドポイントおよび規則 | Microsoft Docs
description: サブネットを仮想ネットワーク サービス エンドポイントとしてマークします。 その後、仮想ネットワーク規則としてのエインドポイントを Azure SQL Database の ACL に追加します。 SQL Database では、すべての仮想マシンとサブネット上の他のノードからの通信を許可します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 08/27/2019
ms.openlocfilehash: 485c79bab90295cf9af9ef1dbbc209d46931a485
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984934"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>データベース サーバー用の仮想ネットワーク サービス エンドポイントおよび規則を使用する

*仮想ネットワーク規則*は 1 つのファイアウォール セキュリティ機能であり、Azure [SQL Database](sql-database-technical-overview.md) 内の単一データベースおよびエラスティック プール用、または [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 内のデータベース用のデータベース サーバーが、仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御します。 この記事では、仮想ネットワーク規則機能が、場合によっては Azure SQL Database と SQL Data Warehouse への通信を安全に許可するための最適な選択肢となる理由を説明します。

> [!IMPORTANT]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。 関連付けられているサービス エンドポイントがないため、この記事は Azure SQL Database の**マネージド インスタンス** デプロイには*適用されません*。

仮想ネットワーク規則を作成するには、まず、参照する規則の[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。

## <a name="how-to-create-a-virtual-network-rule"></a>仮想ネットワーク規則の作成方法

仮想ネットワーク規則を作成するだけであれば、途中を読み飛ばして、[この記事で後述](#anchor-how-to-by-using-firewall-portal-59j)している手順と説明に進んでください。

<!--<a name="anch-details-about-vnet-rules-38q"/> -->

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各仮想ネットワーク サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク規則は、サーバー上の 1 つの特定のデータベースだけではなく、お使いの Azure SQL Database サーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

- 対照的に、IP 規則はどちらのレベルにも適用できます。

### <a name="security-administration-roles"></a>セキュリティ管理ロール

仮想ネットワーク サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理者:** &nbsp; エンドポイントを有効にします。
- **データベース管理者:** &nbsp;アクセス制御リスト (ACL) を更新して、指定されたサブネットを SQL Database サーバーに追加します。

*RBAC による代替:*

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブネットのみを保持する単一のカスタム ロールを作成するために、Azure には[ロールベースのアクセス制御 (RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム ロールにユーザーを追加する場合と、他の 2 つの主要な管理者ロールにユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

> [!NOTE]
> Azure SQL Database と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションが同じ Azure Active Directory テナントに存在する必要がある。
> - ユーザーに操作 (サービス エンドポイントの有効化や、特定のサーバーへの VNet サブネットの追加など) を開始するために必要な権限がある。
> - 両方のサブスクリプションに Microsoft.Sql プロバイダーが登録されている。

## <a name="limitations"></a>制限事項

Azure SQL Database の場合、仮想ネットワーク規則機能には以下のような制限事項があります。

- SQL Database のファイアウォールでは、各仮想ネットワーク規則はサブネットを参照します。 これらの参照されるサブネットはすべて、SQL Database がホストされているのと同じ geographic 型のリージョンでホストされている必要があります。

- 各 Azure SQL Database サーバーは、指定された仮想ネットワークに対して最大 128 個までの ACL エントリを保持できます。

- 仮想ネットワーク規則は[クラシック デプロイ モデル][arm-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークにのみ適用されます。

- Azure SQL Database に対する仮想ネットワーク サービス エンドポイントを有効にすると、MySQL および PostgreSQL Azure サービスに対してもエンドポイントが有効になります。 ただし、エンドポイントを有効にすると、エンドポイントから MySQL または PostgreSQL インスタンスへの接続の試行は失敗します。
  - 根本的な理由は、MySQL および PostgreSQL には仮想ネットワーク規則が構成されていない可能性があるためです。 接続を成功させるには、Azure Database for MySQL と Azure Database for PostgreSQL の仮想ネットワーク規則を構成する必要があります。

- ファイアウォールでは、IP アドレスは以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
  - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
  - [ExpressRoute][expressroute-indexmd-744v] 経由のオンプレミス

### <a name="considerations-when-using-service-endpoints"></a>サービス エンドポイントを使用する場合の考慮事項

Azure SQL Database のサービス エンドポイントを使用する場合、次の考慮事項を確認してください。

- **Azure SQL Database のパブリック IP へのアウトバウンドが必要**:ネットワーク セキュリティ グループ (NSG) は、接続を許可するために Azure SQL Database IP に対して開かれている必要があります。 Azure SQL Database に NSG の[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して、この設定を行うことができます。

### <a name="expressroute"></a>ExpressRoute

パブリック ピアリングまたは Microsoft ピアリングのためにオンプレミスから [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) を使用している場合、使用されている NAT の IP アドレスを識別する必要があります。 パブリック ピアリングの場合、既定で、Azure サービスのトラフィックが Microsoft Azure のネットワーク バックボーンに入ったときに適用される 2 つの NAT IP アドレスが各 ExpressRoute 回線に使用されます。 Microsoft ピアリングの場合、使用される NAT の IP アドレスは、ユーザーが指定するか、サービス プロバイダーが指定します。 サービス リソースへのアクセスを許可するには、リソースの IP ファイアウォール設定でこれらのパブリック IP アドレスを許可する必要があります。 パブリック ピアリングの ExpressRoute 回線の IP アドレスを確認するには、Azure Portal から [ExpressRoute のサポート チケットを開いて](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)ください。 詳細については、[ExpressRoute のパブリック ピアリングと Microsoft ピアリングの NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering) に関するセクションを参照してください。
  
回線から Azure SQL Database への通信を許可するには、NAT のパブリック IP アドレスに対する IP ネットワーク規則を作成する必要があります。

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->



## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Azure Storage で VNet サービス エンドポイントを使用した場合の影響

Azure Storage は、Azure ストレージ アカウントへの接続を制限できる同じ機能を実装しています。 Azure SQL Server で使用されている Azure ストレージ アカウントでこの機能を使用することにした場合は、問題が発生する可能性があります。 次に、この影響を受ける Azure SQL Database と Azure SQL Data Warehouse の機能の一覧と説明を示します。

### <a name="azure-sql-data-warehouse-polybase"></a>Azure SQL Data Warehouse の PolyBase

PolyBase は、Azure ストレージ アカウントから Azure SQL Data Warehouse にデータを読み込むときによく使用されます。 データの読み込み元の Azure ストレージ アカウントが、アクセス先を一連の VNet サブネットだけに制限している場合、PolyBase からアカウントへの接続は切断されます。 VNet に結び付けられた Azure Storage に接続する Azure SQL Data Warehouse で PolyBase のインポートとエクスポート両方のシナリオを有効にするには、次に示す手順に従います。

#### <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

1.  この[ガイド](https://docs.microsoft.com/powershell/azure/install-az-ps)を使用して、Azure PowerShell をインストールします。
2.  汎用 v1 または BLOB ストレージ アカウントを使用している場合は、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)を使用して、最初に汎用 v2 にアップグレードする必要があります。
3.  Azure ストレージ アカウントの **[Firewalls and Virtual networks]\(ファイアウォールと仮想ネットワーク\)** 設定メニューで、 **[Allow trusted Microsoft services to access this storage account]\(信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します\)** をオンにする必要があります。 詳しくは、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)をご覧ください。
 
#### <a name="steps"></a>手順
1. PowerShell で、Azure SQL Data Warehouse インスタンスをホストする **Azure SQL Server を Azure Active Directory (AAD) に登録します**。

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-SQL-servername -AssignIdentity
   ```
    
   1. この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)を使用して、**汎用 v2 ストレージ アカウント**を作成します。

   > [!NOTE]
   > - 汎用 v1 または BLOB ストレージ アカウントを使用している場合は、この[ガイド](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade)を使用して、**最初に v2 にアップグレードする**必要があります。
   > - Azure Data Lake Storage Gen2 に関する既知の問題については、この[ガイド](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues)をご覧ください。
    
1. お使いのストレージ アカウントで、 **[アクセス制御 (IAM)]** に移動し、 **[ロール割り当ての追加]** をクリックします。 手順 1 で azure Active Direcotory (AAD) に登録した Azure SQL Data Warehouse をホストする Azure SQL Server に**ストレージ BLOB データ共同作成者** RBAC ロールを割り当てます。

   > [!NOTE] 
   > 所有者特権を持つメンバーのみが、この手順を実行できます。 Azure リソースのさまざまな組み込みロールについては、この[ガイド](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)をご覧ください。
  
1. **Azure ストレージ アカウントへの Polybase 接続:**

   1. データベースの **[マスター キー](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** をまだ作成していない場合は、作成します。
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. **IDENTITY = 'Managed Service Identity'** を使用して、データベース スコープ資格情報を作成します。

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - このメカニズムは内部的に[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) を使用するため、Azure Storage アクセス キーにシークレットを指定する必要はありません。
       > - VNet に結び付けられた Azure ストレージ アカウントを PolyBase 接続で操作するためには、ID の名前を **"Managed Service Identity"** にする必要があります。    
    
   1. PolyBase を使用して汎用 v2 ストレージ アカウントに接続するために、abfss:// スキームを使用して外部データ ソースを作成します。

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - 汎用 v1 または BLOB ストレージ アカウントに外部テーブルが既に関連付けられている場合は、まずそれらの外部テーブルを削除した後、対応する外部データ ソースを削除する必要があります。 次に上記のように、汎用 v2 ストレージ アカウントに接続するために abfss:// スキームを使用して外部データ ソースを作成し、この新しい外部データ ソースを使用して、すべての外部テーブルを再作成します。 [スクリプトの生成とパブリッシュ ウィザード](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard)を使用して、すべての外部テーブルを簡単に作成するスクリプトを生成できます。
       > - abfss:// スキームの詳細については、この[ガイド](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri)をご覧ください。
       > - 外部データ ソースの作成の詳細については、この[ガイド](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)をご覧ください。
        
   1. [外部テーブル](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql)を使用して、通常どおりクエリを実行します。

### <a name="azure-sql-database-blob-auditing"></a>Azure SQL Database BLOB の監査

BLOB 監査では、監査ログをユーザー独自のストレージ アカウントにプッシュします。 このストレージ アカウントが VNet サービス エンドポイント機能を使用している場合、Azure SQL Database からストレージ アカウントへの接続は切断されます。

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNet サービス エンドポイントをオンにすることなく VNet ファイアウォール規則をサーバーに追加する

この機能が強化される以前は、ライブ VNet ルールをファイアウォールに実装するには、VNet サービス エンドポイントをオンにする必要がありました。 エンドポイントが特定の VNet サブネットを Azure SQL Database に関連付けていました。 しかし、2018 年 1 月現在からは、**IgnoreMissingVNetServiceEndpoint** フラグを設定することでこの要件を回避できます。

単にファイアウォール規則を設定するだけでは、サーバーのセキュリティ保護には役立ちません。 セキュリティを有効にするには、VNet サービス エンドポイントをオンにする必要もあります。 サービス エンドポイントをオンにする場合、オフからオンへの切り替えが完了するまで VNet サブネットでダウンタイムが発生します。 これは、大規模 VNet のコンテキストに特に当てはまります。 **IgnoreMissingVNetServiceEndpoint** フラグを使用すると、切り替え中のダウンタイムを軽減するか、なくすことができます。

PowerShell を使用して、**IgnoreMissingVNetServiceEndpoint** フラグを設定できます。 詳細については、[PowerShell での仮想ネットワーク サービス エンドポイントと Azure SQL Database の規則の作成][sql-db-vnet-service-endpoint-rule-powershell-md-52d]に関する記事をご覧ください。

## <a name="errors-40914-and-40615"></a>エラー 40914 および 40615

接続エラー 40914 は、Azure Portal の [ファイアウォール] ウィンドウで指定されている "*仮想ネットワーク規則*" に関係があります。 エラー 40615 も同様ですが、ファイアウォールでの "*IP アドレス規則*" に関係している点が異なります。

### <a name="error-40914"></a>エラー 40914

*メッセージ テキスト:* ログインで要求されたサーバー ' *[サーバー名]* ' を開くことができません。 クライアントはサーバーへのアクセスが許可されていません。

*エラーの説明:* クライアントは、仮想ネットワーク サーバーのエンドポイントを持つサブネット内にあります。 しかし、Azure SQL Database サーバーには、SQL Database と通信する権限をサブネットに付与する仮想ネットワーク規則がありません。

*エラーの解決策:* Azure portal の [ファイアウォール] ウィンドウで、仮想ネットワーク規則 コントロールを使って、サブネットの[仮想ネットワーク規則を追加](#anchor-how-to-by-using-firewall-portal-59j)します。

### <a name="error-40615"></a>エラー 40615

*メッセージ テキスト:* ログインにより要求されたサーバー {0} を開けません。 IP アドレス '{1}' のクライアントはこのサーバーへのアクセスが許可されていません。

*エラーの説明:* クライアントは、Azure SQL Database サーバーへの接続を許可されていない IP アドレスから接続しようとしています。 サーバーのファイアウォールには、指定された IP アドレスから SQL Database への通信をクライアントに許可する IP アドレス規則がありません。

*エラーの解決策:* IP ルールとしてクライアントの IP アドレスを入力します。 それには、Azure Portal の [ファイアウォール] ウィンドウを使います。

SQL Database のエラー メッセージの一覧については、[こちら][sql-database-develop-error-messages-419g]をご覧ください。

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal で仮想ネットワーク規則を作成する

このセクションでは、[Azure portal][http-azure-portal-link-ref-477t] を使用して、お使いの Azure SQL Database に*仮想ネットワーク規則*を作成する方法を説明します。 規則では、*仮想ネットワーク サービス エンドポイント*としてタグ付けされた特定のサブネットからの通信を許可するように、お使いの SQL Database に指示します。

> [!NOTE]
> Azure SQL Database サーバーの VNet ファイアウォール規則にサービス エンドポイントを追加する場合は、まず、そのサブネットに対するサービス エンドポイントを有効にする必要があります。
>
> サブネットに対するサービス エンドポイントが有効になっていない場合、それらを有効にするようポータルから求められます。 規則を追加するブレードの **[有効にする]** ボタンをクリックしてください。

## <a name="powershell-alternative"></a>PowerShell による代替

PowerShell スクリプトでも、仮想ネットワーク規則を作成できます。 重要なコマンドレットは **New-AzSqlServerVirtualNetworkRule** です。 ご興味がある方は、[PowerShell での仮想ネットワーク サービス エンドポイントと Azure SQL Database の規則の作成][sql-db-vnet-service-endpoint-rule-powershell-md-52d]に関する記事をご覧ください。

## <a name="rest-api-alternative"></a>REST API の代替手段

内部的には、SQL VNet アクション用の PowerShell コマンドレットは REST API を呼び出します。 REST API を直接呼び出すことができます。

- [仮想ネットワーク規則:操作][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>前提条件

保持している 1 つのサブネットが、Azure SQL Database に関連する特定の Virtual Network エンドポイントの*種類名*で既にタグ付けされている必要があります。

- 関連するエンドポイントの種類名は **Microsoft.Sql** です。
- サブネットが種類名でタグ付けされていない場合は、「[自分のサブネットがエンドポイントであることを確認する][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]」をご覧ください。

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Azure Portal の手順

1. [Azure Portal][http-azure-portal-link-ref-477t] にサインインします。

2. ポータルで **[SQL サーバー]** &gt; **[ファイアウォール / 仮想ネットワーク]** の順に移動します。

3. **[Azure サービスへのアクセスを許可]** の制御を [オフ] に設定します。

    > [!IMPORTANT]
    > 制御を [オン] に設定したままにすると、Azure SQL Database サーバーは、Azure の境界内のすべてのサブネットからの通信 (つまり、Azure データ センターに定義された範囲内にあるものとして認識されたいずれかの IP アドレスからの発信) を受け入れます。 制御を [オン] に設定したままにすると、セキュリティの観点からアクセス過多になる可能性があります。 Microsoft Azure 仮想ネットワーク サービス エンドポイント機能は、SQL Database の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

4. **[仮想ネットワーク]** セクションにある **[既存の追加]** コントロールをクリックします。

    ![[既存の追加] (SQL 規則としてのサブネット エンドポイント) をクリックします。][image-portal-firewall-vnet-add-existing-10-png]

5. 新しい **[作成/更新]** ペインで、お使いの Azure リソース名をコントロールに入力します。

    > [!TIP]
    > お使いのサブネットの正しい**アドレス プレフィックス**を含める必要があります。 ポータルで値を確認できます。
    > **[All resources]\(すべてのリソース\)** &gt; **[All types]\(すべての種類\)** &gt; **[仮想ネットワーク]** の順に移動します。 フィルターにお使いの仮想ネットワークが表示されます。 お使いの仮想ネットワークをクリックし、 **[サブネット]** をクリックします。 **ADDRESS RANGE** 列に、必要なアドレス プレフィックスが含まれています。

    ![新しい規則のフィールドを入力します。][image-portal-firewall-create-update-vnet-rule-20-png]

6. ペイン下部付近にある **[OK]** ボタンをクリックします。

7. ファイアウォール ペインで結果の仮想ネットワーク規則を確認します。

    ![ファイアウォール ペインで新しい規則を確認する][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> 規則には次の状態が適用されます。
> - **準備完了:** 開始した操作が成功したことを示します。
> - **失敗:** 開始した操作が失敗したことを示します。
> - **削除済み:** 削除操作にのみ適用されます。ルールが削除され、適用されなくなったことを示します。
> - **進行中:** 操作が進行中であることを示します。 操作がこの状態にある間は、古い規則が適用されます。

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>関連記事

- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database のサーバー レベルおよびデータベース レベルのファイアウォール規則][sql-db-firewall-rules-config-715d]

Azure SQL Database の仮想ネットワーク規則機能は、2017 年 9 月下旬に利用可能になりました。

## <a name="next-steps"></a>次の手順

- [PowerShell を使用して、仮想ネットワーク サービス エンドポイントと、Azure SQL Database の仮想ネットワーク規則を作成する。][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [仮想ネットワーク規則:REST API を使用した操作][rest-api-virtual-network-rules-operations-862r]

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
