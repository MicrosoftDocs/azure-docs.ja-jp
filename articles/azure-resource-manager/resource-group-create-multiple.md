---
title: "Azure リソースの複数インスタンスのデプロイ | Microsoft Docs"
description: "Azure リソース マネージャー テンプレートで copy 操作と配列を使用して、リソースをデプロイする際に複数回反復処理する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/24/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 04a3866f88b00486c30c578699d34cd6e8e776d7
ms.openlocfilehash: 056ee5e67b9a6d396586c53b04d50f89e6fbb560
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-multiple-instances-of-resources-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでの複数のリソース インスタンスのデプロイ
このトピックでは、Azure リソース マネージャー テンプレートで反復処理して、リソースの複数のインスタンスを作成する方法について説明します。

## <a name="copy-copyindex-and-length"></a>copy、copyIndex、length
複数回作成するリソース内では、反復処理する回数を指定する **copy** オブジェクトを定義できます。 copy は次の形式で指定します。

```json
"copy": { 
    "name": "websitescopy", 
    "count": "[parameters('count')]" 
} 
```

現在の反復値には、**copyIndex()** 関数を使用してアクセスすることができます。 次の例では、名前の作成に concat 関数と copyIndex を使用しています。

```json
[concat('examplecopy-', copyIndex())]
```

値の配列から複数のリソースを作成する場合は、 **length** 関数を使用して数を指定できます。 length 関数のパラメーターとして、配列を指定します。

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

copy オブジェクトは、最上位のリソースにのみ適用できます。 リソースの種類のプロパティや子リソースには適用できません。 ただし、このトピックでは、プロパティに複数の項目を指定し、子リソースの複数のインスタンスを作成する方法を説明します。 次の擬似コードの例で、copy を適用できる場所を示します。

```json
"resources": [
  {
    "type": "{provider-namespace-and-type}",
    "name": "parentResource",
    "copy": {  
      /* yes, copy can be applied here */
    },
    "properties": {
      "exampleProperty": {
        /* no, copy cannot be applied here */
      }
    },
    "resources": [
      {
        "type": "{provider-type}",
        "name": "childResource",
        /* copy can be applied if resource is promoted to top level */ 
      }
    ]
  }
] 
```

**copy** をプロパティに適用することはできませんが、プロパティは、今までどおりそのプロパティが含まれるリソースの反復の一部です。 したがって、プロパティ内で **copyIndex()** を使用して値を指定することができます。

リソース内でプロパティの反復処理が必要になるシナリオはいくつかあります。 たとえば、1 つの仮想マシンに複数のデータ ディスクを指定することが必要な場合があります。 プロパティを反復処理する方法を確認するには、「[コピーが動作しない場合の複数のインスタンスの作成](#create-multiple-instances-when-copy-wont-work)」を参照してください。 

子リソースを操作するには、「[子リソースの複数のインスタンスの作成](#create-multiple-instances-of-a-child-resource)」を参照してください。

## <a name="use-index-value-in-name"></a>name でのインデックス値の使用
copy 操作では、増分するインデックス値に基づいた一意の名前を付けたリソースの複数のインスタンスを作成できます。 たとえば、デプロイ済みの各リソース名の末尾に一意の数値を追加することもできます。 次の&3; つの Web サイトをデプロイするには:

* examplecopy-0
* examplecopy-1
* examplecopy-2.

次のテンプレートを使用します。

```json
"parameters": { 
  "count": { 
    "type": "int", 
    "defaultValue": 3 
  } 
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', copyIndex())]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[parameters('count')]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      }
  } 
]
```

## <a name="offset-index-value"></a>インデックス値のオフセット
前の例では、インデックス値は 0 から 2 の値を取ります。 インデックス値をオフセットするには、**copyIndex(1)** のように、**copyIndex()** 関数に値を渡します。 実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。 そのため前の例と同じテンプレートを使用しても、 **copyIndex(1)** を指定すると、次の名前の&3; つの Web サイトがデプロイされます。

* examplecopy-1
* examplecopy-2
* examplecopy-3

## <a name="use-copy-with-array"></a>配列でのコピーの使用
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに便利です。 次の&3; つの Web サイトをデプロイするには:

* examplecopy-Contoso
* examplecopy-Fabrikam
* examplecopy-Coho

次のテンプレートを使用します。

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "Contoso", 
         "Fabrikam", 
         "Coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
      "type": "Microsoft.Web/sites", 
      "location": "East US", 
      "apiVersion": "2015-08-01",
      "copy": { 
         "name": "websitescopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      "properties": {
          "serverFarmId": "hostingPlanName"
      } 
  } 
]
```

もちろん、コピー数を配列の長さ以外の値に設定できます。 たとえば、さまざまな値を含む配列を作成し、デプロイする配列要素の数を指定するパラメーターの値を渡すことができます。 この場合は、最初の例に示すようにコピー数を設定します。 

## <a name="depend-on-resources-in-a-loop"></a>ループ内のリソースへの依存
**dependsOn** 要素を使用し、あるリソースを別のリソースの後にデプロイするように指定できます。 ループ内のリソースの集合に依存するリソースをデプロイするには、**dependsOn** 要素にコピー ループの名前を指定します。 次の例では、仮想マシンをデプロイする前に&3; つのストレージ アカウントをデプロイする方法を示します。 完全な仮想マシン定義は示されていません。 コピー要素の **name** が **storagecopy** に設定され、Virtual Machines の **dependsOn** 要素も **storagecopy** に設定されていることに注意してください。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2015-06-15",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id), copyIndex())]",
            "location": "[resourceGroup().location]",
            "properties": {
                "accountType": "Standard_LRS"
            },
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

## <a name="create-multiple-instances-of-a-child-resource"></a>子リソースの複数のインスタンスの作成
子リソースにコピー ループを使用することはできません。 通常他のリソース内の入れ子として定義されるリソースの複数のインスタンスを作成するには、代わりにそのリソースを最上位のリソースとして作成する必要があります。 **type** および **name** の各プロパティを使用して、親リソースとの関係を定義します。

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
}]
```

データセットの複数のインスタンスを作成するには、データ ファクトリの外部に移動します。 データセットは、データ ファクトリと同じレベルである必要がありますが、今までどおりデータ ファクトリの子リソースです。 **type** および **name** の各プロパティを使用して、データセットとデータ ファクトリの関係を保存します。 テンプレート内の位置から type を推論できなくなったため、`{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` の形式で完全修飾型を指定する必要があります。

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
    } 
    ...
}]
```

## <a name="create-multiple-instances-when-copy-wont-work"></a>コピーが動作しない場合の複数のインスタンスの作成
**copy** は、リソースの種類に対してのみ使用できます。リソースの種類内のプロパティに対して使用することはできません。 この要件により、リソースの一部の複数のインスタンスを作成する場合に問題が発生する可能性があります。 一般的なシナリオでは、仮想マシン用に複数のデータ ディスクを作成します。 **dataDisks** は仮想マシンのプロパティであり、それ自体がリソース タイプではないので、データ ディスクで **copy** を使用することはできません。 代わりに、必要な数のデータ ディスクで配列を作成し、作成するデータ ディスクの実際の数を渡します。 仮想マシンの定義で、**take** 関数を使用し、配列から実際に必要な要素の数のみを取得します。

このパターンの完全な例は、 [Create a VM with a dynamic selection of data disks (データディスクの動的な選択による VM の作成)](https://azure.microsoft.com/documentation/templates/201-vm-dynamic-data-disks-selection/) テンプレートに示されています。

デプロイ テンプレートの該当するセクションを次の例に示します。 複数のデータ ディスクの動的な作成に関するセクションがよくわかるように、多くのテンプレートを削除してあります。 作成するディスクの数を渡すことができる **numDataDisks** パラメーターに注意してください。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numDataDisks": {
      "type": "int",
      "maxValue": 64,
      "metadata": {
        "description": "This parameter allows you to select the number of disks you want"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'dynamicdisk')]",
    "sizeOfDataDisksInGB": 100,
    "diskCaching": "ReadWrite",
    "diskArray": [
      {
        "name": "datadisk1",
        "lun": 0,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk1.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk2",
        "lun": 1,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk2.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk3",
        "lun": 2,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk3.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk4",
        "lun": 3,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk4.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      ...
      {
        "name": "datadisk63",
        "lun": 62,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk63.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      },
      {
        "name": "datadisk64",
        "lun": 63,
        "vhd": {
          "uri": "[concat('http://', variables('storageAccountName'),'.blob.core.windows.net/vhds/', 'datadisk64.vhd')]"
        },
        "createOption": "Empty",
        "caching": "[variables('diskCaching')]",
        "diskSizeGB": "[variables('sizeOfDataDisksInGB')]"
      }
    ]
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Compute/virtualMachines",
      "properties": {
        ...
        "storageProfile": {
          ...
          "dataDisks": "[take(variables('diskArray'),parameters('numDataDisks'))]"
        },
        ...
      }
      ...
    }
  ]
}
```

プロパティの変動的な数の項目とリソースの複数のインスタンスを作成する必要がある場合は、**take** 関数および **copy** 要素を一緒に使用することができます。 たとえば、仮想マシンごとにデータ ディスク数が異なる複数の仮想マシンを作成する必要があるとします。 関連する仮想マシンを識別する名前を各データ ディスクに付けるには、データ ディスクの配列を別のテンプレートに追加します。 仮想マシンの名前と、返されるデータ ディスクの数を表すパラメーターを組み込みます。 outputs セクションで、指定した項目の数を返します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "numDataDisks": {
      "type": "int",
      "maxValue": 16,
      "metadata": {
        "description": "This parameter allows the user to select the number of disks they want"
      }
    }
  },
  "variables": {
    "diskArray": [
      {
        "name": "[concat(parameters('vmName'), '-datadisk1')]",
        "vhd": {
          "uri": "[concat('http://', parameters('storageAccountName'),'.blob.core.windows.net/vhds/', parameters('vmName'), '-datadisk1.vhd')]"
        },
        ...
      },
      ...
    ],
  },
  "resources": [
  ],
  "outputs": {
    "result": {
      "type": "array",
      "value": "[take(variables('diskArray'),parameters('numDataDisks'))]"
    }
  }
}
``` 

親テンプレートに、仮想マシンの数と、各仮想マシンのデータ ディスクの数の配列を表すパラメーターを含めます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
    "numberOfInstances": {
      "type": "int",
      "defaultValue": 2,
      "metadata": {
        "description": "Number of VMs to deploy"
      }
    },
    "numberOfDataDisksPerVM": {
      "type": "array",
      "defaultValue": [1,2]
    }
  },
```

resources セクションで、データ ディスクを定義するテンプレートの複数のインスタンスをデプロイします。 

```json
{
  "apiVersion": "2016-09-01",
  "name": "[concat('nested-', copyIndex())]",
  "type": "Microsoft.Resources/deployments",
  "copy": {
    "name": "deploycopy",
    "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "mode": "incremental",
    "templateLink": {
      "uri": "{data-disk-template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "parameters": {
      "vmName": { "value": "[concat('myvm', copyIndex())]" },
      "storageAccountName": { "value": "[variables('storageAccountName')]" },
      "numDataDisks": { "value": "[parameters('numberOfDataDisksPerVM')[copyIndex()]]" }
    }
  }
},
```

resources セクションで、仮想マシンの複数のインスタンスをデプロイします。 データ ディスクについては、データ ディスクの正しい数とデータ ディスクの正しい名前を格納する入れ子になったデプロイを参照します。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[concat('myvm', copyIndex())]",
  "copy": {
    "name": "virtualMachineLoop",
      "count": "[parameters('numberOfInstances')]"
  },
  "properties": {
    "storageProfile": {
      ...
      "dataDisks": "[reference(concat('nested-', copyIndex())).outputs.result.value]"
    },
    ...
  },
  ...
}
```

## <a name="return-values-from-a-loop"></a>ループからの戻り値
リソースの種類の複数のインスタンスを作成すると便利ですが、ループから値を戻すことが難しくなる場合があります。 値を保持して戻す方法の&1; つに、**copy** と入れ子になったテンプレートを使用し、すべての戻り値を格納した配列のラウンド トリップを行う方法があります。 たとえば、複数のストレージ アカウントを作成し、それぞれのプライマリ エンドポイントを返す必要があるとします。 

最初に、ストレージ アカウントを作成する入れ子になったテンプレートを作成します。 このテンプレートが、BLOB URI の配列パラメーターを受け入れることに注意してください。 このパラメーターを使用して、それまでのデプロイのすべての値に対してラウンド トリップを行います。 テンプレートから出力されるのは、新しい BLOB URI をそれまでの URI に連結した配列です。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "indexValue": {
      "type":"int"
    },
    "blobURIs": {
        "type": "array",
      "defaultValue": []
    }
  },
    "variables": {
    "storageName": "[concat('storage', uniqueString(resourceGroup().id), parameters('indexValue'))]"
  },
    "resources": [
    {
        "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {  
      }
    }
    ],
    "outputs": {
      "result": {
        "type": "array",
      "value": "[concat(parameters('blobURIs'),split(reference(variables('storageName')).primaryEndpoints.blob, ','))]"
    }
  }
}
```

ここで、入れ子になったテンプレートの静的インスタンスを&1; つ持ち、入れ子になったテンプレートの残りのインスタンスに対してループする親テンプレートを作成します。 ループされるデプロイのインスタンスごとに、直前のデプロイの出力である配列を渡します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberofStorage": { "type": "int", "minValue": 2 }
  },
  "resources": [
    {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate0",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": 0}
        }
      }
    },
    {
      "apiVersion": "2016-09-01",
      "name": "[concat('nestedTemplate', copyIndex(1))]",
      "type": "Microsoft.Resources/deployments",
      "copy": {
        "name": "storagecopy",
        "count": "[sub(parameters('numberofStorage'), 1)]"
      },
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "{storage-template-uri}",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "indexValue": {"value": "[copyIndex(1)]"},
          "blobURIs": {"value": "[reference(concat('nestedTemplate', copyIndex())).outputs.result.value]"}
        }
      }
    }
  ],
  "outputs": {
    "result": {
      "type": "object",
      "value": "[reference(concat('nestedTemplate', sub(parameters('numberofStorage'), 1))).outputs.result]"
    }
  }
}
```

## <a name="next-steps"></a>次のステップ
* テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
* テンプレートで使用できるすべての関数については、「[Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* テンプレートをデプロイする方法については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。


