---
title: テンプレートの構造と構文
description: 宣言型 JSON 構文を使用した Azure Resource Manager テンプレート (ARM テンプレート) の構造とプロパティについて説明します。
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: da64eb8abeaf45f58933dfbddaf954cad8e66f4a
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120418"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>ARM テンプレートの構造と構文について

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) の構造について説明します。 テンプレートの各種セクションとそこで使用できるプロパティを紹介しています。

この記事は、ARM テンプレートにある程度慣れているユーザー向けです。 テンプレートの構造に関する詳細情報を提供します。 テンプレートの作成手順について説明したチュートリアルについては、「[チュートリアル:初めての ARM テンプレートを作成してデプロイする](template-tutorial-create-first-template.md)」を参照してください。 Microsoft Learn のガイド付きモジュール セットを使用して ARM テンプレートを学習するには、「[ARM テンプレートを使用して Azure にリソースをデプロイして管理する](/learn/paths/deploy-manage-resource-manager-templates/)」を参照してください。

## <a name="template-format"></a>テンプレートの形式

最も単純な構造のテンプレートは、次の要素で構成されます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
| $schema |はい |テンプレート言語のバージョンが記述されている JSON (JavaScript Object Notation) スキーマ ファイルの場所。 使用するバージョン番号は、デプロイのスコープと JSON エディターによって異なります。<br><br>[Visual Studio Code を Azure Resource Manager ツール拡張機能](quickstart-create-templates-use-visual-studio-code.md)と共に使用している場合は、リソース グループのデプロイの最新バージョンを使用します。<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>他のエディター (Visual Studio を含む) では、このスキーマを処理できない場合があります。 そうしたエディターの場合、次を使用します。<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>サブスクリプション デプロイの場合、次を使用します。<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>管理グループのデプロイの場合、次を使用します。<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>テナントのデプロイの場合、次を使用します。<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |はい |テンプレートのバージョン (1.0.0.0 など)。 この要素には任意の値を指定できます。 この値を使用し、テンプレートの大きな変更を記述します。 テンプレートを使用してリソースをデプロイする場合は、この値を使用して、適切なテンプレートが使用されていることを確認できます。 |
| apiProfile |いいえ | リソースの種類に対する API のバージョンのコレクションとして機能する API バージョン。 この値を使用すると、テンプレートのリソースごとに API バージョンを指定しなくて済みます。 API プロファイルのバージョンを指定し、リソースの種類の API バージョンを指定しないと、Resource Manager では、プロファイルでそのリソースの種類に対して定義されている API バージョンが使用されます。<br><br>API プロファイル プロパティは、Azure Stack やグローバルな Azure など、さまざまな環境にテンプレートをデプロイする場合に特に便利です。 API プロファイルのバージョンを使用し、両方の環境でサポートされているバージョンがテンプレートで自動的に使用されるようにします。 現在の API プロファイルのバージョンおよびリソース プロファイルで定義されている API バージョンの一覧については、「[API Profile (API プロファイル)](https://github.com/Azure/azure-rest-api-specs/tree/master/profile)」をご覧ください。<br><br>詳しくは、「[API プロファイルを使用してバージョンを追跡する](templates-cloud-consistency.md#track-versions-using-api-profiles)」をご覧ください。 |
| [parameters](#parameters) |いいえ |リソースのデプロイをカスタマイズするのにはデプロイを実行すると、提供されている値です。 |
| [variables](#variables) |いいえ |テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値。 |
| [functions](#functions) |いいえ |テンプレート内で使用できるユーザー定義関数。 |
| [resources](#resources) |はい |リソース グループまたはサブスクリプション内でデプロイまたは更新されるリソースの種類。 |
| [outputs](#outputs) |いいえ |デプロイ後に返される値。 |

各要素には、設定できるプロパティがあります。 この記事では、テンプレートのセクションについて詳しく説明します。

## <a name="parameters"></a>パラメーター

テンプレートの `parameters` セクションでは、リソースをデプロイするときにどのような値を入力できるかを指定します。 テンプレートではパラメーターが 256 個に制限されます。 複数のプロパティを含むオブジェクトを使用すると、パラメーター数を減らすことができます。

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
| type |はい |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 「[ARM テンプレートのデータ型](data-types.md)」を参照してください。 |
| defaultValue |いいえ |パラメーターに値が指定されない場合のパラメーターの既定値。 |
| allowedValues |いいえ |適切な値が確実に指定されるように、パラメーターに使用できる値の配列。 |
| minValue |いいえ |int 型パラメーターの最小値。 |
| maxValue |いいえ |int 型パラメーターの最大値。 |
| minLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最小値 (この値を含む)。 |
| maxLength |いいえ |string 型、securestring 型、array 型パラメーターの長さの最大値 (この値を含む)。 |
| description |いいえ |ポータルを通じてユーザーに表示されるパラメーターの説明。 詳しくは、[テンプレート内のコメント](#comments)に関するページをご覧ください。 |

パラメーターの使用方法の例については、「[ARM テンプレートのパラメーター](template-parameters.md)」を参照してください。

## <a name="variables"></a>変数

テンプレート内で使用できる値は、`variables` セクションで作成します。 必ずしも変数を定義する必要はありませんが、変数を定義することによって複雑な式が減り、テンプレートが単純化されることはよくあります。 各変数の形式は、いずれかの[データ型](data-types.md)に一致します。

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

`copy` を使用して変数に複数の値を作成する方法については、「[変数の反復処理](copy-variables.md)」をご覧ください。

変数の使用方法の例については、「[ARM テンプレートの変数](template-variables.md)」を参照してください。

## <a name="functions"></a>関数

テンプレート内で、独自の関数を作成できます。 これらの関数は、テンプレートで使用可能です。 通常は、テンプレート全体で繰り返したくない複雑な式を定義します。 ユーザー定義関数は、テンプレートでサポートされている[関数](template-functions.md)および式から作成します。

ユーザー関数を定義するときに、適用される制限がいくつかあります。

* 関数は変数にアクセスできません。
* 関数は、関数内で定義されているパラメーターのみを使用できます。 ユーザー定義関数内で[パラメーター関数](template-functions-deployment.md#parameters)を使用する場合、その関数のパラメーターに制限されます。
* 関数は、その他のユーザー定義関数を呼び出すことはできません。
* 関数は [reference 関数](template-functions-resource.md#reference)を使用できません。
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
| function-name |はい |カスタム関数の名前。 関数を呼び出すときに、関数名と名前空間を組み合わせます。 たとえば、`uniqueName` という名前の関数を名前空間 contoso で呼び出すには、`"[contoso.uniqueName()]"` を使用します。 |
| parameter-name |いいえ |カスタム関数内で使用されるパラメーターの名前。 |
| parameter-value |いいえ |パラメーター値の型。 使用できる型および値は、**string**、**securestring**、**int**、**bool**、**object**、**secureObject**、**array** です。 |
| output-type |はい |出力値の型。 出力値では、関数入力パラメーターと同じ型がサポートされています。 |
| output-value |はい |評価され、関数から返されるテンプレート言語式。 |

カスタム関数の使用方法の例については、「[ARM テンプレートのユーザー定義関数](template-user-defined-functions.md)」を参照してください。

## <a name="resources"></a>リソース

`resources` セクションでは、デプロイまたは更新されるリソースを定義します。

リソースは、次の構造で定義します。

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
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
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| condition | いいえ | このデプロイの間にリソースがプロビジョニングされるかどうかを示すブール値。 `true` の場合、デプロイ時にリソースが作成されます。 `false` の場合、このデプロイでは、リソースはスキップされます。 [条件](conditional-resource-deployment.md)をご覧ください。 |
| type |はい |リソースの種類。 この値は、リソース プロバイダーの名前空間とリソースの種類を組み合わせたものです (例: `Microsoft.Storage/storageAccounts`)。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 子リソースの場合、type の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |
| apiVersion |はい |リソースの作成に使用する REST API バージョン。 新しいテンプレートを作成するときは、デプロイするリソースの最新バージョンをこの値に設定します。 テンプレートが必要に応じて動作する限り、同じ API バージョンを使用し続けてください。 同じ API バージョンを使用し続けることで、新しい API バージョンにより、テンプレートの動作方法が変更されるリスクを最小限に抑えることができます。 新しいバージョンで導入された新機能を使用する場合にのみ、API バージョンを更新することを検討してください。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| name |はい |リソースの名前。 この名前は、RFC3986 で定義されている URI コンポーネントの制限に準拠する必要があります。 リソース名を外部に公開する Azure サービスでは、名前が別の ID になりすますことがないように、その名前を検証します。 子リソースの場合、name の形式は、親リソース内に入れ子にされているか、親リソースの外側で定義されているかによって変わります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |
| comments |いいえ |テンプレート内にドキュメント化するリソースについてのメモ。 詳しくは、[テンプレート内のコメント](template-syntax.md#comments)に関するページをご覧ください。 |
| location |場合により異なる |指定されたリソースのサポートされている地理的な場所。 利用可能な任意の場所を選択できますが、一般的に、ユーザーに近い場所を選択します。 また、通常、相互に対話するリソースを同じリージョンに配置します。 ほとんどのリソースの種類では場所が必要となりますが、場所を必要としない種類 (ロールの割り当てなど) もあります。 [リソースの場所の設定](resource-location.md)に関するページを参照してください。 |
| dependsOn |いいえ |このリソースが配置される前に配置される必要があるリソース。 Resource Manager により、リソース間の依存関係が評価され、リソースが正しい順序でデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 値には、リソース名またはリソースの一意識別子のコンマ区切りリストを指定できます。 このテンプレートに配置されたリソースのみをリストします。 このテンプレートで定義されていないリソースは、既に存在している必要があります。 不要な依存関係は追加しないでください。こうした依存関係によりデプロイの速度が遅くなり、循環依存関係を作成されることがあります。 依存関係の設定のガイダンスについては、「[ARM テンプレートでのリソース デプロイ順序の定義](define-resource-dependency.md)」を参照してください。 |
| tags |いいえ |リソースに関連付けられたタグ。 サブスクリプション間でリソースを論理的に編成するためのタグを適用します。 |
| sku | いいえ | 一部のリソースでは、デプロイする SKU を定義する値が許可されます。 たとえば、ストレージ アカウントの冗長性の種類を指定することができます。 |
| kind | いいえ | 一部のリソースでは、デプロイするリソースの種類を定義する値が許可されます。 たとえば、作成する Cosmos DB の種類を指定することができます。 |
| copy |いいえ |複数のインスタンスが必要な場合に作成するリソースの数。 既定のモードはパラレルです。 すべてのリソースを同時にデプロイする必要がない場合は、シリアル モードを指定します。 詳しくは、「[Azure Resource Manager でリソースの複数のインスタンスを作成する](copy-resources.md)」をご覧ください。 |
| plan | いいえ | 一部のリソースでは、デプロイするプランを定義する値が許可されます。 たとえば、仮想マシンのマーケットプレース イメージを指定することができます。 |
| properties |いいえ |リソース固有の構成設定。 properties の値は、リソースを作成するために REST API 操作 (PUT メソッド) の要求本文に指定した値と同じです。 コピー配列を指定して、1 つのプロパティの複数のインスタンスを作成することもできます。 使用可能な値を確認するには、[テンプレートのリファレンス](/azure/templates/)に関する記事をご覧ください。 |
| resources |いいえ |定義されているリソースに依存する子リソース。 親リソースのスキーマで許可されているリソースの種類のみを指定します。 親リソースへの依存関係は示されません。 この依存関係は明示的に定義する必要があります。 [子リソースの名前と種類の設定](child-resource-name-type.md)に関する記事を参照してください。 |

## <a name="outputs"></a>出力

`outputs` セクションではデプロイから返される値を指定します。 通常は、デプロイされたリソースからの値を返します。

次の例では、出力の定義の構造を示します。

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| 要素名 | 必須 | 説明 |
|:--- |:--- |:--- |
| output-name |はい |出力値の名前。 有効な JavaScript 識別子で指定する必要があります。 |
| condition |いいえ | この出力値が返されたかどうかを示すブール値。 `true` の場合、値はデプロイの出力に含まれています。 `false` の場合、このデプロイでは、出力値はスキップされます。 指定しない場合、既定値は `true` です。 |
| type |はい |出力値の型。 出力値では、テンプレート入力パラメーターと同じ型がサポートされています。 出力の種類に **securestring** を指定した場合、値はデプロイ履歴に表示されず、他のテンプレートから取得できません。 複数のテンプレートでシークレット値を使用するには、シークレットをキー コンテナーに格納し、パラメーター ファイルでそのシークレットを参照します。 詳細については、「[デプロイ時に Azure Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す](key-vault-parameter.md)」を参照してください |
| value |いいえ |評価され、出力値として返されるテンプレート言語式。 **値** または **copy** を指定します。 |
| copy |いいえ | 出力に対して複数の値を返すために使用されます。 **値** または **copy** を指定します。 詳細については、「[ARM テンプレートでの出力の反復処理](copy-outputs.md)」を参照してください。 |

出力の使用方法の例については、「[ARM テンプレートの出力](template-outputs.md)」を参照してください。

<a id="comments"></a>

## <a name="comments-and-metadata"></a>コメントとメタデータ

テンプレートにコメントとメタデータを追加するためのオプションがいくつかあります。

### <a name="comments"></a>説明

インライン コメントの場合、`//` または `/* ... */` を使用できますが、この構文はすべてのツールでは機能しません。 このスタイルのコメントを追加する場合は、ご使用のツールでインライン JSON コメントがサポートされていることを確認してください。

> [!NOTE]
> 2\.3.0 以前のバージョンの Azure CLI を使用してコメントが含まれるテンプレートをデプロイするには、`--handle-extended-json-format` スイッチを使用する必要があります。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Visual Studio Code では、[Azure Resource Manager ツール拡張機能](quickstart-create-templates-use-visual-studio-code.md)により、ARM テンプレートが自動的に検出され、言語モードが変更されます。 Visual Studio Code の右下隅に **[Azure Resource Manager テンプレート]** が表示されている場合は、インライン コメントを使用できます。 インライン コメントは無効としてマークされなくなります。

![Visual Studio Code での Azure Resource Manager テンプレート モード](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadata

`metadata` オブジェクトは、テンプレート内のほとんどどこにでも追加できます。 このオブジェクトは、Resource Manager では無視されますが、お使いの JSON エディターによっては、プロパティが無効であると警告される場合があります。 オブジェクトで、必要なプロパティを定義します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

`parameters`の場合、`description` プロパティを使って `metadata` オブジェクトを追加します。

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

![パラメーター ヒントの表示](./media/template-syntax/show-parameter-tip.png)

`resources` の場合、`comments` 要素または `metadata` オブジェクトを追加します。 次の例は、`comments` 要素と `metadata` オブジェクトの両方を示しています。

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
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

`outputs` の場合、`metadata` オブジェクトを出力値に追加します。

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

`metadata` オブジェクトをユーザー定義関数に追加することはできません。

## <a name="multi-line-strings"></a>複数行の文字列

文字列を複数の行に分割することができます。 例として、次の JSON の例の `location` プロパティといずれかのコメントを参照してください。

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

> [!NOTE]
> 2\.3.0 以前のバージョンの Azure CLI を使用して複数行の文字列が含まれるテンプレートをデプロイするには、`--handle-extended-json-format` スイッチを使用する必要があります。

## <a name="next-steps"></a>次のステップ

* さまざまな種類のソリューションのテンプレートについては、「 [Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」をご覧ください。
* テンプレート内から使用できる関数の詳細については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
* デプロイ中に複数のテンプレートを結合するには、「[Azure リソース デプロイ時のリンクされたテンプレートおよび入れ子になったテンプレートの使用](linked-templates.md)」をご覧ください。
* テンプレートの作成に関するレコメンデーションについては、「[ARM テンプレートのベストプラクティス](template-best-practices.md)」を参照してください。
* 一般的な質問に対する回答については、「[ARM テンプレートに関してよく寄せられる質問](frequently-asked-questions.md)」を参照してください。
