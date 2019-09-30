---
title: Azure Resource Manager テンプレートの構造と構文 | Microsoft Docs
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレートの構造とプロパティについて説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: tomfitz
ms.openlocfilehash: 4a5c1a99911c31f539d4f55adefb2c5f06243dd0
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984095"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートの構造と構文の詳細

この記事では、Azure Resource Manager テンプレートの構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。

この記事は、Resource Manager テンプレートにある程度慣れているユーザー向けです。 テンプレートの構造に関する詳細情報を提供します。 テンプレートの作成の概要を知りたい場合は、「[Azure Resource Manager テンプレート](template-deployment-overview.md)」をご覧ください。

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

## <a name="parameters"></a>parameters

テンプレートの parameters セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 テンプレートではパラメーターが 256 個に制限されます。 複数のプロパティを含むオブジェクトを使用すると、パラメーター数を減らすことができます。

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
| parameter-name |はい |パラメーターの名前。 有効な JavaScript 識別子で指定する必要があります。 |
| type |はい |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 |
| defaultValue |いいえ |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ |int 型パラメーターの最小値。 |
| maxValue |いいえ |int 型パラメーターの最大値。 |
| minLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最小値 (この値を含む)。 |
| maxLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最大値 (この値を含む)。 |
| description |いいえ |ポータルを通じてユーザーに表示されるパラメーターの説明。 詳しくは、[テンプレート内のコメント](#comments)に関するページをご覧ください。 |

パラメーターの使用方法の例については、「[Azure Resource Manager テンプレートのパラメーター](template-parameters.md)」を参照してください。

## <a name="variables"></a>変数

テンプレート内で使用できる値は、variables セクションで構築します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。

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

変数の使用方法の例については、「[Azure Resource Manager テンプレートの変数](template-variables.md)」を参照してください。

## <a name="functions"></a>Functions

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 通常は、テンプレート内で繰り返したくない複雑な式を定義します。 ユーザー定義関数は、テンプレートでサポートされている[関数](resource-group-template-functions.md)および式から作成します。

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で[パラメーター関数](resource-group-template-functions-deployment.md#parameters)を使用する場合、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数は [reference 関数](resource-group-template-functions-resource.md#reference)を使用できません。
* 関数のパラメーターでは既定値を指定できません。

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| namespace |はい |カスタム関数の名前空間。 テンプレート関数との名前の競合を回避するために使用します。 |
| function-name |はい |カスタム関数の名前。 関数を呼び出すときに、関数名と名前空間を組み合わせます。 たとえば、uniqueName という名前の関数を名前空間 contoso で呼び出すには、`"[contoso.uniqueName()]"` を使用します。 |
| parameter-name |いいえ |カスタム関数内で使用されるパラメーターの名前。 |
| parameter-value |いいえ |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 |
| output-type |はい |出力値の型。 出力値では、関数入力パラメーターと同じ型がサポートされています。 |
| output-value |はい |評価され、関数から返されるテンプレート言語式。 |

カスタム関数の使用方法の例については、「[Azure Resource Manager テンプレートのユーザー定義関数](template-user-defined-functions.md)」を参照してください。

## <a name="resources"></a>リソース

resources セクションでは、デプロイまたは更新されるリソースを定義します。

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
| condition | いいえ | このデプロイの間にリソースがプロビジョニングされるかどうかを示すブール値。 `true` の場合、デプロイ時にリソースが作成されます。 `false` の場合、このデプロイでは、リソースはスキップされます。 [条件](conditional-resource-deployment.md)をご覧ください。 |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間と、リソースの種類の組み合わせです (例: **Microsoft.Storage/storageAccounts**)。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 子リソースの場合、type の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |
| 名前 |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 リソース名を外部に公開する Azure サービスでは、名前が別の ID になりすますことがないように、その名前を検証します。 子リソースの場合、name の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |
| location |多様 |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 [リソースの場所の設定](resource-location.md)に関するページを参照してください。 |
| tags |いいえ |リソースに関連付けられたタグ。 サブスクリプション間でリソースを論理的に編成するためのタグを適用します。 |
| コメント |いいえ |テンプレート内にドキュメント化するリソースについてのメモ。 詳しくは、[テンプレート内のコメント](resource-group-authoring-templates.md#comments)に関するページをご覧ください。 |
| copy |いいえ |複数のインスタンスが必要な場合に作成するリソースの数。 既定のモードはパラレルです。 すべてのリソースを同時にデプロイする必要がない場合は、シリアル モードを指定します。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。 |
| dependsOn |いいえ |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 詳細については、[Azure Resource Manager テンプレートの依存関係の定義](resource-group-define-dependencies.md)に関するページをご覧ください。 |
| properties |いいえ |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 コピー配列を指定して、1 つのプロパティの複数のインスタンスを作成することもできます。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| sku | いいえ | 一部のリソースでは、デプロイする SKU を定義する値が許可されます。 たとえば、ストレージ アカウントの冗長性の種類を指定することができます。 |
| kind | いいえ | 一部のリソースでは、デプロイするリソースの種類を定義する値が許可されます。 たとえば、作成する Cosmos DB の種類を指定することができます。 |
| プラン | いいえ | 一部のリソースでは、デプロイするプランを定義する値が許可されます。 たとえば、仮想マシンのマーケットプレース イメージを指定することができます。 | 
| resources |いいえ |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |

## <a name="outputs"></a>出力

[出力] セクションではデプロイから返される値を指定します。 通常は、デプロイされたリソースからの値を返します。

次の例では、出力の定義の構造を示します。

```json
"outputs": {
  "<output-name>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| output-name |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| condition |いいえ | この出力値が返されたかどうかを示すブール値。 `true` の場合、値はデプロイの出力に含まれています。 `false` の場合、このデプロイでは、出力値はスキップされます。 指定しない場合、既定値は `true` です。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 出力の種類に **securestring** を指定した場合、値はデプロイ履歴に表示されず、他のテンプレートから取得できません。 複数のテンプレートでシークレット値を使用するには、シークレットをキー コンテナーに格納し、パラメーター ファイルでそのシークレットを参照します。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](resource-manager-keyvault-parameter.md)」を参照してください |
| value |はい |評価され、出力値として返されるテンプレート言語式。 |

出力の使用方法の例については、「[Azure Resource Manager テンプレートの出力](template-outputs.md)」を参照してください。

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

## <a name="next-steps"></a>次の手順

* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「 [Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「[Azure Resource Manager でのリンクされたテンプレートの使用](resource-group-linked-templates.md)」をご覧ください。
* テンプレート作成に関する推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を参照してください。
* すべての Azure 環境および Azure Stack で使用できる Resource Manager テンプレートの作成に関する推奨事項については、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」をご覧ください。
