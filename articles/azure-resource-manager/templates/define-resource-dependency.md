---
title: リソースにデプロイ順序を設定する
description: デプロイ時に、ある Azure リソースが別のリソースに依存するように設定する方法について説明します。 依存関係によって、リソースが適切な順序でデプロイされます。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: f6b63b066da06a17c3a2e51ab0f3ab9bf521a144
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934749"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>ARM テンプレートでのリソース デプロイ順序の定義

リソースをデプロイするときに、一部のリソースが他のリソースの前に存在することを確認しなければならない場合があります。 たとえば、データベースをデプロイする前に論理 SQL サーバーが必要です。 このリレーションシップは、あるリソースが他のリソースに依存しているとマークすることで確立します。 明示的な依存関係を定義するには、`dependsOn` 要素を使用します。 暗黙的な依存関係を定義するには、**reference** または **list** 関数を使用します。

Azure Resource Manager により、リソース間の依存関係が評価され、リソースは依存する順にデプロイされます。 相互依存していないリソースは、平行してデプロイされます。 同じテンプレートでデプロイされるリソースの依存関係だけを定義する必要があります。

## <a name="dependson"></a>dependsOn

Azure Resource Manager テンプレート (ARM テンプレート) 内で、`dependsOn` 要素を使用すると、1 つのリソースが 1 つ以上のリソースに依存していることを定義できます。 その値は文字列の JSON (JavaScript Object Notation) 配列であり、それぞれがリソース名または ID です。 配列には、[条件付きでデプロイされた](conditional-resource-deployment.md)リソースを含めることができます。 条件付きリソースがデプロイされていない場合、Azure Resource Manager によって必要な依存関係からそれが自動的に削除されます。

次の例は、仮想ネットワーク、ネットワーク セキュリティ グループ、およびパブリック IP アドレスに依存するネットワーク インターフェイスを示しています。 完全なテンプレートについては、[Linux VM 用のクイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-simple-linux/azuredeploy.json)を参照してください。

```json
{
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-06-01",
    "name": "[variables('networkInterfaceName')]",
    "location": "[parameters('location')]",
    "dependsOn": [
      "[resourceId('Microsoft.Network/networkSecurityGroups/', parameters('networkSecurityGroupName'))]",
      "[resourceId('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
      "[resourceId('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
    ],
    ...
}
```

`dependsOn` を使用してリソース間のリレーションシップをマップする傾向があるものの、重要なのは、その操作を行う理由を理解することです。 たとえば、リソースが相互にどのように接続されているかをドキュメント化するには、`dependsOn` は適切な方法ではありません。 どのリソースが `dependsOn` 要素で定義されたかを、デプロイ後に照会することはできません。 Resource Manager ではこれらのリソースを並行してデプロイすることはできないため、不要な依存関係を設定するとデプロイ時間が遅くなります。

## <a name="child-resources"></a>子リソース

[子リソース](child-resource-name-type.md)と親リソースの間に、デプロイの暗黙的な依存関係が自動的に作成されることはありません。 親リソースの後に子リソースをデプロイする必要がある場合は、`dependsOn` プロパティを設定します。

次の例では、論理 SQL サーバーとデータベースを示します。 データベースがサーバーの子である場合でも、データベースとサーバーの間に明示的な依存関係が定義されることに注目してください。

```json
"resources": [
  {
    "type": "Microsoft.Sql/servers",
    "apiVersion": "2020-02-02-preview",
    "name": "[parameters('serverName')]",
    "location": "[parameters('location')]",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "type": "databases",
        "apiVersion": "2020-08-01-preview",
        "name": "[parameters('sqlDBName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
          },
        "dependsOn": [
          "[resourceId('Microsoft.Sql/servers', concat(parameters('serverName')))]"
        ]
      }
    ]
  }
]
```

完全なテンプレートについては、[Azure SQL Database 用のクイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-sql-database/azuredeploy.json)を参照してください。

## <a name="reference-and-list-functions"></a>reference 関数と list 関数

[reference 関数](template-functions-resource.md#reference) を使用すると、式では、他の JSON の名前と値のペアまたはランタイム リソースからその値を導出することができます。 [list* 関数](template-functions-resource.md#list)はリスト操作からリソースの値を返します。

reference 式と list 式では、あるリソースが別のリソースに依存することが暗黙的に宣言されます。 不要な依存関係が追加されないように、できる限り、暗黙的な参照を使用してください。

暗黙的な依存関係を適用するには、リソースをリソース ID ではなく名前で参照します。 reference 関数または list 関数にリソース ID を渡す場合は、暗黙的な参照は作成されません。

`reference` 関数の一般的な形式は次のとおりです。

```json
reference('resourceName').propertyPath
```

`listKeys` 関数の一般的な形式は次のとおりです。

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

詳細については、「 [reference 関数](template-functions-resource.md#reference)」を参照してください。

## <a name="depend-on-resources-in-a-loop"></a>ループ内のリソースへの依存

[コピー ループ](copy-resources.md)内のリソースに依存するリソースをデプロイするには、2 つのオプションがあります。 ループ内の個々のリソースまたはループ全体のいずれかに依存関係を設定できます。

> [!NOTE]
> ほとんどのシナリオでは、コピー ループ内の個々のリソースに依存関係を設定する必要があります。 次のリソースを作成する前に、ループ内のすべてのリソースが存在する必要がある場合にのみ、ループ全体に依存します。 ループ全体に依存関係を設定すると、特にループされるリソースが他のリソースに依存している場合、依存関係グラフが著しく膨張します。 依存関係の拡大によって、デプロイを効率的に完了するのが困難になります。

次の例は、複数の仮想マシンをデプロイする方法を示しています。 このテンプレートでは、同じ数のネットワーク インターフェイスが作成されます。 各仮想マシンは、ループ全体ではなく、1 つのネットワーク インターフェイスに依存しています。

```json
{
  "type": "Microsoft.Network/networkInterfaces",
  "apiVersion": "2020-05-01",
  "name": "[concat(variables('nicPrefix'),'-',copyIndex())]",
  "location": "[parameters('location')]",
  "copy": {
    "name": "nicCopy",
    "count": "[parameters('vmCount')]"
  },
  ...
},
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "name": "[concat(variables('vmPrefix'),copyIndex())]",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]"
  ],
  "copy": {
    "name": "vmCopy",
    "count": "[parameters('vmCount')]"
  },
  "properties": {
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(variables('nicPrefix'),'-',copyIndex()))]",
          "properties": {
            "primary": "true"
          }
        }
      ]
    },
    ...
  }
}
```

次の例では、仮想マシンをデプロイする前に 3 つのストレージ アカウントをデプロイする方法を示します。 `copy` 要素の `name` が `storagecopy` に設定され、仮想マシンの `dependsOn` 要素が `storagecopy` に設定されるよう注意してください。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
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
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="circular-dependencies"></a>循環依存関係

Resource Manager では、テンプレートの検証中に循環依存関係を識別します。 循環依存の関係でエラーが発生した場合は、テンプレートを評価して、削除できる依存関係があるかどうかを確認します。 依存関係を削除してもエラーが解消されない場合は、いくつかのデプロイ操作を子リソースに移動することで、循環依存関係を回避できます。 循環依存関係があるリソースの後に、子リソースをデプロイします。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

デプロイ順序の評価と依存関係のエラーの解決については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* チュートリアルについては、「[チュートリアル:依存リソースを含む ARM テンプレートを作成する](template-tutorial-create-templates-with-dependent-resources.md)」の「テンプレートのデプロイ」セクションを参照してください。
* リソースの依存関係について取り上げた Microsoft Learn モジュールについては、「[高度な ARM テンプレート機能を使用して複雑なクラウド デプロイを管理する](/learn/modules/manage-deployments-advanced-arm-template-features/)」を参照してください。
* 依存関係を設定する際の推奨事項については、「[ARM テンプレートのベスト プラクティス](template-best-practices.md)」をご覧ください。
* デプロイ中の依存関係のトラブルシューティングについては、「[Troubleshoot common Azure deployment errors with Azure Resource Manager (Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング)](common-deployment-errors.md)」を参照してください。
* Azure Resource Manager テンプレートの作成については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
* テンプレートで使用可能な関数の一覧については、「[ARM テンプレート関数](template-functions.md)」を参照してください。
