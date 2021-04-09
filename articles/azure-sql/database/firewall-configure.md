---
title: IP ファイアウォール規則
description: Azure SQL Database または Azure Synapse Analytics のファイアウォールに、データベース用のサーバー レベルの IP ファイアウォール規則を構成します。 SQL Database のアクセスを管理し、データベース レベルの IP ファイアウォール規則を構成します。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: bbad7dcaa1d92df4969c88e4ba86a62987509e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632801"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database と Azure Synapse の IP ファイアウォール規則
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

たとえば、Azure SQL Database または Azure Synapse Analytics 内に *mysqlserver* という名前の新しいサーバーを作成すると、サーバー レベルのファイアウォールによって、そのサーバー (*mysqlserver.database.windows.net* でアクセス可能) のパブリック エンドポイントへのすべてのアクセスがブロックされます。 わかりやすいように、*SQL Database* という言葉で SQL Database と Azure Synapse Analytics の両方を言い表します。

> [!IMPORTANT]
> この記事は、*Azure SQL Managed Instance* には適用され "*ません*"。 ネットワーク構成については、「[Azure SQL Managed Instance にアプリケーションを接続する](../managed-instance/connect-application-instance.md)」を参照してください。
>
> Azure Synapse では、サーバー レベルの IP ファイアウォール規則のみがサポートされます。 データベース レベルの IP ファイアウォール規則はサポートされていません。

## <a name="how-the-firewall-works"></a>ファイアウォールのしくみ

インターネットおよび Azure からの接続の試行は、次の図に示すように、サーバーまたはデータベースに到達する前にファイアウォールを通過する必要があります。

   ![ファイアウォールの構成の図][1]

### <a name="server-level-ip-firewall-rules"></a>サーバーレベルの IP ファイアウォール規則

これらのルールにより、クライアントがサーバー全体、つまりサーバーで管理されているすべてのデータベースにアクセスできるようになります。 規則は、*master* データベースに保存されます。 1 つのサーバーに対し、最大 128 個のサーバー レベルの IP ファイアウォール規則を作成できます。 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** の設定を有効にしてある場合、これはサーバーに対する 1 つのファイアウォール規則としてカウントされます。
  
サーバー レベルの IP ファイアウォール規則を構成するには、Azure portal、PowerShell、または Transact-SQL ステートメントを使用します。

- ポータルまたは PowerShell を使用するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。
- Transact-SQL を使用するには、サーバーレベル プリンシパル ログインまたは Azure Active Directory 管理者として "*マスター*" データベースに接続する必要があります。 (サーバー レベルの IP ファイアウォール規則は、Azure レベルのアクセス許可を持つユーザーが最初に作成する必要があります。)

> [!NOTE]
> Azure portal から新しい論理 SQL サーバーを作成するとき、 **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** 設定は、既定では **[いいえ]** に設定されます。

### <a name="database-level-ip-firewall-rules"></a>データベース レベルの IP ファイアウォール規則

データベース レベルの IP ファイアウォール規則により、クライアントは、特定の (セキュリティで保護された) データベースにアクセスできるようになります。 規則は、データベースごと (*master* データベースを含む) に作成し、個々のデータベースに格納されます。
  
- master データベースとユーザー データベースのデータベース レベルの IP ファイアウォール規則は、Transact-SQL ステートメントを使うことによって、最初のサーバー レベルのファイアウォール規則を構成した後でのみ、作成および管理できます。
- サーバー レベルの IP ファイアウォール規則の範囲外にあるデータベース レベルの IP ファイアウォール規則で IP アドレスの範囲を指定した場合、データベース レベルの範囲の IP アドレスを持つクライアントのみがデータベースにアクセスできます。
- データベースに対し、最大 128 個のデータベース レベルの IP ファイアウォール規則を作成できます。 データベース レベルの IP ファイアウォール規則の構成の詳細については、この記事で後述する例と、「[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)」を参照してください。

### <a name="recommendations-for-how-to-set-firewall-rules"></a>ファイアウォール規則の設定方法に関する推奨事項

可能な限り、データベース レベルの IP ファイアウォール規則を使用することをお勧めします。 この手法により、セキュリティが強化され、データベースの移植性が向上します。 管理者の場合は、サーバー レベルの IP ファイアウォール規則を使用してください。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースを個別に構成したくない場合にも使用します。

> [!NOTE]
> ビジネス継続性のコンテキストにおける移植可能なデータベースについては、[障害復旧の認証要件](active-geo-replication-security-configure.md)に関する記事を参照してください。

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>サーバー レベルとデータベース レベルの IP ファイアウォール規則

"*個々のデータベースのユーザーを別のデータベースから完全に分離する必要がありますか?* "

答えが "*はい*" の場合は、データベース レベルの IP ファイアウォール規則を使用してアクセスを付与します。 この方法により、サーバー レベルの IP ファイアウォール規則の使用を回避できます。この規則では、すべてのデータベースへのファイアウォール経由のアクセスが許可されます。 それによって、セキュリティが低下します。

"*指定した IP アドレスでユーザーがすべてのデータベースにアクセスする必要がありますか?* "

答えが "*はい*" の場合は、サーバー レベルの IP ファイアウォール規則を使用して、IP ファイアウォール規則を構成する回数を減らします。

"*IP ファイアウォール規則を構成する個人またはチームが、Azure portal、PowerShell、または REST API からのみ、アクセスが許可されていますか?* "

そうである場合、サーバー レベルの IP ファイアウォール規則を使用する必要があります。 データベース レベルの IP ファイアウォール規則は、Transact-SQL でのみ構成できます。  

"*IP ファイアウォール規則を構成する個人またはチームが、データベース レベルでの高度なアクセス許可を持つことが禁止されていますか?* "

そうである場合、サーバー レベルの IP ファイアウォール規則を使用します。 Transact-SQL を使用してデータベース レベルの IP ファイアウォール規則を構成するには、データベース レベルで少なくとも *CONTROL DATABASE* 権限が必要です。  

"*IP ファイアウォール規則を構成または監査する個人またはチームが、多くの (数百の) データベースの IP ファイアウォール規則を一元的に管理していますか?* "

このシナリオでは、ベスト プラクティスはニーズと環境によって決まります。 サーバー レベルの IP ファイアウォール規則の方が簡単に構成できると思われますが、スクリプトを使用すればデータベース レベルで規則を構成できます。 また、サーバー レベルの IP ファイアウォール規則を使用する場合でも、データベース レベルの IP ファイアウォール規則の監査が必要になることもあります。これは、データベースに対する *CONTROL* 権限を持つユーザーがデータベース レベルの IP ファイアウォール規則を作成するかどうかを確認するためです。

"*サーバー レベルの IP ファイアウォール規則とデータベース レベルの IP ファイアウォール規則を組み合わせて使用できますか?* "

はい。 サーバー レベルの IP ファイアウォール規則が必要なユーザー (管理者など) と、 データベース レベルの IP ファイアウォール規則が必要なユーザー (データベース アプリケーションのユーザーなど) がいる可能性があります。

### <a name="connections-from-the-internet"></a>インターネットからの接続

コンピューターがインターネットからサーバーに接続しようとすると、ファイアウォールは、最初に、接続を要求するデータベースについて、データベース レベルの IP ファイアウォール規則に対する要求の送信元の IP アドレスを確認します。

- アドレスがデータベース レベルの IP ファイアウォール規則で指定された範囲内にある場合は、その規則を含むデータベースへの接続が許可されます。
- アドレスがデータベース レベルの IP ファイアウォール規則の範囲内にない場合、ファイアウォールはサーバー レベルの IP ファイアウォール規則を確認します。 アドレスがサーバー レベルの IP ファイアウォール規則の範囲内にある場合は、接続が許可されます。 サーバーレベルの IP ファイアウォール規則は、サーバーによって管理されているすべてのデータベースに適用されます。  
- アドレスが、データベース レベルおよびサーバー レベルのどの IP ファイアウォール規則の範囲内にもない場合は、接続要求は失敗します。

> [!NOTE]
> ローカル コンピューターから Azure SQL Database にアクセスするには、ネットワーク上のファイアウォール、およびローカル コンピューターで、TCP ポート 1433 上の送信が確実に許可されるようにします。

### <a name="connections-from-inside-azure"></a>Azure 内からの接続

Azure 内でホストされているアプリケーションから SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。 Azure 接続を有効にするには、開始 IP アドレスと終了 IP アドレスが 0.0.0.0 に設定されているファイアウォール規則が必要です。

Azure のアプリケーションがサーバーに接続しようとすると、ファイアウォールは、このファイアウォール規則の存在を確認することで、Azure 接続が許可されていることを確認します。 これは、 **[ファイアウォールと仮想ネットワーク]** 設定で **[Azure サービスおよびリソースにこのサーバーへのアクセスを許可する]** を **[オン]** に切り替えることで Azure portal から直接オンにすることができます。 [オン] に設定すると、**AllowAllWindowsIP** という名前の IP 0.0.0.0 から 0.0.0.0 の受信ファイアウォール規則が作成されます。 ポータルを使用していない場合は、PowerShell または Azure CLI を使用して、開始 IP アドレスと終了 IP アドレスが 0.0.0.0 に設定されたファイアウォール規則を作成します。 

> [!IMPORTANT]
> このオプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

## <a name="permissions"></a>アクセス許可

Azure SQL Server の IP ファイアウォール規則を作成して管理できるようにするには、次のいずれかの操作を行う必要があります。

- [SQL Server 共同作成者](../../role-based-access-control/built-in-roles.md#sql-server-contributor)ロール
- [SQL セキュリティ管理者](../../role-based-access-control/built-in-roles.md#sql-security-manager)ロール
- Azure SQL Server を含むリソースの所有者

## <a name="create-and-manage-ip-firewall-rules"></a>IP ファイアウォール規則の作成および管理

最初のサーバー レベルのファイアウォール設定は、[Azure portal](https://portal.azure.com/) を使用するか、プログラムで [Azure PowerShell](/powershell/module/az.sql)、[Azure CLI](/cli/azure/sql/server/firewall-rule)、または Azure [REST API](/rest/api/sql/firewallrules/createorupdate) を使用して作成します。 追加のサーバー レベルの IP ファイアウォール規則を作成して管理するには、これらの方法または Transact-SQL を使用します。

> [!IMPORTANT]
> データベース レベルの IP ファイアウォール規則は、Transact-SQL でのみ作成と管理が可能です。

パフォーマンスを向上させるため、サーバー レベルの IP ファイアウォール規則はデータベース レベルで一時的にキャッシュされます。 キャッシュを更新する方法については、「[DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql)」をご覧ください。

> [!TIP]
> [データベース監査](../../azure-sql/database/auditing-overview.md)を使用して、サーバー レベルおよびデータベース レベルのファイアウォールの変更を監査できます。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure portal を使用してサーバー レベルの IP ファイアウォール規則を管理する

Azure portal でサーバー レベルの IP ファイアウォール規則を設定するには、ご利用のデータベースまたはサーバーの概要ページに移動します。

> [!TIP]
> チュートリアルについては、[Azure portal を使用したデータベースの作成](single-database-create-quickstart.md)に関するページをご覧ください。

#### <a name="from-the-database-overview-page"></a>データベースの概要ページから

1. データベースの概要ページからサーバー レベルの IP ファイアウォール規則を設定するには、次の画像に示すように、ツールバーの **[サーバー ファイアウォールの設定]** を選択します。

    ![サーバーの IP ファイアウォール規則](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    サーバーの **[ファイアウォール設定]** ページが開きます。

2. ツールバーの **[クライアント IP の追加]** を選択し、使用しているコンピューターの IP アドレスを追加したら、 **[保存]** を選択します。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

    ![サーバー レベルの IP ファイアウォール規則の設定](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>サーバーの概要ページから

サーバーの概要ページが開きます。 完全修飾サーバー名 (*mynewserver20170403.database.windows.net* など) や追加の構成オプションが表示されます。

1. このページからサーバー レベルの規則を設定するには、左側の **[設定]** メニューから **[ファイアウォール]** を選択します。

2. ツールバーの **[クライアント IP の追加]** を選択し、使用しているコンピューターの IP アドレスを追加したら、 **[保存]** を選択します。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL を使用して IP ファイアウォール規則を管理する

| カタログ ビューまたはストアド プロシージャ | Level | 説明 |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |データベース |現在のデータベース レベルの IP ファイアウォール規則を表示する |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |データベース |データベース レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |データベース |データベース レベルの IP ファイアウォール規則を削除する |

次の例では、既存の規則を確認し、*Contoso* サーバーで一定範囲の IP アドレスを有効にして、IP ファイアウォール規則を削除します。

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

次に、サーバー レベルの IP ファイアウォール規則を追加します。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

サーバー レベルの IP ファイアウォール規則を削除するには、*sp_delete_firewall_rule* ストアド プロシージャを実行します。 次の例では、*ContosoFirewallRule* という規則を削除します。

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>PowerShell を使用してサーバー レベルの IP ファイアウォール規則を管理する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、現在、開発はすべて Az.Sql モジュールを対象に行われています。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

| コマンドレット | Level | 説明 |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |サーバー |現在のサーバー レベルのファイアウォール規則を返す |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |サーバー |新しいサーバー レベルのファイアウォール規則を作成 |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |サーバー |既存のサーバー レベルのファイアウォール規則のプロパティを更新 |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |サーバー |サーバー レベルのファイアウォール規則を削除 |

次の例では、PowerShell を使用してサーバー レベルの IP ファイアウォール規則を設定します。

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> $servername には、完全修飾 DNS 名ではなくサーバー名を指定します。たとえば、**mysqldbserver.database.windows.net** の代わりに **mysqldbserver** を指定します。
>
> クイックスタートのコンテキストでの PowerShell の例については、[DB の作成 - PowerShell](powershell-script-content-guide.md) に関するページと [PowerShell を使用した単一データベースの作成とサーバー レベルの IP ファイアウォール規則の構成](scripts/create-and-configure-database-powershell.md)に関するページを参照してください。

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>CLI を使用してサーバー レベルの IP ファイアウォール規則を管理する

| コマンドレット | Level | 説明 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|サーバー|サーバーの IP ファイアウォール規則を作成する|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|サーバー|サーバーの IP ファイアウォール規則を一覧表示する|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|サーバー|IP ファイアウォール規則の詳細を表示する|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|サーバー|IP ファイアウォール規則を更新する|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|サーバー|IP ファイアウォール規則を削除する|

次の例では、CLI を使用してサーバー レベルの IP ファイアウォール規則を設定します。

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> $servername には、完全修飾 DNS 名ではなくサーバー名を指定します。たとえば、**mysqldbserver.database.windows.net** の代わりに **mysqldbserver** を指定します。
>
> クイックスタートのコンテキストでの CLI の例については、[DB の作成 - Azure CLI](az-cli-script-samples-content-guide.md) に関するページと [Azure CLI を使用した単一データベースの作成とサーバーレベルの IP ファイアウォール規則の構成](scripts/create-and-configure-database-cli.md)に関するページを参照してください。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API を使用してサーバー レベルの IP ファイアウォール規則を管理する

| API | Level | 説明 |
| --- | --- | --- |
| [ファイアウォール規則の一覧表示](/rest/api/sql/firewallrules/listbyserver) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [ファイアウォール規則の作成または更新](/rest/api/sql/firewallrules/createorupdate) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [ファイアウォール規則の削除](/rest/api/sql/firewallrules/delete) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [ファイアウォール規則の取得](/rest/api/sql/firewallrules/get) | サーバー | サーバー レベルの IP ファイアウォール規則を取得する |

## <a name="troubleshoot-the-database-firewall"></a>データベース ファイアウォールのトラブルシューティング

Azure SQL Database に期待どおりにアクセスできない場合は、次の点を検討してください。

- **ローカル ファイアウォールの構成:**

  Azure SQL Database へアクセスする前に、TCP ポート 1433 に対し、コンピューターでファイアウォール例外を作成することが必要な場合があります。 Azure クラウド境界内で接続を行うには、追加のポートを開かなければならない場合があります。 詳細については、SQL Database の外部と内部に関するセクションを、[ADO.NET 4.5 と Azure SQL Database における 1433 以外のポート](adonet-v12-develop-direct-route-ports.md)に関する記事で参照してください。

- **ネットワーク アドレス変換:**

  ネットワーク アドレス変換 (NAT) が原因で、Azure SQL Database に接続するためにコンピューターで使用される IP アドレスが、コンピューターの IP 構成設定内の IP アドレスと異なる場合があります。 コンピューターが Azure への接続に使用している IP アドレスを表示するには、次のようにします。
    1. ポータルにサインインします。
    1. データベースをホストするサーバーの **[構成]** タブに移動します。
    1. **[使用できる IP アドレス]** セクションに **[現在のクライアント IP アドレス]** が表示されます。 **[使用できる IP アドレス]** の **[追加]** を選択し、このコンピューターのサーバーへのアクセスを許可します。

- **許可リストに対する変更が反映されない:**

  Azure SQL Database のファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

- **ログインが許可されない、または正しくないパスワードが使用された:**

  ログインでのサーバーに対するアクセス許可がないか、パスワードが正しくない場合、サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、サーバーへの接続を試行する "*機会*" をクライアントに提供するだけです。 クライアントには、必要なセキュリティ資格情報の提供が依然として必要です。 ログインの準備の詳細については、[データベース アクセスの制御と許可](logins-create-manage.md)に関する記事を参照してください。

- **動的 IP アドレス:**

  動的 IP アドレス指定を使用するインターネット接続があり、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。
  
  - サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダーに問い合わせます。 その IP アドレス範囲を IP ファイアウォール規則として追加します。
  - クライアント コンピューターの静的 IP アドレス指定を代わりに取得します。 それらの IP アドレスを IP ファイアウォール規則として追加します。

## <a name="next-steps"></a>次のステップ

- 企業ネットワーク環境で、Azure データセンターで使用されるコンピューティング IP アドレス範囲 (SQL 範囲を含む) からの受信通信が許可されていることを確認します。 これらの IP アドレスを許可リストに追加することが必要な場合があります。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を参照してください。  
- [Azure SQL Database での単一データベースの作成](single-database-create-quickstart.md)に関するクイックスタートを参照してください。
- オープンソースまたはサードパーティ製のアプリケーションから Azure SQL Database に接続する方法のヘルプについては、[Azure SQL Database のクライアント クイックスタート コード サンプル](connect-query-content-reference-guide.md#libraries)に関するページを参照してください。
- 他に開くことが必要な可能性のあるポートについては、SQL Database の外部と内部に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](adonet-v12-develop-direct-route-ports.md)に関する記事で参照してください
- Azure SQL Database のセキュリティの概要については、[データベースの保護](security-overview.md)に関する記事を参照してください。

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
