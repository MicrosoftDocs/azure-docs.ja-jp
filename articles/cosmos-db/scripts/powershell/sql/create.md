---
title: Azure Cosmos DB SQL API のデータベースとコンテナーを作成する PowerShell スクリプト
description: Azure PowerShell スクリプト - Azure Cosmos DB SQL API のデータベースとコンテナーを作成する
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 2a098d3eff7bc4a8a78a880386145457b80b42d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675565"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Azure Cosmos DB - SQL API のデータベースとコンテナーを作成する
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

このサンプルには、Azure PowerShell Az 5.4.0 以降が必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認します。
インストールする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を実行して Azure にサインインします。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトは、2 つのリージョンにセッション レベルの一貫性を持つ SQL (Core) API 用の Cosmos アカウントを作成します。また、データベースを作成し、さらに、パーティション キー、カスタム インデックス ポリシー、一意キー ポリシー、TTL、専用スループット、および `multipleWriteLocations=true` である場合に使用されるカスタム競合解決パスのある最終書き込み者優先競合解決ポリシーを持つコンテナーを作成します。

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Cosmos DB アカウントを作成します。 |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Cosmos DB SQL データベースを作成します。 |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | New-AzCosmosDBSqlUniqueKeyPolicy のパラメーターとして使用する PSSqlUniqueKey オブジェクトを作成します。 |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | New-AzCosmosDBSqlContainer のパラメーターとして使用する PSSqlUniqueKeyPolicy オブジェクトを作成します。 |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | New-AzCosmosDBSqlIndexingPolicy のパラメーターとして使用するPSCompositePath オブジェクトを作成します。 |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | New-AzCosmosDBSqlIncludedPath のパラメーターとして使用する PSIndexes オブジェクトを作成します。 |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | New-AzCosmosDBSqlIndexingPolicy のパラメーターとして使用するPSIncludedPath オブジェクトを作成します。 |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | New-AzCosmosDBSqlContainer のパラメーターとして使用する PSSqlIndexingPolicy オブジェクトを作成します。 |
| [New-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | New-AzCosmosDBSqlContainer のパラメーターとして使用する PSSqlConflictResolutionPolicy オブジェクトを作成します。 |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | 新しい Cosmos DB SQL コンテナーを作成します。 |
|**Azure リソース グループ**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)を参照してください。