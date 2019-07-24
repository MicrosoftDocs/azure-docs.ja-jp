---
title: Azure PowerShell スクリプト - Azure Cosmos DB アカウント キーを再生成する
description: Azure PowerShell サンプル スクリプト - Azure Cosmos DB アカウント キーを再生成する
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: SnehaGunda
ms.author: sngun
ms.devlang: PowerShell
ms.topic: sample
ms.date: 05/10/2017
ms.reviewer: sngun
ms.openlocfilehash: bd4e512710130a59461e515792c1d6d216d02621
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496771"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-powershell"></a>PowerShell を使用して Azure Cosmos DB アカウント キーを再生成する

このサンプルでは、Azure CLI を使用して任意の種類の Azure Cosmos DB アカウント キーを再生成します。  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/cosmosdb/regenerate-account-keys/regenerate-account-keys.ps1?highlight=36-41 "Regenerate Azure Cosmos DB account keys")]

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
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Azure Cosmos DB アカウントに対するアクションを呼び出します。 |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Cosmos DB のその他の PowerShell サンプル スクリプトについては、[Azure Cosmos DB の PowerShell スクリプト](../powershell-samples.md)に関する記事をご覧ください。
