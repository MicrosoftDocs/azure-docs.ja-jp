---
title: "Azure リソースのデプロイ順序の設定 | Microsoft Docs"
description: "デプロイ時にリソースが正しい順序でデプロイされるように、あるリソースが別のリソースに依存するように設定する方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 006d8e10acd6b4b756c0b78988176f71c3802080
ms.lasthandoff: 03/06/2017


---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでのリソース デプロイ順序の定義
リソースによっては、デプロイする前に、他のリソースが存在している必要がある場合があります。 たとえば、SQL データベースをデプロイするには、先に SQL Server が存在している必要があります。 このリレーションシップは、一方のリソースがもう一方のリソースに依存しているとマークすることで定義します。 依存関係を定義するには、**dependsOn** 要素または **reference** 関数を使用します。 

Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 同じテンプレートでデプロイされるリソースの依存関係だけを定義する必要があります。 

## <a name="dependson"></a>dependsOn
テンプレート内で dependsOn 要素を使用すると、1 つのリソースが&1; つ以上のリソースに依存していることを定義できます。 その値には、リソース名のコンマ区切りリストを指定できます。 

次の例では、ロード バランサー、仮想ネットワーク、および複数のストレージ アカウントを作成するループに依存する仮想マシン スケール セットを示します。 こうした他のリソースは、次の例には示されていませんが、テンプレートの他の場所に存在する必要があります。

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
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

前の例では、依存関係は **storageLoop** という名前のコピー ループを通じて作成されたリソースにのみ含まれます。 例が必要であれば、「 [Azure リソース マネージャーでリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」を参照してください。

依存関係を定義するときに、あいまいにならないように、リソースプロバイダーの名前空間とリソースの種類を含めることができます。 たとえば、他のリソースと同じ名前を持つロード バランサーと仮想ネットワークを明確にするには、次の形式を使用します。

```json
"dependsOn": [
  "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
]
``` 

dependsOn を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。 たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、dependsOn は適切な方法ではありません。 どのリソースが dependsOn 要素で定義されたかを、デプロイ後に照会することはできません。 Resource Manager では、依存関係のある&2; つのリソースが並列でデプロイされないため、dependsOn を使用すると、デプロイ時間に影響を及ぼす可能性があります。 リソース間のリレーションシップをドキュメント化するには、代わりに[リソース リンク](/rest/api/resources/resourcelinks)を使用します。

## <a name="child-resources"></a>子リソース
resources プロパティを使用すると、定義されているリソースに関連する子リソースを指定できます。 子リソースの定義の深さは&5; レベルまでです。 重要なのは、子リソースと親リソースの間に暗黙的な依存関係を作成しないことです。 親リソースの後に子リソースをデプロイする必要がある場合は、dependsOn プロパティを使用してその依存関係を明示的に指定する必要があります。 

各親リソースは、子リソースとして特定のリソースの種類のみを受け取ります。 許容されるリソースの種類は、親リソースの[テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas)で指定されます。 子リソースの種類の名前には、親リソースの種類の名前 (**Microsoft.Web/sites/config**、**Microsoft.Web/sites/extensions** など。これは両方とも **Microsoft.Web/sites** の子リソース)。

次の例では、SQL Server と SQL データベースを示します。 データベースが SQL Server の子である場合でも、SQL データベースと SQL Server の間に明示的な依存関係が定義されることに注目してください。

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
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

## <a name="reference-function"></a>reference 関数
[reference 関数](resource-group-template-functions.md#reference) を使用すると、式では、他の JSON の名前と値のペアまたはランタイム リソースからその値を導出することができます。 参照式では、あるリソースが別のリソースに依存することを暗黙的に宣言します。 一般的な形式は次のとおりです。

```json
reference('resourceName').propertyPath
```

次の例の CDN エンドポイントは CDN プロファイルに明示的に依存し、Web アプリに暗黙的に依存しています。

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

この要素または dependsOn 要素のいずれかを使用して依存関係を指定できますが、同じ依存リソースに両方の要素を使用する必要はありません。 不要な依存関係が追加されないように、できる限り、暗黙的な参照を使用してください。

詳細については、「 [reference 関数](resource-group-template-functions.md#reference)」を参照してください。

## <a name="recommendations-for-setting-dependencies"></a>依存関係の設定に関する推奨事項

どのような依存関係を設定するかを決めるときは、次のガイドラインを使用してください。

* 設定する依存関係の数はできるだけ少なくします。
* 子リソースは、その親リソースに依存するように設定します。
* **reference** 関数を使用して、プロパティを共有する必要があるリソース間に暗黙的な依存関係を設定します。 暗黙的な依存関係を設定した場合、明示的な依存関係 (**dependsOn**) は追加しないでください。 これにより、不要な依存関係が設定されるリスクを減らすことができます。 
* 他のリソースの機能がないとリソースを**作成**できないときに、依存関係を設定します。 デプロイ後にやり取りするだけのリソースには、依存関係を設定しないでください。
* 明示的に設定しなくても連鎖的な依存関係になるようにします。 たとえば、仮想マシンが仮想ネットワーク インターフェイスに依存し、その仮想ネットワーク インターフェイスは仮想ネットワークとパブリック IP アドレスに依存しているとします。 この場合、仮想マシンは、この&3; つのリソースすべての後にデプロイされますが、この仮想マシンを&3; つのリソースすべてに依存するものとして明示的に設定しないでください。 これにより依存関係の順序が明確になり、後でテンプレートを変更するのも簡単になります。
* デプロイの前に値を指定できる場合は、依存関係なしでリソースをデプロイしてみます。 たとえば、構成値に他のリソースの名前を必要とする場合は、依存関係が不要なことがあります。 ただし、これは必ずしも有効であるとは限りません。リソースによっては、他のリソースが存在するかどうかを確認することがあるためです。 エラーが発生したら、依存関係を追加してください。 

Resource Manager では、テンプレートの検証中に循環依存関係を識別します。 循環依存関係が存在することを示すエラーが発生した場合は、テンプレートを評価して、削除できる不要な依存関係がないかどうかを確認します。 依存関係を削除してもエラーが解消されない場合は、循環依存関係が含まれるリソースの後にデプロイされている子リソースに対するデプロイ操作を移動すると、循環依存関係を回避できることがあります。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

デプロイ順序の評価と依存関係のエラーの解決については、「[Check deployment sequence (デプロイの順序の確認)](resource-manager-common-deployment-errors.md#check-deployment-sequence)」を参照してください。

## <a name="next-steps"></a>次のステップ
* デプロイ中の依存関係のトラブルシューティングについては、「[Troubleshoot common Azure deployment errors with Azure Resource Manager (Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング)](resource-manager-common-deployment-errors.md)」を参照してください。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。


