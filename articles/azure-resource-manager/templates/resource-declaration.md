---
title: テンプレート内でリソースを宣言する
description: Azure Resource Manager テンプレート (ARM テンプレート) 内で、デプロイするリソースを宣言する方法について説明します。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743828"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM テンプレートでのリソースの宣言

Azure Resource Manager テンプレート (ARM テンプレート) を使用してリソースをデプロイするには、リソース宣言を追加します。 JSON テンプレート用の `resources` 配列か、Bicep 用の `resource` キーワードを使用します。

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>リソースの種類とバージョンの設定

テンプレートにリソースを追加する際には、まずリソースの種類と API バージョンを設定します。 これらの値によって、リソースで使用できるその他のプロパティが決まります。

次の例は、ストレージ アカウントのリソースの種類と API バージョンを設定する方法を示したものです。 この例は、リソース宣言の内容を完全に示したものではありません。

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Bicep の場合は、リソースのシンボリック名を設定します。 前の例では、シンボリック名は `sa` です。 シンボル名には任意の値を使用できますが、テンプレート内の他のリソース、パラメーター、または変数と同じにすることはできません。 シンボリック名はリソース名と同じものではありません。 シンボル名は、テンプレートの他の部分にあるリソースを簡単に参照するために使用されます。

## <a name="set-resource-name"></a>リソース名を設定する

各リソースには名前があります。 リソース名を設定する際には、[リソース名の規則と制限事項](../management/resource-name-rules.md)に注意してください。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>場所の設定

多くのリソースには場所が必要です。 リソースに場所が必要かどうかは、Intellisense または[テンプレート参照](/azure/templates/)を使用して判断できます。 次の例では、ストレージ アカウントに使用される location パラメーターを追加しています。

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

詳細については、「[ARM テンプレートでリソースの場所を設定する](resource-location.md)」を参照してください。

## <a name="set-tags"></a>タグを設定する

デプロイ時には、リソースにタグを適用することができます。 タグは、デプロイされたリソースを論理的に整理するために役立ちます。 タグを指定するさまざまな方法の例については、[ARM テンプレートのタグ](../management/tag-resources.md#arm-templates)に関する記事を参照してください。

## <a name="set-resource-specific-properties"></a>リソース固有のプロパティを設定する

上記のプロパティは、ほとんどの種類のリソースに共通するものです。 これらの値を設定した後、デプロイするリソースの種類に固有のプロパティを設定する必要があります。

使用可能なプロパティと必要なプロパティを特定するには、Intellisense または[テンプレート参照](/azure/templates/)を使用します。 次の例では、ストレージ アカウントの残りのプロパティを設定しています。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>次のステップ

* リソースを条件付きでデプロイするには、「[ARM テンプレートでの条件付きデプロイ](conditional-resource-deployment.md)」を参照してください。
* リソースの依存関係を設定するには、「[ARM テンプレートでのリソース デプロイ順序の定義](define-resource-dependency.md)」を参照してください。
