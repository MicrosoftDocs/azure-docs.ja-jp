---
title: "テンプレートにおける Azure リソースの場所 | Microsoft Docs"
description: "Azure Resource Manager テンプレートでリソースの場所を設定する方法を示します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: b949318eb689eec9f0d08e91f2a9d0169d9d816f
ms.lasthandoff: 02/21/2017


---
# <a name="set-resource-location-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでリソースの場所を設定する
テンプレートをデプロイするときに、各リソースの場所を指定する必要があります。 このトピックでは、リソースの種類ごとに、サブスクリプションで利用できる場所を決定する方法について説明します。

## <a name="determine-supported-locations"></a>サポートされる場所を確認する

リソースの種類別のサポートされる場所の詳しい一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」を参照してください。 ただし、使用しているサブスクリプションでは、この一覧の一部の場所にはアクセスできない可能性もあります。 サブスクリプションで利用できる場所のカスタマイズされた一覧を表示するには、Azure PowerShell または Azure CLI を使用します。 

次の例では、PowerShell を使用して、`Microsoft.Web\sites` リソースの種類の場所を取得しています。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

次の例では、Azure CLI 2.0 を使用して、`Microsoft.Web\sites` リソースの種類の場所を取得しています。

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="set-location-in-template"></a>テンプレートでの場所の設定

サポートされるリソースの場所を確認した後は、テンプレートにその場所を設定する必要があります。 この値を設定する最も簡単な方法は、リソースの種類をサポートしている場所にリソース グループを作成し、各場所を `[resourceGroup().location]` に設定することです。 テンプレートやパラメーターの値は変更せずに、別の場所のリソース グループにテンプレートを再デプロイすることができます。 

次の例では、リソース グループと同じ場所にデプロイされたストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
      "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

テンプレートの場所をハードコーディングする必要がある場合は、サポートされているリージョンのいずれかの名前を指定します。 次の例では、常に米国中北部にデプロイされるストレージ アカウントを示しています。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storageloc', uniqueString(resourceGroup().id))]",
      "location": "North Central US",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

## <a name="next-steps"></a>次のステップ
* テンプレートの作成方法の推奨事項については、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](resource-manager-template-best-practices.md)」を参照してください。


