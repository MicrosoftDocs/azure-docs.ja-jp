---
title: テンプレート リソースの場所
description: Azure Resource Manager テンプレート (ARM テンプレート) でリソースの場所を設定する方法について説明します。
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: ''
ms.openlocfilehash: 84a818109e6681b8d0e18de4d2d7969310582818
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96922395"
---
# <a name="set-resource-location-in-arm-template"></a>ARM テンプレートでリソースの場所を設定する

Azure Resource Manager テンプレート (ARM テンプレート) をデプロイするときに、各リソースの場所を指定する必要があります。 場所は、リソース グループの場所と同じ場所である必要はありません。

## <a name="get-available-locations"></a>利用可能な場所を取得する

場所ごとに、異なるリソースの種類がサポートされます。 リソースの種類にサポートされている場所を取得するには、Azure PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>location パラメーターを使用する

テンプレートを柔軟にデプロイできるようにするには、パラメーターを使用してリソースの場所を指定します。 パラメーターの既定値を `resourceGroup().location` に設定します。

次の例は、パラメーターとして指定された場所にデプロイされたストレージ アカウントを示したものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ

* テンプレート関数の完全な一覧については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
* テンプレート ファイルの詳細については、「[ARM テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
