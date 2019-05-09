---
title: 複数のサブスクリプションとリソース グループに Azure リソースをデプロイする | Microsoft Docs
description: デプロイ時に複数の Azure サブスクリプションとリソース グループを対象にする方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: 3641833f0b55f20066302de350bfab17adfade0e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107006"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

テンプレートに含まれているリソースはすべて 1 つの[リソース グループ](resource-group-overview.md)にデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで、複数のリソース グループまたはサブスクリプションにデプロイしたい状況もあります。 たとえば Azure Site Recovery に使うバックアップ仮想マシンは、別のリソース グループと場所にデプロイした方がよい場合があります。 Resource Manager では、入れ子になったテンプレートを使用することで、親テンプレートに使われているサブスクリプションおよびリソース グループとは異なる、サブスクリプションとリソース グループを対象にすることができます。

> [!NOTE]
> 単一のデプロイにデプロイできるリソース グループは 5 つまでです。 通常、この制限は、親テンプレートに対しては 1 つのリソース グループを、入れ子になったデプロイまたはリンクされたデプロイでは最大 4 つのリソース グループを指定してデプロイできることを意味します。 ただし、親テンプレートが入れ子になったテンプレートまたはリンクされたテンプレートのみを含んでいて、親テンプレート自体はリソースをデプロイしない場合は、入れ子になったデプロイまたはリンクされたデプロイで最大 5 つのリソース グループを含めることができます。

## <a name="specify-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループの指定

異なるリソースを対象とするには、入れ子になった、またはリンクされたテンプレートを使用します。 `Microsoft.Resources/deployments` リソース タイプは、`subscriptionId` および `resourceGroup` にパラメーターを提供します。 こうしたプロパティを使用すると、入れ子になったデプロイ用に異なるサブスクリプションとリソース グループを指定することができます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。 サブスクリプション ID またはリソース グループを指定しない場合は、親テンプレートのサブスクリプションおよびリソース グループが使用されます。

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

次の例では、2 つのストレージ アカウントをデプロイしています。1 つは、デプロイ時に指定されるリソース グループのストレージ アカウントで、もう 1 つは、`secondResourceGroup` パラメーターで指定されるリソース グループのストレージ アカウントです。

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
        },
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
        }
    ]
}
```

存在しないリソース グループの名前を `resourceGroup` に設定した場合、デプロイは失敗します。

## <a name="use-the-resourcegroup-and-subscription-functions"></a>ResourceGroup() および subscription() 関数の使用

リソース グループ間のデプロイの場合、[resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) および [subscription()](resource-group-template-functions-resource.md#subscription) 関数は、入れ子になったテンプレートの指定方法に基づいてさまざまに解決されます。 

あるテンプレートを別のテンプレートに埋め込む場合、入れ子になったテンプレート内の関数は親リソース グループおよびサブスクリプションに解決されます。 埋め込みテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

別のテンプレートにリンクする場合、リンクされたテンプレート内の関数は入れ子になったリソース グループおよびサブスクリプションに解決されます。 リンクされたテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>サンプル テンプレート

以下のテンプレートは、複数のリソース グループのデプロイを示しています。 テンプレートをデプロイするスクリプトは、表の後に示されています。

|Template  |説明  |
|---------|---------|
|[クロス サブスクリプション テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |1 つのストレージ アカウントを 1 つのリソース グループに、また 1 つのストレージ アカウントを 2 番目のリソース グループにデプロイします。 2 番目のリソース グループが別のサブスクリプションにある場合は、サブスクリプション ID の値が含まれます。 |
|[クロス リソース グループ プロパティ テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |`resourceGroup()` 関数の解決方法を示します。 このテンプレートではリソースはデプロイされません。 |

### <a name="powershell"></a>PowerShell

PowerShell の場合、2 つのストレージ アカウントを、**同じサブスクリプション**内の 2 つのリソース グループにデプロイするには、次を使用します。

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

PowerShell の場合、2 つのストレージ アカウントを **2 つのサブスクリプション**にデプロイするには、次を使用します。

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

PowerShell の場合、**リソース グループ オブジェクト**が親テンプレート、インライン テンプレート、およびリンクされたテンプレートに対してどのように解決されるかをテストするには、次を使用します。

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

上記の例では、**parentRG** と **inlineRG** は両方とも **parentGroup** に解決されます。 **linkedRG** は **linkedGroup** に解決されます。 前の例からの出力は次のようになります。

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure CLI

Azure CLI の場合、2 つのストレージ アカウントを、**同じサブスクリプション**の 2 つのリソース グループにデプロイするには、次を使用します。

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

Azure CLI の場合、2 つのストレージ アカウントを、**2 つのサブスクリプション**にデプロイするには、次を使用します。

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

Azure CLI の場合、**リソース グループ オブジェクト**が親テンプレート、インライン テンプレート、およびリンクされたテンプレートに対してどのように解決されるかをテストするには、次を使用します。

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

上記の例では、**parentRG** と **inlineRG** は両方とも **parentGroup** に解決されます。 **linkedRG** は **linkedGroup** に解決されます。 前の例からの出力は次のようになります。

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>次の手順

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
