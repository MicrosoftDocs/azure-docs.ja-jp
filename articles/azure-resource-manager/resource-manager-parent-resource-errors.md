---
title: Azure 親リソース エラー | Microsoft Docs
description: 親リソースの操作時のエラーを解決する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.author: tomfitz
ms.openlocfilehash: 6111f9128c56fed97414734275a21612544cccb8
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205385"
---
# <a name="resolve-errors-for-parent-resources"></a>親リソースのエラーを解決する

この記事では、親リソースに依存しているリソースをデプロイするときに起こりうるエラーについて説明します。

## <a name="symptom"></a>症状

別のリソースの子となるリソースをデプロイするときに、次のエラーが発生することがあります。

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>原因

あるリソースが別のリソースの子である場合、子リソースを作成する前に親リソースが存在する必要があります。 子リソースの名前によって、親リソースとの接続が定義されます。 子リソースの名前の形式は `<parent-resource-name>/<child-resource-name>` です。 たとえば、SQL Database は次のように定義できます。

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

同じテンプレートでサーバーとデータベースの両方をデプロイするとき、サーバーで依存関係を指定しない場合、サーバーがデプロイされる前にデータベースのデプロイが開始されることがあります。 

親リソースが既に存在し、同じテンプレートにデプロイされない場合、Resource Manager で子リソースと親リソースを関連付けることができないとき、このエラーが発生します。 このエラーは、子リソースの形式が正しくないときや、親リソースのリソース グループとは異なるリソース グループに子リソースがデプロイされるときに発生することがあります。

## <a name="solution"></a>解決策

親リソースと子リソースが同じテンプレートにデプロイされるとき、このエラーを解決するには、依存関係を追加します。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

親リソースが以前、別のテンプレートにデプロイされていたとき、このエラーを解決するには、依存関係を設定せず、 子を同じリソース グループにデプロイし、親リソースの名前を与えます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlServerName": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2014-04-01",
            "type": "Microsoft.Sql/servers/databases",
            "location": "[resourceGroup().location]",
            "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
            "properties": {
                "collation": "SQL_Latin1_General_CP1_CI_AS",
                "edition": "Basic"
            }
        }
    ],
    "outputs": {}
}
```

詳細については、「[Azure Resource Manager テンプレートでのリソース デプロイ順序の定義](resource-group-define-dependencies.md)」を参照してください。