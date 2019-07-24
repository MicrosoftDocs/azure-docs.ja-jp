---
title: Azure PowerShell スクリプト - MongoDB 用の Azure Cosmos DB API アカウントを作成する
description: Azure PowerShell スクリプト サンプル - MongoDB 用の Azure Cosmos DB API アカウントを作成する
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.devlang: PowerShell
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/29/2018
ms.reviewer: sngun
ms.openlocfilehash: 2db1cdc74fc986d5b8aeec441c3178f781d5248c
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497280"
---
# <a name="create-an-azure-cosmos-db-account-with-azure-cosmos-dbs-api-for-mongodb-using-powershell"></a>PowerShell と Azure Cosmos DB の MongoDB 用 API を使用して Azure Cosmos DB アカウントを作成する

このサンプルの PowerShell スクリプトでは、Azure Cosmos DB の MongoDB 用 API を使用して Cosmos アカウントを作成します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/cosmosdb/create-and-configure-mongodb-database/create-and-configure-mongodb-database.ps1?highlight=9,12-15,18,21-23,26-29,32-37 "Create an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../powershell-samples.md)に関する記事をご覧ください。
