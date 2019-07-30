---
title: Azure リソースが見つからないエラー | Microsoft Docs
description: リソースが見つからない場合に、エラーを解決する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 9c999a70ffdbed0c954cfc960b5febdaff06e4ae
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206185"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Azure リソースが見つからないエラーを解決する

この記事では、デプロイ時にリソースが見つからない場合に発生するエラーについて説明します。

## <a name="symptom"></a>症状

解決できないリソースの名前がテンプレートに含まれている場合、次のようなエラーが表示されます。

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

解決できないリソースで [reference](resource-group-template-functions-resource.md#reference) または [listKeys](resource-group-template-functions-resource.md#listkeys) 関数を使用すると、次のエラーが発生します。

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

Resource Manager はリソースのプロパティを取得する必要がありますが、サブスクリプション内のリソースを識別できません。

## <a name="solution-1---set-dependencies"></a>解決策 1 - 依存関係を設定する

不足しているリソースをテンプレートにデプロイする場合は、依存関係を追加する必要があるかどうかを確認してください。 Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。 リソースを順番にデプロイする必要がある場合は、テンプレートで **dependsOn** 要素を使用する必要があります。 たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。 Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。 また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。 このエラーは、Web アプリに依存関係を設定することで回避します。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

また、不要な依存関係を設定するのは望ましくありません。 不要な依存関係があると、相互に依存していないリソースを並行してデプロイすることを妨げるため、デプロイ時間が長くなります。 さらに、循環依存関係が作成されてデプロイがブロックされる恐れがあります。 [reference](resource-group-template-functions-resource.md#reference) 関数および [list*](resource-group-template-functions-resource.md#list) 関数では、参照されているリソースが同じテンプレートでデプロイされ、(リソース ID ではなく) 名前によって参照されている場合、そのリソースに対する暗黙的な依存関係が作成されます。 このため、**dependsOn** プロパティで指定したよりも多くの依存関係が作成されることがあります。 [resourceId](resource-group-template-functions-resource.md#resourceid) 関数は暗黙的な依存関係を作成しません。また、リソースの存在を検証することもしません。 [reference](resource-group-template-functions-resource.md#reference) 関数および [list*](resource-group-template-functions-resource.md#list) 関数は、リソースがリソース ID によって参照されているときは、暗黙的な依存関係を作成しません。 暗黙的な依存関係を作成するには、同じテンプレートでデプロイされるリソースの名前を渡します。

依存関係の問題が発生した場合は、リソースのデプロイ順序を把握する必要があります。 デプロイ操作の順序を確認するには、次の手順に従います。

1. リソース グループのデプロイ履歴を選択します。

   ![デプロイ履歴の選択](./media/resource-manager-not-found-errors/select-deployment.png)

2. 履歴からデプロイを選択し、 **[イベント]** を選択します。

   ![デプロイ イベントの選択](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. 各リソースのイベントの順序を調べます。 各操作の状態に注意してください。 たとえば、次の図には、並列でデプロイされた 3 つのストレージ アカウントが示されています。 3 つとも同時に開始されていることがわかります。

   ![並列デプロイ](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   次の図には、並列でデプロイされていない 3 つのストレージ アカウントが示されています。 2 つ目のストレージ アカウントは最初のストレージ アカウントに依存し、3 つ目のストレージ アカウントは 2 つ目のストレージ アカウントに依存します。 最初のストレージ アカウントの開始、受け入れ、完了の後に次のストレージ アカウントが開始されます。

   ![連続デプロイ](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>解決策 2 - 別のリソース グループからリソースを取得する

デプロイ先とは別のリソース グループにリソースが存在する場合は、[resourceId 関数](resource-group-template-functions-resource.md#resourceid)を使用して、リソースの完全修飾名を取得します。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>解決策 3 - reference 関数を確認する

[reference](resource-group-template-functions-resource.md#reference) 関数を含む式を検索してください。 指定する値は、リソースが同じテンプレート、リソース グループ、およびサブスクリプション内にあるかどうかに応じて異なります。 シナリオで必要なパラメーター値を指定していることを再確認してください。 リソースが別のリソース グループ内にある場合は、完全なリソース ID を指定します。 たとえば、別のリソース グループのストレージ アカウントを参照するには、次のコードを使用します。

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```