---
title: IP ファイアウォール規則
description: SQL Database または SQL Data Warehouse のファイアウォールにサーバー レベルの IP ファイアウォール規則を構成します。 1 つまたはプールされたデータベースに対して、アクセスを管理し、データベース レベルの IP ファイアウォール規則を構成します。
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 5c1a146a12fd8881982826e0a87868a6eaf05cb1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851828"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database と Azure SQL Data Warehouse の IP ファイアウォール規則

> [!NOTE]
> この記事は Azure SQL サーバーのほか、Azure SQL サーバー上の Azure SQL Database と Azure SQL Data Warehouse データベースの両方に適用されます。 簡潔にするため、*SQL Database* という言葉を使用して、SQL Database と SQL Data Warehouse の両方を言い表します。

> [!IMPORTANT]
> この記事は、*Azure SQL Database Managed Instance* には "*適用されません*"。 ネットワーク構成については、「[Azure SQL Database Managed Instance にアプリケーションを接続する](sql-database-managed-instance-connect-app.md)」を参照してください。

たとえば、*mysqlserver* という名前の新しい Azure SQL サーバーを作成すると、SQL Database ファイアウォールによって、そのサーバー (*mysqlserver.database.windows.net* でアクセス可能) のパブリック エンドポイントへのすべてのアクセスがブロックされます。

> [!IMPORTANT]
> SQL Data Warehouse では、サーバー レベルの IP ファイアウォール規則のみがサポートされます。 データベース レベルの IP ファイアウォール規則はサポートされていません。

## <a name="how-the-firewall-works"></a>ファイアウォールのしくみ
インターネットおよび Azure からの接続の試行は、次の図に示すように、SQL サーバーまたは SQL データベースに到達する前にファイアウォールを通過する必要があります。

   ![ファイアウォールの構成の図][1]

### <a name="server-level-ip-firewall-rules"></a>サーバーレベルの IP ファイアウォール規則

  これらの規則により、クライアントは、Azure SQL サーバー全体、つまり、同じ SQL Database サーバー内のすべてのデータベースにアクセスできるようになります。 規則は、*master* データベースに保存されます。 Azure SQL Server に対し、最大 128 個のサーバー レベルの IP ファイアウォール規則を作成できます。
  
  サーバー レベルの IP ファイアウォール規則を構成するには、Azure portal、PowerShell、または Transact-SQL ステートメントを使用します。
  - ポータルまたは PowerShell を使用するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。
  - Transact-SQL を使用するには、サーバーレベル プリンシパル ログインまたは Azure Active Directory 管理者として SQL Database インスタンスに接続する必要があります。 (サーバー レベルの IP ファイアウォール規則は、Azure レベルのアクセス許可を持つユーザーが最初に作成する必要があります。)

### <a name="database-level-ip-firewall-rules"></a>データベース レベルの IP ファイアウォール規則

  これらの規則により、クライアントは、同じ SQL Database サーバー内の特定の (セキュリティで保護された) データベースにアクセスできるようになります。 規則は、データベースごと (*master* データベースを含む) に作成し、個々のデータベースに格納されます。
  
  master データベースとユーザー データベースのデータベース レベルの IP ファイアウォール規則は、Transact-SQL ステートメントを使うことによって、最初のサーバー レベルのファイアウォール規則を構成した後でのみ、作成および管理できます。
  
  サーバー レベルの IP ファイアウォール規則の範囲外にあるデータベース レベルの IP ファイアウォール規則で IP アドレスの範囲を指定した場合、データベース レベルの範囲の IP アドレスを持つクライアントのみがデータベースにアクセスできます。
  
  データベースに対し、最大 128 個のデータベース レベルの IP ファイアウォール規則を作成できます。 データベース レベルの IP ファイアウォール規則の構成の詳細については、この記事で後述する例と、「[sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)」を参照してください。

### <a name="recommendations-for-how-to-set-firewall-rules"></a>ファイアウォール規則の設定方法に関する推奨事項

可能な限り、データベース レベルの IP ファイアウォール規則を使用することをお勧めします。 この手法により、セキュリティが強化され、データベースの移植性が向上します。 管理者の場合は、サーバー レベルの IP ファイアウォール規則を使用してください。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースを個別に構成したくない場合にも使用します。

> [!NOTE]
> ビジネス継続性のコンテキストにおける移植可能なデータベースについては、[障害復旧の認証要件](sql-database-geo-replication-security-config.md)に関する記事を参照してください。

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

コンピューターがインターネットからデータベース サーバーに接続しようとすると、ファイアウォールは、最初に、接続を要求するデータベースについて、データベース レベルの IP ファイアウォール規則に対する要求の送信元の IP アドレスを確認します。

- アドレスがデータベース レベルの IP ファイアウォール規則で指定された範囲内にある場合は、その規則を含む SQL データベースへの接続が許可されます。
- アドレスがデータベース レベルの IP ファイアウォール規則の範囲内にない場合、ファイアウォールはサーバー レベルの IP ファイアウォール規則を確認します。 アドレスがサーバー レベルの IP ファイアウォール規則の範囲内にある場合は、接続が許可されます。 サーバー レベルの IP ファイアウォール規則は、Azure SQL Server 上のすべての SQL Database に適用されます。  
- アドレスが、データベース レベルおよびサーバー レベルのどの IP ファイアウォール規則の範囲内にもない場合は、接続要求は失敗します。

> [!NOTE]
> ローカル コンピューターから SQL Database にアクセスするには、ネットワーク上のファイアウォール、およびローカル コンピューターで、TCP ポート 1433 上の送信が許可されるようにします。

### <a name="connections-from-inside-azure"></a>Azure 内からの接続

Azure 内でホストされているアプリケーションから SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 開始 IP アドレスと終了 IP アドレスが *0.0.0.0* であるファイアウォール設定は、Azure 接続が許可されていることを示します。 接続が許可されていない場合、要求は SQL Database サーバーに到達しません。

> [!IMPORTANT]
> このオプションでは、他のお客様のサブスクリプションからの接続を含む、Azure からのすべての接続を許可するようにファイアウォールが構成されます。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

## <a name="create-and-manage-ip-firewall-rules"></a>IP ファイアウォール規則の作成および管理

最初のサーバー レベルのファイアウォール設定は、[Azure portal](https://portal.azure.com/) を使用するか、プログラムで [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql)、[Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)、または Azure [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) を使用して作成します。 追加のサーバー レベルの IP ファイアウォール規則を作成して管理するには、これらの方法または Transact-SQL を使用します。

> [!IMPORTANT]
> データベース レベルの IP ファイアウォール規則は、Transact-SQL でのみ作成と管理が可能です。

パフォーマンスを向上させるため、サーバー レベルの IP ファイアウォール規則はデータベース レベルで一時的にキャッシュされます。 キャッシュを更新する方法については、「[DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)」をご覧ください。

> [!TIP]
> [SQL Database 監査](sql-database-auditing.md)を使用して、サーバー レベルおよびデータベース レベルのファイアウォールの変更を監査できます。

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Azure portal を使用してサーバー レベルの IP ファイアウォール規則を管理する

Azure portal でサーバー レベルの IP ファイアウォール規則を設定するには、ご利用の Azure SQL データベースまたは SQL Database サーバーの概要ページに移動します。

> [!TIP]
> チュートリアルについては、「[Create a DB using the Azure portal (Azure Portal を使用した DB の作成)](sql-database-single-database-get-started.md)」を参照してください。

#### <a name="from-the-database-overview-page"></a>データベースの概要ページから

1. データベースの概要ページからサーバー レベルの IP ファイアウォール規則を設定するには、次の画像に示すように、ツールバーの **[サーバー ファイアウォールの設定]** を選択します。 

    ![サーバーの IP ファイアウォール規則](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。

2. ツールバーの **[クライアント IP の追加]** を選択し、使用しているコンピューターの IP アドレスを追加したら、 **[保存]** を選択します。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

    ![サーバー レベルの IP ファイアウォール規則の設定](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>サーバーの概要ページから

サーバーの概要ページが開きます。 完全修飾サーバー名 (*mynewserver20170403.database.windows.net* など) や追加の構成オプションが表示されます。

1. このページからサーバー レベルの規則を設定するには、左側の **[設定]** メニューから **[ファイアウォール]** を選択します。

2. ツールバーの **[クライアント IP の追加]** を選択し、使用しているコンピューターの IP アドレスを追加したら、 **[保存]** を選択します。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Transact-SQL を使用して IP ファイアウォール規則を管理する

| カタログ ビューまたはストアド プロシージャ | Level | 説明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |現在のデータベース レベルの IP ファイアウォール規則を表示する |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |データベース レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |データベース |データベース レベルの IP ファイアウォール規則を削除する |

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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、現在、開発はすべて Az.Sql モジュールを対象に行われています。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

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

> [!TIP]
> クイックスタートのコンテキストでの PowerShell の例については、[DB の作成 - PowerShell](sql-database-powershell-samples.md) に関するページと [PowerShell を使用した単一データベースの作成と SQL Database のサーバー レベルの IP ファイアウォール規則の構成](scripts/sql-database-create-and-configure-database-powershell.md)に関するページを参照してください。

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

> [!TIP]
> クイックスタートのコンテキストでの CLI の例については、[DB の作成 - Azure CLI](sql-database-cli-samples.md) に関するページと [Azure CLI を使用した単一データベースの作成と SQL Database の IP ファイアウォール規則の構成](scripts/sql-database-create-and-configure-database-cli.md)に関するページを参照してください。

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>REST API を使用してサーバー レベルの IP ファイアウォール規則を管理する

| API | Level | 説明 |
| --- | --- | --- |
| [ファイアウォール規則の一覧表示](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [ファイアウォール規則の作成または更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [ファイアウォール規則の削除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [ファイアウォール規則の取得](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | サーバー | サーバー レベルの IP ファイアウォール規則を取得する |

## <a name="troubleshoot-the-database-firewall"></a>データベース ファイアウォールのトラブルシューティング

SQL Database サービスに期待どおりにアクセスできない場合は、次の点を検討してください。

- **ローカル ファイアウォールの構成:**

  SQL Database にアクセスする前に、TCP ポート 1433 に対し、コンピューターでファイアウォール例外を作成することが必要な場合があります。 Azure クラウド境界内で接続を行うには、追加のポートを開かなければならない場合があります。 詳細については、SQL Database の外部と内部に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事で参照してください。

- **ネットワーク アドレス変換:**

  ネットワーク アドレス変換 (NAT) が原因で、SQL Database に接続するためにコンピューターで使用される IP アドレスが、コンピューターの IP 構成設定内の IP アドレスと異なる場合があります。 コンピューターが Azure への接続に使用している IP アドレスを表示するには、次のようにします。
    1. ポータルにサインインします。
    1. データベースをホストするサーバーの **[構成]** タブに移動します。
    1. **[使用できる IP アドレス]** セクションに **[現在のクライアント IP アドレス]** が表示されます。 **[使用できる IP アドレス]** の **[追加]** を選択し、このコンピューターのサーバーへのアクセスを許可します。

- **許可リストに対する変更が反映されない:**

  SQL Database のファイアウォール構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

- **ログインが許可されない、または正しくないパスワードが使用された:**

  ログインでの SQL Database サーバーに対するアクセス許可がないか、パスワードが正しくない場合、サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、サーバーへの接続を試行する "*機会*" をクライアントに提供するだけです。 クライアントには、必要なセキュリティ資格情報の提供が依然として必要です。 ログインの準備の詳細については、「[SQL Database と SQL Data Warehouse へのデータベース アクセスの制御と許可](sql-database-manage-logins.md)」を参照してください。

- **動的 IP アドレス:**

  動的 IP アドレス指定を使用するインターネット接続があり、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。
  
  - SQL Database サーバーにアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダーに問い合わせます。 その IP アドレス範囲を IP ファイアウォール規則として追加します。
  - クライアント コンピューターの静的 IP アドレス指定を代わりに取得します。 それらの IP アドレスを IP ファイアウォール規則として追加します。

## <a name="next-steps"></a>次の手順

- 企業ネットワーク環境で、Azure データセンターで使用されるコンピューティング IP アドレス範囲 (SQL 範囲を含む) からの受信通信が許可されていることを確認します。 これらの IP アドレスを許可リストに追加することが必要な場合があります。 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)を参照してください。  
- サーバー レベルの IP ファイアウォール規則の作成に関するクイックスタートについては、[Azure SQL データベースの作成](sql-database-single-database-get-started.md)に関するページを参照してください。
- オープンソースまたはサードパーティ製のアプリケーションから Azure SQL データベースに接続する方法のヘルプについては、[SQL Database のクライアント クイックスタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関するページを参照してください。
- 他に開くことが必要な可能性のあるポートについては、SQL Database の外部と内部に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事で参照してください
- Azure SQL Database のセキュリティの概要については、[データベースの保護](sql-database-security-overview.md)に関する記事を参照してください。

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
