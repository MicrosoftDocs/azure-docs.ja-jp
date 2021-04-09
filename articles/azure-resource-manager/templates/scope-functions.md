---
title: スコープが指定されたデプロイにおけるテンプレート関数
description: スコープが指定されたデプロイにおけるテンプレート関数の解決方法について説明します。 スコープには、テナント、管理グループ、サブスクリプション、リソース グループが含まれます。
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492095"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>デプロイ スコープにおける ARM テンプレートの関数

Azure Resource Manager テンプレート (ARM テンプレート) を使用すると、リソース グループ、サブスクリプション、管理グループ、またはテナントにデプロイできます。 一般に、[ARM テンプレート関数](template-functions.md)は、すべてのスコープで同じように機能します。 この記事では、スコープによって異なる一部の関数について説明します。

## <a name="supported-functions"></a>サポートされる関数

異なるスコープにデプロイする場合に考慮する必要がある事項を以下に示します。

* [resourceGroup ()](template-functions-resource.md#resourcegroup) 関数は、リソース グループへのデプロイ用に **サポートされています**。
* [subscription()](template-functions-resource.md#subscription) 関数は、リソース グループへのデプロイとサブスクリプションへのデプロイ用に **サポートされています**。
* [reference()](template-functions-resource.md#reference) および [list()](template-functions-resource.md#list) の各関数は、すべてのスコープ用に **サポートされています**。
* リソース グループにデプロイされたリソースの ID を取得するには、[resourceId ()](template-functions-resource.md#resourceid) を使用します。

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* サブスクリプションにデプロイされたリソースの ID を取得するには、[subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid) 関数を使用します。

  たとえば、サブスクリプションにデプロイされているポリシー定義のリソース ID を取得するには、次のように使用します。

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* 管理グループの拡張機能として実装されているリソースに対しては、[extensionResourceId](template-functions-resource.md#extensionresourceid) 関数を使用します。 管理グループにデプロイされているカスタム ポリシー定義は、管理グループの拡張機能です。

  管理グループ レベルでカスタム ポリシー定義のリソース ID を取得するには、次を使用します。

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* テナントにデプロイされているリソースの ID を取得するには、[tenantResourceId()](template-functions-resource.md#tenantresourceid) 関数を使用します。 組み込みのポリシー定義は、テナント レベルのリソースです。 組み込みポリシーを管理グループ レベルで割り当てる場合は、tenantResourceId 関数を使用します。

  組み込みのポリシー定義のリソース ID を取得するには、次を使用します。

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>スコープにおける関数の解決

複数のスコープにデプロイする場合、[resourceGroup()](template-functions-resource.md#resourcegroup) および [subscription()](template-functions-resource.md#subscription) 関数は、テンプレートをどのように指定するかに基づいて異なった方法で解決されます。 外部テンプレートにリンクする場合、これらの関数は、常にそのテンプレートのスコープに解決されます。 親テンプレート内でテンプレートを入れ子にする場合は、`expressionEvaluationOptions` プロパティを使用して、これらの関数が親テンプレートと入れ子になったテンプレートのどちらのリソース グループおよびサブスクリプションに解決されるかを指定します。 入れ子になったテンプレートのスコープに解決されるようにするには、このプロパティを `inner` に設定します。 親テンプレートのスコープに解決されるようにするには、このプロパティを `outer` に設定します。

次の表は、これらの関数が親と埋め込みのどちらのリソース グループおよびサブスクリプションに解決されるかを示しています。

| テンプレートの種類 | Scope | 解像度 |
| ------------- | ----- | ---------- |
| 入れ子        | outer (既定) | 親リソース グループ |
| 入れ子        | inner | サブ リソース グループ |
| リンク済み        | 該当なし   | サブ リソース グループ |

次の[テンプレートの例](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)は、次のものを示しています。

* 既定の (outer) スコープを持つ入れ子になったテンプレート
* inner スコープを持つ入れ子になったテンプレート
* リンク済みテンプレート

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

前のテンプレートをテストし、その結果を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前の例からの出力は次のようになります。

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

前の例からの出力は次のようになります。

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>次のステップ

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[SAS トークンを使用してプライベート ARM テンプレートをデプロイする](secure-template-with-sas-token.md)」を参照してください。
