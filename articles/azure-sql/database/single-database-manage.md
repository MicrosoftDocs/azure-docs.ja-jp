---
title: サーバーと単一データベースの作成と管理
description: Azure portal、PowerShell、Azure CLI、Transact-SQL (T-SQL)、および REST API を使用した Azure SQL Database でのサーバーと単一データベースの作成および管理について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93319290"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Azure SQL Database でのサーバーと単一データベースの作成と管理

Azure portal、PowerShell、Azure CLI、REST API、および Transact-SQL を使用して、Azure SQL Database でサーバーと単一データベースを作成および管理できます。

## <a name="the-azure-portal"></a>Azure ポータル

サーバー自体を作成する前に、または作成している間に、Azure SQL Database のリソース グループを作成できます。

### <a name="create-a-server"></a>サーバーの作成

[Azure portal](https://portal.azure.com) を使用してサーバーを作成するには、Azure Marketplace から新しい[サーバー](logical-servers.md) リソースを作成します。 または、Azure SQL Database をデプロイするときにサーバーを作成することもできます。

  ![サーバーの作成](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>空のデータベースまたはサンプルのデータベースを作成する

[Azure portal](https://portal.azure.com) を使用して単一の Azure SQL Database を作成するには、Azure Marketplace で Azure SQL Database リソースを選択します。 単一のデータベース自体を作成する前に、または作成している間に、リソース グループとサーバーを作成できます。 Adventure Works LT に基づいて空のデータベースやサンプル データベースを作成できます。

  ![データベースの作成 -1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> データベースの価格レベルを選択する方法については、[DTU ベースの購入モデル](service-tiers-dtu.md)および[仮想コアベースの購入モデル ](service-tiers-vcore.md)に関するページを参照してください。

## <a name="manage-an-existing-server"></a>既存のサーバーを管理する

既存のサーバーを管理するには、さまざまな方法を利用してサーバーに移動します。たとえば、特定のデータベース ページ、 **[SQL サーバー]** ページ、 **[すべてのリソース]** ページから移動します。

既存のデータベースを管理するには、 **[SQL データベース]** ページに移動し、管理するデータベースを選択します。 次のスクリーンショットでは、データベースの **概要** ページからデータベースにサーバーレベルのファイアウォールを設定する方法を確認できます。

   ![サーバーのファイアウォール規則](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> データベースのパフォーマンス プロパティを構成するには、[DTU ベースの購入モデル](service-tiers-dtu.md)および[仮想コアベースの購入モデル](service-tiers-vcore.md)に関するページを参照してください。
> [!TIP]
> Azure portal クイックスタートについては、「[Azure portal の SQL Database でデータベースを作成する](single-database-create-quickstart.md)」を参照してください。

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

Azure PowerShell を利用して、サーバー、単一データベースおよびプールされたデータベース、およびサーバーレベルのファイアウォールを作成し、管理するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

> [!TIP]
> PowerShell のスクリプトの例については、[PowerShell を使用した SQL Database 内のデータベースの作成とサーバー レベルのファイアウォール規則の構成](scripts/create-and-configure-database-powershell.md)に関する記事と、[PowerShell を使用した SQL Database 内のデータベースの監視およびスケーリング](scripts/monitor-and-scale-database-powershell.md)に関する記事を参照してください。

| コマンドレット | 説明 |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|データベースを作成します。 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|1 つまたは複数のデータベースを取得します。|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|データベースのプロパティを設定するか、既存のデータベースをエラスティック プールに移動します。|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|データベースを削除します。|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|リソース グループを作成します。|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|サーバーを作成します。|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|サーバーに関する情報を返します。|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|サーバーのプロパティを変更します。|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|サーバーを削除します。|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|サーバーレベルのファイアウォール規則を作成します。 |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|サーバーのファイアウォール規則を取得します。|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|サーバーのファイアウォール規則を変更します。|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|サーバーからファイアウォール規則を削除します。|
| New-AzSqlServerVirtualNetworkRule | 仮想ネットワーク サービス エンドポイントであるサブネットに基づいて、[*仮想ネットワーク規則*](vnet-service-endpoint-rule-overview.md)を作成します。 |

## <a name="the-azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure) を使用してサーバー、データベース、ファイアウォールを作成および管理するには、次の [Azure CLI](/cli/azure/sql/db) コマンドを使用します。 [Cloud Shell](../../cloud-shell/overview.md) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。 エラスティック プールの作成と管理については、[エラスティック プール](elastic-pool-overview.md)に関する記事をご覧ください。

> [!TIP]
> Azure CLI クイックスタートについては、[Azure CLI を使用した単一の Azure SQL Database の作成](az-cli-script-samples-content-guide.md)に関するページを参照してください。 Azure CLI のスクリプトの例については、[CLI を使用した Azure SQL Database 内のデータベースの作成と SQL Database ファイアウォール規則の構成](scripts/create-and-configure-database-cli.md)に関する記事と、[CLI を使用した Azure SQL Database 内のデータベースの監視およびスケーリング](scripts/monitor-and-scale-database-cli.md)に関する記事を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |データベースを作成します。|
|[az sql db list](/cli/azure/sql/db#az-sql-db-list)|サーバー内のすべてのデータベースとデータ ウェアハウス、またはエラスティック プール内のすべてのデータベースを一覧表示します。|
|[az sql db list-editions](/cli/azure/sql/db#az-sql-db-list-editions)|利用可能なサービス目標と容量の上限を一覧表示します。|
|[az sql db list-usages](/cli/azure/sql/db#az-sql-db-list-usages)|データベースの使用状況を返します。|
|[az sql db show](/cli/azure/sql/db#az-sql-db-show)|データベースまたはデータ ウェアハウスを取得します。|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|データベースを更新します。|
|[az sql db delete](/cli/azure/sql/db#az-sql-db-delete)|データベースを削除します。|
|[az group create](/cli/azure/group#az-group-create)|リソース グループを作成します。|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|サーバーを作成します。|
|[az sql server list](/cli/azure/sql/server#az-sql-server-list)|サーバーを一覧表示します。|
|[az sql server list-usages](/cli/azure/sql/server#az-sql-server-list-usages)|サーバーの使用状況を返します。|
|[az sql server show](/cli/azure/sql/server#az-sql-server-show)|サーバーを取得します。|
|[az sql server update](/cli/azure/sql/server#az-sql-server-update)|サーバーを更新します。|
|[az sql server delete](/cli/azure/sql/server#az-sql-server-delete)|サーバーを削除します。|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|サーバーのファイアウォール規則を作成します。|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|サーバーのファイアウォール規則を一覧表示します。|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|ファイアウォール規則の詳細を表示します。|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|ファイアウォール規則を更新します。|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|ファイアウォール規則を削除します。|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Transact-SQL を利用してサーバー、データベース、ファイアウォールを作成し、管理するには、次の T-SQL コマンドレットを使用します。 これらのコマンドは、Azure portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)、または SQL Database のサーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。 エラスティック プールの管理については、[エラスティック プール](elastic-pool-overview.md)に関する記事をご覧ください。

> [!TIP]
> Microsoft Windows で SQL Server Management Studio を使用する方法に関するクイックスタートについては、[Azure SQL Database でSQL Server Management Studio を使ってデータに接続してクエリを行う方法](connect-query-ssms.md)に関する記事をご覧ください。 macOS、Linux、Windows で Visual Studio Code を使用する方法に関するクイックスタートについては、[Azure SQL Database でVisual Studio Code を使ってデータに接続してクエリを行う方法](connect-query-vscode.md)に関する記事をご覧ください。
> [!IMPORTANT]
> Transact-SQL を利用してサーバーを作成または削除することはできません。

| コマンド | 説明 |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|新しい単一データベースを作成します。 新しいデータベースを作成するには、master データベースに接続している必要があります。|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |データベースまたはエラスティック プールを変更します。 |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|データベースを削除します。|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database、または Azure Synapse Analytics の専用 SQL プールのエディション (サービス レベル)、サービス目標 (価格レベル)、およびエラスティック プール名 (存在する場合) が返されます。 SQL Database のサーバーのマスター データベースにログオンしている場合は、すべてのデータベースの情報が返されます。 Azure Synapse Analytics の場合は、master データベースに接続する必要があります。|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Azure SQL Database 内のデータベースの CPU、IO、メモリ使用量を返します。 データベースにアクティビティがない場合でも、15 秒ごとに 1 つの行が存在します。|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Azure SQL Database 内のデータベースの CPU 使用率とストレージ データを返します。 データは、5 分間隔で収集と集計が実行されます。|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|SQL Database 接続イベントの統計が含まれており、データベース接続の成功と失敗の概要を示します。 |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|成功した Azure SQL Database の接続、接続エラー、デッドロックを返します。 この情報を使用して、SQL Database を使用したデータベース アクティビティの追跡またはトラブルシューティングを行うことができます。|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|サーバーのサーバーレベル ファイアウォールの設定を作成または更新します。 このストアド プロシージャは、マスター データベースのサーバーレベル プリンシパル ログインでのみ利用できます。 サーバーレベルのファイアウォール規則は、Azure レベルのアクセス許可を持つユーザーにより最初のサーバーレベルのファイアウォール規則が作成された後で Transact-SQL を使用しなければ作成できません。|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Azure SQL Database のデータベースに関連付けられているサーバー レベルのファイアウォール設定に関する情報が返されます。|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|サーバーからサーバーレベルのファイアウォール設定を削除します。 このストアド プロシージャは、マスター データベースのサーバーレベル プリンシパル ログインでのみ利用できます。|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Azure SQL Database 内のデータベースのデータベース レベルのファイアウォール規則を作成または更新します。 マスター データベースと SQL Database のユーザー データベースにデータベース ファイアウォール規則を構成できます。 データベース ファイアウォール規則は、包含データベース ユーザーの利用時に便利です。 |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Azure SQL Database のデータベースに関連付けられているデータベース レベルのファイアウォール設定に関する情報が返されます。 |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|データベースから、データベースレベルのファイアウォールの設定を削除します。 |

## <a name="rest-api"></a>REST API

サーバー、データベース、ファイアウォールを作成して管理するには、以下の REST API 要求を使用します。

| コマンド | 説明 |
| --- | --- |
|[サーバー - 作成または更新](/rest/api/sql/servers/createorupdate)|新しいサーバーを作成または更新します。|
|[サーバー - 削除](/rest/api/sql/servers/delete)|SQL サーバーを削除します。|
|[サーバー - 取得](/rest/api/sql/servers/get)|サーバーを取得します。|
|[サーバー - 一覧取得](/rest/api/sql/servers/list)|サブスクリプション内のサーバーの一覧を取得します。|
|[サーバー - リソース グループごとの一覧取得](/rest/api/sql/servers/listbyresourcegroup)|リソース グループ内のサーバーの一覧を取得します。|
|[サーバー - 更新](/rest/api/sql/servers/update)|既存のサーバーを更新します。|
|[データベース - 作成または更新](/rest/api/sql/databases/createorupdate)|新しいデータベースを作成するか、既存のデータベースを更新します。|
|[データベース - 削除](/rest/api/sql/databases/delete)|データベースを削除します。|
|[データベース - 取得](/rest/api/sql/databases/get)|データベースを取得します。|
|[データベース - エラスティック プールごとの一覧取得](/rest/api/sql/databases/listbyelasticpool)|エラスティック プール内のデータベースの一覧を返します。|
|[データベース - サーバーごとの一覧取得](/rest/api/sql/databases/listbyserver)|サーバー内のデータベースの一覧を返します。|
|[データベース - 更新](/rest/api/sql/databases/update)|既存のデータベースを更新します。|
|[ファイアウォール規則 - 作成または更新](/rest/api/sql/firewallrules/createorupdate)|ファイアウォール規則を作成または更新します。|
|[ファイアウォール規則 - 削除](/rest/api/sql/firewallrules/delete)|ファイアウォール規則を作成します。|
|[ファイアウォール規則 - 取得](/rest/api/sql/firewallrules/get)|ファイアウォール規則を取得します。|
|[ファイアウォール規則 - サーバーごとに一覧取得](/rest/api/sql/firewallrules/listbyserver)|ファイアウォール規則の一覧を返します。|

## <a name="next-steps"></a>次のステップ

- SQL Server データベースを Azure に移行する方法については、「[Azure SQL Database に移行](migrate-to-database-from-sql-server.md)」を参照してください。
- サポートされている機能については、[機能](features-comparison.md)に関する記事をご覧ください。
 
