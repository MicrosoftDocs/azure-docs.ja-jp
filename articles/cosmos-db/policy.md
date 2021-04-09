---
title: Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する
description: Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する方法について説明します。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080653"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Azure Policy を使用して Azure Cosmos DB リソースのガバナンスとコントロールを実装する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[Azure Policy](../governance/policy/overview.md) は、組織のガバナンス標準の実施、リソースのコンプライアンスの評価、自動修復の実装を支援します。 一般的なユースケースには、セキュリティ、コスト管理、構成の一貫性などがあります。

Azure Policy には組み込みポリシー定義が用意されています。 組み込みポリシー定義では対応できないシナリオには、カスタムのポリシー定義を作成できます。 詳細については、[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

> [!IMPORTANT]
> Azure Policy は、Azure サービスのリソース プロバイダー レベルで適用されます。 Cosmos DB SDK を使用すると、Cosmos DB のリソース プロバイダーをバイパスするデータベース、コンテナー、およびスループット リソースに対してほとんどの管理操作を実行できるため、Azure Policy を使用して作成されたポリシーはすべて無視されます。 ポリシーの適用を確実にするには、[Azure Cosmos DB SDK からの変更の防止](role-based-access-control.md#prevent-sdk-changes)に関する記事を参照してください。

## <a name="assign-a-built-in-policy-definition"></a>組み込みポリシー定義の割り当て

ポリシー定義には、リソースのコンプライアンス条件と、条件が満たされた場合に実行する効果が記述されます。 ポリシー "_割り当て_" は、ポリシー "_定義_" から作成されます。 Azure Cosmos DB リソースには、組み込みまたはカスタムのポリシー定義を使用できます。 ポリシー割り当ては、スコープが Azure 管理グループ、Azure サブスクリプション、リソース グループのいずれかに設定され、選択されたスコープ内のリソースに適用されます。 必要に応じて、スコープから特定のリソースを除外できます。

ポリシー割り当ては、[Azure portal](../governance/policy/assign-policy-portal.md)、[Azure PowerShell](../governance/policy/assign-policy-powershell.md)、[Azure CLI](../governance/policy/assign-policy-azurecli.md)、または [ARM テンプレート](../governance/policy/assign-policy-template.md)で作成できます。

Azure Cosmos DB に対して組み込みポリシー定義からポリシー割り当てを作成するには、[Azure portal でのポリシー割り当ての作成](../governance/policy/assign-policy-portal.md)に関する記事の手順に従ってください。

ポリシー定義を選択する手順で、[検索] フィールドに「`Cosmos DB`」と入力して、使用可能な組み込みポリシー定義の一覧をフィルター処理します。 使用可能な組み込みポリシー定義の 1 つを選択し、 **[選択]** を選んでポリシー割り当ての作成を進めます。

> [!TIP]
> **[Available Definitions]\(使用可能な定義\)** ペインに表示される組み込みポリシー定義名を、Azure PowerShell、Azure CLI、または ARM テンプレートで使用してポリシー割り当てを作成することもできます。

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Azure Cosmos DB の組み込みポリシー定義の検索":::

## <a name="create-a-custom-policy-definition"></a>カスタム ポリシー定義の作成

組み込みポリシーで対応できない特定のシナリオには、[カスタム ポリシー定義](../governance/policy/tutorials/create-custom-policy-definition.md)を作成できます。 この後に、カスタム ポリシー "_定義_" からポリシー "_割り当て_" を作成します。

### <a name="property-types-and-property-aliases-in-policy-rules"></a>ポリシー ルールのプロパティの種類とプロパティ エイリアス

[カスタム ポリシー定義の手順](../governance/policy/tutorials/create-custom-policy-definition.md)に従って、ポリシー ルールの作成に必要なリソース プロパティとプロパティ エイリアスを特定します。

Azure Cosmos DB 固有のプロパティ エイリアスを特定するには、カスタム ポリシー定義の手順の記事に示されているいずれかの方法で名前空間 `Microsoft.DocumentDB` を使用します。

#### <a name="use-the-azure-cli"></a>Azure CLI を使用する場合:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Azure PowerShell を使用する場合:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

これらのコマンドによって、Azure Cosmos DB プロパティのプロパティ エイリアス名の一覧が出力されます。 この出力の抜粋を次に示します。

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

[カスタム ポリシー定義のルール](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)では、これらのプロパティ エイリアス名をどれでも使用できます。

次に示すのは、Azure Cosmos DB アカウントが複数の書き込み場所で構成されているかどうかを確認するポリシー定義の例です。 カスタム ポリシー定義には 2 つのルールが含まれています。1 つは特定の種類のプロパティ エイリアスをチェックするルールで、もう 1 つはその種類の特定のプロパティをチェックするルールです。この場合は、複数の書き込み場所の設定を格納するフィールドです。 どちらのルールもエイリアス名を使用します。

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

組み込みポリシー定義を使用する場合と同様に、カスタム ポリシー定義を使用してポリシー割り当てを作成することができます。

## <a name="policy-compliance"></a>ポリシーのコンプライアンス

ポリシー割り当てを作成すると、Azure Policy によって割り当てのスコープ内のリソースが評価されます。 ポリシーに対する各リソースの "_コンプライアンス_" が評価されます。 準拠していないリソースには、ポリシーで指定されている "_効果_" が適用されます。

コンプライアンスの結果と修復の詳細は、[Azure portal](../governance/policy/how-to/get-compliance-data.md#portal)、[Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line)、または [Azure Monitor ログ](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs)で確認できます。

次のスクリーンショットは、2 つのポリシー割り当ての例を示しています。

一方の割り当ては組み込みポリシー定義に基づいており、Azure Cosmos DB リソースが許可された Azure リージョンのみにデプロイされているかをチェックします。 リソース コンプライアンスは、スコープ内リソースのポリシー評価結果 (準拠または非準拠) を表示します。

もう一方の割り当ては、カスタム ポリシー定義に基づいています。 この割り当てでは、Cosmos DB アカウントが複数の書き込み場所で構成されていることを確認します。

ポリシー割り当てが展開されると、コンプライアンス ダッシュボードに評価結果が表示されます。 これには、ポリシー割り当てを展開してから最大で 30 分かかる場合があることに注意してください。 また、ポリシーの割り当てを作成した直後に、[ポリシー評価スキャンをオンデマンドで開始する](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan)こともできます。

スクリーンショットには、以下のスコープ内 Azure Cosmos DB アカウントのコンプライアンス評価結果が示されています。

- 2 つのアカウントのうち、Virtual Network (VNet) フィルタリングを構成する必要があるポリシーに準拠しているのは 0 個です。
- 2 つのアカウントのうち、アカウントを複数の書き込み場所で構成する必要があるポリシーに準拠しているのは 0 個です。
- 2 つのアカウントのうち、許可されている Azure リージョンにリソースがデプロイされたポリシーに準拠しているのは 0 個です。

:::image type="content" source="./media/policy/compliance.png" alt-text="リストされている Azure Policy の割り当てに関するコンプライアンス結果":::

準拠していないリソースを修復するには、[Azure Policy でのリソースの修復方法](../governance/policy/how-to/remediate-resources.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB のカスタム ポリシー定義のサンプルを確認](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)します。これには、上に示した複数の書き込み場所と VNet フィルターのポリシーが含まれています。
- [Azure portal でポリシー割り当てを作成する](../governance/policy/assign-policy-portal.md)
- [Azure Cosmos DB 用の Azure Policy 組み込みポリシー定義を確認する](./policy-reference.md)