---
title: 変更されないようにリソースをロックする
description: Azure リソースの更新または削除をユーザーに禁止するには、すべてのユーザーとロールを対象にロックを適用します。
ms.topic: conceptual
ms.date: 03/09/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 28c31681b8fbe981cd51db294c91276dfd65d71f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102619173"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>リソースのロックによる予期せぬ変更の防止

管理者は、サブスクリプション、リソース グループ、またはリソースをロックし、組織の他のユーザーが誤って重要なリソースを削除したり変更したりするのを防止することができます。 ロックは、ユーザーが持っている可能性のあるどのアクセス許可よりも優先されます。

ロック レベルは **CanNotDelete** または **ReadOnly** に設定できます。 ポータルでは、これらのロックはそれぞれ **[削除]** と **[読み取り専用]** と表示されます。

* **CanNotDelete** は、正規ユーザーはリソースの読み取りと変更を実行できますが、削除は実行できないことを示します。
* **ReadOnly** は、正規ユーザーはリソースの読み取りを実行できますが、リソースの更新は実行できないことを示します。 このロックの適用は、すべての正規ユーザーのアクセス許可を、**閲覧者** ロールによって与えられるアクセス許可に制限することに似ています。

## <a name="how-locks-are-applied"></a>ロックが適用されるしくみ

親スコープでロックを適用すると、そのスコープ内のすべてのリソースは同じロックを継承します。 後で追加するリソースも、親からロックを継承します。 継承されるロックの中で最も制限の厳しいロックが優先されます。

ロールベースのアクセス制御とは異なり、管理ロックを使用すると、すべてのユーザーとロールに対して制限を適用することができます。 ユーザーとロールのアクセス許可を設定する方法については、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) に関する記事を参照してください。

Resource Manager のロックは、管理ウィンドウで実行され、`https://management.azure.com` に送信される操作で構成される操作のみに適用されます。 ロックは、リソースが独自の機能を実行する方法を制限しません。 リソースの変更は制限されますが、リソースの操作は制限されません。 たとえば、SQL Database 論理サーバーに ReadOnly ロックを設定すると、サーバーの削除または変更を実行できなくなりますが、 そのサーバーではデータベース内のデータを作成、更新、削除できます。 データのトランザクションは `https://management.azure.com` に送信されないため、これらの操作は許可されます。

## <a name="considerations-before-applying-locks"></a>ロック適用前の考慮事項

ロックを適用すると予期しない結果につながる可能性があります。リソースを変更する操作のように見えなくても、実際はロックによってブロックされているアクションを必要とする場合があるためです。 ロックにより、Azure Resource Manager API への POST 要求を必要とする操作を防ぐことができます。 ロックによってブロックされる一般的な操作の例には、次のようなものがあります。

* 読み取り専用ロックを **ストレージ アカウント** に対して設定すると、ユーザーがアカウント キーを一覧表示できなくなります。 Azure Storage の[キーの一覧表示](/rest/api/storagerp/storageaccounts/listkeys)操作は、アカウント キーへのアクセスを保護する POST 要求によって処理されます。これにより、ストレージ アカウントのデータに完全にアクセスできるようになります。 ストレージ アカウントに対して読み取り専用ロックが設定されている場合、アカウント キーを持っていないユーザーは、BLOB またはキュー データへのアクセスに Azure AD 資格情報を使用する必要があります。 読み取り専用ロックによって、ストレージ アカウントまたはデータ コンテナー (BLOB コンテナーまたはキュー) にスコープが設定されている Azure RBAC ロールの割り当てもできなくなります。

* **ストレージ アカウント** に削除不可のロックを設定しても、そのアカウント内のデータが削除または変更されるのを防ぐことはできません。 この種類のロックでは、ストレージ アカウント自体が削除されないように保護するだけであり、そのストレージ アカウント内の BLOB、キュー、テーブル、またはファイルのデータは保護されません。 

* **ストレージ アカウント** に読み取り専用ロックを設定しても、そのアカウント内のデータが削除または変更されるのを防ぐことはできません。 この種類のロックでは、ストレージ アカウント自体が削除または変更されないように保護するだけであり、そのストレージ アカウント内の BLOB、キュー、テーブル、またはファイルのデータは保護されません。 

* 読み取り専用ロックを **App Service** リソースに設定すると、Visual Studio のサーバー エクスプローラーの操作には書き込みアクセスが必要となるため、Visual Studio のサーバー エクスプローラーはリソース用のファイルを表示できなくなります。

* 読み取り専用ロックを、**仮想マシン** を含む **リソース グループ** に設定すると、どのユーザーも仮想マシンを起動したり、再起動したりできなくなります。 これらの操作では、POST 要求が必要です。

* 削除不可ロックを **リソース グループ** に設定すると、Azure Resource Manager が履歴内の [デプロイを自動的に削除](../templates/deployment-history-deletions.md)できなくなります。 履歴内のデプロイが 800 に達した場合、デプロイは失敗します。

* **Azure Backup サービス** によって作成された **リソース グループ** に削除不可のロックを設定した場合、バックアップは失敗するようになります。 このサービスでは、最大 18 個の復元ポイントがサポートされています。 ロックされている場合、バックアップ サービスは復元ポイントをクリーンアップできません。 詳細については、「[よく寄せられる質問 - Azure VM のバックアップ](../../backup/backup-azure-vm-backup-faq.yml)」を参照してください。

* **サブスクリプション** に読み取り専用ロックを設定すると、**Azure Advisor** が正常に機能しなくなります。 Advisor は、クエリの結果を格納できません。

## <a name="who-can-create-or-delete-locks"></a>誰がロックを作成または削除できるか

管理ロックを作成または削除するには、`Microsoft.Authorization/*` または `Microsoft.Authorization/locks/*` アクションにアクセスできる必要があります。 組み込みロールのうち、**所有者** と **ユーザー アクセス管理者** にのみこれらのアクションが許可されています。

## <a name="managed-applications-and-locks"></a>Managed Applications とロック

Azure Databricks などの一部の Azure サービスでは、[マネージド アプリケーション](../managed-applications/overview.md)を使用してサービスが実装されています。 その場合、サービスで 2 つのリソース グループが作成されます。 1 つのリソース グループにはサービスの概要が含まれ、ロックされません。 もう 1 つのリソース グループにはサービスのインフラストラクチャが含まれ、ロックされます。

インフラストラクチャのリソース グループを削除しようとすると、リソース グループがロックされていることを示すエラーが表示されます。 インフラストラクチャのリソース グループのロックを削除しようとすると、システム アプリケーションによって所有されているためロックを削除できないことを示すエラーが表示されます。

代わりに、サービスを削除すると、インフラストラクチャのリソース グループも削除されます。

マネージド アプリケーションでは、デプロイしたサービスを選択します。

![サービスを選択する](./media/lock-resources/select-service.png)

サービスに **マネージド リソース グループ** へのリンクが含まれることに注意してください。 そのリソース グループがインフラストラクチャを保持しており、ロックされています。 直接削除することはできません。

![マネージド グループを表示する](./media/lock-resources/show-managed-group.png)

ロックされているインフラストラクチャ リソース グループを含め、サービスのすべてのものを削除するには、サービスの **[削除]** を選択します。

![サービスの削除](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>ロックの構成

### <a name="portal"></a>ポータル

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="arm-template"></a>ARM テンプレート

Azure Resource Manager テンプレート (ARM テンプレート) を使用してロックをデプロイする場合は、ロックのスコープとデプロイのスコープに注意する必要があります。 リソース グループやサブスクリプションのロックなど、デプロイ スコープでロックを適用する場合、スコープ プロパティを設定しないでください。 デプロイ スコープ内のリソースをロックする場合は、スコープ プロパティを設定します。

次のテンプレートは、デプロイ先のリソース グループにロックを適用します。 ロックのスコープがデプロイのスコープと一致するため、ロック リソースにスコープ プロパティがないことに注意してください。 このテンプレートは、リソース グループ レベルでデプロイされます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {  
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/locks",
            "apiVersion": "2016-09-01",
            "name": "rgLock",
            "properties": {
                "level": "CanNotDelete",
                "notes": "Resource Group should not be deleted."
            }
        }
    ]
}
```

リソース グループを作成してロックするには、サブスクリプション レベルで次のテンプレートをデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2019-10-01",
            "name": "[parameters('rgName')]",
            "location": "[parameters('rgLocation')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "lockDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Authorization/locks",
                            "apiVersion": "2016-09-01",
                            "name": "rgLock",
                            "properties": {
                                "level": "CanNotDelete",
                                "notes": "Resource group and its resources should not be deleted."
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

リソース グループ内の **リソース** にロックを適用する場合は、スコープ プロパティを追加します。 スコープを、ロックするリソースの名前に設定します。

次の例では、App Service プラン、Web サイト、および Web サイトに対するロックを作成するテンプレートを示します。 ロックのスコープは、Web サイトに設定されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-06-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
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
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-06-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
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

### <a name="azure-powershell"></a>Azure PowerShell

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

リソースのロックを削除するには、次のように入力します。

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

リソース グループのロックを削除するには、次のように入力します。

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

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

リソースのロックを削除するには、次のように入力します。

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

リソース グループのロックを削除するには、次のように入力します。

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST API

[管理ロック用の REST API](/rest/api/resources/managementlocks) を使用して、デプロイ済みのリソースをロックできます。 REST API を使用すると、ロックを作成し、削除できます。また、既存のロックに関する情報を取得することもできます。

ロックを作成するには、次のように実行します。

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

スコープには、サブスクリプション、リソース グループ、またはリソースを使用できます。 lock-name には、ロックの名前を指定できます。 api-version には、**2016-09-01** を使用します。

要求に、ロックのプロパティを指定する JSON オブジェクトを含めます。

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>次のステップ

* リソースを理論的に整理する方法については、[タグを使用したリソースの整理](tag-resources.md)に関するページをご覧ください。
* カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。 詳細については、「[Azure Policy とは](../../governance/policy/overview.md)」を参照してください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](/azure/architecture/cloud-adoption-guide/subscription-governance)」を参照してください。
