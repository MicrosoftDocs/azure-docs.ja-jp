---
title: Azure Resource Manager テンプレートの変数
description: Azure Resource Manager テンプレートで変数を定義する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 5ed6bb58f2f45de557f2127fdc8abd5cdf2ef965
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383990"
---
# <a name="variables-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの変数

この記事では、Azure Resource Manager テンプレートで変数を定義および使用する方法について説明します。 変数を使用してテンプレートを簡略化します。 テンプレート全体で複雑な式を繰り返すのではなく、複雑な式を含む変数を定義します。 次に、テンプレート全体で、必要に応じてその変数を参照します。

Resource Manager は、デプロイ操作を開始する前に変数を解決します。 テンプレートで変数が使用されている場合、Resource Manager はそれを解決済みの値に置き換えます。

## <a name="define-variable"></a>変数を定義する

変数を定義する例を以下に示します。 これにより、ストレージ アカウント名の文字列値が作成されます。 テンプレート関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

変数セクションでは、[reference](resource-group-template-functions-resource.md#reference) 関数も、いずれの [list](resource-group-template-functions-resource.md#list) 関数も使用できません。 これらの関数は、リソースのランタイム状態を取得します。これらの関数は、変数が解決されるときに、デプロイ前に実行することはできません。

## <a name="use-variable"></a>変数を使用する

テンプレートでは、[variables](resource-group-template-functions-deployment.md#variables) 関数を使用してパラメーターの値を参照します。 次の例は、リソース プロパティに変数を使用する方法を示しています。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>構成変数

環境を構成するための関連する値を保持する変数を定義できます。 変数は、値を持つオブジェクトとして定義します。 次の例は、**test** と **prod** という 2 つの環境の値を保持するオブジェクトを示しています。

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

パラメーターで、使用する構成の値を示す値を作成します。

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

指定された環境の設定を取得するには、変数とパラメーターを一緒に使用します。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、変数を使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
| [変数の定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | さまざまな種類の変数を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [構成変数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 構成の値を定義する変数の用法を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [ネットワーク セキュリティの規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)と[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | ネットワーク セキュリティ グループにセキュリティの規則を割り当てるために、配列を正しい形式で作成します。 |

## <a name="next-steps"></a>次の手順

* 変数に使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* 変数の作成に関する推奨事項については、[ベスト プラクティス - 変数](template-best-practices.md#variables)に関する記事をご覧ください。
