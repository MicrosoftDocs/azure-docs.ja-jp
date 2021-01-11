---
title: リソースをテナントにデプロイする
description: Azure Resource Manager テンプレートでテナントのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2f5249eb54a62e4df082a18b22625bb93a0f09f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002759"
---
# <a name="create-resources-at-the-tenant-level"></a>テナント レベルでリソースを作成する

組織が成熟するにつれて、[ポリシー](../../governance/policy/overview.md)または [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義し、Azure AD テナント全体に割り当てる必要性が生じることがあります。 テナント レベルのテンプレートを使用すると、宣言によってポリシーを適用し、グローバル レベルでロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をテナント レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure ポリシーでは、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

ロールベースのアクセス制御では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

管理グループ、サブスクリプション、またはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

管理グループを作成するには、以下を使用します。

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

コストを管理するには、以下を使用します。

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

### <a name="schema"></a>スキーマ

テナントのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>必要なアクセス

テンプレートをデプロイするプリンシパルには、テナントのスコープでリソースを作成するためのアクセス許可が必要です。 このプリンシパルには、デプロイ アクション (`Microsoft.Resources/deployments/*`) を実行し、テンプレートに定義されているリソースを作成するためのアクセス許可が必要です。 たとえば、管理グループを作成するには、プリンシパルにテナントのスコープの共同作成者アクセス許可が必要です。 ロールの割り当てを作成するには、プリンシパルに所有者アクセス許可が必要です。

Azure Active Directory の全体管理者には、ロールを割り当てるアクセス許可が自動的に付与されるわけではありません。 テナントのスコープでテンプレートのデプロイを有効にするには、全体管理者が次の手順を実行する必要があります。

1. 全体管理者がロールを割り当てることができるように、アカウントのアクセス権を昇格させます。 詳細については、「[Azure のすべてのサブスクリプションと管理グループを管理する目的でアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)」を参照してください。

1. テンプレートをデプロイする必要があるプリンシパルに、所有者または共同作成者を割り当てます。

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

これで、テンプレートをデプロイするために必要なアクセス許可がプリンシパルに付与されました。

## <a name="deployment-commands"></a>デプロイ コマンド

テナントのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドとは異なります。

Azure CLI の場合は、[az deployment tenant create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create) を使用します。

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Azure PowerShell の場合は [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment) を使用します。

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

REST API の場合は、「[デプロイ - テナントのスコープでの作成または更新](/rest/api/resources/deployments/createorupdateattenantscope)」を使用します。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

テナント レベルのデプロイの場合は、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

テナントにデプロイするときに、テナントまたは管理グループ、サブスクリプション、およびリソース グループをテナントでターゲットにすることができます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

テンプレートのリソース セクション内で定義されたリソースは、テナントに適用されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        tenant-level-resources
    ],
    "outputs": {}
}
```

テナント内で管理グループを対象にするには、入れ子になったデプロイを追加し、`scope` プロパティを指定します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedMG",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

## <a name="use-template-functions"></a>テンプレート関数を使用する

テナント デプロイの場合、テンプレート関数を使用する際に重要な考慮事項がいくつかあります。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [subscription()](template-functions-resource.md#subscription) 関数は、サポートされて**いません**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) 関数がサポートされています。
* テナント レベルでデプロイされているリソースのリソース ID を取得するには、[tenantResourceId()](template-functions-resource.md#tenantresourceid) 関数を使用します。

  たとえば、ポリシー定義のリソース ID を取得するには、次を使用します。

  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```

  返されるリソース ID の形式は次のとおりです。

  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>管理グループの作成

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg)を使うと、管理グループを作成できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>ロールを割り当てる

[次のテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment)を使うと、テナントのスコープでロールを割り当てることができます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

* ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。
* また、[サブスクリプション レベル](deploy-to-subscription.md)または[管理グループ レベル](deploy-to-management-group.md)でテンプレートをデプロイすることもできます。
