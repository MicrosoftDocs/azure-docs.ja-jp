---
title: Azure Resource Manager テンプレートの出力
description: Azure Resource Manager テンプレートで出力値を定義する方法について説明します。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 3805e0bb86772836ba4a1c91661477f29d5e0f70
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383994"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの出力

この記事では、Azure Resource Manager テンプレートで出力値を定義する方法について説明します。 デプロイされたリソースから値を返す必要がある場合に出力を使用します。

## <a name="define-output-values"></a>出力値の定義

次の例は、パブリック IP アドレスのリソース ID を返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>条件付き出力

outputs セクションでは、値を条件付きで返すことができます。 通常、リソースを[条件付きでデプロイ](conditional-resource-deployment.md)した場合に出力で条件を使用します。 次の例は、新しくデプロイされたかどうかに基づいて、パブリック IP アドレスのリソース ID を条件付きで返す方法を示しています。

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

条件付き出力の簡単な例については、[条件付き出力テンプレート](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)に関する説明をご覧ください。

## <a name="linked-templates"></a>リンク済みテンプレート

リンク済みテンプレートから出力値を取得するには、親テンプレートで [reference](resource-group-template-functions-resource.md#reference) 関数を使用します。 親テンプレートの構文は次のとおりです。

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

リンクされたテンプレートから出力プロパティを取得する場合、プロパティ名にダッシュを含めることはできません。

次の例では、リンクされているテンプレートから値を取得することによってロード バランサーの IP アドレスを設定する方法を示します。

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

[入れ子になったテンプレート](resource-group-linked-templates.md#link-or-nest-a-template) の出力セクションでは `reference` 関数を使用できません。 入れ子になったテンプレート内のデプロイされたリソースの値を返すには、入れ子になったテンプレートをリンク済みテンプレートに変換します。

## <a name="get-output-values"></a>出力値の取得

デプロイが成功すると、出力値はデプロイの結果で自動的に返されます。

デプロイ履歴から出力値を取得するには、スクリプトを使用できます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>サンプル テンプレート

次の例は、出力を使用するためのシナリオを示しています。

|Template  |説明  |
|---------|---------|
|[Copy variables](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | 複合変数を作成し、それらの値を出力します。 リソースはデプロイしません。 |
|[パブリック IP アドレス](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | パブリック IP アドレスを作成し、リソース ID を出力します。 |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | 前述のテンプレートにリンクします。 ロード バランサーの作成時に出力内のリソース ID を使用します。 |

## <a name="next-steps"></a>次の手順

* 出力に使用できるプロパティの詳細については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」をご覧ください。
* 出力の作成に関する推奨事項については、[ベスト プラクティス - 出力](template-best-practices.md#outputs)に関する記事をご覧ください。
