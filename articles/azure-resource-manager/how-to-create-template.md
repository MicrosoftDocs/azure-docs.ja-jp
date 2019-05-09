---
title: Azure Resource Manager のテンプレートの作成方法
description: Azure Resource Manager テンプレートの作成プロセスについて説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57403517"
---
# <a name="create-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを作成する

この記事では、Azure Resource Manager テンプレートを作成する場合のプロセスと意思決定について説明します。 テンプレートの作成時に役立つサンプルや機能の概要について説明します。 この記事では、ユーザーがリソースをリソース グループにデプロイしていることを想定しています。 Azure Policy の作成やロールベースのアクセス制御の割り当てなど、Azure サブスクリプションにリソースをデプロイする必要がある場合は、「[Azure サブスクリプションのリソース グループとリソースを作成する](deploy-to-subscription.md)」を参照してください。

## <a name="select-json-editor"></a>JSON エディターを選択する

Resource Manager テンプレートは JSON ファイルです。 JSON ファイルで作業する場合は、優れた作成ツールが必要です。 多くのオプションがありますが、好みのエディターをまだお持ちでない場合は、[Visual Studio Code (VS Code)](https://code.visualstudio.com/) をインストールしてください。 

VS Code をインスールした後、[Azure Resource Manager ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)を追加します。 この拡張機能により、テンプレートの作成を簡略化する多くの機能が追加されます。

![Visual Studio Code テンプレート](./media/how-to-create-template/template-visual-studio-code.png)

このスクリーンショットは、Visual Studio Code で開いた Resource Manager テンプレートを示しています。 

Resource Manager ツールの拡張機能をインストールするチュートリアル、および VS Code の使用方法については、「[クイック スタート:Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)」をご覧ください。

## <a name="understand-the-template-structure"></a>テンプレートの構造を理解する

テンプレートのしくみを理解するためにテンプレートの一部を確認してみましょう。 テンプレートに一部のセクションしかない場合もあります。 注目すべきセクションは次のとおりです。

* [parameters](resource-group-authoring-templates.md#parameters) セクション: デプロイ中に指定してデプロイされるインフラストラクチャをカスタマイズできる値を示します。 

* [variables](resource-group-authoring-templates.md#variables) セクション: テンプレート内で使用される値を示します。

* [functions](resource-group-authoring-templates.md#functions) セクション: テンプレート内で使用されるカスタマイズされたテンプレートの正規表現を示します。

* [resources](resource-group-authoring-templates.md#resources) セクション： サブスクリプションにデプロイされる Azure リソースを示します。

* [outputs](resource-group-authoring-templates.md#outputs) セクション： デプロイ完了後に返される値を示します。

## <a name="look-for-similar-templates"></a>類似のテンプレートを検索する

多くの場合、必要なソリューションと同じようなソリューションをデプロイする既存のテンプレートを検索できます。 [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)には、コミュニティの共同作成者からの何百ものテンプレートがあります。

![テンプレート クイック スタートのリポジトリ](./media/how-to-create-template/template-quickstart-repo.png)

必要なものと同じようなテンプレートのリポジトリを検索してください。 テンプレートが必要なものと厳密に同じでなくてもかまいません。それをカスタマイズできます。

テンプレートを見つけたら、**[GitHub で参照する]** を選択し、リポジトリから **azuredeploy.json** ファイルをコピーします。 VS Code で新しい **azuredeploy.json** という名前のファイルを作成し、クイック スタート リポジトリからコピーしたテンプレート ファイルの内容を追加します。

## <a name="add-resources"></a>リソースを追加する

おそらく厳密に必要なテンプレートとなるようにするにはテンプレートをカスタマイズする必要があります。 まず、デプロイされるリソースを確認します。 場合によってはテンプレートにリソースを追加する、テンプレートのリソースを削除する、またはテンプレートのリソースを変更する必要があります。 リソースの説明と構文については、「[Azure Resource Manager テンプレートのリファレンス](/azure/templates/)」を参照してください。

![テンプレート リファレンス](./media/how-to-create-template/template-reference.png)

これらのプロパティを確認した後、必要な変更を加えます。 リソースの定義方法についての推奨事項については、「[リソース ‐ 推奨プラクティス](template-best-practices.md#resources)」を参照してください。

## <a name="add-or-remove-parameters"></a>パラメーターを追加または削除する

また、テンプレートに合わせてパラメーターを調整する必要がある場合があります。 デプロイ中にどのくらいのカスタマイズを有効にするかによってパラメーターを追加または削除できます。 パラメーターの使用方法の推奨事項については、「[パラメーター ‐ 推奨ベスト プラクティス](template-best-practices.md#parameters)」を参照してください。

## <a name="add-tags"></a>タグを追加する

リソースにタグを追加してカテゴリ別に論理的に整理し、請求先コストを除算できます。 タグの追加は簡単で、タグはリソースの JSON に適用します。 たとえば、以下のストレージ アカウントには 2 つのタグがあります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

タグはパラメーターから動的に適用することもできます。 詳細については、「[テンプレートのタグ](resource-group-using-tags.md#templates)」を参照してください。

## <a name="review-template-functions"></a>テンプレート関数を確認する

`"[some-expression]"` など、ご覧のとおりブラケットで囲まれたテンプレートの式があります。 これらの式では、テンプレート関数を使用してデプロイ中に動的に値を構築します。

たとえば、次のような式が使われます。

```json
"name": "[parameters('siteName')]"
```

その式ではパラメーターの値を示します。 この値は名前プロパティに割り当てられます。

または、次のように複数の式を使用したより複雑な式もあります。

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

その式ではストレージ アカウントのプロパティを使用してオブジェクトを示します。

関数が何を実行するかを理解するには、[テンプレートの関数リファレンス](resource-group-template-functions.md) ドキュメントを確認してください。

## <a name="create-more-than-one-instance"></a>複数のインスタンスを作成する

時にはリソースの複数のインスタンスを作成する必要があります。 たとえば、複数のストレージ アカウントが必要な場合です。 テンプレートでリソースを繰り返すのではなく、複数のインスタンスを指定する `copy` 構文を使用することができます。

次の例では、3 つのストレージ アカウントを作成します。

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

また、パラメーターから動的にインスタンスの数を指定することもできます。 詳しくは、「[Azure Resource Manager テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする](resource-group-create-multiple.md)」を参照してください。

## <a name="conditionally-deploy-a-resource"></a>リソースを条件付きでデプロイする

場合によっては、デプロイ中にテンプレートのリソースをデプロイするかどうかを指定する必要があります。 たとえば、新しいリソースをデプロイするか、既存のリソースを使用するかの柔軟性が必要となる場合があります。 `condition` 要素によってリソースのデプロイをオン/オフできます。 condition 要素で式が true である場合、リソースはデプロイされます。 false の場合、デプロイ時にそのリソースはスキップされます。

次の例では、ストレージ アカウントを条件付きでデプロイします。

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

詳細については、[condition 要素](resource-group-authoring-templates.md#condition)をご覧ください。

## <a name="review-dependencies"></a>依存関係を表示する

テンプレートの一部のリソースは他のリソースより先にデプロイする必要があります。 たとえば、SQL サーバーは、SQL データベースを作成する前に存在している必要があります。 [reference 関数](resource-group-template-functions-resource.md#reference)を使用している場合、Resource Manager は、リソースのデプロイの順序を黙示的に決定します。 ただし、場合によっては、`dependsOn` 要素を使用して明示的に依存関係を定義する必要があります。 テンプレートを表示し、依存関係を追加する必要があるかどうかを確認してください。 不要な依存関係は追加しないように注意してください。こうした依存関係によりデプロイの速度が遅くなったり、循環依存関係が作成されたりすることがあります。

次の図は、さまざまな App Service リソースの依存関係の順序を示しています。

![Web アプリの依存関係](./media/how-to-create-template/web-dependencies.png)

次の例は、依存関係を定義するテンプレートの一部を示しています。

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

詳細については、「[Define the order for deploying resources in Azure Resource Manager Templates](resource-group-define-dependencies.md)」 (Azure Resource Manager テンプレートでリソースをデプロイする順序を定義する) を参照してください。

## <a name="review-recommended-practices"></a>推奨プラクティスを確認する

テンプレートをデプロイする前に、「[Azure Resource Manager テンプレートのベスト プラクティス](template-best-practices.md)」を確認し、テンプレートで実装したい推奨アプローチがあるかどうかを確認してください。

別の Azure クラウド環境でテンプレートを使用する必要がある場合は、「[クラウドの一貫性のための Azure Resource Manager テンプレートを開発する](templates-cloud-consistency.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

* テンプレートをデプロイするには、「[Azure CLI でのデプロイ](resource-group-template-deploy-cli.md)」または「[PowerShell でデプロイする](resource-group-template-deploy.md)」を参照してください。
* テンプレートの作成方法を詳しく解説したクイック スタートについては、「[Visual Studio Code を使って Azure Resource Manager テンプレートを作成する](resource-manager-quickstart-create-templates-use-visual-studio-code.md)」を参照してください。
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。
