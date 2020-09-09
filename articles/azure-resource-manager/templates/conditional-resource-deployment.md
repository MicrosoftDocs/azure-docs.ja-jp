---
title: テンプレートを使用した条件付きデプロイ
description: Azure Resource Manager テンプレート内のリソースを条件付きでデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: effa7fe6ee1393e44a124bc087609da5d4898210
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259322"
---
# <a name="conditional-deployment-in-arm-templates"></a>ARM テンプレートでの条件付きデプロイ

場合によっては、Azure Resource Manager (ARM) テンプレートでリソースをデプロイすることが必要となります。 リソースをデプロイするかどうかを指定するには、`condition` 要素を使用します。 この要素の値は、true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

> [!NOTE]
> 条件付きのデプロイは[子リソース](child-resource-name-type.md)にはカスケードされません。 リソースとその子リソースを条件付きでデプロイする場合は、リソースの種類ごとに同じ条件を適用する必要があります。

## <a name="new-or-existing-resource"></a>新規または既存のリソース

条件付きデプロイを使用して、新しいリソースを作成したり、既存のリソースを使用したりすることができます。 次の例は、条件を使用して新しいストレージ アカウントをデプロイする方法、または既存のストレージ アカウントを使用する方法を示します。

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

パラメーター **newOrExisting** が **new** に設定されると、その条件は true に評価されます。 ストレージ アカウントはデプロイされます。 ただし、**newOrExisting** が **existing** に設定されると、その条件は false に評価され、ストレージ アカウントはデプロイされません。

`condition` 要素を使用する完全なテンプレート例については、[新規または既存の仮想ネットワーク、ストレージ、およびパブリック IP を使用する VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) に関するページを参照してください。

## <a name="allow-condition"></a>条件を許可する

条件が許可されるかどうかを示すパラメーター値を渡すことができます。 次の例では、SQL サーバーがデプロイされ、必要に応じて Azure IP が許可されます。

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

完全なテンプレートについては、[Azure SQL 論理サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)に関するページをご覧ください。

## <a name="runtime-functions"></a>ランタイム関数

条件付きでデプロイされるリソースで [reference](template-functions-resource.md#reference) または [list](template-functions-resource.md#list) 関数を使用した場合、この関数はリソースがデプロイされていなくても評価されます。 この関数が存在しないリソースを参照する場合、エラーが返されます。

リソースがデプロイされるときにのみ条件に対してこの関数が評価されるようにするには、[if](template-functions-logical.md#if) 関数を使用します。 条件付きでデプロイされるリソースで if と reference を使用するサンプル テンプレートについては、[if 関数](template-functions-logical.md#if)に関する説明を参照してください。

他のリソースと同じように、条件付きリソースにる[依存するリソース](define-resource-dependency.md)として設定します。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

## <a name="complete-mode"></a>完全モード

[完全モード](deployment-modes.md)を使用したテンプレートをデプロイする場合で、なおかつ condition が false と評価されるためにリソースがデプロイされない場合、テンプレートをデプロイするために使用する REST API のバージョンによって結果が異なります。 2019-05-10 より前のバージョンを使用する場合、リソースは**削除されません**。 2019-05-10 以降では、リソースは**削除されます**。 最新バージョンの Azure PowerShell および Azure CLI では、condition が false の場合、リソースが削除されます。

## <a name="next-steps"></a>次のステップ

* テンプレートの作成に関するレコメンデーションについては、「[ARM テンプレートのベストプラクティス](template-best-practices.md)」を参照してください。
* リソースから複数のインスタンスを作成するには、「[ARM テンプレートでのリソースのイテレーション](copy-resources.md)」を参照してください。
