---
title: リソースにデプロイ順序を設定する
description: デプロイ時にリソースが正しい順序でデプロイされるように、あるリソースが別のリソースに依存するように設定する方法について説明します。
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 84cea915565ec6ac9872681e1d4173abacb46ac4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255213"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>ARM テンプレートでのリソース デプロイ順序の定義

リソースをデプロイするときに、デプロイ前に他のリソースが存在することを確認する必要がある場合があります。 たとえば、データベースをデプロイする前に論理 SQL サーバーが必要です。 このリレーションシップは、一方のリソースがもう一方のリソースに依存しているとマークすることで定義します。 依存関係を定義するには、**dependsOn** 要素または **reference** 関数を使用します。

Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 同じテンプレートでデプロイされるリソースの依存関係だけを定義する必要があります。

## <a name="dependson"></a>dependsOn

テンプレート内で dependsOn 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 その値は文字列の JSON 配列であり、それぞれがリソース名です。 配列には、[条件付きでデプロイされた](conditional-resource-deployment.md)リソースを含めることができます。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

次の例では、ロード バランサー、仮想ネットワーク、および複数のストレージ アカウントを作成するループに依存する仮想マシン スケール セットを示します。 こうした他のリソースは、次の例には示されていませんが、テンプレートの他の場所に存在する必要があります。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

前の例では、依存関係は **storageLoop** という名前のコピー ループを通じて作成されたリソースにのみ含まれます。 例が必要であれば、「 [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](copy-resources.md)」を参照してください。

依存関係を定義するときに、あいまいにならないように、リソースプロバイダーの名前空間とリソースの種類を含めることができます。 たとえば、他のリソースと同じ名前を持つロード バランサーと仮想ネットワークを明確にするには、次の形式を使用します。

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

dependsOn を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。 たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、dependsOn は適切な方法ではありません。 どのリソースが dependsOn 要素で定義されたかを、デプロイ後に照会することはできません。 Resource Manager では、依存関係のある 2 つのリソースが並列でデプロイされないため、dependsOn を使用すると、デプロイ時間に影響を及ぼす可能性があります。

## <a name="child-resources"></a>子リソース

resources プロパティを使用すると、定義されているリソースに関連する子リソースを指定できます。 子リソースの定義の深さは 5 レベルまでです。 重要なのは、子リソースと親リソースの間に暗黙的なデプロイの依存関係を作成しないことです。 親リソースの後に子リソースをデプロイする必要がある場合は、dependsOn プロパティを使用してその依存関係を明示的に指定する必要があります。

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。 許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。 子リソースの種類の名前には、親リソースの種類の名前 (**Microsoft.Web/sites/config**、**Microsoft.Web/sites/extensions** など。これは両方とも **Microsoft.Web/sites** の子リソース)。

次の例では、論理 SQL サーバーとデータベースを示します。 データベースがサーバーの子である場合でも、データベースとサーバーの間に明示的な依存関係が定義されることに注目してください。

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>reference 関数と list 関数

[reference 関数](template-functions-resource.md#reference) を使用すると、式では、他の JSON の名前と値のペアまたはランタイム リソースからその値を導出することができます。 [list* 関数](template-functions-resource.md#list)はリスト操作からリソースの値を返します。  reference 式と list 式は、参照されているリソースが同じテンプレート内でデプロイされ、(リソース ID ではなく) 名前によって参照されていると、あるリソースが他のリソースに依存することを暗黙的に宣言します。 reference 関数または list 関数にリソース ID を渡す場合は、暗黙的な参照は作成されません。

reference 関数の一般的な形式は次のとおりです。

```json
reference('resourceName').propertyPath
```

listKeys 関数の一般的な形式は次のとおりです。

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

次の例の CDN エンドポイントは CDN プロファイルに明示的に依存し、Web アプリに暗黙的に依存しています。

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

この要素または dependsOn 要素のいずれかを使用して依存関係を指定できますが、同じ依存リソースに両方の要素を使用する必要はありません。 不要な依存関係が追加されないように、できる限り、暗黙的な参照を使用してください。

詳細については、「 [reference 関数](template-functions-resource.md#reference)」を参照してください。

## <a name="circular-dependencies"></a>循環依存関係

Resource Manager では、テンプレートの検証中に循環依存関係を識別します。 循環依存関係が存在することを示すエラーが発生した場合は、テンプレートを評価して、削除できる不要な依存関係がないかどうかを確認します。 依存関係を削除してもエラーが解消されない場合は、循環依存関係が含まれるリソースの後にデプロイされている子リソースに対するデプロイ操作を移動すると、循環依存関係を回避できることがあります。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

デプロイ順序の評価と依存関係のエラーの解決については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* チュートリアルについては、「[チュートリアル: 依存リソースを含む Azure Resource Manager テンプレートを作成する](template-tutorial-create-templates-with-dependent-resources.md)」を参照してください。
* 依存関係を設定する際の推奨事項については、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」をご覧ください。
* デプロイ中の依存関係のトラブルシューティングについては、「[Troubleshoot common Azure deployment errors with Azure Resource Manager (Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング)](common-deployment-errors.md)」を参照してください。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](template-syntax.md)に関するページを参照してください。
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](template-functions.md)に関するページを参照してください。

