---
title: 拡張リソースの種類のスコープ
description: 拡張リソースの種類を展開するときに scope プロパティを使用する方法について説明します。
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ce08ca951e24c1c0a5450052cf814a68888837c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492163"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>ARM テンプレートで拡張リソースのスコープを設定する

拡張リソースは、別のリソースを変更するリソースです。 たとえば、リソースにロールを割り当てることができます。 ロールの割り当ては、拡張リソースの種類の 1 つです。

拡張リソースの種類の完全な一覧については、「[他のリソースの機能を拡張するリソースの種類](../management/extension-resource-types.md)」を参照してください。

この記事では、Azure Resource Manager テンプレート (ARM テンプレート) を使用して展開されている場合に、拡張リソースの種類のスコープを設定する方法について説明します。 リソースに適用するときに拡張リソースで使用できる scope プロパティについて説明します。

> [!NOTE]
> スコープ プロパティは、拡張リソースの種類でのみ使用できます。 拡張の種類ではないリソースの種類に別のスコープを指定するには、入れ子になっているか、リンクされたデプロイを使用します。 詳細については、[リソース グループへのデプロイ](deploy-to-resource-group.md)、[サブスクリプションのデプロイ](deploy-to-subscription.md)、[管理グループへのデプロイ](deploy-to-management-group.md)、[テナントのデプロイ](deploy-to-tenant.md)に関するページを参照してください。

## <a name="apply-at-deployment-scope"></a>展開スコープで適用する

ターゲット展開スコープで拡張リソースの種類を適用するには、他のリソースの種類と同様に、リソースをテンプレートに追加します。 利用可能なスコープは、[リソース グループ](deploy-to-resource-group.md)、[サブスクリプション](deploy-to-subscription.md)、[管理グループ](deploy-to-management-group.md)、[テナント](deploy-to-tenant.md)です。 展開スコープで、そのリソースの種類がサポートされている必要があります。

次のテンプレートによって、ロックが展開されます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

リソース グループに展開すると、リソース グループがロックされます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

次の例では、ロールを割り当てます。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

サブスクリプションに展開すると、サブスクリプションにロールが割り当てられます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>リソースに適用

リソースに拡張リソースを適用するには、`scope` プロパティを使用します。 scope プロパティを、拡張を追加するリソースの名前に設定します。 scope プロパティは、拡張リソースの種類のルート プロパティです。

次の例では、ストレージ アカウントを作成し、それにロールを適用します。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>次のステップ

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](template-syntax.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[SAS トークンを使用してプライベート ARM テンプレートをデプロイする](secure-template-with-sas-token.md)」を参照してください。
