---
title: Azure Policy を使用したコンプライアンス
description: Azure Policy で組み込みポリシーを割り当て、Azure コンテナー レジストリのコンプライアンスを監査します
ms.topic: article
ms.date: 06/11/2020
ms.openlocfilehash: 26c56616bcc411063d0ebfda28ba1e6fdf44c7fb
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291019"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Azure Policy を使用した Azure コンテナー レジストリのコンプライアンスの監査

[Azure Policy](../governance/policy/overview.md) は、ポリシーの作成、割り当て、管理に使用する Azure のサービスです。 これらのポリシーは、リソースにさまざまなルールと効果を適用して、それらのリソースが会社の標準とサービス レベル アグリーメントに準拠した状態に保たれるようにします。

この記事では、Azure Container Registry の組み込みポリシーについて説明します。 これらのポリシーを使用し、新しいレジストリと既存のレジストリのコンプライアンスを監査します。

Azure Policy を使用するのに料金は一切かかりません。

## <a name="built-in-policy-definitions"></a>組み込みのポリシー定義

次の組み込みのポリシー定義は Azure Container Registry に固有となります。

[!INCLUDE [azure-policy-reference-policies-container-registry](../../includes/policy/reference/bycat/policies-container-registry.md)]

組み込みのネットワーク ポリシー定義(「[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78)」)も参照してください。

## <a name="assign-policies"></a>ポリシーの割り当て

* [Azure portal](../governance/policy/assign-policy-portal.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、[Resource Manager テンプレート](../governance/policy/assign-policy-template.md)または Azure Policy SDK を使用してポリシーを割り当てます。
* ポリシーの割り当て範囲をリソース グループ、サブスクリプション、または [Azure 管理グループ](../governance/management-groups/overview.md)に設定します。 コンテナー レジストリのポリシー割り当ては、範囲内の既存のコンテナー レジストリと新しいコンテナー レジストリに適用されます。
* [ポリシー適用](../governance/policy/concepts/assignment-structure.md#enforcement-mode)の有効と無効はいつでも切り替えられます。

> [!NOTE]
> ポリシーを割り当てるか、更新した後、定義されている範囲内のリソースに割り当てが適用されるまで少し時間がかかります。 詳細は、[ポリシー評価トリガー](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)を参照してください。

## <a name="review-policy-compliance"></a>ポリシーのコンプライアンスを確認する

Azure portal、Azure コマンドライン ツール、または Azure Policy SDK を使用し、ポリシー割り当てによって生成されたコンプライアンス情報にアクセスします。 詳細については、「[Azure リソースのコンプライアンス データを取得する](../governance/policy/how-to/get-compliance-data.md)」を参照してください。

リソースのコンプライアンス違反には多くの理由が考えられます。 理由や原因となった変更を特定する方法については、「[コンプライアンス違反の原因の特定](../governance/policy/how-to/determine-non-compliance.md)」をご覧ください。

### <a name="policy-compliance-in-the-portal"></a>ポータルのポリシー コンプライアンス:

1. **[すべてのサービス]** を選択し、**ポリシー**を検索します。
1. **[コンプライアンス]** を選択します。
1. フィルターを使用し、コンプライアンスの状態を限定するか、ポリシーを検索します。

    ![ポータルのポリシーのコンプライアンス](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. ポリシーを選択し、集約コンプライアンスの詳細とイベントを確認します。 必要であれば、リソース コンプライアンスに対して特定のレジストリを選択します。

### <a name="policy-compliance-in-the-azure-cli"></a>Azure CLI のポリシー コンプライアンス

Azure CLI を利用してコンプライアンス データを取得することもできます。 たとえば、CLI で [az policy assignment list](/cli/azure/policy/assignment#az-policy-assignment-list) コマンドを使用し、適用されている Azure Container Registry ポリシーのポリシー ID を取得します。

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

サンプル出力:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

次に [az policy state list](/cli/azure/policy/state#az-policy-state-list) を実行すると、特定のポリシー ID の下にあるすべてのリソースに関して JSON で書式設定されたコンプライアンスの状態が返されます。

```azurecli
az policy state list \
  --resource <policyID>
```

あるいは [az policy state list](/cli/azure/policy/state#az-policy-state-list) を実行すると、*myregistry* など、特定のレジストリ リソースの JSON で書式設定されたコンプライアンスの状態が返されます。

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>次のステップ

* Azure Policy の[定義](../governance/policy/concepts/definition-structure.md)と[効果](../governance/policy/concepts/effects.md)について知る。

* [カスタム ポリシー定義](../governance/policy/tutorials/create-custom-policy-definition.md)を作成する。

* Azure の[ガバナンス機能](../governance/index.yml)について知る。
