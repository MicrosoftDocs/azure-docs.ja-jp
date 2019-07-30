---
title: Azure Resource Manager テンプレートの構造と構文 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: ab8e4f5f6506f80b62c112298f73f95bc7fedeaf
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204360"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構造と構文の詳細

この記事では、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。

この記事は、Resource Manager テンプレートにある程度慣れているユーザー向けです。 テンプレートの構造と構文に関する詳細情報を提供します。 テンプレートの作成の概要を知りたい場合は、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」をご覧ください。

## <a name="template-format"></a>テンプレートの形式

最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| $schema |はい |テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。<br><br> リソース グループ デプロイの場合、次を使用します。`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>サブスクリプション デプロイの場合、次を使用します。`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#` |
| contentVersion |はい |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 この値を使用し、テンプレートの大きな変更を記述します。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| apiProfile |いいえ | リソースの種類に対する API のバージョンのコレクションとして機能する API バージョン。 この値を使用すると、テンプレートのリソースごとに API バージョンを指定しなくて済みます。 API プロファイルのバージョンを指定し、リソースの種類の API バージョンを指定しないと、Resource Manager では、プロファイルでそのリソースの種類に対して定義されている API バージョンが使用されます。<br><br>API プロファイル プロパティは、Azure Stack やグローバルな Azure など、さまざまな環境にテンプレートをデプロイする場合に特に便利です。 API プロファイルのバージョンを使用し、両方の環境でサポートされているバージョンがテンプレートで自動的に使用されるようにします。 現在の API プロファイルのバージョンおよびリソース プロファイルで定義されている API バージョンの一覧については、「[API Profile (API プロファイル)](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)」をご覧ください。<br><br>詳しくは、「[API プロファイルを使用してバージョンを追跡する](templates-cloud-consistency.md#track-versions-using-api-profiles)」をご覧ください。 |
| [parameters](#parameters) |いいえ |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| [variables](#variables) |いいえ |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| [functions](#functions) |いいえ |テンプレート内で使用できるユーザー定義関数。 |
| [resources](#resources) |はい |リソース グループまたはサブスクリプション内でデプロイまたは更新されるリソースの種類。 |
| [outputs](#outputs) |いいえ |デプロイ後に返される値。 |

各要素には、設定できるプロパティがあります。 この記事では、テンプレートのセクションについて詳しく説明します。

## <a name="syntax"></a>構文

テンプレートの基本的な構文は JSON です。 ただし、式を使用して、テンプレート内で使用できる JSON の値を拡張できます。  式の始まりと終わりは、それぞれ角かっこ `[` と `]` です。 式の値は、テンプレートのデプロイ時に評価されます。 式では、文字列、整数、ブール値、配列、またはオブジェクトを返すことができます。 次の例では、パラメーターの既定値での式を示します。

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

式の構文 `resourceGroup()` では、Resource Manager によってテンプレート内で使用するために提供されている関数の 1 つを呼び出します。 JavaScript の場合と同様に、関数呼び出しは `functionName(arg1,arg2,arg3)` という形式になります。 構文 `.location` では、その関数によって返されたオブジェクトから 1 つのプロパティを取得します。

テンプレート関数とそのパラメーターでは大文字と小文字が区別されません。 たとえば、Resource Manager では、**variables('var1')** と **VARIABLES('VAR1')** が同じものとして解決されます。 評価の際、関数は、大文字/小文字を明確に変更する (toUpper、toLower など) 場合を除き、大文字/小文字を保持します。 特定のリソースの種類では、関数の評価方法とは無関係に、大文字/小文字の要件が存在する場合があります。

左かっこ `[` で始まり右かっこ `]`で終わるリテラル文字列を使用するが、それが式として解釈されないようにするには、余分にかっこを追加して文字列が `[[` から始まるようにします。 たとえば、変数は次のようになります。

```json
"demoVar1": "[[test value]"
```

`[test value]` に解決されます。

しかし、リテラル文字列がかっこで終わらないのであれば、最初のかっこはエスケープしないでください。 たとえば、変数は次のようになります。

```json
"demoVar2": "[test] value"
```

`[test] value` に解決されます。

文字列値をパラメーターとして関数に渡すには、単一引用符を使用します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

テンプレートでの JSON オブジェクトの追加など、式の中で二重引用符をエスケープするには、円記号を使用します。

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

テンプレート式は、24,576 文字を超えることはできません。

テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。 

## <a name="parameters"></a>parameters

テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 特定の環境 (開発、テスト、運用など) に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 テンプレートでは必ずしもパラメーターを使用する必要はありませんが、パラメーターを使わなかった場合、常に同じリソースが同じ名前、同じ場所、同じプロパティでデプロイされます。

テンプレートではパラメーターが 256 個に制限されます。 この記事で説明するように、複数のプロパティを含むオブジェクトを使用すると、パラメーター数を減らすことができます。

### <a name="available-properties"></a>利用可能なプロパティ

パラメーターで使用可能なプロパティは次のとおりです。

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| parameterName |はい |パラメーターの名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 |
| defaultValue |いいえ |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ |int 型パラメーターの最小値。 |
| maxValue |いいえ |int 型パラメーターの最大値。 |
| minLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最小値 (この値を含む)。 |
| maxLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最大値 (この値を含む)。 |
| description |いいえ |ポータルを通じてユーザーに表示されるパラメーターの説明。 詳しくは、[テンプレート内のコメント](#comments)に関するページをご覧ください。 |

### <a name="define-and-use-a-parameter"></a>パラメーターの定義と使用

単純なパラメーター定義の例を次に示します。 ここでは、パラメーターの名前が定義され、パラメーターが文字列値を取ることが指定されています。 パラメーターは、使用目的に合う値のみを受け入れます。 デプロイ時に値が指定されなかった場合の既定値が指定されています。 さらに、このパラメーターには使用方法の説明も含まれています。

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

テンプレートでは、次の構文でパラメーターの値を参照できます。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>テンプレート関数とパラメーター

パラメーターの既定値を指定する場合は、ほとんどのテンプレート関数を使用できます。 別のパラメーター値を使用して既定値を作成できます。 次のテンプレートは、既定値に関する関数の使用方法を示します。

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

parameters セクションで `reference` 関数を使用することはできません。 パラメーターはデプロイ前に評価されるため、`reference` 関数はリソースのランタイム状態を取得できません。 

### <a name="objects-as-parameters"></a>パラメーターとしてのオブジェクト

関連する値は 1 つのオブジェクトとして渡すことにより整理しやすくなります。 この方法により、テンプレート内のパラメータ数も減少します。

テンプレートでパラメーターを定義し、デプロイ時に 1 つの値ではなく、JSON オブジェクトを指定します。 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

その後、ドット演算子を使用して、パラメーターのサブプロパティを参照します。

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>パラメーターの例が示されているテンプレート

次のサンプル テンプレートでは、パラメーターを使用するいくつかのシナリオを例示します。 さまざまなシナリオでパラメーターがどのように処理されるかを、このサンプルをデプロイして試してください。

|Template  |説明  |
|---------|---------|
|[既定値のための関数を含むパラメーター](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | パラメーターの既定値を定義する際のテンプレート関数の使用方法を説明します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |
|[パラメーター オブジェクト](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | パラメーターのオブジェクトの使用方法を示します。 このテンプレートではリソースをデプロイしません。 パラメーターの値を作成して、その値を返します。 |

## <a name="variables"></a>変数

テンプレート内で使用できる値は、variables セクションで構築します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

### <a name="available-definitions"></a>使用可能な定義

次の例では、変数の定義に使用できるオプションを示します。

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

`copy` を使用して変数に複数の値を作成する方法については、「[変数の反復処理](resource-group-create-multiple.md#variable-iteration)」をご覧ください。

### <a name="define-and-use-a-variable"></a>変数の定義と使用

変数を定義する例を以下に示します。 これにより、ストレージ アカウント名の文字列値が作成されます。 テンプレート関数をいくつか使用してパラメーター値を取得し、連結して一意の文字列にします。

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

リソースを定義するときに、この変数を使用します。

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>構成変数

複雑な JSON 型を使用して、環境に関連する値を定義できます。

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

現在の設定を取得するには次のようにします。

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>変数の例のテンプレート

次のサンプル テンプレートでは、変数を使用するいくつかのシナリオを例示します。 さまざまなシナリオで変数がどのように処理されるかを、このサンプルを展開して試してください。 

|Template  |説明  |
|---------|---------|
| [変数の定義](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | さまざまな種類の変数を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [構成変数](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | 構成の値を定義する変数の用法を例示します。 このテンプレートではリソースをデプロイしません。 変数の値を作成して、その値を返します。 |
| [ネットワーク セキュリティの規則](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json)と[パラメーター ファイル](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | ネットワーク セキュリティ グループにセキュリティの規則を割り当てるために、配列を正しい形式で作成します。 |


## <a name="functions"></a>Functions

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 通常は、テンプレート内で繰り返したくない複雑な式を定義します。 ユーザー定義関数は、テンプレートでサポートされている[関数](resource-group-template-functions.md)および式から作成します。

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で[パラメーター関数](resource-group-template-functions-deployment.md#parameters)を使用する場合、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数は [reference 関数](resource-group-template-functions-resource.md#reference)を使用できません。
* 関数のパラメーターでは既定値を指定できません。

テンプレート関数との名前の競合を回避するために、お使いの関数には名前空間の値が必要です。 次の例は、ストレージ アカウント名を返す関数を示しています。

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

関数を呼び出すには、次を使用します。

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>リソース
resources セクションでは、デプロイまたは更新されるリソースを定義します。

### <a name="available-properties"></a>利用可能なプロパティ

リソースは、次の構造で定義します。

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| condition | いいえ | このデプロイの間にリソースがプロビジョニングされるかどうかを示すブール値。 `true` の場合、デプロイ時にリソースが作成されます。 `false` の場合、このデプロイでは、リソースはスキップされます。 [条件](#condition)をご覧ください。 |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間と、リソースの種類の組み合わせです (例: **Microsoft.Storage/storageAccounts**)。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 子リソースの場合、type の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 「[子リソース](#child-resources)」を参照してください。 |
| 名前 |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 また、リソース名を外部に公開する Azure サービスでは、名前が別の ID になりすますことがないように、その名前を検証します。 子リソースの場合、name の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 「[子リソース](#child-resources)」を参照してください。 |
| location |多様 |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 |
| tags |いいえ |リソースに関連付けられたタグ。 サブスクリプション間でリソースを論理的に編成するためのタグを適用します。 |
| コメント |いいえ |テンプレート内にドキュメント化するリソースについてのメモ。 詳しくは、[テンプレート内のコメント](resource-group-authoring-templates.md#comments)に関するページをご覧ください。 |
| copy |いいえ |複数のインスタンスが必要な場合に作成するリソースの数。 既定のモードはパラレルです。 すべてのリソースを同時にデプロイする必要がない場合は、シリアル モードを指定します。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 |
| dependsOn |いいえ |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 詳細については、[Azure Resource Manager テンプレートの依存関係の定義](resource-group-define-dependencies.md)に関するページをご覧ください。 |
| properties |いいえ |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 コピー配列を指定して、1 つのプロパティの複数のインスタンスを作成することもできます。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| sku | いいえ | 一部のリソースでは、デプロイする SKU を定義する値が許可されます。 たとえば、ストレージ アカウントの冗長性の種類を指定することができます。 |
| kind | いいえ | 一部のリソースでは、デプロイするリソースの種類を定義する値が許可されます。 たとえば、作成する Cosmos DB の種類を指定することができます。 |
| プラン | いいえ | 一部のリソースでは、デプロイするプランを定義する値が許可されます。 たとえば、仮想マシンのマーケットプレース イメージを指定することができます。 | 
| resources |いいえ |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 「[子リソース](#child-resources)」を参照してください。 |

### <a name="condition"></a>条件

デプロイ時にリソースを作成するどうかを決定する必要がある場合は、`condition` 要素を使用します。 この要素の値は、true または false に解決されます。 値が true の場合、リソースが作成されます。 値が false の場合、リソースは作成されません。 この要素の値は、リソース全体にのみ適用できます。

通常、新しいリソースを作成するか、既存のリソースを使用する場合は、この値を使用します。 たとえば、新しいストレージ アカウントをデプロイするか、既存のストレージ アカウントを使用するかを指定するには、次のようにします。

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

`condition` 要素を使用する完全なテンプレート例については、[新規または既存の仮想ネットワーク、ストレージ、およびパブリック IP を使用する VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) に関するページを参照してください。

条件付きでデプロイされるリソースで [reference](resource-group-template-functions-resource.md#reference) または [list](resource-group-template-functions-resource.md#list) 関数を使用した場合、この関数はリソースがデプロイされていなくても評価されます。 この関数が存在しないリソースを参照する場合、エラーが返されます。 リソースがデプロイされるときにのみ条件に対してこの関数が評価されるようにするには、[if](resource-group-template-functions-logical.md#if) 関数を使用します。 条件付きでデプロイされるリソースで if と reference を使用するサンプル テンプレートについては、[if 関数](resource-group-template-functions-logical.md#if)に関する説明を参照してください。

### <a name="resource-names"></a>リソース名

Resource Manager では、一般に、次の 3 種類のリソース名を使用します。

* 一意である必要があるリソース名
* 一意である必要がないリソース名。ただし、リソースを識別するのに役立つ名前を指定するようにします。
* 一般的なものでよいリソース名

データ アクセス エンドポイントを持つリソースの種類に対しては、**一意のリソース名**を指定します。 一意の名前にする必要がある一般的なリソースの種類には次のようなものがあります。

* Azure Storage<sup>1</sup> 
* Azure App Service の Web Apps の機能
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure の Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup> ストレージ アカウント名も、小文字で 24 文字以下にする必要があり、ハイフンを含めることはできません。

名前を設定する際には、一意の名前を手動で作成するか、[uniqueString()](resource-group-template-functions-string.md#uniquestring) 関数を使用して名前を生成します。 **uniqueString** の結果に、プレフィックスまたはサフィックスを追加することもできます。 一意の名前を変更すると、リソースの種類を名前から簡単に識別するのに役立ちます。 たとえば、次の変数を使用してストレージ アカウントの一意の名前を生成できます。

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

リソースの種類によっては、**識別名**を指定したいことがありますが、その名前は一意である必要はありません。 このようなリソースの種類では、その用途と特性がわかる名前を指定します。

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

別のリソースからアクセスされることの多いリソースの種類に対しては、テンプレートでハードコーディングされた**一般的な名前**を使用できます。 たとえば、SQL Server のファイアウォール規則には、標準的で一般的な名前を設定できます。

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>リソースの場所

テンプレートをデプロイするときに、各リソースの場所を指定する必要があります。 場所ごとに、異なるリソースの種類がサポートされます。 リソースの種類に対してサポートされている場所を取得するには、「[Azure リソース プロバイダーとリソースの種類](resource-manager-supported-services.md)」をご覧ください。

パラメーターを使用して、リソースの場所を指定し、既定値を `resourceGroup().location` に設定します。

次の例は、パラメーターとして指定された場所にデプロイされたストレージ アカウントを示したものです。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
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

### <a name="child-resources"></a>子リソース

一部のリソースの種類では、一連の子リソースを定義することができます。 子リソースとは、別のリソースのコンテキスト内でのみ存在するリソースのことです。 たとえば、SQL データベースは SQL サーバーなしでは存在できないので、データベースはサーバーの子であることになります。 データベースはサーバーの定義内に定義することができます。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

ただし、データベースをサーバー内で定義する必要はありません。 最上位レベルで子リソースを定義できます。 親リソースが同じテンプレート内にデプロイされていない場合、または複数の子リソースを作成するために `copy` を使う場合は、このアプローチを使用することがあります。 このアプローチの場合、完全なリソースの種類を指定する必要があり、子リソースの名前に親のリソースの名前を含める必要があります。

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

type と name に指定する値は、子リソースが親リソース内で定義されているか、親リソースの外側で定義されているかによって変わります。

親リソースに入れ子にされている場合は、次のように使用します。

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

親リソースの外側で定義されている場合は、次のように使用します。

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

入れ子にされている場合、type は `databases` に設定されますが、全体的なリソースの type は `Microsoft.Sql/servers/databases` のままです。 親リソースの種類から想定されるため、`Microsoft.Sql/servers/` は不要です。 子リソースの名前は `exampledatabase` が設定されていますが、完全な名前には親の名前が含まれています。 親リソースから想定されるため、`exampleserver` は不要です。

リソースに対する完全修飾参照を作成する場合、種類と名前からセグメントを結合する順序は、単に 2 つの連結ではありません。 名前空間の後に、"*種類/名前*" のペアを具体性の低いものから高いものへの順に使用します。

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

例:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`は正しい`Microsoft.Compute/virtualMachines/extensions/myVM/myExt`は正しくない

## <a name="outputs"></a>出力

[出力] セクションではデプロイから返される値を指定します。 通常は、デプロイされたリソースからの値を返します。

### <a name="available-properties"></a>利用可能なプロパティ

次の例では、出力の定義の構造を示します。

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| outputName |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| condition |いいえ | この出力値が返されたかどうかを示すブール値。 `true` の場合、値はデプロイの出力に含まれています。 `false` の場合、このデプロイでは、出力値はスキップされます。 指定しない場合、既定値は `true` です。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 出力の種類に **securestring** を指定した場合、値はデプロイ履歴に表示されず、他のテンプレートから取得できません。 複数のテンプレートでシークレット値を使用するには、シークレットをキー コンテナーに格納し、パラメーター ファイルでそのシークレットを参照します。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](resource-manager-keyvault-parameter.md)」を参照してください |
| value |はい |評価され、出力値として返されるテンプレート言語式。 |

### <a name="define-and-use-output-values"></a>出力値の定義および使用

次の例は、パブリック IP アドレスのリソース ID を返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

条件付き出力の簡単な例については、[条件付き出力テンプレート](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)に関する説明をご覧ください。

デプロイ後、スクリプトを使用して値を取得できます。 PowerShell では、次を使用します。

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Azure CLI では、次を使用します。

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

[reference](resource-group-template-functions-resource.md#reference) 関数を使用して、リンクされているテンプレートから出力値を取得できます。 リンクされたテンプレートから出力値を取得するには、`"[reference('deploymentName').outputs.propertyName.value]"` のような構文でプロパティ値を取得します。

リンクされたテンプレートから出力プロパティを取得する場合、プロパティ名にダッシュを含めることはできません。

次の例では、リンクされているテンプレートから値を取得することによってロード バランサーの IP アドレスを設定する方法を示します。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

[入れ子になったテンプレート](resource-group-linked-templates.md#link-or-nest-a-template) の出力セクションでは `reference` 関数を使用できません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。

### <a name="output-example-templates"></a>出力の例のテンプレート

|Template  |説明  |
|---------|---------|
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 複合変数を作成し、それらの値を出力します。 リソースはデプロイしません。 |
|[パブリック IP アドレス](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | パブリック IP アドレスを作成し、リソース ID を出力します。 |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 前述のテンプレートにリンクします。 ロード バランサーの作成時に出力内のリソース ID を使用します。 |


<a id="comments" />

## <a name="comments-and-metadata"></a>コメントとメタデータ

テンプレートにコメントとメタデータを追加するためのオプションがいくつかあります。

`metadata` オブジェクトは、テンプレート内のほとんどどこにでも追加できます。 このオブジェクトは、Resource Manager では無視されますが、お使いの JSON エディターによっては、プロパティが無効であると警告される場合があります。 オブジェクトで、必要なプロパティを定義します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

**パラメーター**の場合、`description` プロパティを使って `metadata` オブジェクトを追加します。

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

ポータルからテンプレートをデプロイする場合、説明で指定したテキストがそのパラメーターのヒントとして自動的に使用されます。

![パラメーター ヒントの表示](./media/resource-group-authoring-templates/show-parameter-tip.png)

**リソース**の場合、`comments` 要素またはメタデータ オブジェクトを追加します。 次の例は、コメント要素とメタデータ オブジェクトの両方を示しています。

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

**出力**の場合、メタデータ オブジェクトを出力値に追加します。

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

メタデータ オブジェクトをユーザー定義関数に追加することはできません。

インライン コメントの場合、`//` を使用できますが、この構文はすべてのツールで機能しません。 Azure CLI を使用してインライン コメントを使用したテンプレートをデプロイすることはできません。 また、インライン コメントを使用したテンプレートでの作業に、ポータルのテンプレート エディターは使用できません。 このスタイルのコメントを追加する場合は、ご使用のツールでインライン JSON コメントがサポートされていることを確認してください。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

VS Code では、コメントを使用した JSON に言語モードを設定できます。 インライン コメントは無効としてマークされなくなります。 モードを変更するには:

1. 言語モードの選択を開きます (Ctrl + K M)

1. **[JSON with Comments]\(コメントを使用した JSON\)** を選択します。

   ![言語モードの選択](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>次の手順
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* テンプレート作成に関する推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* すべての Azure 環境および Azure Stack で使用できる Resource Manager テンプレートの作成に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」をご覧ください。
