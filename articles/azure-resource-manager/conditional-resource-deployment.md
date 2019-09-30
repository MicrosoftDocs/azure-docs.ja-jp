---
title: Azure Resource Manager テンプレートでの条件付きデプロイ
description: Azure Resource Manager テンプレート内のリソースを条件付きでデプロイする方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259443"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Resource Manager テンプレートでの条件付きデプロイ

オプションでテンプレート内のリソースをデプロイする必要があることがあります。 リソースをデプロイするかどうかを指定するには、`condition` 要素を使用します。 この要素の値は、true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

## <a name="new-or-existing-resource"></a>新規または既存のリソース

条件付きデプロイを使用して、新しいリソースを作成したり、既存のリソースを使用したりすることができます。 次の例は、条件を使用して新しいストレージ アカウントをデプロイする方法、または既存のストレージ アカウントを使用する方法を示します。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
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
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
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
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
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

条件付きでデプロイされるリソースで [reference](resource-group-template-functions-resource.md#reference) または [list](resource-group-template-functions-resource.md#list) 関数を使用した場合、この関数はリソースがデプロイされていなくても評価されます。 この関数が存在しないリソースを参照する場合、エラーが返されます。

リソースがデプロイされるときにのみ条件に対してこの関数が評価されるようにするには、[if](resource-group-template-functions-logical.md#if) 関数を使用します。 条件付きでデプロイされるリソースで if と reference を使用するサンプル テンプレートについては、[if 関数](resource-group-template-functions-logical.md#if)に関する説明を参照してください。

## <a name="next-steps"></a>次の手順

* テンプレート作成に関する推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* リソースの複数のインスタンスを作成するには、「[Azure Resource Manager テンプレートでのリソース、プロパティ、または変数の反復](resource-group-create-multiple.md)」を参照してください。