---
title: Azure リソースの複数インスタンスのデプロイ | Microsoft Docs
description: Azure リソース マネージャー テンプレートで copy 操作と配列を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: tomfitz
ms.openlocfilehash: 22317372a7d954286ebcb0b59aea293c746b2a58
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508181"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでのリソース、プロパティ、または変数の反復

この記事では、Azure Resource Manager テンプレートでリソース、変数、またはプロパティの複数のインスタンスを作成する方法について説明します。 複数のインスタンスを作成するには、`copy` オブジェクトをテンプレートに追加します。

リソースで使用した場合、copy オブジェクトは次の形式になります。

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

変数またはプロパティで使用した場合、copy オブジェクトは次の形式になります。

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

この記事では両方の使用法を詳しく説明します。 チュートリアルについては、「[Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」を参照してください。

リソースをデプロイするかどうかを指定する必要がある場合は、[condition 要素](resource-group-authoring-templates.md#condition)に関する記述を参照してください。

## <a name="copy-limits"></a>コピー制限

反復回数を指定するには、count プロパティの値を指定します。 count は 800 を超えることはできません。

count は負の数値にすることはできません。 REST API バージョン **2019-05-10** 以降のテンプレートをデプロイする場合、count を 0 に設定できます。 それ以前の REST API のバージョンでは、count をゼロに設定することはサポートされていません。 現時点では、Azure CLI または PowerShell では count をゼロに設定することはサポートされていませんが、このサポートは将来のリリースで追加される予定です。

コピーで[完全モード デプロイ](deployment-modes.md)を使用する際は注意してください。 完全モードでリソース グループに再デプロイする場合、コピー ループを解決した後でテンプレートに指定されていないリソースはすべて削除されます。

count の制限は、リソース、変数、プロパティのいずれで使用する場合でも同じです。

## <a name="resource-iteration"></a>リソースの反復

デプロイ時に、リソースのインスタンスを 1 つまたは複数作成することを決定する必要がある場合は、リソースの種類に `copy` 要素を追加します。 copy 要素には、そのループの反復回数と名前を指定します。

複数回作成するリソースは、次の形式を取ります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
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

インデックス値をオフセットするには、copyIndex() 関数に値を渡します。 反復回数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。 次の例を見てください。

```json
"name": "[concat('storage', copyIndex(1))]",
```

この場合、以下の名前が作成されます。

* storage1
* storage2
* storage3

コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 配列で反復回数を指定するには `length` 関数を使います。また、配列における現在のインデックスを取得するには `copyIndex` を使います。 次の例を見てください。

```json
"parameters": { 
  "org": { 
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
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

この場合、以下の名前が作成されます。

* storagecontoso
* storagefabrikam
* storagecoho

既定では、リソース マネージャーは並列でリソースを作成します。 それらが作成される順序は保証されません。 しかし、リソースが順番にデプロイされるように指定したい場合もあります。 たとえば、運用環境を更新するとき、一度に特定の数だけ更新されるように更新時間をずらす必要がある場合があります。

リソースの複数のインスタンスを連続的にデプロイするには、`mode` を **serial** に、`batchSize` を一度にデプロイするインスタンスの数に設定します。 シリアル モードでは、Resource Manager はループ内で前のインスタンスへの依存関係を作成するので、前のバッチが完了するまで次のバッチは実行されません。

たとえば、ストレージ アカウントを一度に 2 つずつ、逐次的にデプロイするには、次のコマンドを使用します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

mode プロパティでも **parallel** が既定値として使用されます。

入れ子になったテンプレートで copy を使用する方法については、「[copy の使用](resource-group-linked-templates.md#using-copy)」を参照してください。

## <a name="property-iteration"></a>プロパティの反復処理

リソース上のプロパティの複数の値を作成するには、プロパティ要素に `copy` 配列を追加します。 この配列にはオブジェクトが含まれていて、各オブジェクトには次のプロパティがあります。

* name - 複数の値を作成するプロパティの名前
* count - 作成する値の数。
* input - プロパティに割り当てる値を格納するオブジェクト  

次の例は、仮想マシンで dataDisks プロパティに `copy` を適用する方法を示しています。

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

プロパティの反復処理内で `copyIndex` を使用する場合、反復処理の名前を指定する必要があります。 リソースの反復処理で使用する場合には名前を指定する必要はありません。

Resource Manager はデプロイ中に `copy` 配列を展開します。 配列の名前がプロパティの名前になります。 入力値がオブジェクトのプロパティになります。 デプロイされたテンプレートは次のようになります。

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

copy 要素は配列であるため、リソースの複数のプロパティを指定できます。 作成するプロパティごとにオブジェクトを追加します。

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

リソースの反復処理とプロパティの反復処理は同時に使用できます。 プロパティの反復処理を名前で参照します。

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>変数の反復処理

変数のインスタンスを複数作成するには、variables セクション内で `copy` プロパティを使用します。 `input` プロパティの値から構築された要素の配列を作成します。 `copy` プロパティは、変数内で使用することも、variables セクションの最上位レベルで使用することもできます。 変数の反復処理内で `copyIndex` を使用するときは、反復処理の名前を指定する必要があります。

文字列値の配列を作成する簡単な例については、[配列のコピーのテンプレート](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json)を参照してください。

次の例は、動的に構築された要素を持つ配列変数のさまざまな作成方法を示しています。 これを見ると、変数内で copy を使用して、オブジェクトと文字列の配列を作成する方法がわかります。 また、最上位レベルで copy を使用して、オブジェクト、文字列、および整数の配列を作成する方法もわかります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

作成される変数の型は、入力オブジェクトによって決まります。 たとえば、前の例の **top-level-object-array** という名前の変数は、次のものを返します。

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

また、**top-level-string-array** という名前の変数は、次のものを返します。

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>ループ内のリソースへの依存

`dependsOn` 要素を使用することで、リソースを別のリソースの後にデプロイするよう指定することが可能です。 ループ内のリソースの集合に依存するリソースをデプロイするには、dependsOn 要素にコピー ループの名前を指定します。 次の例では、仮想マシンをデプロイする前に 3 つのストレージ アカウントをデプロイする方法を示します。 完全な仮想マシン定義は示されていません。 コピー要素の name が `storagecopy` に設定され、Virtual Machines の dependsOn 要素が `storagecopy` に設定されるよう注意してください。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

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
|[VM deployment with a variable number of data disks](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |仮想マシンと共に複数のデータ ディスクをデプロイします。 |
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |変数を反復処理する各種の方法を示します。 |
|[Multiple security rules](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |ネットワーク セキュリティ グループに複数のセキュリティ規則をデプロイします。 セキュリティ規則はパラメーターから構築されます。 パラメーターについては、[複数の NSG パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json)に関するページを参照してください。 |

## <a name="next-steps"></a>次の手順

* チュートリアルについては、「[チュートリアル: Resource Manager テンプレートを使用した複数のリソース インスタンスの作成](./resource-manager-tutorial-create-multiple-instances.md)」を参照してください。

* テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。

