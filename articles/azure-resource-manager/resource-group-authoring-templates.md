---
title: "Azure Resource Manger テンプレートの構造と構文 | Microsoft Docs"
description: "宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: e5e793eeab46b31c728e7dbb493c6396d6daad08
ms.lasthandoff: 03/24/2017


---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構造と構文の詳細
このトピックでは、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 テンプレートの作成方法を詳しく解説したチュートリアルについては、「[初めての Azure Resource Manager テンプレートを作成する](resource-manager-create-first-template.md)」を参照してください。

テンプレートのサイズを 1 MB に、各パラメーター ファイルのサイズを 64 KB に制限します。 1 MB の制限は、反復的なリソースの定義と変数およびパラメーターの値で拡張された後のテンプレートの最終的な状態に適用されます。 

## <a name="template-format"></a>テンプレートの形式
最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "resources": [  ],
    "outputs": {  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| $schema |はい |テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所。 前の例に示されている URL を使用します。 |
| contentVersion |はい |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| parameters |いいえ |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| variables |いいえ |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| resources |はい |リソース グループ内でデプロイまたは更新されるリソースの種類。 |
| outputs |いいえ |デプロイ後に返される値。 |

各要素には、設定できるプロパティが含まれています。 次の例には、テンプレートの完全な構文が含まれています。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
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
    },
    "variables": {  
        "<variable-name>": "<variable-value>",
        "<variable-name>": { 
            <variable-complex-type-value> 
        }
    },
    "resources": [
      {
          "apiVersion": "<api-version-of-resource>",
          "type": "<resource-provider-namespace/resource-type-name>",
          "name": "<name-of-the-resource>",
          "location": "<location-of-resource>",
          "tags": "<name-value-pairs-for-resource-tagging>",
          "comments": "<your-reference-notes>",
          "dependsOn": [
              "<array-of-related-resource-names>"
          ],
          "properties": "<settings-for-the-resource>",
          "copy": {
              "name": "<name-of-copy-loop>",
              "count": "<number-of-iterations>"
          },
          "resources": [
              "<array-of-child-resources>"
          ]
      }
    ],
    "outputs": {
        "<outputName>" : {
            "type" : "<type-of-output-value>",
            "value": "<output-value-expression>"
        }
    }
}
```

テンプレートのセクションについては、後で詳しく説明します。

## <a name="expressions-and-functions"></a>式と関数

テンプレートの基本的な構文は JSON です。 ただし、式や関数により、テンプレートで使用できる JSON 値が拡張されます。  式は、最初と最後の文字が角かっこ (`[` および `]`) の JSON 文字列リテラル内に記述されます。 式の値は、テンプレートのデプロイ時に評価されます。 文字列リテラルとして記述される一方で、式の評価の結果は、実際の式に応じて、配列、整数など、さまざまな JSON 型にすることができます。  角かっこ `[` で始まるリテラル文字列を使用するが、式として解釈されないようにするには、文字列が `[[` で始まるように追加の角かっこを追加します。

通常、関数と式を使用してデプロイを構成する操作を実行します。 JavaScript の場合と同様に、関数呼び出しは `functionName(arg1,arg2,arg3)` という形式になります。 プロパティの参照には、ドットと [index] 演算子を使用します。

次の例では、値を構築する際にいくつかの関数を使用する方法を示します。

```json
"variables": {
    "location": "[resourceGroup().location]",
    "usernameAndPassword": "[concat(parameters('username'), ':', parameters('password'))]",
    "authorizationHeader": "[concat('Basic ', base64(variables('usernameAndPassword')))]"
}
```

テンプレート関数の完全一覧が必要な場合、「 [Azure リソース マネージャーのテンプレートの関数](resource-group-template-functions.md)」を参照してください。 

## <a name="parameters"></a>parameters
テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 特定の環境 (開発、テスト、運用など) に合った値をパラメーターに渡すことで、デプロイをカスタマイズすることができます。 テンプレートでは必ずしもパラメーターを使用する必要はありませんが、パラメーターを使わなかった場合、常に同じリソースが同じ名前、同じ場所、同じプロパティでデプロイされます。

次の構造でパラメーターを定義します。

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

| 要素名 | 必須 | Description |
|:--- |:--- |:--- |
| parameterName |はい |パラメーターの名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |パラメーター値の型。 この表の後に示す使用できる型を参照してください。 |
| defaultValue |いいえ |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ |int 型パラメーターの最小値。 |
| maxValue |いいえ |int 型パラメーターの最大値。 |
| minLength |いいえ |文字列型、secureString 型、配列型パラメーターの長さの最小値。 |
| maxLength |いいえ |文字列型、secureString 型、配列型パラメーターの長さの最大値。 |
| description |いいえ |ポータルを通じてユーザーに表示されるパラメーターの説明。 |

使用できる型と値は次のとおりです。

* **string**
* **secureString**
* **int**
* **bool**
* **object** 
* **secureObject**
* **array**

パラメーターを省略可能に指定するには、defaultValue を設定します (空の文字列を指定できます)。 

コマンドのパラメーターと同じ名前のパラメーターをテンプレートで指定して、そのテンプレートをデプロイすると、指定する値があいまいになる可能性があります。 Resource Manager では、接尾辞 **FromTemplate** をテンプレート パラメーターに追加することで、このような混乱を防ぎます。 たとえば、**ResourceGroupName** という名前のパラメーターをテンプレートに追加した場合、このパラメーターは、[New-AzureRmResourceGroupDeployment][deployment2cmdlet] コマンドレットの **ResourceGroupName** パラメーターと競合するため、 デプロイ中、**ResourceGroupNameFromTemplate** に値を指定するように求められます。 一般的に、このような混乱を防ぐために、デプロイ処理に使用したパラメーターと同じ名前をパラメーターに付けないことが推奨されます。

> [!NOTE]
> すべてのパスワード、キー、およびその他のシークレットでは、 **secureString** 型を使用する必要があります。 JSON オブジェクトに機密データを渡す場合は、**secureObject** 型を使用します。 secureString 型または secureObject 型を含むテンプレート パラメーターをリソースのデプロイ後に読み取ることはできません。 
> 
> たとえば、デプロイ履歴の次のエントリには文字列とオブジェクトの値が表示されますが、secureString と secureObject の値は表示されません。
>
> ![デプロイの値の表示](./media/resource-group-authoring-templates/show-parameters.png)  
>

次の例では、パラメーターを定義する方法を示します。

```json
"parameters": {
    "siteName": {
        "type": "string",
        "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
    },
    "hostingPlanName": {
        "type": "string",
        "defaultValue": "[concat(parameters('siteName'),'-plan')]"
    },
    "skuName": {
        "type": "string",
        "defaultValue": "F1",
        "allowedValues": [
          "F1",
          "D1",
          "B1",
          "B2",
          "B3",
          "S1",
          "S2",
          "S3",
          "P1",
          "P2",
          "P3",
          "P4"
        ]
    },
    "skuCapacity": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1
    }
}
```

デプロイ時にパラメーター値を入力する方法については、 [Azure Resource Manager テンプレートを使用したリソースのデプロイ](resource-group-template-deploy.md)に関するページをご覧ください。 

## <a name="variables"></a>variables
テンプレート内で使用できる値は、variables セクションで構築します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

変数は、次の構造で定義します。

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    }
}
```

次の例では、2 つのパラメーター値で構成される変数の定義方法を示しています。

```json
"variables": {
    "connectionString": "[concat('Name=', parameters('username'), ';Password=', parameters('password'))]"
}
```

次の例では、複雑な JSON 型である変数と、その他の変数で構成される変数を示します。

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
"variables": {
    "environmentSettings": {
        "test": {
            "instancesSize": "Small",
            "instancesCount": 1
        },
        "prod": {
            "instancesSize": "Large",
            "instancesCount": 4
        }
    },
    "currentEnvironmentSettings": "[variables('environmentSettings')[parameters('environmentName')]]",
    "instancesSize": "[variables('currentEnvironmentSettings').instancesSize]",
    "instancesCount": "[variables('currentEnvironmentSettings').instancesCount]"
}
```

## <a name="resources"></a>リソース
resources セクションでは、デプロイまたは更新されるリソースを定義します。 このセクションは、複雑になりやすい部分です。適切な値を指定するためには、デプロイするリソースの種類を理解している必要があるためです。 設定が必要なリソース固有の値 (apiVersion、種類、プロパティ) については、「[Define resources in Azure Resource Manager templates (Azure Resource Manager テンプレートのリソースを定義する)](/azure/templates/)」を参照してください。 

リソースは、次の構造で定義します。

```json
"resources": [
  {
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": "<name-value-pairs-for-resource-tagging>",
      "comments": "<your-reference-notes>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": "<settings-for-the-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": "<number-of-iterations>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間と、リソースの種類の組み合わせです (例: **Microsoft.Storage/storageAccounts**)。 |
| name |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 また、リソース名を外部に公開する Azure サービスは、名前が別の ID になりすますことがないように、その名前を検証します。 |
| location |多様 |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 「[Azure Resource Manager テンプレートでリソースの場所を設定する](resource-manager-template-location.md)」を参照してください。 |
| tags |いいえ |リソースに関連付けられたタグ。 ｢[Azure Resource Manager テンプレートのリソースにタグを付ける](resource-manager-template-tags.md)」を参照してください。 |
| コメント |いいえ |テンプレート内にドキュメント化するリソースについてのメモ。 |
| dependsOn |なし |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 詳細については、[Azure Resource Manager テンプレートの依存関係の定義](resource-group-define-dependencies.md)に関するページをご覧ください。 |
| properties |いいえ |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 |
| copy |いいえ |複数のインスタンスが必要な場合に作成するリソースの数。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 |
| resources |いいえ |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 子リソースの完全修飾型には親リソースの種類が含まれます (例: **Microsoft.Web/sites/extensions**)。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 |

resources セクションには、デプロイの対象となる一連のリソースが記述されます。 また、リソースごとに子リソースを複数定義することができます。 その場合、resources セクションは次のような構造となります。

```json
"resources": [
  {
      "name": "resourceA",
  },
  {
      "name": "resourceB",
      "resources": [
        {
            "name": "firstChildResourceB",
        },
        {   
            "name": "secondChildResourceB",
        }
      ]
  },
  {
      "name": "resourceC",
  }
]
```      

子リソースの定義の詳細については、「[Resource Manager テンプレートの子リソースに関する名前と種類の設定](resource-manager-template-child-resource.md)」を参照してください。

## <a name="outputs"></a>出力

[出力] セクションではデプロイから返される値を指定します。 たとえば、デプロイされたリソースにアクセスするための URI を返すことができます。

次の例では、出力の定義の構造を示します。

```json
"outputs": {
    "<outputName>" : {
        "type" : "<type-of-output-value>",
        "value": "<output-value-expression>"
    }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| outputName |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 |
| 値 |はい |評価され、出力値として返されるテンプレート言語式。 |

次の例では、outputs セクションで返される値を示します。

```json
"outputs": {
    "siteUri" : {
        "type" : "string",
        "value": "[concat('http://',reference(resourceId('Microsoft.Web/sites', parameters('siteName'))).hostNames[0])]"
    }
}
```

出力を操作する方法の詳細については、「 [Azure Resource Manager のテンプレートでの状態の共有](best-practices-resource-manager-state.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「 [Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* 別のリソース グループ内に存在するリソースの使用が必要になる場合があります。 このシナリオは、複数のリソース グループ間で共有されているストレージ アカウントまたは仮想ネットワークを使用している場合は一般的です。 詳細については、 [resourceId 関数](resource-group-template-functions.md#resourceid)に関するセクションをご覧ください。

[deployment2cmdlet]: https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.2.0/new-azurermresourcegroupdeployment

