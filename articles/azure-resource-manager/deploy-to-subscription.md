---
title: サブスクリプションでリソース グループとリソースを作成する - Azure Resource Manager テンプレート
description: Azure Resource Manager テンプレートでリソース グループを作成する方法について説明します。 Azure サブスクリプション スコープでリソースをデプロイする方法も示します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: tomfitz
ms.openlocfilehash: 913014a9b7e24345cd21979ba20ea1a1a938d022
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001605"
---
# <a name="create-resource-groups-and-resources-at-the-subscription-level"></a>サブスクリプション レベルでリソース グループとリソースを作成する

通常、Azure リソースは Azure サブスクリプションのリソース グループにデプロイします。 ただし、サブスクリプション レベルでリソースを作成することもできます。 サブスクリプション レベルのデプロイを使用して、リソース グループの作成や[ロールベースのアクセス制御](../role-based-access-control/overview.md)の割り当てなど、そのレベルで意味のあるアクションを実行します。

サブスクリプション レベルでテンプレートをデプロイするには、Azure CLI、PowerShell、または REST API を使用します。 Azure portal は、サブスクリプション レベルでのデプロイをサポートしていません。

## <a name="supported-resources"></a>サポートされているリソース

サブスクリプション レベルでは、次のリソースの種類をデプロイできます。

* [deployments](/azure/templates/microsoft.resources/deployments) 
* [peerAsns](/azure/templates/microsoft.peering/peerasns)
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>スキーマ

サブスクリプション レベルのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマと異なります。

スキーマについては、以下を使用します。

```json
https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#
```

## <a name="deployment-commands"></a>デプロイ コマンド

サブスクリプション レベルのデプロイ用のコマンドは、リソース グループのデプロイ用のコマンドと異なります。

Azure CLI の場合は、[az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurecli-interactive
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```


PowerShell デプロイ コマンドについては、[New-AzDeployment](/powershell/module/az.resources/new-azdeployment) を使用します。 次の例では、リソース グループを作成するテンプレートがデプロイされます。

```azurepowershell-interactive
New-AzDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

REST API の場合は、[デプロイ - サブスクリプション スコープでの作成](/rest/api/resources/deployments/createorupdateatsubscriptionscope)に関するページを参照してください。

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

サブスクリプション レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、**azuredeploy.json** という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所でデプロイを作成しようとしても、同じ名前で場所が異なるデプロイが既にある場合は、作成することができません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="use-template-functions"></a>テンプレート関数を使用する

サブスクリプション レベルのデプロイでは、テンプレート関数を使用する場合に、次のようないくつかの重要な考慮事項があります。

* [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) 関数は、サポートされて**いません**。
* [resourceId()](resource-group-template-functions-resource.md#resourceid) 関数は、サポートされています。 これを使用して、サブスクリプション レベルのデプロイで使用されているリソースのリソース ID を取得します。 たとえば、`resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))` で、ポリシー定義のリソース ID を取得します。
* [reference()](resource-group-template-functions-resource.md#reference) および [list()](resource-group-template-functions-resource.md#list) 関数がサポートされています。

## <a name="create-resource-groups"></a>リソース グループを作成する

Azure Resource Manager テンプレートでリソース グループを作成するには、[Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) リソースを定義してリソース グループの名前と場所を指定します。 同じテンプレートでリソース グループを作成し、そのリソース グループにリソースをデプロイすることができます。

次のテンプレートでは、空のリソース グループを作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

複数のリソース グループを作成するには、リソース グループで [copy 要素](resource-group-create-multiple.md)を使用します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
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

リソースのイテレーションについては、「[Azure Resource Manager テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする](./resource-group-create-multiple.md)」と、「[チュートリアル:Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」を参照してください。

## <a name="resource-group-and-resources"></a>リソース グループとリソース

リソース グループを作成してそれにリソースをデプロイするには、入れ子になったテンプレートを使います。 入れ子になったテンプレートでは、リソース グループにデプロイするリソースを定義します。 リソースをデプロイする前にリソース グループを確実に存在させるには、リソース グループに依存するように入れ子になったテンプレートを設定します。

次の例では、リソース グループを作成し、ストレージ アカウントをリソース グループにデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
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
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2018-05-01",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
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

## <a name="create-policies"></a>ポリシーの作成

### <a name="assign-policy"></a>ポリシーの割り当て

次の例は、既存のポリシー定義をサブスクリプションに割り当てます。 ポリシーがパラメーターを受け取る場合は、オブジェクトとして指定します。 ポリシーがパラメーターを受け取らない場合は、既定の空のオブジェクトを使用します。

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
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
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
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="define-and-assign-policy"></a>ポリシーを定義して割り当てる

ポリシーは同じテンプレートで[定義](../governance/policy/concepts/definition-structure.md)して割り当てることができます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
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
            "name": "location-lock",
            "apiVersion": "2018-05-01",
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
az deployment create \
  --name demoDeployment \
  --location centralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

PowerShell を使用してこのテンプレートをデプロイするには、以下を使用します。

```azurepowershell
New-AzDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="next-steps"></a>次の手順

* ロールの割り当てについては、「[RBAC と Azure Resource Manager テンプレートを使用して Azure リソースへのアクセスを管理する](../role-based-access-control/role-assignments-template.md)」をご覧ください。
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* サンプル テンプレートは [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments) に掲載されています。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。
