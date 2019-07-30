---
title: Azure リソースをロックして変更を防止する | Microsoft Docs
description: 重要な Azure リソースの更新または削除をユーザーに禁止するには、すべてのユーザーとロールを対象にロックを適用します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 31d77b4ea6e7594cd3ed4dba264f9ea6db4ca290
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155221"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>リソースのロックによる予期せぬ変更の防止 

管理者は、サブスクリプション、リソース グループ、またはリソースをロックし、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止しなければならないことがあります。 ロック レベルは **CanNotDelete** または **ReadOnly** に設定できます。 ポータルでは、これらのロックはそれぞれ **[削除]** と **[読み取り専用]** と表示されます。

* **CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、削除は実行できないことを示します。 
* **ReadOnly** は、正規ユーザーはリソースの読み取りを実行できますが、リソースの更新は実行できないことを示します。 このロックの適用は、すべての正規ユーザーのアクセス許可を、**閲覧者**ロールによって与えられるアクセス許可に制限することに似ています。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>ロックが適用されるしくみ

親スコープでロックを適用すると、そのスコープ内のすべてのリソースは同じロックを継承します。 後で追加するリソースも、親からロックを継承します。 継承されるロックの中で最も制限の厳しいロックが優先されます。

ロールベースのアクセス制御とは異なり、管理ロックを使用すると、すべてのユーザーとロールに対して制限を適用することができます。 ユーザーとロールのアクセス許可を設定する方法については、「[Azure のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)」を参照してください。

Resource Manager のロックは、管理ウィンドウで実行され、`https://management.azure.com` に送信される操作で構成される操作のみに適用されます。 ロックは、リソースが独自の機能を実行する方法を制限しません。 リソースの変更は制限されますが、リソースの操作は制限されません。 たとえば、SQL Database に ReadOnly ロックを設定すると、データベースの削除または変更を実行できなくなりますが、 データベース内のデータの作成、更新、または削除は実行できます。 データのトランザクションは `https://management.azure.com` に送信されないため、これらの操作は許可されます。

**ReadOnly** を適用すると予期しない結果につながる可能性があります。リソースを変更する操作のように見えなくても、実際はロックによってブロックされているアクションを必要とする場合があるためです。 **ReadOnly** ロックは、リソースまたはリソースを含むリソース グループに適用できます。 **ReadOnly** ロックによってブロックされる一般的な操作の例には、次のようなものがあります。

* **ReadOnly** ロックをストレージ アカウントに設定すると、どのユーザーもキーを一覧表示できなくなります。 返されるキーは書き込み操作に使用できるため、キーの一覧表示操作は POST 要求を介して処理されます。

* **ReadOnly** ロックを App Service リソースに設定すると、Visual Studio のサーバー エクスプローラーの操作には書き込みアクセスが必要となるため、Visual Studio のサーバー エクスプローラーはリソース用のファイルを表示できなくなります。

* **ReadOnly** ロックを、仮想マシンを含むリソース グループに設定すると、どのユーザーも仮想マシンを起動したり、再起動したりできなくなります。 これらの操作では、POST 要求が必要です。

## <a name="who-can-create-or-delete-locks"></a>誰がロックを作成または削除できるか
管理ロックを作成または削除するには、`Microsoft.Authorization/*` または `Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、**所有者**と**ユーザー アクセス管理者**にのみこれらのアクションが許可されています。

## <a name="managed-applications-and-locks"></a>Managed Applications とロック

Azure Databricks などの一部の Azure サービスでは、[マネージド アプリケーション](../managed-applications/overview.md)を使用してサービスが実装されています。 その場合、サービスで 2 つのリソース グループが作成されます。 1 つのリソース グループにはサービスの概要が含まれ、ロックされません。 もう 1 つのリソース グループにはサービスのインフラストラクチャが含まれ、ロックされます。

インフラストラクチャのリソース グループを削除しようとすると、リソース グループがロックされていることを示すエラーが表示されます。 インフラストラクチャのリソース グループのロックを削除しようとすると、システム アプリケーションによって所有されているためロックを削除できないことを示すエラーが表示されます。

代わりに、サービスを削除すると、インフラストラクチャのリソース グループも削除されます。

マネージド アプリケーションでは、デプロイしたサービスを選択します。

![サービスを選択する](./media/resource-group-lock-resources/select-service.png)

サービスに**マネージド リソース グループ**へのリンクが含まれることに注意してください。 そのリソース グループがインフラストラクチャを保持しており、ロックされています。 直接削除することはできません。

![マネージド グループを表示する](./media/resource-group-lock-resources/show-managed-group.png)

ロックされているインフラストラクチャ リソース グループを含め、サービスのすべてのものを削除するには、サービスの **[削除]** を選択します。

![サービスの削除](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>ポータル
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Template

ロックをデプロイするために Resource Manager テンプレートを使用する場合は、ロックの範囲に応じて名前と型に異なる値を使用します。

ロックを**リソース**に適用するときには、次の形式を使用します。

* 名前 - `{resourceName}/Microsoft.Authorization/{lockName}`
* 種類 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

**リソース グループ**または**サブスクリプション**にロックを適用するときには、次の形式を使用します。

* 名前 - `{lockName}`
* 種類 - `Microsoft.Authorization/locks`

次の例では、App Service プラン、Web サイト、および Web サイトに対するロックを作成するテンプレートを示します。 ロックのリソースの種類は、ロック対象リソースのリソースの種類と **/providers/locks** です。 ロックの名前は、リソース名に **/Microsoft.Authorization/** とロックの名前を連結して作成されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

リソース グループに対するロックの設定の例は、「[Create a resource group and lock it (リソース グループの作成とロック)](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)」を参照してください。

## <a name="powershell"></a>PowerShell
デプロイされているリソースを Azure PowerShell でロックするには、[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) コマンドを使います。

リソースをロックするには、対象となるリソースの名前とそのリソース タイプ、リソース グループ名を指定します。

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

リソース グループをロックするには、そのリソース グループの名前を指定します。

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

ロックについての情報を取得するには、[Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) を使います。 サブスクリプション内のすべてのロックを取得するには、次のように入力します。

```azurepowershell-interactive
Get-AzResourceLock
```

特定のリソースのロックをすべて取得するには、次のように入力します。

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

特定のリソース グループのロックをすべて取得するには、次のように入力します。

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

ロックを削除するには、次のように入力します。

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

デプロイされているリソースを Azure CLI でロックするには、[az lock create](/cli/azure/lock#az-lock-create) コマンドを使います。

リソースをロックするには、対象となるリソースの名前とそのリソース タイプ、リソース グループ名を指定します。

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

リソース グループをロックするには、そのリソース グループの名前を指定します。

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

ロックについての情報を取得するには、[az lock list](/cli/azure/lock#az-lock-list) を使います。 サブスクリプション内のすべてのロックを取得するには、次のように入力します。

```azurecli
az lock list
```

特定のリソースのロックをすべて取得するには、次のように入力します。

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

特定のリソース グループのロックをすべて取得するには、次のように入力します。

```azurecli
az lock list --resource-group exampleresourcegroup
```

ロックを削除するには、次のように入力します。

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
[管理ロック用の REST API](https://docs.microsoft.com/rest/api/resources/managementlocks) を使用して、デプロイ済みのリソースをロックできます。 REST API を使用すると、ロックを作成し、削除できます。また、既存のロックに関する情報を取得することもできます。

ロックを作成するには、次のように実行します。

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 lock-name には、ロックの名前を指定できます。 api-version には、**2016-09-01** を使用します。

要求に、ロックのプロパティを指定する JSON オブジェクトを含めます。

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>次の手順
* リソースを理論的に整理する方法については、「 [タグを使用したリソースの整理](resource-group-using-tags.md)
* カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。 詳細については、「[Azure Policy とは](../governance/policy/overview.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。

