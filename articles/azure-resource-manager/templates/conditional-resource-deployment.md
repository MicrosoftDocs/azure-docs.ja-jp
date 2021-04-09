---
title: テンプレートを使用した条件付きデプロイ
description: Azure Resource Manager テンプレート (ARM テンプレート) 内のリソースを条件付きでデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741176"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM テンプレートでの条件付きデプロイ

場合によっては、Azure Resource Manager テンプレート (ARM テンプレート) または Bicep ファイルで必要に応じてリソースをデプロイすることが必要となります。 JSON テンプレートの場合は、`condition` 要素を使用してリソースをデプロイするかどうかを指定します。 Bicep の場合は、`if` キーワードを使用してリソースをデプロイするかどうかを指定します。 この条件の値は true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

> [!NOTE]
> 条件付きのデプロイは[子リソース](child-resource-name-type.md)にはカスケードされません。 リソースとその子リソースを条件付きでデプロイする場合は、リソースの種類ごとに同じ条件を適用する必要があります。

## <a name="deploy-condition"></a>デプロイの条件

リソースをデプロイするかどうかを示すパラメーター値を渡すことができます。 次の例では、DNS ゾーンを条件付きでデプロイします。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

より複雑な例については、「[Azure SQL 論理サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)」を参照してください。

## <a name="new-or-existing-resource"></a>新規または既存のリソース

条件付きデプロイを使用して、新しいリソースを作成したり、既存のリソースを使用したりすることができます。 次の例は、新しいストレージ アカウントをデプロイするか、または既存のストレージ アカウントを使用する方法を示しています。

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
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
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

パラメーター `newOrExisting` が **new** に設定されると、その条件は true に評価されます。 ストレージ アカウントはデプロイされます。 ただし、`newOrExisting` が **existing** に設定されると、その条件は false に評価され、ストレージ アカウントはデプロイされません。

`condition` 要素を使用する完全なテンプレート例については、[新規または既存の仮想ネットワーク、ストレージ、およびパブリック IP を使用する VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) に関するページを参照してください。

## <a name="runtime-functions"></a>ランタイム関数

条件付きでデプロイされるリソースで [reference](template-functions-resource.md#reference) または [list](template-functions-resource.md#list) 関数を使用した場合、この関数はリソースがデプロイされていなくても評価されます。 この関数が存在しないリソースを参照する場合、エラーが返されます。

リソースがデプロイされるときにのみ条件に対してこの関数が評価されるようにするには、[if](template-functions-logical.md#if) 関数を使用します。 条件付きでデプロイされるリソースで `if` と `reference` を使用するサンプル テンプレートについては、[if 関数](template-functions-logical.md#if)に関するページを参照してください。

他のリソースと同じように、条件付きリソースにる[依存するリソース](define-resource-dependency.md)として設定します。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

## <a name="complete-mode"></a>完全モード

[完全モード](deployment-modes.md)でテンプレートをデプロイし、`condition` が false に評価されるためにリソースがデプロイされない場合は、テンプレートをデプロイするためにどの REST API バージョンを使用するかによって結果が異なります。 2019-05-10 より前のバージョンを使用する場合、リソースは **削除されません**。 2019-05-10 以降では、リソースは **削除されます**。 最新バージョンの Azure PowerShell および Azure CLI では、condition が false の場合、リソースが削除されます。

## <a name="next-steps"></a>次のステップ

* 条件付きデプロイについて取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」をご覧ください。
* テンプレートの作成に関するレコメンデーションについては、「[ARM テンプレートのベストプラクティス](template-best-practices.md)」を参照してください。
* リソースから複数のインスタンスを作成するには、「[ARM テンプレートでのリソースのイテレーション](copy-resources.md)」を参照してください。
