---
title: "複数のリソース グループに Azure リソースをデプロイする | Microsoft Docs"
description: "デプロイ時に複数の Azure リソース グループを対象にする方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: d8b041213b269775175a810e585103d3c538557f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/17/2017


---

# <a name="deploy-azure-resources-to-more-than-one-resource-group"></a>複数のリソース グループに Azure リソースをデプロイする

テンプレートに含まれているリソースはすべて 1 つのリソース グループにデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで複数のリソース グループにデプロイしたい状況もあります。 たとえば Azure Site Recovery に使うバックアップ仮想マシンは、別のリソース グループと場所にデプロイした方がよい場合があります。 Resource Manager では、入れ子になったテンプレートを使用することで、親テンプレートに使われているリソース グループとは異なるリソース グループを対象にすることができます。

アプリケーションとその一連のリソースにとって、リソース グループはライフサイクルのコンテナーと言えます。 リソース グループはテンプレートの外で作成し、デプロイ時にその対象として、そのリソース グループを指定することになります。 リソース グループの概要については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。

## <a name="example-template"></a>テンプレートの例

異なるリソースをデプロイ対象とするには、入れ子になった (リンクされた) テンプレートをデプロイ時に使う必要があります。 `Microsoft.Resources/deployments` リソース タイプには、`resourceGroup` というパラメーターがあり、入れ子になったデプロイ用に異なるリソース グループを指定することができます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。 次の例では、2 つのストレージ アカウントをデプロイしています。1 つは、デプロイ時に指定されるリソース グループ内のストレージ アカウントで、もう 1 つは、`crossResourceGroupDeployment` という名前のリソース グループ内のストレージ アカウントです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "StorageAccountName1": {
            "type": "string"
        },
        "StorageAccountName2": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "crossResourceGroupDeployment",
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
                            "name": "[parameters('StorageAccountName2')]",
                            "apiVersion": "2015-06-15",
                            "location": "West US",
                            "properties": {
                                "accountType": "Standard_LRS"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('StorageAccountName1')]",
            "apiVersion": "2015-06-15",
            "location": "West US",
            "properties": {
                "accountType": "Standard_LRS"
            }
        }
    ]
}
```

存在しないリソース グループの名前を `resourceGroup` に設定した場合、デプロイは失敗します。 `resourceGroup` の値を指定しなかった場合は、親リソース グループが Resource Manager で使用されます。  

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートの例をデプロイするには、ポータル、Azure PowerShell、または Azure CLI を使用できます。 Azure PowerShell または Azure CLI の場合、2017 年 5 月以降のリリースを使用する必要があります。 この例は、テンプレートが **crossrgdeployment.json** というファイル名でローカルに保存されていることを前提としています。

PowerShell の場合

```powershell
Login-AzureRmAccount

New-AzureRmResourceGroup -Name mainResourceGroup -Location "South Central US"
New-AzureRmResourceGroup -Name crossResourceGroupDeployment -Location "Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName mainResourceGroup `
  -TemplateFile c:\MyTemplates\crossrgdeployment.json
```

Azure CLI の場合

```azurecli
az login

az group create --name mainResourceGroup --location "South Central US"
az group create --name crossResourceGroupDeployment --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group mainResourceGroup \
    --template-file crossrgdeployment.json
```

デプロイが完了すると、2 つのリソース グループが確認できます。 それぞれのリソース グループにストレージ アカウントが存在します。

## <a name="use-resourcegroup-function"></a>resourceGroup() 関数の使用

リソース グループ間のデプロイの場合、[resouceGroup() 関数](resource-group-template-functions-resource.md#resourcegroup)は、入れ子になったテンプレートの指定方法に基づいてさまざまに解決されます。 

あるテンプレートを別のテンプレートに埋め込む場合、入れ子になったテンプレート内の resouceGroup() は親リソース グループに解決されます。 埋め込みテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

別のテンプレートにリンクする場合、リンクされたテンプレート内の resouceGroup() は入れ子になったリソース グループに解決されます。 リンクされたテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="next-steps"></a>次のステップ

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。

