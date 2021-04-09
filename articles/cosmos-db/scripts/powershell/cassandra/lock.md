---
title: Azure Cosmos Cassandra API のキースペースとテーブルに対するリソース ロックを作成する PowerShell スクリプト
description: Azure Cosmos Cassandra API のキースペースとテーブルに対するリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: f8e1e2172137c1c6a569fb169c59191ab11cedfe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684377"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Azure PowerShell を使用して Azure Cosmos Cassandra API のキースペースとテーブルに対するリソース ロックを作成する
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

このサンプルには、Azure PowerShell Az 5.4.0 以降が必要です。 `Get-Module -ListAvailable Az` を実行して、インストールされているバージョンを確認します。
インストールする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) を実行して Azure にサインインします。

> [!IMPORTANT]
> 先に `disableKeyBasedMetadataWriteAccess` プロパティを有効にして Cosmos DB アカウントをロックしておかないと、ユーザーが Cassandra SDK、CQL Shell、または Azure portal を使って行った変更に対してリソース ロックが機能しません。 このプロパティを有効にする方法の詳細については、[SDK からの変更の防止](../../../role-based-access-control.md#prevent-sdk-changes)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
|**Azure リソース**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | リソース ロックを作成します。 |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | 1 つのリソース ロックを取得するか、リソース ロックを一覧表示します。 |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | リソース ロックを削除します。 |
|||

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](/powershell/)をご覧ください。