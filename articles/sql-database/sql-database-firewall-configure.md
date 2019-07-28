---
title: Azure SQL Database と Data Warehouse の IP ファイアウォール規則 | Microsoft Docs
description: アクセスを管理するために、サーバー レベルのファイアウォール規則を使用して SQL Database または SQL Data Warehouse ファイアウォールを構成する方法と、データベース レベルの IP ファイアウォール規則で単一データベースまたはプールされたデータベースを構成する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vanmsft
ms.author: vanto
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 513836257a292069da709ad7a71e480f2b4d069d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66158278"
---
# <a name="azure-sql-database-and-sql-data-warehouse-ip-firewall-rules"></a>Azure SQL Database と SQL Data Warehouse の IP ファイアウォール規則

Microsoft Azure [SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) は、Azure とその他のインターネット ベースのアプリケーション用のリレーショナル データベース サービスを提供します。 データを保護するため、ファイアウォールは、どのコンピューターに権限を持たせるかを指定するまで、データベース サーバーへのすべてのアクセスを遮断します。 ファイアウォールは、各要求の送信元 IP アドレスに基づいてデータベースへのアクセス権を付与します。

> [!NOTE]
> この記事は Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。
> [!IMPORTANT]
> この記事は、**Azure SQL Database Managed Instance** には "*適用されません*"。 必要なネットワーク構成について詳しくは、[Managed Instance への接続](sql-database-managed-instance-connect-app.md)に関する記事をご覧ください。

## <a name="virtual-network-rules-as-alternatives-to-ip-rules"></a>IP 規則に代わる手段としての仮想ネットワーク規則

IP 規則に加えて、ファイアウォールは*仮想ネットワーク規則*も管理します。 仮想ネットワーク規則は、仮想ネットワーク サービス エンドポイントに基づいています。 仮想ネットワーク規則は、場合によっては IP 規則より望ましい場合があります。 詳細については、[Azure SQL Database の仮想ネットワーク サービス エンドポイントと規則](sql-database-vnet-service-endpoint-rule-overview.md)に関する記事をご覧ください。

## <a name="overview"></a>概要

最初は、Azure SQL サーバーへのすべてのアクセスが、SQL Database ファイアウォールによってブロックされます。 データベース サーバーにアクセスするには、Azure SQL サーバーへのアクセスを有効にするサーバー レベルの IP ファイアウォール規則を、1 つまたは複数指定する必要があります。 IP ファイアウォール規則を使用すると、インターネットからのアクセスを許可する IP アドレス範囲、および Azure アプリケーションから Azure SQL サーバーへの接続を試みることができるかどうかを指定できます。

Azure SQL サーバーにある 1 つのデータベースにのみ選択的にアクセス許可を付与するには、必要なデータベース用のデータベース レベルの規則を作成する必要があります。 データベース IP ファイアウォール規則に対して、サーバー レベルの IP ファイアウォール規則で指定された IP アドレス範囲外の IP アドレスの範囲を指定し、クライアントの IP アドレス範囲が、データベース レベルの規則で指定された範囲内に確実に収まるようにします。

> [!IMPORTANT]
> SQL Data Warehouse では、サーバー レベルの IP ファイアウォール規則のみがサポートされており、データベース レベルの IP ファイアウォール規則はサポートされていません。

インターネットおよび Azure からの接続の試行は、次の図に示されるように、Azure SQL サーバーまたは SQL Database に到達する前にファイアウォールを通過する必要があります。

   ![Diagram describing firewall configuration.][1]

- **サーバーレベルの IP ファイアウォール規則:**

  これらの規則により、クライアントは、Azure SQL サーバー全体、つまり、同じ SQL Database サーバー内のすべてのデータベースにアクセスできるようになります。 これらの規則は、 **マスター** データベースに保存されます。 サーバー レベルの IP ファイアウォール規則を構成するには、ポータルまたは Transact-SQL ステートメントを使用します。 Azure portal または PowerShell を使用してサーバー レベルの IP ファイアウォール規則を作成するには、サブスクリプション所有者またはサブスクリプション共同作成者である必要があります。 Transact-SQL を使用してサーバー レベルの IP ファイアウォール規則を作成するには、サーバー レベルのプリンシパル ログインまたは Azure Active Directory 管理者として SQL データベース インスタンスに接続する必要があります (つまり、サーバー レベルの IP ファイアウォール規則は、最初に Azure レベルのアクセス許可を持つユーザーが作成する必要があります)。

- **データベース レベルの IP ファイアウォール規則:**

  これらの規則により、クライアントは、同じ SQL Database サーバー内の特定の (セキュリティで保護された) データベースにアクセスできるようになります。 これらの規則は、データベースごと (**master** データベースを含む) に作成することができ、個々のデータベースに格納されます。 master データベースとユーザー データベースのデータベース レベルの IP ファイアウォール規則は、Transact-SQL ステートメントを使うことによって、最初のサーバーレベルのファイアウォール規則を構成した後でのみ、作成および管理できます。 サーバー レベルの IP ファイアウォール規則で指定された範囲外にあるデータベース レベルの IP ファイアウォール規則で IP アドレスの範囲を指定した場合、データベース レベルの範囲の IP アドレスを持つクライアントのみがデータベースにアクセスできます。 データベースに対し、最大 128 個のデータベース レベルの IP ファイアウォール規則を作成できます。 データベース レベルの IP ファイアウォール規則の構成の詳細については、この記事で後述する例と、「[sp_set_database_firewall_rule (Azure SQL Database)](https://msdn.microsoft.com/library/dn270010.aspx)」を参照してください。

### <a name="recommendation"></a>推奨

ただし、セキュリティを強化しデータベースの移植性を高めるため、可能な限りデータベース レベルの IP ファイアウォール規則を使用することをお勧めします。 アクセス要件が同じデータベースが多数存在し、それぞれのデータベースの設定に時間を費やしたくない場合は、管理者向けのサーバー レベルの IP ファイアウォール規則を使用します。

> [!IMPORTANT]
> Windows Azure SQL Database では、最大 128 個の IP ファイアウォール規則がサポートされています。
> [!NOTE]
> ビジネス継続性のコンテキストにおける移植可能なデータベースについては、[障害復旧の認証要件](sql-database-geo-replication-security-config.md)に関する記事を参照してください。

### <a name="connecting-from-the-internet"></a>インターネットからの接続

コンピューターがインターネットからデータベース サーバーに接続しようとすると、ファイアウォールは、最初に、接続を要求しているデータベースについて、データベース レベルの IP ファイアウォール規則に対する要求の送信元の IP アドレスを確認します。

- 要求の IP アドレスがデータベース レベルの IP ファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、その規則を含む SQL Database への接続が許可されます。
- 要求の IP アドレスがデータベース レベルの IP ファイアウォール規則で指定されたいずれかの IP アドレス範囲内にない場合は、サーバー レベルの IP ファイアウォール規則が確認されます。 要求の IP アドレスがサーバー レベルの IP ファイアウォール規則で指定されたいずれかの IP アドレス範囲内にある場合は、接続が許可されます。 サーバー レベルの IP ファイアウォール規則は、Azure SQL Server 上のすべての SQL Database に適用されます。  
- 要求の IP アドレスがデータベース レベルの IP ファイアウォール規則またはサーバー レベルの IP ファイアウォール規則で指定された IP アドレス範囲内にない場合、接続要求は失敗します。

> [!NOTE]
> ローカル コンピューターから Azure SQL Database にアクセスするには、ネットワーク上のファイアウォール、およびローカル コンピューターで、TCP ポート 1433 上の送信が許可されていることを確認します。

### <a name="connecting-from-azure"></a>Azure からの接続

Azure のアプリケーションから Azure SQL Server に接続を許可するには、Azure の接続を有効にする必要があります。 Azure からアプリケーションがデータベース サーバーに接続しようとした場合、ファイアウォールは、Azure の接続が許可されていることを確認します。 開始アドレスと終了アドレスが 0.0.0.0 であるファイアウォール設定は、Azure 接続が許可されていることを示します。 接続試行が許可されていない場合、要求は、Azure SQL Database サーバーに到達しません。

> [!IMPORTANT]
> このオプションは、ファイアウォールを構成して、他のお客様のサブスクリプションからの接続を含むすべての接続を許可します。 このオプションを選択する場合は、ログインおよびユーザーのアクセス許可が、承認されたユーザーのみにアクセスを制限していることを確認してください。

## <a name="creating-and-managing-ip-firewall-rules"></a>IP ファイアウォール規則の作成と管理

最初のサーバー レベルのファイアウォール設定は、[Azure ポータル](https://portal.azure.com/)を使用するか、プログラムで [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql)、[Azure CLI](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)、または [REST API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) を使用して作成できます。 それ以降のサーバー レベルの IP ファイアウォール規則の作成と管理も、これらの方法や Transact-SQL を使用して実行できます。

> [!IMPORTANT]
> データベース レベルの IP ファイアウォール規則は、Transact-SQL でのみ作成と管理が可能です。

パフォーマンスを向上させるため、サーバー レベルの IP ファイアウォール規則はデータベース レベルで一時的にキャッシュされます。 キャッシュを更新する方法については、「[DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx)」をご覧ください。

> [!TIP]
> [SQL Database 監査](sql-database-auditing.md)を使用して、サーバー レベルおよびデータベース レベルのファイアウォールの変更を監査できます。

## <a name="manage-server-level-ip-firewall-rules-using-the-azure-portal"></a>Azure portal を使用してサーバー レベルの IP ファイアウォール規則を管理する

Azure portal でサーバー レベルの IP ファイアウォール規則を設定するには、ご利用の Azure SQL データベースの [概要] ページまたはご利用の SQL Database サーバーの [概要] ページに移動します。

> [!TIP]
> チュートリアルについては、「[Create a DB using the Azure portal (Azure Portal を使用した DB の作成)](sql-database-single-database-get-started.md)」を参照してください。

### <a name="from-database-overview-page"></a>データベースの概要ページから

1. データベースの概要ページからサーバー レベルの IP ファイアウォール規則を設定するには、次の画像に示されているように、ツールバー上の **[サーバー ファイアウォールの設定]** をクリックします。SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。

      ![サーバーの IP ファイアウォール規則](./media/sql-database-get-started-portal/server-firewall-rule.png)

2. ツールバーの **[クライアント IP の追加]** をクリックし、現在使用しているコンピューターの IP アドレスを追加したら、 **[保存]** をクリックします。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

      ![サーバー レベルの IP ファイアウォール規則を設定する](./media/sql-database-get-started-portal/server-firewall-rule-set.png)

### <a name="from-server-overview-page"></a>サーバーの概要ページから

サーバーの概要ページが開き、完全修飾サーバー名 (**mynewserver20170403.database.windows.net** など) や追加の構成オプションが表示されます。

1. サーバーの概要ページからサーバー レベルの規則を設定するには、[設定] の下の左側のメニューで **[ファイアウォール]** をクリックします。

2. ツールバーの **[クライアント IP の追加]** をクリックし、現在使用しているコンピューターの IP アドレスを追加したら、 **[保存]** をクリックします。 現在の IP アドレスに対してサーバー レベルの IP ファイアウォール規則が作成されます。

## <a name="manage-ip-firewall-rules-using-transact-sql"></a>Transact-SQL を使って IP ファイアウォール規則を管理する

| カタログ ビューまたはストアド プロシージャ | Level | 説明 |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Database |現在のデータベース レベルの IP ファイアウォール規則を表示する |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Database |データベース レベルの IP ファイアウォール規則を作成または更新する |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |データベース |データベース レベルの IP ファイアウォール規則を削除する |

次の例では、既存の規則を確認し、Contoso サーバーで一定範囲の IP アドレスを有効にして、IP ファイアウォール規則を削除します。

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

次に、サーバー レベルの IP ファイアウォール規則を追加します。

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

サーバー レベルの IP ファイアウォール規則を削除するには、sp_delete_firewall_rule ストアド プロシージャを実行します。 次の例では、ContosoFirewallRule という名前の規則を削除します。

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

## <a name="manage-server-level-ip-firewall-rules-using-azure-powershell"></a>Azure PowerShell を使用してサーバー レベルの IP ファイアウォール規則を管理する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

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
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
```

> [!TIP]
> クイック スタートのコンテキストでの PowerShell の例については、[DB の作成 - PowerShell](sql-database-powershell-samples.md) に関するページと [PowerShell を使用した単一データベースの作成と SQL Database のサーバー レベルの IP ファイアウォール規則の構成](scripts/sql-database-create-and-configure-database-powershell.md)に関するページを参照してください

## <a name="manage-server-level-ip-firewall-rules-using-azure-cli"></a>Azure CLI を使用してサーバー レベルの IP ファイアウォール規則を管理する

| コマンドレット | Level | 説明 |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|サーバー|サーバーの IP ファイアウォール規則を作成する|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|サーバー|サーバーの IP ファイアウォール規則を一覧表示する|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|サーバー|IP ファイアウォール規則の詳細を表示する|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|サーバー|IP ファイアウォール規則を更新する|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|サーバー|IP ファイアウォール規則を削除する|

次の例では、Azure CLI を使用してサーバー レベルの IP ファイアウォール規則を設定します。

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n AllowYourIp --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> クイック スタートのコンテキストでの Azure CLI の例については、[DB の作成 - Azure CLI](sql-database-cli-samples.md) に関するページと [Azure CLI を使用した単一データベースの作成と SQL Database の IP ファイアウォール規則の構成](scripts/sql-database-create-and-configure-database-cli.md)に関するページを参照してください

## <a name="manage-server-level-ip-firewall-rules-using-rest-api"></a>REST API を使用してサーバー レベルの IP ファイアウォール規則を管理する

| API | Level | 説明 |
| --- | --- | --- |
| [ファイアウォール規則の一覧表示](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |サーバー |現在のサーバー レベルの IP ファイアウォール規則を表示する |
| [ファイアウォール規則の作成または更新](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |サーバー |サーバー レベルの IP ファイアウォール規則を作成または更新する |
| [ファイアウォール規則の削除](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |サーバー |サーバー レベルの IP ファイアウォール規則を削除する |
| [ファイアウォール規則の取得](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | サーバー | サーバー レベルの IP ファイアウォール規則を取得する |

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>サーバー レベルとデータベース レベルの IP ファイアウォール規則

Q. 個々のデータベースのユーザーを別のデータベースから完全に分離する必要がありますか?
分離する必要がある場合は、データベース レベルの IP ファイアウォール規則を使用してアクセスを許可します。 これにより、サーバー レベルの IP ファイアウォール規則の使用を回避できます。サーバー レベルの IP ファイアウォール規則では、すべてのデータベースへのファイアウォール経由のアクセスが許可されるので、セキュリティが低下します。

Q. 指定した IP アドレスのユーザーがすべてのデータベースにアクセスする必要がありますか?
サーバー レベルの IP ファイアウォール規則を使用すると、IP ファイアウォール規則を構成する回数を減らすことができます。

Q. IP ファイアウォール規則を構成する個人またはチームが、Azure portal、PowerShell、または REST API からのみ、アクセスが許可されていますか?
サーバー レベルの IP ファイアウォール規則を使用する必要があります。 データベース レベルの IP ファイアウォール規則は、Transact-SQL でのみ構成できます。  

Q. IP ファイアウォール規則を構成する個人またはチームが、データベース レベルでの高度なアクセス許可を持つことが禁止されていますか?
サーバー レベルの IP ファイアウォール規則を使用します。 Transact-SQL でデータベース レベルの IP ファイアウォール規則を構成するには、データベース レベルで少なくとも `CONTROL DATABASE` のアクセス許可が必要です。  

Q. IP ファイアウォール規則を構成または監査する個人またはチームが、多くの (数百の) データベースの IP ファイアウォール規則を一元的に管理していますか?
この場合はニーズや環境によって異なります。 サーバー レベルの IP ファイアウォール規則の方が簡単に構成できると思われますが、スクリプトを使用すればデータベース レベルで規則を構成できます。 また、サーバー レベルの IP ファイアウォール規則を使用する場合でも、データベース レベルの IP ファイアウォール規則の監査が必要になることもあります。これは、データベースに対する `CONTROL` のアクセス許可を持つユーザーは、データベース レベルの IP ファイアウォール規則を作成できるためです。

Q. サーバー レベルの IP ファイアウォール規則とデータベース レベルの IP ファイアウォール規則を混在させることはできますか?
はい。 サーバー レベルの IP ファイアウォール規則が必要なユーザー (管理者など) と、 データベース レベルの IP ファイアウォール規則が必要なユーザー (データベース アプリケーションのユーザーなど) がいる可能性があります。

## <a name="troubleshooting-the-database-firewall"></a>データベース ファイアウォールのトラブルシューティング

Microsoft Azure SQL Database サービスへ期待どおりにアクセスできない場合は、次の点を検討してください。

- **ローカル ファイアウォールの構成:**

  Azure SQL Database へアクセスする前に、TCP ポート 1433 に対し、コンピューターでファイアウォール例外を作成することが必要な場合があります。 Azure クラウド境界内で接続を行う場合は、追加のポートをいくつか開かなければならない場合があります。 詳細については、**SQL Database の外部と内部**に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事で確認してください。

- **ネットワーク アドレス変換 (NAT):**

  NAT が原因で、Azure SQL Database に接続するコンピューターで使用される IP アドレスが、コンピューターの IP 構成設定で表示される IP アドレスと異なっている場合があります。 Azure に接続するためにコンピューターで使用している IP アドレスを表示するには、ポータルにログインし、データベースをホストしているサーバーの **[構成]** タブに移動します。 **[許可された IP アドレス]** セクションに、**現在のクライアント IP アドレス**が表示されます。 **[追加]** をクリックして **[使用できる IP アドレス]** に追加し、このコンピューターのサーバーへのアクセスを許可します。

- **許可一覧に変更が反映されない:**

  Azure SQL Database ファイアウォールの構成に対する変更が反映されるまで最大 5 分間の遅延が発生する場合があります。

- **ログインが許可されない、または正しくないパスワードが使用された:**

  Azure SQL Database サーバーでは、ログインのアクセス許可がないか、使用したパスワードが正しくない場合、Azure SQL Database サーバーへの接続は拒否されます。 ファイアウォール設定の作成は、クライアントに対し、サーバーへの接続を試行する機会のみを提供します。それぞれのクライアントは、必要なセキュリティ資格情報を提供する必要があります。 ログインを準備する方法の詳細については、「[データベースの管理」、「ログイン」、および「Azure SQL Database 内のユーザー](sql-database-manage-logins.md)」を参照してください。

- **動的 IP アドレス:**

  動的 IP アドレス指定によるインターネット接続を使用しており、ファイアウォールの通過に問題が発生している場合は、次の解決策のいずれかをお試しください。
  
  - Azure SQL Database サーバーへアクセスするクライアント コンピューターに割り当てられている IP アドレス範囲について、インターネット サービス プロバイダー (ISP) に問い合わせ、IP ファイアウォール規則として、IP アドレス範囲を追加してください。
  - 代わりに、クライアント コンピューター用に静的 IP アドレスを取得し、IP ファイアウォール規則として、IP アドレスを追加してください。

## <a name="next-steps"></a>次の手順

- 企業ネットワーク環境で、Microsoft Azure データセンターで使用されるコンピューティング IP アドレス範囲 (SQL 範囲を含む) からの受信通信が許可されていることを確認します。 これらの IP アドレスをホワイトリストに登録することが必要な場合があります。[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。  
- サーバー レベルの IP ファイアウォール規則の作成に関するクイック スタートについては、「[Azure SQL データベースの作成](sql-database-single-database-get-started.md)」を参照してください。
- オープン ソースまたはサードパーティ製のアプリケーションから Azure SQL データベースに接続する方法の詳細については、 [SQL Database のクライアント クイック スタート コード サンプル](https://msdn.microsoft.com/library/azure/ee336282.aspx)に関する記事をご覧ください。
- 他に開くことが必要な可能性のあるポートの詳細については、**SQL Database の外部と内部**に関するセクションを、[ADO.NET 4.5 と SQL Database における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)に関する記事で確認してください。
- Azure SQL Database のセキュリティの概要については、「[データベースの保護](sql-database-security-overview.md)」を参照してください。

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
