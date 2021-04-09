---
title: PowerShell:SQL データ同期の同期スキーマを更新する
description: SQL データ同期の同期スキーマを更新するための Azure PowerShell のサンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792720"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell を使用して、既存の同期グループの同期スキーマを更新する
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

この Azure PowerShell の例では、既存の SQL データ同期の同期グループの同期スキーマを更新します。 複数のテーブルを同期しているときは、同期スキーマの効率的な更新にこのスクリプトが役立ちます。 この例では、**UpdateSyncSchema** スクリプトの使用方法を示します。このスクリプトは、GitHub で [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) として入手できます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Az PowerShell 1.4.0 以降が必要になります。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

SQL データ同期の概要については、[Azure SQL データ同期を使用した複数のクラウドおよびオンプレミス データベース間でのデータの同期](../sql-data-sync-data-sql-server-sql-database.md)に関する記事を参照してください。

> [!IMPORTANT]
> 現時点では、SQL データ同期で Azure SQL Managed Instance はサポートされていません。

## <a name="examples"></a>例

### <a name="add-all-tables-to-the-sync-schema"></a>同期スキーマにすべてのテーブルを追加する

次の例では、データベース スキーマを最新の情報に更新し、ハブ データベースのすべての有効なテーブルを同期スキーマに追加します。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>テーブルおよび列を追加および削除する

次の例では、`[dbo].[Table1]` と `[dbo].[Table2].[Column1]` を同期スキーマに追加し、`[dbo].[Table3]` を削除します。

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>スクリプト パラメーター

**UpdateSyncSchema** スクリプトには、次のパラメーターがあります。

| パラメーター | Notes |
|---|---|
| $subscriptionId | 同期グループが作成されるサブスクリプション。 |
| $resourceGroupName | 同期グループが作成されるリソース グループ。|
| $serverName | ハブ データベースのサーバー名。|
| $databaseName | ハブ データベース名。 |
| $syncGroupName | 同期グループ名。 |
| $memberName | ハブ データベースからではなく同期メンバーからデータベース スキーマを読み込む場合は、メンバー名を指定します。 ハブからデータベース スキーマを読み込む場合は、このパラメーターは空のままにします。 |
| $timeoutInSeconds | スクリプトがデータベース スキーマを最新の情報に更新するときのタイムアウト。 既定値は 900 秒です。 |
| $refreshDatabaseSchema | スクリプトがデータベース スキーマを最新の情報に更新する必要があるかどうかを指定します。 データベース スキーマが以前の構成から変化している場合 (たとえば、新しいテーブルまたは新しい列を追加した場合)、スキーマを再構成する前に最新の情報に更新する必要があります。 既定値は false です。 |
| $addAllTables | この値を true にすると、すべての有効なテーブルと列が同期スキーマに追加されます。 $TablesAndColumnsToAdd と $TablesAndColumnsToRemove の値は無視されます。 |
| $tablesAndColumnsToAdd | 同期スキーマに追加するテーブルまたは列を指定します。 各テーブルまたは列の名前は、スキーマ名で完全に区切る必要があります。 例: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 複数のテーブルまたは列の名前をコンマ (,) で区切って指定できます。 |
| $tablesAndColumnsToRemove | 同期スキーマから削除するテーブルまたは列を指定します。 各テーブルまたは列の名前は、スキーマ名で完全に区切る必要があります。 例: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`。 複数のテーブルまたは列の名前をコンマ (,) で区切って指定できます。 |

## <a name="script-explanation"></a>スクリプトの説明

**UpdateSyncSchema** スクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | 同期グループに関する情報を返します。 |
| [Update-AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | 同期グループを更新します。 |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | 同期メンバーに関する情報を返します。 |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | 同期スキーマに関する情報を返します。 |
| [Update-AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | 同期スキーマを更新します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の SQL Database 用の PowerShell サンプル スクリプトは、[Azure SQL Database 用の PowerShell スクリプト](../powershell-script-content-guide.md)のページにあります。

SQL データ同期の詳細については、以下を参照してください。

- 概要 - [Azure の SQL データ同期を使用して Azure SQL Database と SQL Server の間でデータを同期する](../sql-data-sync-data-sql-server-sql-database.md)
- データ同期の設定
    - Azure portal の使用 - [チュートリアル: Azure SQL Database と SQL Server の間でデータを同期するように SQL データ同期を設定する](../sql-data-sync-sql-server-configure.md)
    - PowerShell の使用
        -  [PowerShell を使用して Azure SQL Database の複数データベース間でデータを同期する](sql-data-sync-sync-data-between-sql-databases.md)
        -  [PowerShell を使用して Azure SQL Database と SQL Server の間でデータを同期する](sql-data-sync-sync-data-between-azure-onprem.md)
- データ同期エージェント - [Azure の SQL データ同期の Data Sync Agent](../sql-data-sync-agent-overview.md)
- ベスト プラクティス - [Azure の SQL データ同期のベスト プラクティス](../sql-data-sync-best-practices.md)
- 監視 - [Azure Monitor ログによる SQL データ同期の監視](../monitor-tune-overview.md)
- トラブルシューティング - [Azure の SQL データ同期に関する問題のトラブルシューティング](../sql-data-sync-troubleshoot.md)
- 同期スキーマの更新
    - Transact-SQL の使用 - [Azure の SQL データ同期内でスキーマ変更のレプリケートを自動化する](../sql-data-sync-update-sync-schema.md)

SQL Database の詳細については、以下をご覧ください。

- [SQL Database の概要](../sql-database-paas-overview.md)
- [データベースのライフサイクル管理](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))