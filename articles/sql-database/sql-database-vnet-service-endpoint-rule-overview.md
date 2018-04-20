---
title: Azure SQL Database の Virtual Network サービス エンドポイントと規則 | Microsoft Docs
description: サブネットを Virtual Network サービス エンドポイントとしてマークします。 その後、仮想ネットワーク規則としてのエインドポイントを Azure SQL Database の ACL に追加します。 SQL Database では、すべての仮想マシンとサブネット上の他のノードからの通信を許可します。
services: sql-database
ms.service: sql-database
author: MightyPen
manager: craigg
ms.custom: VNet Service endpoints
ms.topic: article
ms.date: 03/15/2018
ms.reviewer: genemi
ms.author: dmalik
ms.openlocfilehash: 7622c6e6ffb1410cc2cbd42f6ac3601d281832da
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Azure SQL Database の Virtual Network サービス エンドポイントと規則の使用

*仮想ネットワーク規則*は、Azure SQL Database サーバーが仮想ネットワーク内の特定のサブネットから送信される通信を許可するかどうかを制御する 1 つのファイアウォール セキュリティ機能です。 この記事では、仮想ネットワーク規則機能が、場合によっては Azure SQL Database への通信を安全に許可するための最善の選択になる理由を説明します。

仮想ネットワーク規則を作成するには、まず、参照する規則の[仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]が必要です。


#### <a name="how-to-create-a-virtual-network-rule"></a>仮想ネットワーク規則の作成方法

仮想ネットワーク規則を作成するだけであれば、途中を読み飛ばして、[この記事で後述](#anchor-how-to-by-using-firewall-portal-59j)している手順と説明に進んでください。






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>用語と説明

**仮想ネットワーク:** ユーザーは、お使いの Azure サブスクリプションに関連付けられた仮想ネットワークを保持できます。

**サブネット:** 仮想ネットワークには**サブネット**が含まれます。 保持している任意の Azure 仮想マシン (VM) がサブネットに割り当てられます。 1 つのサブネットには、複数の VM や他のコンピューティング ノードが含まれる場合があります。 お使いの仮想ネットワークの外部にあるコンピューティング ノードは、アクセスを許可するようにセキュリティを構成しない限り、お使いの仮想ネットワークにはアクセスできません。

**Virtual Network サービス エンドポイント:** [Virtual Network サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]は、プロパティ値に 1 つ以上の Azure サービスの種類名が含まれるサブネットです。 この記事では、"SQL Database" という名前の Azure サービスを参照する **Microsoft.Sql** という種類名に注目します。

**仮想ネットワーク規則:** お使いの SQL Database サーバーの仮想ネットワーク規則は、SQL データベース サーバーのアクセス制御リスト (ACL) に記載されているサブネットです。 SQL Database の ACL 内に記載するためには、サブネットに **Microsoft.Sql** という種類名が含まれている必要があります。

仮想ネットワーク規則は、サブネット上にあるどのノードからの通信も許可するように、お使いの SQL Database サーバーに指示します。







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>仮想ネットワーク規則の利点

操作を実行するまで、サブネット上の VM は SQL Database と通信できません。 通信を確立するアクションの 1 つは、仮想ネットワーク ルールの作成です。 VNet ルールの方法を選択する根拠については、ファイアウォールで提供される競合するセキュリティ オプションと比較対照して考察する必要があります。

#### <a name="a-allow-access-to-azure-services"></a>A. Azure サービスへのアクセス許可

ファイアウォール ペインには、**[Azure サービスへのアクセスを許可]** とラベル付けされた **[オン/オフ]**  ボタンがあります。 **[オン]** 設定は、すべての Azure IP アドレスと Azure サブネットからの通信を許可します。 これらの Azure IP またはサブネットは、ユーザーが所有していない場合もあります。 この **[オン]** 設定は、おそらくは SQL Database に期待する範囲を超えて開かれています。 仮想ネットワーク規則機能によって、さらにきめ細かい制御が提供されます。

#### <a name="b-ip-rules"></a>B. IP 規則

SQL Database のファイアウォールでは、SQL Database への通信が許可される IP アドレス範囲を指定できます。 この方法は、Azure プライベート ネットワークの外部にある安定した IP アドレスに適しています。 しかし、Azure プライベート ネットワーク内にある多数のノードは、*動的* IP アドレスで構成されています。 動的 IP アドレスは、VM が再起動されたときなどに変更される場合があります。 運用環境では、ファイアウォール規則に動的 IP アドレスを指定することは、賢明ではありません。

お使いの VM 用に*静的* IP アドレスを取得することで、IP のオプションを復旧することができます。 詳細については、「[Azure Portal を使用して仮想マシンのプライベート IP アドレスを構成する][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]」をご覧ください。

ただし、静的 IP の方法は管理が困難になる場合があり、まとめて実行すると負荷がかかります。 仮想ネットワーク規則を確立して管理するほうが簡単です。

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. サブネット上に SQL Database を保持することは、まだできません。

Azure SQL Database サーバーが仮想ネットワーク内のサブネット上のノードになった場合、仮想ネットワークはお使いの SQL Database と通信できます。 この場合、仮想ネットワーク ルールや IP ルールがなくても、VM は SQL Database と通信できます。

しかし、2017 年 9 月時点ではまだ、Azure SQL Database サービスは、サブネットに割り当て可能なサービスの範囲には含まれていません。






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>仮想ネットワーク規則の詳細

このセクションでは、仮想ネットワーク規則に関する詳細について、いくつか説明します。

#### <a name="only-one-geographic-region"></a>geography 型の 1 つのリージョンのみ

各 Virtual Network サービス エンドポイントは、1 つの Azure リージョンだけに適用されます。 エンドポイントが他のリージョンを有効にして、サブネットからの通信を許可することはありません。

どの仮想ネットワーク規則も、その基本となるエンドポイントが適用先のリージョンに制限されます。

#### <a name="server-level-not-database-level"></a>データベース レベルではなく、サーバー レベル

各仮想ネットワーク規則は、サーバー上の 1 つの特定のデータベースだけではなく、お使いの Azure SQL Database サーバー全体に適用されます。 言い換えると、仮想ネットワーク規則はデータベース レベルではなく、サーバー レベルに適用されます。

- 対照的に、IP 規則はどちらのレベルにも適用できます。

#### <a name="security-administration-roles"></a>セキュリティ管理ロール

Virtual Network サービス エンドポイントの管理では、セキュリティ ロールが分離されています。 以下の各ロールでは、次の操作が必要です。

- **ネットワーク管理:** &nbsp; エンドポイントをオンにする。
- **データベース管理:** &nbsp; アクセス制御リスト (ACL) を更新して、指定されたサブネットを SQL Database サーバーに追加する。

*RBAC による代替:*

ネットワーク管理およびデータベース管理のロールには、仮想ネットワーク規則の管理に必要とされる機能以外もあります。 それらの機能のうち 1 つのサブネットだけが必要になります。

必要な機能のサブネットのみを保持する単一のカスタム ロールを作成するために、Azure には[ロールベースのアクセス制御 (RBAC)][rbac-what-is-813s] を使用するオプションがあります。 ネットワーク管理またはデータベース管理に関連付ける代わりに、カスタム ロールを使用できます。カスタム ロールにユーザーを追加する場合と、他の 2 つの主要な管理者ロールにユーザーを追加する場合では、前者の方がセキュリティ脅威にさらされる領域が少なくなります。

> [!NOTE]
> Azure SQL Database と VNet サブネットが異なるサブスクリプションに存在する場合があります。 このような場合は、次の構成を確認する必要があります。
> - 両方のサブスクリプションが同じ Azure Active Directory テナントに存在する必要がある。
> - ユーザーに操作 (サービス エンドポイントの有効化や、特定のサーバーへの VNet サブネットの追加など) を開始するために必要な権限がある。

## <a name="limitations"></a>制限事項

Azure SQL Database の場合、仮想ネットワーク規則機能には以下のような制限事項があります。

- Web App は、VNet/サブネット内のプライベート IP にマップできます。 サービス エンドポイントが特定の VNet/サブネットから有効化されている場合でも、Web App からサーバーへの接続には、VNet/サブネットのソースではなく、Azure のパブリック IP ソースが使用されます。 サーバーに VNet ファイアウォール ルールがある場合、Web App からそのサーバーへの接続を有効にするには、サーバーで**すべての Azure サービスを許可する**必要があります。

- SQL Database のファイアウォールでは、各仮想ネットワーク規則はサブネットを参照します。 これらの参照されるサブネットはすべて、SQL Database がホストされているのと同じ geographic 型のリージョンでホストされている必要があります。

- 各 Azure SQL Database サーバーは、指定された仮想ネットワークに対して最大 128 個までの ACL エントリを保持できます。

- 仮想ネットワーク規則は[従来のデプロイメント モデル][arm-deployment-model-568f] ネットワークではなく、Azure Resource Manager の仮想ネットワークのみに適用されます。

- Azure SQL Database に対する仮想ネットワーク サービス エンドポイントをオンにすると、MySQL および PostGres Azure サービスに対してもエンドポイントが有効になります。 ただし、エンドポイントをオンにすると、エンドポイントから MySQL または Postgres のインスタンスへの接続の試行は失敗します。
    - 根本的な理由は、MySQL と Postgres が現在は ACL 処理をサポートしていないためです。

- ファイアウォールでは、IP アドレスは以下のネットワーク項目に適用されますが、仮想ネットワーク規則は適用されません。
    - [サイト間 (S2S) 仮想プライベート ネットワーク (VPN)][vpn-gateway-indexmd-608y]
    - [ExpressRoute][expressroute-indexmd-744v] 経由のオンプレミス

#### <a name="considerations-when-using-service-endpoints"></a>サービス エンドポイントを使用する場合の考慮事項
Azure SQL Database のサービス エンドポイントを使用する場合、次の考慮事項を確認してください。

- **Azure SQL Database Public IP への送信が必要**: ネットワーク セキュリティ グループ (NSG) は、接続を許可するために Azure SQL Database IP に対して開かれている必要があります。 Azure SQL Database に NSG の[サービス タグ](../virtual-network/security-overview.md#service-tags)を使用して、この設定を行うことができます。
- **Azure Database for PostgreSQL と MySQL はサポートされない**: サービス エンドポイントは、Azure Database for PostgreSQL または MySQL に対してサポートされていません。 SQL Database へのサービス エンドポイントを有効にすると、これらのサービスへの接続が中断されます。 これに対する軽減策があります。*dmalik@microsoft.com* までお問い合わせください。

#### <a name="expressroute"></a>ExpressRoute

ネットワークが [ExpressRoute][expressroute-indexmd-744v] を使用して Azure ネットワークに接続されている場合、各回線は、Microsoft Edge で 2 つのパブリック IP アドレスを使用して構成されています。 2 つの IP アドレスは、Azure パブリック ピアリングを使用して、Azure Storage などの Microsoft サービスへの接続に使用されます。

回線から Azure SQL Database への通信を許可するには、回線のパブリック IP アドレスに対する IP ネットワーク規則を作成する必要があります。 ExpressRoute 回線のパブリック IP アドレスを見つけるには、Azure Portal を使用して ExpressRoute のサポート チケットを開きます。


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-all-azure-services"></a>[Allow all Azure Services]\(すべての Azure サービスを許可\) を削除した場合の影響

多くのユーザーが、Azure SQL Server から **[Allow all Azure Services]\(すべての Azure サービスを許可\)** を削除し、VNet ファイアウォール規則に置き換えることを望んでいます。
ただし、これを削除すると、Azure SQLDB の次の機能に影響します。

#### <a name="import-export-service"></a>Import Export Service
Azure SQLDB Import Export Service は、Azure の VM 上で実行されます。 これらの VM は VNet に存在しないため、データベースに接続するときに Azure IP を取得します。 **[Allow all Azure Services]\(すべての Azure サービスを許可\)** を削除すると、これらの VM はデータベースにアクセスできなくなります。
この問題は回避することができます。 DACFx API を使用して、BACPAC インポート/エクスポートをコードで直接実行します。 ファイアウォール規則を設定した VNet サブネット内の VM にこれがデプロイされていることを確認してください。

#### <a name="sql-database-query-editor"></a>SQL Database クエリ エディター
Azure SQL Database クエリ エディターは、Azure の VM にデプロイされます。 これらの VM は VNet に存在しません。 そのため、VM はデータベースに接続するときに Azure IP を取得します。 **[Allow all Azure Services]\(すべての Azure サービスを許可\)** を削除すると、これらの VM はデータベースにアクセスできなくなります。

#### <a name="table-auditing"></a>テーブル監査
現在、SQL Database で監査を有効にする方法が 2 つあります。 Azure SQL Server でサービス エンドポイントを有効にすると、テーブル監査は失敗します。 この問題を軽減するには、BLOB 監査に移行します。

#### <a name="impact-on-data-sync"></a>データ同期への影響
Azure SQLDB には、Azure IP を使用してデータベースに接続するデータ同期機能があります。 サービス エンドポイントを使用する場合、使用している論理サーバーへの**すべての Azure サービス**のアクセスの許可をオフにすることがあります。 これにより、データ同期機能が中断されます。

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Azure Storage で VNet サービス エンドポイントを使用した場合の影響

Azure Storage は、ストレージ アカウントへの接続を制限できる同じ機能を実装しています。
Azure SQL Server で使用されているストレージ アカウントでこの機能を使用すると、問題が発生する可能性があります。 この影響を受ける Azure SQLDB の機能について以下に説明します。

#### <a name="azure-sqldw-polybase"></a>Azure SQLDW PolyBase
PolyBase は、ストレージ アカウントから Azure SQLDW にデータを読み込むときによく使用されます。 データの読み込み元のストレージ アカウントが、アクセス先を一連の VNet サブネットだけに制限している場合、PolyBase からアカウントへの接続は切断されます。 これに対する軽減策があります。詳細については、*dmalik@microsoft.com* までお問い合わせください。

#### <a name="azure-sqldb-blob-auditing"></a>Azure SQLDB の BLOB 監査
BLOB 監査では、監査ログをユーザー独自のストレージ アカウントにプッシュします。 このストレージ アカウントが VENT サービス エンドポイント機能を使用している場合、Azure SQLDB からストレージ アカウントへの接続は切断されます。


## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>VNET サービス エンドポイントをオンにすることなく VNET ファイアウォール規則をサーバーに追加する

この機能が強化される以前は、ライブ VNet ルールをファイアウォールに実装するには、VNet サービス エンドポイントをオンにする必要がありました。 エンドポイントが特定の VNet サブネットを Azure SQL Database に関連付けていました。 しかし、2018 年 1 月現在からは、**IgnoreMissingServiceEndpoint** フラグを設定することでこの要件を回避できます。

単にファイアウォール規則を設定するだけでは、サーバーのセキュリティ保護には役立ちません。 セキュリティを有効にするには、VNet サービス エンドポイントをオンにする必要もあります。 サービス エンドポイントをオンにする場合、オフからオンへの切り替えが完了するまで VNet サブネットでダウンタイムが発生します。 これは、大規模 VNet のコンテキストに特に当てはまります。 **IgnoreMissingServiceEndpoint** フラグを使用すると、切り替え中のダウンタイムを軽減または除去できます。

PowerShell を使用することで、**IgnoreMissingServiceEndpoint** フラグを設定できます。 詳細については、「[PowerShell を使用して、Azure SQL Database 用の仮想サービス エンドポイントと規則を作成する][sql-db-vnet-service-endpoint-rule-powershell-md-52d]」をご覧ください。


## <a name="errors-40914-and-40615"></a>エラー 40914 および 40615

接続エラー 40914 は、Azure Portal の [ファイアウォール] ウィンドウで指定されている "*仮想ネットワーク規則*" に関係があります。 エラー 40615 も同様ですが、ファイアウォールでの "*IP アドレス規則*" に関係している点が異なります。

#### <a name="error-40914"></a>エラー 40914

*メッセージ テキスト:* ログインで要求されたサーバー '*[サーバー名]*' を開くことができません。 クライアントはサーバーへのアクセスが許可されていません。

*エラーの説明:* クライアントは、仮想ネットワーク サーバーのエンドポイントを持つサブネット内にあります。 しかし、Azure SQL Database サーバーには、SQL Database と通信する権限をサブネットに付与する仮想ネットワーク規則がありません。

*エラーの解決策:* Azure Portal の [ファイアウォール] ウィンドウの仮想ネットワーク ルール コントロールを使って、サブネットの[仮想ネットワーク規則を追加](#anchor-how-to-by-using-firewall-portal-59j)します。

#### <a name="error-40615"></a>エラー 40615

*メッセージ テキスト:* ログインで要求されたサーバー '{0}' を開くことができません。 IP アドレス '{1}' のクライアントはこのサーバーへのアクセスが許可されていません。

*エラーの説明:* クライアントは、Azure SQL Database サーバーへの接続を許可されていない IP アドレスから接続しようとしています。 サーバーのファイアウォールには、指定された IP アドレスから SQL Database への通信をクライアントに許可する IP アドレス規則がありません。

*エラーの解決策:* IP 規則としてクライアントの IP アドレスを入力します。 それには、Azure Portal の [ファイアウォール] ウィンドウを使います。


SQL Database のエラー メッセージの一覧については、[こちら][sql-database-develop-error-messages-419g]をご覧ください。





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Portal で仮想ネットワーク規則を作成する

このセクションでは、[Azure Portal][http-azure-portal-link-ref-477t] を使用して、お使いの Azure SQL Database に*仮想ネットワーク規則*を作成する方法を説明します。 規則では、*Virtual Network サービス エンドポイント*としてタグ付けされた特定のサブネットからの通信を許可するように、お使いの SQL Database に指示します。

#### <a name="powershell-alternative"></a>PowerShell による代替

PowerShell スクリプトでも、仮想ネットワーク規則を作成できます。 重要なコマンドレットは **New-AzureRmSqlServerVirtualNetworkRule** です。 ご興味がある方は、「[PowerShell to create a Virtual Network service endpoint and rule for Azure SQL Database (PowerShell で Azure SQL Database の Virtual Network サービス エンドポイントと規則を作成する)][sql-db-vnet-service-endpoint-rule-powershell-md-52d]」をご覧ください。

#### <a name="rest-api-alternative"></a>REST API の代替手段

内部的には、SQL VNet アクション用の PowerShell コマンドレットは REST API を呼び出します。 REST API を直接呼び出すことができます。

- [仮想ネットワーク ルール: 操作][rest-api-virtual-network-rules-operations-862r]

#### <a name="prerequisites"></a>前提条件

保持している 1 つのサブネットが、Azure SQL Database に関連する特定の Virtual Network エンドポイントの*種類名*で既にタグ付けされている必要があります。

- 関連するエンドポイントの種類名は **Microsoft.Sql** です。
- サブネットが種類名でタグ付けされていない場合は、「[Verify your subnet is an endpoint (サブネットが 1 つのエンドポイントであることを確認する)][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]」をご覧ください。

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>Azure Portal の手順

1. [Azure Portal][http-azure-portal-link-ref-477t] にログインします。

2. ポータルで **[SQL サーバー]** &gt; **[ファイアウォール / 仮想ネットワーク]** の順に移動します。

3. **[Azure サービスへのアクセスを許可]** の制御を [オフ] に設定します。

    > [!IMPORTANT]
    > 制御を [オン] に設定したままにすると、Azure SQL Database サーバーはすべてのサブネットからの通信を受け入れます。 制御を [オン] に設定したままにすると、セキュリティの観点からアクセス過多になる可能性があります。 Microsoft Azure Virtual Network サービス エンドポイント機能は、SQL Database の仮想ネットワーク規則機能と共に使用することで、セキュリティ脅威にさらされる領域を減少させることができます。

4. **[仮想ネットワーク]** セクションにある **[既存の追加]** コントロールをクリックします。

    ![[既存の追加] (SQL 規則としてのサブネット エンドポイント) をクリックします。][image-portal-firewall-vnet-add-existing-10-png]

5. 新しい **[作成/更新]** ペインで、お使いの Azure リソース名をコントロールに入力します。

    > [!TIP]
    > お使いのサブネットの正しい**アドレス プレフィックス**を含める必要があります。 ポータルで値を確認できます。
    > **[All resources]\(すべてのリソース\)** &gt; **[All types]\(すべての種類\)** &gt; **[仮想ネットワーク]** の順に移動します。 フィルターにお使いの仮想ネットワークが表示されます。 お使いの仮想ネットワークをクリックし、**[サブネット]** をクリックします。 **ADDRESS RANGE** 列に、必要なアドレス プレフィックスが含まれています。

    ![新しい規則のフィールドを入力します。][image-portal-firewall-create-update-vnet-rule-20-png]

6. ペイン下部付近にある **[OK]** ボタンをクリックします。

7.  ファイアウォール ペインで結果の仮想ネットワーク規則を確認します。

    ![ファイアウォール ペインで新しい規則を確認する][image-portal-firewall-vnet-result-rule-30-png]


> [!NOTE]
> 規則には次の状態が適用されます。
> - **準備完了:** 開始した操作が成功したことを示します。
> - **失敗:** 開始した操作が失敗したことを示します。
> - **削除済み:** 削除操作にのみ適用されます。規則が削除され、適用されなくなったことを示します。
> - **進行中:** 操作が進行中であることを示します。 操作がこの状態にある間は、古い規則が適用されます。


<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>関連記事

- [Azure 仮想ネットワーク サービス エンドポイント][vm-virtual-network-service-endpoints-overview-649d]
- [Azure SQL Database サーバー レベルとデータベース レベルのファイアウォール規則][sql-db-firewall-rules-config-715d]

Azure SQL Database の仮想ネットワーク ルール機能は、2017 年 9 月下旬に利用可能になりました。

## <a name="next-steps"></a>次の手順

- [PowerShell を使用して、仮想ネットワーク サービス エンドポイントと、Azure SQL Database の仮想ネットワーク ルールを作成する][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [仮想ネットワーク ルール: 操作][rest-api-virtual-network-rules-operations-862r] (REST API を使用)



<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules



<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
