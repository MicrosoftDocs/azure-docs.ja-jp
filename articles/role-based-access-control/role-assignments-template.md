---
title: Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる - Azure RBAC
description: Azure Resource Manager テンプレートと Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、ユーザー、グループ、サービス プリンシパル、またはマネージド ID に対して Azure リソースへのアクセス権を付与する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/21/2021
ms.author: rolyon
ms.openlocfilehash: 65b4ec369085e44cdffb0550e9eeaef0196cd35a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556023"
---
# <a name="assign-azure-roles-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Azure でのロールを割り当てる

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)]Azure PowerShell または Azure CLI を使う以外に、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/template-syntax.md)を使ってロールを割り当てることもできます。 リソースを一貫して繰り返しデプロイする場合は、テンプレートが便利です。 この記事では、テンプレートを使用してロールを割り当てる方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="get-object-ids"></a>オブジェクト ID を取得する

ロールを割り当てるには、ロールの割り当て先のユーザー、グループ、またはアプリケーションの ID を指定する必要があります。 ID の形式は `11111111-1111-1111-1111-111111111111` です。 この ID は、Azure portal、Azure PowerShell、または Azure CLI を使用して取得できます。

### <a name="user"></a>User

ユーザーの ID を取得するには、[Get-AzADUser](/powershell/module/az.resources/get-azaduser) または [az ad user show](/cli/azure/ad/user#az-ad-user-show) コマンドを使用します。

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>グループ

グループの ID を取得するには、[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) または [az ad group show](/cli/azure/ad/group#az-ad-group-show) コマンドを使用します。

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>マネージド ID

マネージド ID の ID を取得するには、[Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) コマンド、または [az ad sp](/cli/azure/ad/sp) コマンドを使用できます。

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Application

サービス プリンシパルの ID (アプリケーションによって使用される ID) を取得するには、[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) または [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) コマンドを使用します。 サービス プリンシパルの場合は、アプリケーション ID **ではなく**、オブジェクト ID を使用します。

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="assign-an-azure-role"></a>Azure ロールを割り当てる

Azure RBAC でアクセス権を付与するには、ロールを割り当てます。

### <a name="resource-group-scope-without-parameters"></a>リソース グループのスコープ (パラメーターなし)

次のテンプレートは、ロールを割り当てる基本的な方法を示したものです。 一部の値は、テンプレート内で指定されます。 以下のテンプレートでは次のことを示します。

-  リソース グループのスコープでユーザー、グループ、またはアプリケーションに[閲覧者](built-in-roles.md#reader)ロールを割り当てる方法

テンプレートを使うには、以下を実行する必要があります。

- 新しい JSON ファイルを作成してテンプレートをコピーする
- `<your-principal-id>` を、ロールの割当先となるユーザー、グループ、マネージド ID、またはアプリケーションの ID に置き換える

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

次に示すのは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドの例です。"ExampleGroup" という名前のリソース グループでデプロイを開始する方法を示しています。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json
```

以下では、テンプレートをデプロイした後でユーザーにリソース グループの閲覧者ロールを割り当てる例を示します。

![リソース グループをスコープとするロールの割り当て](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>リソース グループまたはサブスクリプションのスコープ

前述のテンプレートは、それほど柔軟性が高いものではありません。 次のテンプレートでは、パラメーターを使用して、異なるスコープで使用できます。 以下のテンプレートでは次のことを示します。

- リソース グループまたはサブスクリプションのどちらかのスコープでユーザー、グループ、またはアプリケーションにロールを割り当てる方法
- 所有者、共同作成者、閲覧者のロールをパラメーターとして指定する方法

テンプレートを使うには、次の入力を指定する必要があります。

- ロールの割り当て先となるユーザー、グループ、マネージド ID、またはアプリケーションの ID
- ロール割り当てに使用する一意の ID。または既定の ID を使用できます

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> テンプレートの各デプロイのパラメーターとして同じ `roleNameGuid` 値が指定されていない場合、このテンプレートはべき等ではありません。 `roleNameGuid` が指定されていない場合、既定では、デプロイごとに新しい GUID が生成され、後続のデプロイは `Conflict: RoleAssignmentExists` のエラーで失敗します。

ロール割り当てのスコープは、デプロイのレベルから特定できます。 次に示すのは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドの例です。リソース グループのスコープでデプロイを開始する方法を示しています。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

次に示すのは、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) および [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create) コマンドの例です。サブスクリプションのスコープでデプロイを開始し、場所を指定する方法を示しています。

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment sub create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>リソースのスコープ

リソース レベルでロールを割り当てる必要がある場合は、ロールの割り当てで `scope` プロパティをリソースの名前に設定します。

以下のテンプレートでは次のことを示します。

- 新しいストレージ アカウントの作成方法
- ストレージ アカウントのスコープでユーザー、グループ、またはアプリケーションにロールを割り当てる方法
- 所有者、共同作成者、閲覧者のロールをパラメーターとして指定する方法

テンプレートを使うには、次の入力を指定する必要があります。

- ロールの割り当て先となるユーザー、グループ、マネージド ID、またはアプリケーションの ID

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

前述のテンプレートをデプロイするには、リソース グループのコマンドを使用します。 次に示すのは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドの例です。リソースのスコープでデプロイを開始する方法を示しています。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

以下では、テンプレートをデプロイした後でユーザーにストレージ アカウントの共同作成者ロールを割り当てる例を示します。

![リソースをスコープとするロールの割り当て](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>新しいサービス プリンシパル

新しいサービス プリンシパルを作成し、そのサービス プリンシパルにロールをすぐに割り当てようとすると、場合によってはそのロールの割り当てが失敗することがあります。 たとえば、新しいマネージド ID を作成し、同じ Azure Resource Manager テンプレート内でそのサービス プリンシパルにロールを割り当てようとすると、ロールの割り当てが失敗する可能性があります。 このエラーの原因は、レプリケーションの遅延である可能性があります。 サービス プリンシパルは 1 つのリージョンに作成されます。ただし、ロールの割り当ては、サービス プリンシパルがまだレプリケートされていない別のリージョンで発生する可能性があります。

このシナリオに対処するには、ロールの割り当てを作成するときに、`principalType` プロパティを `ServicePrincipal` に設定する必要があります。 また、ロールの割り当ての `apiVersion` を `2018-09-01-preview` 以降に設定する必要もあります。

以下のテンプレートでは次のことを示します。

- 新しいマネージド ID サービス プリンシパルを作成する方法
- `principalType` の指定方法
- リソース グループのスコープで、そのサービス プリンシパルに共同作成者ロールを割り当てる方法

テンプレートを使うには、次の入力を指定する必要があります。

- マネージド ID の基本名。既定の文字列を使用することもできます

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

次に示すのは、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) および [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) コマンドの例です。リソース グループのスコープでデプロイを開始する方法を示しています。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az deployment group create --resource-group ExampleGroup2 --template-file rbac-test.json
```

次に示すのは、テンプレートをデプロイした後で、新しいマネージド ID サービス プリンシパルに共同作成者ロールを割り当てる場合の例です。

![新しいマネージド ID サービス プリンシパルに対するロールの割り当て](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="next-steps"></a>次のステップ

- [クイック スタート:Azure portal を使用して ARM テンプレートを作成およびデプロイする](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [ARM テンプレートの構造と構文の詳細](../azure-resource-manager/templates/template-syntax.md)
- [サブスクリプション レベルでリソース グループとリソースを作成する](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?term=rbac)
