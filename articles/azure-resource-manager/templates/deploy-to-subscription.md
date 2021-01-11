---
title: サブスクリプションにリソースをデプロイする
description: Azure Resource Manager テンプレートでリソース グループを作成する方法について説明します。 Azure サブスクリプション スコープでリソースをデプロイする方法も示します。
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: aca1aaf9d7d0c8a97bf2dad437953ccadc02a924
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002784"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>サブスクリプション レベルでリソース グループとリソースを作成する

リソースの管理を簡略化するには、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure サブスクリプションのレベルでリソースをデプロイします。 たとえば、[ポリシー](../../governance/policy/overview.md)および [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) をサブスクリプションにデプロイすると、これらのリソースがサブスクリプション全体に適用されます。 サブスクリプション内にリソース グループを作成し、サブスクリプションのリソース グループにリソースをデプロイすることもできます。

> [!NOTE]
> サブスクリプション レベルのデプロイでは、800 の異なるリソース グループにデプロイできます。

サブスクリプション レベルでテンプレートをデプロイするには、Azure CLI、PowerShell、REST API、またはポータルを使用します。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類をサブスクリプション レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure Blueprints では、以下を使用します。

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure ポリシーでは、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

ロールベースのアクセス制御では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

リソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

新しいリソース グループを作成する場合は、以下を使用します。

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

サブスクリプションを管理する場合は、以下を使用します。

* [budgets](/azure/templates/microsoft.consumption/budgets)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [tags](/azure/templates/microsoft.resources/tags)

その他に、以下の種類がサポートされています。

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

### <a name="schema"></a>スキーマ

サブスクリプション レベルのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマと異なります。

テンプレートの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>デプロイ コマンド

サブスクリプション レベルのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドと異なります。

Azure CLI の場合は、[az deployment sub create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

PowerShell デプロイ コマンドには、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) または **New-AzSubscriptionDeployment** を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API の場合は、[デプロイ - サブスクリプション スコープでの作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページを参照してください。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

サブスクリプション レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

サブスクリプションにデプロイする場合は、サブスクリプションまたはサブスクリプション内の任意のリソース グループを対象にすることができます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

テンプレートのリソース セクション内で定義されたリソースは、サブスクリプションに適用されます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        subscription-level-resources
    ],
    "outputs": {}
}
```

サブスクリプション内のリソース グループを対象にするには、入れ子になったデプロイを追加して、`resourceGroup` プロパティを含めます。 次の例では、入れ子になったデプロイは `rg2` という名前のリソース グループを対象としています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedDeployment",
            "resourceGroup": "rg2",
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

サブスクリプション レベルのデプロイでは、テンプレート関数を使用する場合に、次のようないくつかの重要な考慮事項があります。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) 関数がサポートされています。
* [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) 関数を使用して、サブスクリプション レベルでデプロイされるリソースのリソース ID を取得します。

  たとえば、ポリシー定義のリソース ID を取得するには、次を使用します。

  ```json
  subscriptionResourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))
  ```

  返されるリソース ID の形式は次のとおりです。

  ```json
  /subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="resource-groups"></a>リソース グループ

### <a name="create-resource-groups"></a>リソース グループを作成する

ARM テンプレートでリソース グループを作成するには、[Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) リソースを定義して、リソース グループの名前と場所を指定します。

次のテンプレートでは、空のリソース グループを作成します。

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
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

複数のリソース グループを作成するには、リソース グループで [copy 要素](copy-resources.md)を使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

リソースの反復については、[Azure Resource Manager テンプレートでリソースの複数のインスタンスをデプロイする](./copy-resources.md)ことに関するページと、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./template-tutorial-create-multiple-instances.md)」を参照してください。

### <a name="create-resource-group-and-resources"></a>リソース グループとリソースを作成する

リソース グループを作成してそれにリソースをデプロイするには、入れ子になったテンプレートを使います。 入れ子になったテンプレートでは、リソース グループにデプロイするリソースを定義します。 リソースをデプロイする前にリソース グループを確実に存在させるには、リソース グループに依存するように入れ子になったテンプレートを設定します。 最大 800 のリソース グループにデプロイできます。

次の例では、リソース グループを作成し、ストレージ アカウントをリソース グループにデプロイします。

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
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
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
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
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

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>ポリシー定義を割り当てる

次の例は、既存のポリシー定義をサブスクリプションに割り当てます。 ポリシー定義がパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシー定義がパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Azure CLI を使ってこのテンプレートをデプロイするには、次のコマンドを使います。

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>ポリシー定義を作成して割り当てる

ポリシー定義は同じテンプレートで[定義](../../governance/policy/concepts/definition-structure.md)して割り当てることができます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

サブスクリプションでポリシー定義を作成し、サブスクリプションに割り当てるには、次の CLI コマンドを使用します。

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprint

### <a name="create-blueprint-definition"></a>ブループリント定義を作成する

テンプレートからブループリント定義を[作成](../../governance/blueprints/tutorials/create-from-sample.md)することができます。

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

サブスクリプションでブループリント定義を作成するには、次の CLI コマンドを使用します。

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>アクセス制御

ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。

次の例では、リソース グループを作成し、そのグループにロックを適用して、プリンシパルにロールを割り当てます。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>次のステップ

* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* サンプル テンプレートは [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments) に掲載されています。
* [管理グループ レベル](deploy-to-management-group.md)と[テナント レベル](deploy-to-tenant.md)でテンプレートをデプロイすることもできます。
