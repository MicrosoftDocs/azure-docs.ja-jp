---
title: デプロイ モード
description: Azure Resource Manager で完全デプロイ モードと増分デプロイ モードのどちらを使用するか指定する方法について説明します。
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 3f1f74c0495e0d43671712281a35a7e74fd7d821
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104888840"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager のデプロイ モード

リソースをデプロイするときには、デプロイが増分更新と完全更新のどちらであるかを指定する必要があります。 これら 2 つのモードの違いは、テンプレートにないリソース グループの既存のリソースを Resource Manager が処理する方法にあります。

いずれのモードでも、Resource Manager はテンプレートで指定されたすべてのリソースの作成を試みます。 リソースが既にリソース グループ内に存在しており、その設定が変更されていない場合、そのリソースに対しては何の操作も行われません。 リソースのプロパティ値を変更した場合、リソースはそれらの新しい値で更新されます。 既存のリソースの場所または種類を更新しようとすると、デプロイがエラーで失敗します。 その代わり、必要な場所または種類で新しいリソースをデプロイできます。

既定のモードは増分です。

## <a name="complete-mode"></a>完全モード

完全モードでは、Resource Manager はリソース グループに存在するがテンプレートに指定されていないリソースを **削除します**。

> [!NOTE]
> テンプレートを完全モードでデプロイする前に、必ず [what-if 操作](template-deploy-what-if.md)を使用してください。 What-if では、作成、削除、または変更されるリソースが示されます。 リソースを誤って削除しないようにするには、what-if を使用します。

[condition](conditional-resource-deployment.md) が false と評価されるためにデプロイされないリソースがテンプレートに含まれている場合、テンプレートをデプロイするために使用する REST API のバージョンによって結果が異なります。 2019-05-10 より前のバージョンを使用する場合、リソースは **削除されません**。 2019-05-10 以降では、リソースは **削除されます**。 最新バージョンの Azure PowerShell および Azure CLI では、リソースは削除されます。

[コピー ループ](copy-resources.md)では完全モードを使用するように注意してください。 コピー ループを解決した後でテンプレートに指定されていないリソースはすべて削除されます。

[テンプレート内の複数のリソース グループ](./deploy-to-resource-group.md)にデプロイする場合、デプロイ操作で指定されたリソース グループ内のリソースは削除対象となります。 セカンダリ リソース グループ内のリソースは削除されません。

完全モードでは、リソースの種類に応じて削除の動作が多少異なります。 親リソースは、完全モードでデプロイされたテンプレートに含まれていない場合、自動的に削除されます。 一部の子リソースは、テンプレートに含まれていなければ、自動的には削除されません。 ただし、親リソースが削除されると、これらの子リソースも削除されます。

たとえば、リソース グループに DNS ゾーン (リソースの種類は `Microsoft.Network/dnsZones`) と CNAME レコード (リソースの種類は `Microsoft.Network/dnsZones/CNAME`) が含まれるとします。DNS ゾーンは CNAME レコードの親リソースです。 テンプレートを完全モードでデプロイし、そこに DNS ゾーンを含めていない場合、DNS ゾーンと CNAME レコードの両方が削除されます。 テンプレートに DNS ゾーンだけを含め、CNAME レコードは含めていない場合、CNAME は削除されません。

リソースの種類別に削除の動作を説明した一覧については、「[Deletion of Azure resources for complete mode deployments (完全モードのデプロイでの Azure リソースの削除)](complete-mode-deletion.md)」をご覧ください。

リソース グループが[ロックされている場合](../management/lock-resources.md)、完全モードはリソースを削除しません。

> [!NOTE]
> 完全デプロイ モードがサポートされるのはルートレベルのテンプレートのみです。 [リンクされたテンプレートまたは入れ子になったテンプレート](linked-templates.md)には、増分モードを使用する必要があります。
>
> [サブスクリプション レベルのデプロイ](deploy-to-subscription.md)では、完全モードはサポートされていません。
>
> 現時点では、ポータルでは完全モードはサポートされていません。
>

## <a name="incremental-mode"></a>増分モード

増分モードでは、Resource Manager はリソース グループに存在するが、テンプレートに指定されていないリソースを **変更せず、そのまま残します**。 テンプレート内のリソースは、リソース グループに **追加されます**。

> [!NOTE]
> 既存のリソースを増分モードで再デプロイすると、すべてのプロパティが再適用されます。 **プロパティは増分的に追加されません**。 テンプレートで指定されていないプロパティは変更されないままであるという一般的な誤解があります。 特定のプロパティを指定しなかった場合、Resource Manager では、デプロイによってそれらの値が上書きされると解釈されます。 テンプレートに含まれていないプロパティは、既定値にリセットされます。 更新対象だけでなく、リソースの既定以外のすべての値を指定します。 テンプレート内のリソース定義には、常にリソースの最終的な状態が含まれています。 既存のリソースに対する部分的な更新を表すことはできません。
>
> まれに、リソースに指定したプロパティが実際に子リソースとして実装されることがあります。 たとえば、Web アプリのサイト構成値を指定すると、それらの値が子リソースの種類 `Microsoft.Web/sites/config` に実装されます。 Web アプリを再デプロイし、サイト構成値に空のオブジェクトを指定した場合、子リソースは更新されません。 ただし、新しいサイト構成値を指定すると、子リソースの種類が更新されます。

## <a name="example-result"></a>結果の例

増分モードと完全モードの違いを説明するために、次のシナリオについて考えます。

**リソース グループ** には、次が含まれます。

* リソース A
* リソース B
* リソース C

**テンプレート** には、次が含まれます。

* リソース A
* リソース B
* リソース D

**増分** モードでデプロイすると、リソース グループには次が含まれます。

* リソース A
* リソース B
* リソース C
* リソース D

**完全** モードでデプロイすると、リソース C が削除されます。 リソース グループには次が含まれます。

* リソース A
* リソース B
* リソース D

## <a name="set-deployment-mode"></a>デプロイ モードの設定

PowerShell を使用してデプロイするときにデプロイ モードを設定するには、`Mode` パラメーターを使用します。

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Azure CLI を使用してデプロイするときにデプロイ モードを設定するには、`mode` パラメーターを使用します。

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

次の例は、増分デプロイ モードに設定された、リンクされたテンプレート セットを示しています。

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>次のステップ

* Resource Manager テンプレートの作成については、「[ARM テンプレートの構造と構文について](template-syntax.md)」を参照してください。
* リソースをデプロイする方法については、「[ARM テンプレートと Azure PowerShell を使用したリソースのデプロイ](deploy-powershell.md)」を参照してください。
* リソース プロバイダーの操作を表示するには、「[Azure REST API](/rest/api/)」を参照してください。
