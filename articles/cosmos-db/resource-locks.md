---
title: Azure Cosmos DB リソースが削除または変更されないようにする
description: Azure リソース ロックを使用して Azure Cosmos DB リソースが削除または変更されないようにします。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636709"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Azure Cosmos DB リソースが削除または変更されないようにする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

管理者は、Azure Cosmos アカウント、データベース、またはコンテナーをロックし、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止しなければならないことがあります。 ロック レベルは CanNotDelete または ReadOnly に設定できます。

- **CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、削除は実行できないことを示します。
- **ReadOnly** は、正規ユーザーはリソースの読み取りを実行できますが、リソースの更新は実行できないことを示します。 このロックの適用は、すべての正規ユーザーのアクセス許可を、閲覧者ロールによって与えられるアクセス許可に制限することに似ています。

## <a name="how-locks-are-applied"></a>ロックが適用されるしくみ

親スコープでロックを適用すると、そのスコープ内のすべてのリソースは同じロックを継承します。 後で追加するリソースも、親からロックを継承します。 継承されるロックの中で最も制限の厳しいロックが優先されます。

Azure ロールベースのアクセス制御とは異なり、管理ロックを使用すると、すべてのユーザーとロールに対して制限を適用することができます。 Azure Cosmos DB の Azure RBAC の詳細については、「[Azure Cosmos DB の Azure ロールベースのアクセス制御](role-based-access-control.md)」を参照してください。

Resource Manager のロックは、管理ウィンドウで実行され、https://management.azure.com に送信される操作で構成される操作のみに適用されます。 ロックは、リソースが独自の機能を実行する方法を制限しません。 リソースの変更は制限されますが、リソースの操作は制限されません。 たとえば、Azure Cosmos コンテナーに ReadOnly ロックを設定すると、コンテナーの削除または変更を実行できなくなります。 コンテナー内のデータの作成、更新、または削除は実行できます。 データのトランザクションは https://management.azure.com に送信されないため、これらの操作は許可されます。

## <a name="manage-locks"></a>ロックの管理

> [!WARNING]
> リソース ロックは、先に disableKeyBasedMetadataWriteAccess プロパティを有効にして Azure Cosmos アカウントをロックしておかない限り、アカウント キーを使用して Azure Cosmos DB にアクセスするユーザーによる変更に対しては機能しません。 このプロパティを有効にする際は、アカウント キーを介して接続してスループットの変更やインデックス ポリシーの更新などのリソース変更を行う SDK、Azure portal、またはサードパーティ ツールを使用してリソースを変更する、既存のアプリケーションを壊さないように注意する必要があります。詳細について、およびアプリケーションが引き続き機能することを確認するためのチェックリストには、「[Azure Cosmos DB SDK からの変更の防止](role-based-access-control.md#prevent-sdk-changes)」を参照してください

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Template

ロックを Azure Cosmos DB リソースに適用するときには、次の形式を使用します。

- 名前 - `{resourceName}/Microsoft.Authorization/{lockName}`
- 種類 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> 既存の Azure Cosmos アカウントを変更する場合は、このプロパティを使用して再デプロイする際に、アカウントと子リソースの他のプロパティを必ず含めてください。 このテンプレートをそのままデプロイしないでください。すべてのアカウント プロパティがリセットされます。

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>次のステップ

- [Azure Resource Manager のロックの概要](../azure-resource-manager/management/lock-resources.md)
