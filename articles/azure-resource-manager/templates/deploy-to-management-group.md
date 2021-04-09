---
title: 管理グループにリソースをデプロイする
description: Azure Resource Manager テンプレートを使用して、管理グループのスコープでリソースをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: dc7418d9e93fb50590c5e2502b3a3ffb3847273f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043310"
---
# <a name="management-group-deployments-with-arm-templates"></a>ARM テンプレートを使用した管理グループへのデプロイ

成熟している組織では、Azure Resource Manager テンプレート (ARM テンプレート) をデプロイして、管理グループ レベルでリソースを作成することができます。 たとえば、場合によっては、管理グループの[ポリシー](../../governance/policy/overview.md)や [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を定義して、割り当てる必要があります。 管理グループ レベルのテンプレートがあれば、宣言という方法を用いて管理グループ レベルでポリシーを適用し、ロールを割り当てることができます。

## <a name="supported-resources"></a>サポートされているリソース

すべてのリソースの種類を管理グループ レベルにデプロイできるわけではありません。 このセクションでは、サポートされているリソースの種類を示します。

Azure Blueprints では、以下を使用します。

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure Policy では、以下を使用します。

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Azure のロールベースのアクセス制御 (Azure RBAC) では、以下を使用します。

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

サブスクリプションまたはリソース グループにデプロイする入れ子になったテンプレートでは、以下を使用します。

* [deployments](/azure/templates/microsoft.resources/deployments)

リソースを管理する場合は、以下を使用します。

* [tags](/azure/templates/microsoft.resources/tags)

管理グループは、テナント レベルのリソースです。 ただし、新しい管理グループのスコープをテナントに設定することにより、管理グループのデプロイで管理グループを作成できます。 「[管理グループ](#management-group)」を参照してください。

## <a name="schema"></a>スキーマ

管理グループのデプロイに使用するスキーマは、リソース グループのデプロイ用のスキーマとは異なります。

テンプレートの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

パラメーター ファイルのスキーマはすべてのデプロイ範囲で同じです。 パラメーター ファイルの場合は、次を使用します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>デプロイ コマンド

管理グループにデプロイするには、管理グループのデプロイ コマンドを使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI の場合は、[az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create) を使用します。

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell では、[New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment) を使用します。

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

ARM テンプレートをデプロイするためのデプロイ コマンドとオプションの詳細については、以下を参照してください。

* [ARM テンプレートと Azure portal でリソースをデプロイする](deploy-portal.md)
* [ARM テンプレートと Azure CLI でリソースをデプロイする](deploy-cli.md)
* [ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)
* [ARM テンプレートと Azure Resource Manager REST API を使用してリソースをデプロイする](deploy-rest.md)
* [デプロイ ボタンを使用して GitHub リポジトリからテンプレートをデプロイする](deploy-to-azure-button.md)
* [Cloud Shell から ARM テンプレートをデプロイする](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>デプロイの場所と名前

管理グループ レベルのデプロイの場合、デプロイの場所を指定する必要があります。 デプロイの場所は、デプロイするリソースの場所とは異なります。 デプロイの場所では、デプロイ データを格納する場所を指定します。 [サブスクリプション](deploy-to-subscription.md)および[テナント](deploy-to-tenant.md)のデプロイにも場所が必要です。 [リソース グループ](deploy-to-resource-group.md)のデプロイの場合、リソース グループの場所を使用してデプロイ データを格納します。

デプロイ名を指定することも、既定のデプロイ名を使用することもできます。 既定の名前は、テンプレート ファイルの名前です。 たとえば、_azuredeploy.json_ という名前のテンプレートをデプロイすると、既定のデプロイ名として **azuredeploy** が作成されます。

デプロイ名ごとに、場所を変更することはできません。 ある場所にデプロイを作成しようとしても、別の場所に同じ名前の既存のデプロイがあると、作成することはできません。 たとえば、**centralus** で **deployment1** という名前の管理グループのデプロイを作成した場合、後で **deployment1** という名前の別のデプロイを **westus** の場所に作成することはできません。 エラー コード `InvalidDeploymentLocation` が表示された場合は、別の名前を使用するか、その名前の以前のデプロイと同じ場所を使用してください。

## <a name="deployment-scopes"></a>デプロイのスコープ

管理グループにデプロイする際には、リソースを以下にデプロイできます。

* 操作のターゲット管理グループ
* テナント内の別の管理グループ
* 管理グループ内のサブスクリプション
* 管理グループ内のリソース グループ
* リソース グループのテナント

[拡張リソース](scope-extension-resources.md)は、デプロイ ターゲットとは異なるターゲットにスコープ設定できます。

テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

このセクションでは、異なるスコープを指定する方法について説明します。 これらの異なるスコープを 1 つのテンプレートで結合することができます。

### <a name="scope-to-target-management-group"></a>ターゲット管理グループへのスコープ

テンプレートのリソース セクション内で定義されたリソースは、デプロイ コマンドで指定された管理グループに適用されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>別の管理グループへのスコープ

別の管理グループを対象にするには、入れ子になったデプロイを追加し、`scope` プロパティを指定します。 `scope` プロパティを `Microsoft.Management/managementGroups/<mg-name>` という形式の値に設定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>サブスクリプションへのスコープ

また、管理グループ内のサブスクリプションを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

管理グループ内のサブスクリプションを対象にするには、入れ子になったデプロイと `subscriptionId` プロパティを使用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>リソース グループへのスコープ

また、管理グループ内のリソース グループを対象にすることもできます。 テンプレートをデプロイするユーザーは、特定のスコープにアクセスできる必要があります。

その管理グループ内のリソース グループを対象にするには、入れ子になったデプロイを使用します。 `subscriptionId` と `resourceGroup` プロパティを設定します。 入れ子になったデプロイの場所は設定しないでください。これは、リソース グループの場所にデプロイされるためです。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

サブスクリプション内にリソース グループを作成し、そのリソース グループにストレージ アカウントをデプロイするために、管理グループのデプロイを使用するには、「[サブスクリプションとリソース グループにデプロイする](#deploy-to-subscription-and-resource-group)」を参照してください。

### <a name="scope-to-tenant"></a>テナントへのスコープ

テナントでリソースを作成するには、`scope` を `/` に設定します。 テンプレートをデプロイするユーザーには、[テナントでデプロイするための必要なアクセス権が必要です](deploy-to-tenant.md#required-access)。

入れ子になったデプロイを使用するには、`scope` と `location` を設定します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

または、管理グループなどの一部のリソースの種類に対して、スコープを `/` に設定することもできます。 新しい管理グループの作成については、次のセクションで説明します。

## <a name="management-group"></a>管理グループ

管理グループのデプロイで管理グループを作成するには、管理グループのスコープを `/` に設定する必要があります。

次の例では、ルート管理グループに新しい管理グループを作成します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

次の例では、親として指定された管理グループに新しい管理グループを作成します。 スコープが `/` に設定されていることに注意してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string",
            "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
        },
        "parentMG": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('mgName')]",
            "type": "Microsoft.Management/managementGroups",
            "apiVersion": "2020-05-01",
            "scope": "/",
            "location": "eastus",
            "properties": {
                "details": {
                    "parent": {
                        "id": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('parentMG'))]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "output": {
            "type": "string",
            "value": "[parameters('mgName')]"
        }
    }
}
```

## <a name="subscriptions"></a>サブスクリプション

ARM テンプレートを使用して、管理グループに新しい Azure サブスクリプションを作成するには、以下を参照してください。

* [プログラムで Azure Enterprise Agreement サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [プログラムで Microsoft 顧客契約の Azure サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [プログラムで Microsoft Partner Agreement の Azure サブスクリプションを作成する](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

既存の Azure サブスクリプションを新しい管理グループに移動するテンプレートをデプロイする場合は、「[ARM テンプレートでのサブスクリプションの移動](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template)」を参照してください

## <a name="azure-policy"></a>Azure Policy

管理グループにデプロイされているカスタム ポリシー定義は、管理グループの拡張機能です。 カスタム ポリシー定義の ID を取得するには、[extensionResourceId()](template-functions-resource.md#extensionresourceid) 関数を使用します。 組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みポリシー定義の ID を取得するには、[tenantResourceId()](template-functions-resource.md#tenantresourceid) 関数を使用します。

次の例は、管理グループ レベルでポリシーを[定義](../../governance/policy/concepts/definition-structure.md)して割り当てる方法を示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
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
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>サブスクリプションとリソース グループにデプロイする

管理グループ レベルのデプロイから、管理グループ内のサブスクリプションを対象にすることができます。 次の例では、サブスクリプション内にリソース グループを作成し、ストレージ アカウントをそのリソース グループにデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-10-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>次のステップ

* ロールの割り当ての詳細については、「[Azure Resource Manager テンプレートを使用して Azure でのロールの割り当てを追加する](../../role-based-access-control/role-assignments-template.md)」を参照してください。
* Azure Security Center のワークスペースの設定をデプロイする例については、[deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) のページを参照してください。
* [サブスクリプション レベル](deploy-to-subscription.md)と[テナント レベル](deploy-to-tenant.md)でテンプレートをデプロイすることもできます。
