---
title: サブスクリプションおよびリソース グループにまたがってリソースをデプロイする
description: デプロイ時に複数の Azure サブスクリプションとリソース グループを対象にする方法について説明します。
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0754895215384f76b1cb44224f3ba06c80181827
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978766"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする

テンプレートに含まれているリソースはすべて 1 つの[リソース グループ](resource-group-overview.md)にデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで、複数のリソース グループまたはサブスクリプションにデプロイしたい状況もあります。 たとえば Azure Site Recovery に使うバックアップ仮想マシンは、別のリソース グループと場所にデプロイした方がよい場合があります。 Resource Manager を使用すると、入れ子になったテンプレートを使用して複数のサブスクリプションやリソース グループを対象にすることができます。

> [!NOTE]
> 単一のデプロイにデプロイできるリソース グループは 5 つまでです。 通常、この制限は、親テンプレートに対しては 1 つのリソース グループを、入れ子になったデプロイまたはリンクされたデプロイでは最大 4 つのリソース グループを指定してデプロイできることを意味します。 ただし、親テンプレートが入れ子になったテンプレートまたはリンクされたテンプレートのみを含んでいて、親テンプレート自体はリソースをデプロイしない場合は、入れ子になったデプロイまたはリンクされたデプロイで最大 5 つのリソース グループを含めることができます。

## <a name="specify-subscription-and-resource-group"></a>サブスクリプションとリソース グループを指定する

異なるリソース グループまたはサブスクリプションを対象にするには、[入れ子になったテンプレートまたはリンク済みテンプレート](resource-group-linked-templates.md)を使用します。 `Microsoft.Resources/deployments` のリソースの種類には、入れ子になったデプロイのサブスクリプションやリソース グループを指定できる `subscriptionId` や `resourceGroup` のパラメーターが用意されています。 サブスクリプション ID またはリソース グループを指定しない場合は、親テンプレートのサブスクリプションとリソース グループが使用されます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。

テンプレートをデプロイするために使用するアカウントには、指定されたサブスクリプション ID にデプロイする権限が必要です。 指定したサブスクリプションが別の Azure Active Directory テナントに存在する場合は、[別のディレクトリからゲスト ユーザーを追加する](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)必要があります。

別のリソース グループとサブスクリプションを指定するには、以下を使用します。

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

リソース グループが同じサブスクリプションにある場合は、**subscriptionId** 値を削除できます。

次の例では、2 つのストレージ アカウントをデプロイします。 最初のストレージ アカウントは、デプロイ中に指定されたリソース グループにデプロイされます。 2 番目のストレージ アカウントは、`secondResourceGroup` および `secondSubscriptionID` パラメーターで指定されたリソース グループにデプロイされます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
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
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        }
    ]
}
```

`resourceGroup` を存在しないリソース グループの名前に設定すると、デプロイは失敗します。

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

2 つのストレージ アカウントを**同じ サブスクリプション**内の 2 つのリソース グループにデプロイするには、次を使用します。

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

2 つのストレージ アカウントを **2 つのサブスクリプション**にデプロイするには、次を使用します。

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

2 つのストレージ アカウントを**同じ サブスクリプション**内の 2 つのリソース グループにデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

2 つのストレージ アカウントを **2 つのサブスクリプション**にデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>関数を使用する

[resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) および [subscription()](resource-group-template-functions-resource.md#subscription) 関数は、テンプレートをどのように指定するかに基づいて異なった方法で解決されます。 外部テンプレートにリンクする場合、これらの関数は、常にそのテンプレートのスコープに解決されます。 親テンプレート内でテンプレートを入れ子にする場合は、`expressionEvaluationOptions` プロパティを使用して、これらの関数が親テンプレートと入れ子になったテンプレートのどちらのリソース グループおよびサブスクリプションに解決されるかを指定します。 入れ子になったテンプレートのスコープに解決されるようにするには、このプロパティを `inner` に設定します。 親テンプレートのスコープに解決されるようにするには、このプロパティを `outer` に設定します。

次の表は、これらの関数が親と埋め込みのどちらのリソース グループおよびサブスクリプションに解決されるかを示しています。

| テンプレートの種類 | スコープ | 解決策 |
| ------------- | ----- | ---------- |
| 入れ子        | 外側 (既定) | 親リソース グループ |
| 入れ子        | 内部 (inner) | サブ リソース グループ |
| リンク済み        | 該当なし   | サブ リソース グループ |

次の[テンプレートの例]((https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json))は、次のものを示しています。

* 既定の (外側の) スコープを持つ入れ子になったテンプレート
* 内側のスコープを持つ入れ子になったテンプレート
* リンク済みテンプレート

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "linkedGroup",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
                },
                "parameters": {}
            }
        }
    ],
    "outputs": {
        "parentRG": {
            "type": "string",
            "value": "[concat('Parent resource group is ', resourceGroup().name)]"
        },
        "defaultScopeRG": {
            "type": "string",
            "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "innerScopeRG": {
            "type": "string",
            "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "linkedRG": {
            "type": "string",
            "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
        }
    }
}
```

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前の例からの出力は次のようになります。

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

前の例からの出力は次のようになります。

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>次の手順

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
