---
title: エラスティック プールの作成と管理 - Azure SQL Database | Microsoft Docs
description: Azure SQL エラスティック プールを作成し、管理します。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: e3f3a2260ba877c94262c12f5a999c6b7bdf5cd1
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58892400"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Azure SQL Database でのエラスティック プールの作成と管理

エラスティック プールを使用する場合、データベースのワークロードを処理するためにエラスティック プールに必要なリソースのサイズと、プールされた各データベースのリソースのサイズを決定します。

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Azure portal: エラスティック プールとプールされたデータベースの管理

すべてのプール設定は、**[プールの構成]** ブレードという 1 つの場所で見つけることができます。 このブレードを表示するには、ポータルでエラスティック プールを検索し、ブレードの上部または左側のリソース メニューのいずれかから **[プールの構成]** をクリックします。

このブレードから、以下のような変更を任意に組み合わせて行い、そららの変更すべてを 1 回にまとめて保存できます。

1. プールのサービス レベルの変更
2. パフォーマンス (DTU または vCores) およびストレージをスケール アップまたはスケール ダウンする
3. プールに対してデータベースを追加または削除する
4. プール内のデータベースの最小 (保証される) および最大のパフォーマンスを設定する
5. 新しい選択の結果としての請求書への変更を表示するコストの概要を確認する

![エラスティック プールの構成のブレード](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell:エラスティック プールとプールされたデータベースの管理

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

Azure PowerShell を使用して SQL Database エラスティック プールとプールされたデータベースを作成および管理するには、次の PowerShell コマンドレットを使用します。 PowerShell をインストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 エラスティック プール用の SQL Database サーバーを作成および管理する方法については、[SQL Database サーバーの作成と管理](sql-database-servers.md)に関する記事を参照してください。 ファイアウォール ルールを作成および管理する方法については、[PowerShell を使用したファイアウォール ルールの作成と管理](sql-database-firewall-configure.md#manage-server-level-ip-firewall-rules-using-azure-powershell)に関する記事を参照してください。

> [!TIP]
> PowerShell のサンプル スクリプトについては、「[Create elastic pools and move databases between pools and out of a pool using PowerShell (エラスティック プールを作成し、PowerShell を使用してデータベースをプール間で、およびプールから外に移動する)](scripts/sql-database-move-database-between-pools-powershell.md)」および「[Use PowerShell to monitor and scale a SQL elastic pool in Azure SQL Database (PowerShell を使用して、Azure SQL Database 内の SQL エラスティック プールを監視およびスケーリングする)](scripts/sql-database-monitor-and-scale-pool-powershell.md)」を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|エラスティック プールを作成します。|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|エラスティック プールとそのプロパティ値を取得します。|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|エラスティック プールのプロパティを変更します。たとえば、**StorageMB** プロパティを使用して、エラスティック プールの最大容量を変更します。|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|エラスティック プールを削除します。|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|エラスティック プールに対する操作の状態を取得します。|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|新しいデータベースを既存のプール内に、または単一のデータベースとして作成します。 |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|1 つまたは複数のデータベースを取得します。|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|データベースのプロパティを設定するか、または既存のデータベースをエラスティック プール内に、エラスティックから外に、またはエラスティック間で移動します。|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|データベースを削除します。|

> [!TIP]
> ポータルまたは PowerShell コマンドレットで一度に作成できるデータベースは 1 つのみであるため、エラスティック プールに多数のデータベースを作成しようとすると時間がかかることがあります。 エラスティック プールへの作成を自動化するには、「[CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)」を参照してください。

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI:エラスティック プールとプールされたデータベースの管理

[Azure CLI](/cli/azure) を使用して SQL Database エラスティック プールを作成および管理するには、次の [Azure CLI SQL Database](/cli/azure/sql/db) コマンドを使用します。 [Cloud Shell](/azure/cloud-shell/overview) を使用して CLI をブラウザーで実行することも、macOS、Linux、または Windows に[インストール](/cli/azure/install-azure-cli)することもできます。

> [!TIP]
> Azure CLI のサンプル スクリプトについては、「[Use CLI to move an Azure SQL database in a SQL elastic pool (CLI を使用して、SQL エラスティック プール内の Azure SQL データベースを移動する)](scripts/sql-database-move-database-between-pools-cli.md)」および「[Use Azure CLI to scale a SQL elastic pool in Azure SQL Database (Azure CLI を使用して、Azure SQL Database 内の SQL エラスティック プールをスケーリングする)](scripts/sql-database-scale-pool-cli.md)」を参照してください。
>

| コマンドレット | 説明 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|エラスティック プールを作成します。|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|サーバー内のエラスティック プールの一覧を返します。|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|エラスティック プール内のデータベースの一覧を返します。|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|使用可能なプール DTU の設定、ストレージ制限、およびデータベースごとの設定も含まれます。 詳細度を減らすために、既定では、追加のストレージ制限とデータベースごとの設定は非表示になります。|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|エラスティック プールを更新します。|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|エラスティック プールを削除します。|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL:プールされたデータベースの管理

Transact-SQL を使用して既存のエラスティック プール内のデータベースを作成および移動するか、または SQL Database エラスティック プールに関する情報を返すには、次の T-SQL コマンドを使用します。 これらのコマンドは、Azure Portal、[SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio)、[Visual Studio Code](https://code.visualstudio.com/docs)、または Azure SQL Database サーバーに接続して Transact-SQL コマンドを渡すことができるその他のプログラムを使用して実行できます。 T-SQL を使用してファイアウォール ルールを作成および管理する方法については、[Transact-SQL を使用したファイアウォール ルールの管理](sql-database-firewall-configure.md#manage-ip-firewall-rules-using-transact-sql)に関する記事を参照してください。

> [!IMPORTANT]
> Transact-SQL を使用して Azure SQL Database エラスティック プールを作成、更新、または削除するすることはできません。 データベースをエラスティック プールに追加または削除したり、DMV を使用して既存のエラスティック プールに関する情報を返したりすることができます。
>

| command | 説明 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|新しいデータベースを既存のプール内に、または単一のデータベースとして作成します。 新しいデータベースを作成するには、マスター データベースに接続する必要があります。|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |データベースをエラスティック プール内に、エラスティックから外に、またはエラスティック間で移動します。|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|データベースを削除します。|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|SQL Database サーバー内のすべてのエラスティック プールのリソース使用状況の統計を返します。 各エラスティック プールのレポート間隔は、15 秒に 1 行 (1 分あたり 4 行) です。 これには、プール内のすべてのデータベースごとの CPU、I/O、ログ、ストレージ消費、および同時実行要求/セッション使用率が含まれます。|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database または Azure SQL Data Warehouse のエディション (サービス レベル)、サービス目標 (価格レベル)、およびエラスティック プール名 (存在する場合) を返します。 Azure SQL Database サーバーの master データベースにログオンしている場合は、すべてのデータベースの情報が返されます。 Azure SQL Data Warehouse の場合は、master データベースに接続する必要があります。|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API:エラスティック プールとプールされたデータベースの管理

SQL Database エラスティック プールとプールされたデータベースを作成して管理するには、これらの REST API 要求を使用します。

| command | 説明 |
| --- | --- |
|[エラスティック プール - 作成または更新](https://docs.microsoft.com/rest/api/sql/elasticpools/createorupdate)|新しいエラスティック プールを作成するか、既存のエラスティック プールを更新します。|
|[エラスティック プール - 削除](https://docs.microsoft.com/rest/api/sql/elasticpools/delete)|エラスティック プールを削除します。|
|[エラスティック プール - 取得](https://docs.microsoft.com/rest/api/sql/elasticpools/get)|エラスティック プールを取得します。|
|[エラスティック プール - サーバーごとの一覧取得](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|サーバー内のエラスティック プールの一覧を返します。|
|[エラスティック プール - 更新](https://docs.microsoft.com/rest/api/sql/elasticpools/listbyserver)|既存のエラスティック プールを更新します。|
|[エラスティック プールのアクティビティ](https://docs.microsoft.com/rest/api/sql/elasticpoolactivities)|エラスティック プールのアクティビティを返します。|
|[エラスティック プール データベースのアクティビティ](https://docs.microsoft.com/rest/api/sql/elasticpooldatabaseactivities)|エラスティック プール内のデータベースのアクティビティを返します。|
|[データベース - 作成または更新](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)|新しいデータベースを作成するか、既存のデータベースを更新します。|
|[データベース - 取得](https://docs.microsoft.com/rest/api/sql/databases/get)|データベースを取得します。|
|[データベース - エラスティック プールごとの一覧取得](https://docs.microsoft.com/rest/api/sql/databases/listbyelasticpool)|エラスティック プール内のデータベースの一覧を返します。|
|[データベース - サーバーごとの一覧取得](https://docs.microsoft.com/rest/api/sql/databases/listbyserver)|サーバー内のデータベースの一覧を返します。|
|[データベース - 更新](https://docs.microsoft.com/rest/api/sql/databases/update)|既存のデータベースを更新します。|

## <a name="next-steps"></a>次の手順

* エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。
* エラスティック プールを使用した SaaS チュートリアルについては、「[Introduction to the Wingtip SaaS application (Wingtip SaaS アプリケーションの概要)](sql-database-wtp-overview.md)」を参照してください。
