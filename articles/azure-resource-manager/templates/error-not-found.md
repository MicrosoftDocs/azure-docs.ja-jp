---
title: リソースが見つからないエラー
description: リソースが見つからない場合のエラーを解決する方法について説明します。 このエラーは、Azure Resource Manager テンプレートをデプロイするとき、または管理アクションを実行するときに発生する可能性があります。
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.openlocfilehash: 224af4ce0fe5053201f25d8207f4ca8cdc73e638
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84667949"
---
# <a name="resolve-resource-not-found-errors"></a>リソースが見つからないエラーを解決する

この記事では、操作中にリソースが見つからないときに発生するエラーについて説明します。 通常、このエラーは、リソースをデプロイしているときに発生します。 また、管理タスクを実行しているときに、Azure Resource Manager で必要なリソースが見つからない場合にもこのエラーは発生します。 たとえば、存在しないリソースにタグを追加しようとすると、このエラーが発生します。

## <a name="symptom"></a>症状

リソースが見つからないことを示す 2 つのエラー コードがあります。 **NotFound** エラーでは、次のような結果が返されます。

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**ResourceNotFound** エラーでは、次のような結果が返されます。

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>原因

Resource Manager でリソースのプロパティを取得する必要がありますが、お使いのサブスクリプション内でリソースを見つけることができません。

## <a name="solution-1---check-resource-properties"></a>解決策 1 - リソースのプロパティを確認する

管理タスクの実行中にこのエラーが発生した場合は、リソースに指定した値を確認してください。 次の 3 つの値を確認します。

* リソース名
* リソース グループ名
* サブスクリプション

PowerShell または Azure CLI を使用している場合は、リソースが含まれているサブスクリプションでコマンドを実行しているかどうかを確認します。 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) または [az account set](/cli/azure/account#az-account-set) を使用して、サブスクリプションを変更できます。 多くのコマンドにも、現在のコンテキストとは異なるサブスクリプションを指定できるサブスクリプション パラメーターが用意されています。

プロパティを確認するのが難しい場合は、[ポータル](https://portal.azure.com)にサインインします。 使用しようとしているリソースを検索し、リソース名、リソース グループ、およびサブスクリプションを調べます。

## <a name="solution-2---set-dependencies"></a>解決策 2 - 依存関係を設定する

テンプレートをデプロイするときにこのエラーが発生した場合は、依存関係の追加が必要になることがあります。 Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。 リソースを順番にデプロイする必要がある場合は、テンプレートで **dependsOn** 要素を使用する必要があります。 たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。 Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。 また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。 このエラーは、Web アプリに依存関係を設定することで回避します。

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

また、不要な依存関係を設定するのは望ましくありません。 不要な依存関係があると、相互に依存していないリソースを並行してデプロイすることを妨げるため、デプロイ時間が長くなります。 さらに、循環依存関係が作成されてデプロイがブロックされる恐れがあります。 [reference](template-functions-resource.md#reference) 関数および [list*](template-functions-resource.md#list) 関数では、参照されているリソースが同じテンプレートでデプロイされ、(リソース ID ではなく) 名前によって参照されている場合、そのリソースに対する暗黙的な依存関係が作成されます。 このため、**dependsOn** プロパティで指定したよりも多くの依存関係が作成されることがあります。 [resourceId](template-functions-resource.md#resourceid) 関数は暗黙的な依存関係を作成しません。また、リソースの存在を検証することもしません。 [reference](template-functions-resource.md#reference) 関数および [list*](template-functions-resource.md#list) 関数は、リソースがリソース ID によって参照されているときは、暗黙的な依存関係を作成しません。 暗黙的な依存関係を作成するには、同じテンプレートでデプロイされるリソースの名前を渡します。

依存関係の問題が発生した場合は、リソースのデプロイ順序を把握する必要があります。 デプロイ操作の順序を確認するには、次の手順に従います。

1. リソース グループのデプロイ履歴を選択します。

   ![デプロイ履歴の選択](./media/error-not-found/select-deployment.png)

2. 履歴からデプロイを選択し、 **[イベント]** を選択します。

   ![デプロイ イベントの選択](./media/error-not-found/select-deployment-events.png)

3. 各リソースのイベントの順序を調べます。 各操作の状態に注意してください。 たとえば、次の図には、並列でデプロイされた 3 つのストレージ アカウントが示されています。 3 つとも同時に開始されていることがわかります。

   ![並列デプロイ](./media/error-not-found/deployment-events-parallel.png)

   次の図には、並列でデプロイされていない 3 つのストレージ アカウントが示されています。 2 つ目のストレージ アカウントは最初のストレージ アカウントに依存し、3 つ目のストレージ アカウントは 2 つ目のストレージ アカウントに依存します。 最初のストレージ アカウントの開始、受け入れ、完了の後に次のストレージ アカウントが開始されます。

   ![連続デプロイ](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>ソリューション 3 - 外部リソースを取得する

テンプレートをデプロイするときに、別のサブスクリプションまたはリソース グループに存在するリソースを取得する必要がある場合は、[resourceId 関数](template-functions-resource.md#resourceid)を使用します。 リソースの完全修飾名を取得するには、この関数が返ります。

resourceId 関数のサブスクリプションとリソース グループのパラメーターは省略可能です。 指定しない場合は、既定で現在のサブスクリプションとリソース グループが使用されます。 別のリソース グループまたはサブスクリプション内のリソースを操作する場合は、それらの値を指定していることを確認してください。

次の例では、別のリソース グループに存在するリソースのリソース ID を取得します。

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>解決策 4 - リソースからマネージド ID を取得する

[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を暗黙的に作成するリソースをデプロイする場合は、そのリソースがデプロイされるまで待機してから、マネージド ID の値を取得する必要があります。 マネージ ID 名を [reference](template-functions-resource.md#reference) 関数に渡すと、Resource Manager はリソースと ID がデプロイされる前に参照の解決を試みます。 代わりに、ID が適用されるリソースの名前を渡します。 この方法では、Resource Manager が参照関数を解決する前に、リソースとマネージド ID が確実にデプロイされます。

Reference 関数では、`Full` を使用して、マネージド ID を含むすべてのプロパティを取得します。

パターンは次のとおりです。

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>, <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> 次のパターンは使用しません。
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> テンプレートが失敗します。

たとえば、仮想マシンに適用されるマネージド ID のプリンシパル ID を取得するには、次を使用します。

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

または、仮想マシン スケール セットに適用されるマネージド ID のテナント ID を取得するには、次を使用します。

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>ソリューション 5 - 関数を確認する

テンプレートをデプロイするときに、[reference](template-functions-resource.md#reference) または [listKeys](template-functions-resource.md#listkeys) 関数を使用する式を探します。 指定する値は、リソースが同じテンプレート、リソース グループ、およびサブスクリプション内にあるかどうかに応じて異なります。 シナリオに必要なパラメーター値を指定していることを確認してください。 リソースが別のリソース グループ内にある場合は、完全なリソース ID を指定します。 たとえば、別のリソース グループのストレージ アカウントを参照するには、次のコードを使用します。

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```