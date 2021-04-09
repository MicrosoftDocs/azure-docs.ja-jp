---
title: リソースの複数のインスタンスをデプロイする
description: Azure Resource Manager テンプレート (ARM テンプレート) で copy 操作と配列を使用して、リソースの種類を複数回デプロイします。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: c9bcb22ec53129520fd9574d0eb58b1e5777531e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97724495"
---
# <a name="resource-iteration-in-arm-templates"></a>ARM テンプレートでのリソースの反復処理

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) でリソースの複数のインスタンスを作成する方法について説明します。 テンプレートのリソース セクションに `copy` 要素を追加することで、デプロイするリソースの数を動的に設定できます。 テンプレートの構文を繰り返す必要もありません。

[プロパティ](copy-properties.md)、[変数](copy-variables.md)、および[出力](copy-outputs.md)でも `copy` を使用できます。

リソースをデプロイするかどうかを指定する必要がある場合は、[condition 要素](conditional-resource-deployment.md)に関する記述を参照してください。

## <a name="syntax"></a>構文

この `copy` 要素には、次の一般的な形式があります。

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name` プロパティは、ループを識別する任意の値です。 `count` プロパティは、リソースの種類に対して必要な反復の数を指定します。

`mode` と `batchSize` プロパティを使用して、リソースを並列または順番に配置するかどうかを指定します。 これらのプロパティについては [シリアルまたは並列](#serial-or-parallel)で説明します。

## <a name="copy-limits"></a>コピー制限

count は 800 を超えることはできません。

count は負の数値にすることはできません。 Azure CLI、PowerShell、または REST API の最新バージョンを使用してテンプレートをデプロイする場合、ゼロを指定できます。 具体的には、次のものを使用する必要があります。

* Azure PowerShell **2.6** 以降
* Azure CLI **2.0.74** 以降
* REST API バージョン **2019-05-10** 以降
* [[Linked deployments]\(リンクされたデプロイ\)](linked-templates.md) には、デプロイ リソースの種類に API バージョン **2019-05-10** 以降を使用する必要があります

以前のバージョンの PowerShell、CLI、および REST API では、count の 0 をサポートしていません。

コピーで[完全モード デプロイ](deployment-modes.md)を使用する際は注意してください。 完全モードでリソース グループに再デプロイする場合、コピー ループを解決した後でテンプレートに指定されていないリソースはすべて削除されます。

## <a name="resource-iteration"></a>リソースの反復

次の例では、`storageCount` パラメーターで指定されているストレージ アカウントの数を作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {}
}
```

各リソースの名前には、現在のループの反復を返す `copyIndex()` 関数が含まれます。 `copyIndex()` は 0 から始まります。 次の例を見てください。

```json
"name": "[concat('storage', copyIndex())]",
```

この場合、以下の名前が作成されます。

* storage0
* storage1
* storage2

インデックス値をオフセットするには、`copyIndex()` 関数に値を渡します。 反復回数は copy 要素で指定されたままですが、`copyIndex` の値が指定された値でオフセットされます。 次の例を見てください。

```json
"name": "[concat('storage', copyIndex(1))]",
```

この場合、以下の名前が作成されます。

* storage1
* storage2
* storage3

コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 配列で反復回数を指定するには `length` 関数を使います。また、配列における現在のインデックスを取得するには `copyIndex` を使います。

次の例では、パラメーターに指定された名前ごとに 1 つのストレージアカウントを作成します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

デプロイされたリソースから値を返す場合は、[outputs セクション内で copy](copy-outputs.md) を使用できます。

## <a name="serial-or-parallel"></a>シリアルまたは並列

既定では、リソース マネージャーは並列でリソースを作成します。 テンプレートの合計リソース数の上限 (800 個) 以外、並列にデプロイされるリソースの数に制限はありません。 それらが作成される順序は保証されません。

しかし、リソースが順番にデプロイされるように指定したい場合もあります。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。 リソースの複数のインスタンスを連続的にデプロイするには、`mode` を **serial** に、`batchSize` を一度にデプロイするインスタンスの数に設定します。 シリアル モードでは、Resource Manager はループ内で前のインスタンスへの依存関係を作成するので、前のバッチが完了するまで次のバッチは実行されません。

`batchSize` の値は、copy 要素の `count` の値を超えることはできません。

たとえば、ストレージ アカウントを一度に 2 つずつ、逐次的にデプロイするには、次のコマンドを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode` プロパティでも **parallel** が既定値として使用されます。

## <a name="iteration-for-a-child-resource"></a>子リソースの反復処理

子リソースにコピー ループを使用することはできません。 通常は別のリソース内で入れ子になっているように定義するリソースの複数のインスタンスを作成するには、代わりに、そのリソースを最上位のリソースとして作成する必要があります。 type および name の各プロパティを使用して、親リソースとの関係を定義します。

たとえば、通常はデータ ファクトリ内の子リソースとしてデータセットを定義するとします。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

複数のデータ セットを作成するには、それをデータ ファクトリの外部に移動します。 データセットは、データ ファクトリと同じレベルである必要がありますが、今までどおりデータ ファクトリの子リソースです。 type および name の各プロパティを使用して、データセットとデータ ファクトリの関係を保存します。 テンプレート内の位置から type を推論できなくなったため、`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` の形式で完全修飾型を指定する必要があります。

データ ファクトリのインスタンスを使用して親子関係を確立するには、親リソースの名前を含むデータ セットの名前を指定します。 `{parent-resource-name}/{child-resource-name}` の形式で入力します。

次の例は、実装を示します。

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>サンプル テンプレート

次の例は、リソースまたはプロパティの複数のインスタンスを作成するための一般的なシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[Copy storage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |名前にインデックス番号を含む複数のストレージ アカウントをデプロイします。 |
|[Serial copy storage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |複数のストレージ アカウントを一度に 1 つずつデプロイします。 名前にはインデックス番号が含まれます。 |
|[Copy storage with array](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |複数のストレージ アカウントをデプロイします。 名前には、配列からの値が含まれます。 |

## <a name="next-steps"></a>次のステップ

* コピー ループで作成されたリソースへの依存関係を設定するには、「[ARM テンプレートでのリソース デプロイ順序の定義](define-resource-dependency.md)」を参照してください。
* チュートリアルについては、「[チュートリアル:ARM テンプレートを使用した複数のリソース インスタンスの作成](template-tutorial-create-multiple-instances.md)」を参照してください。
* リソースのコピーについて取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。
* copy 要素のその他の使用方法については、以下を参照してください。
  * [ARM テンプレートでのプロパティの反復処理](copy-properties.md)
  * [ARM テンプレートでの変数の反復処理](copy-variables.md)
  * [ARM テンプレートでの出力の反復処理](copy-outputs.md)
* 入れ子になったテンプレートで copy を使用する方法については、「[copy の使用](linked-templates.md#using-copy)」を参照してください。
