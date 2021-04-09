---
title: イニシアチブ定義の規制コンプライアンス
description: イニシアチブ定義を使用して、アクセスの制御、構成管理などの規制ドメイン別にポリシーをグループ化する方法について説明します。
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 15b03b5a40cba1290859b02883df70e3df54c5cf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89645534"
---
# <a name="regulatory-compliance-in-azure-policy"></a>Azure Policy の規制コンプライアンス

Azure Policy の規制コンプライアンスには、責任 (_顧客_、_Microsoft_、_共有_) に基づく **コントロール** および **コンプライアンス ドメイン** の一覧を表示するための組み込みのイニシアチブ定義が用意されています。
Microsoft が責任を持つコントロールについては、サード パーティの証明に基づく監査結果の詳細と、そのコンプライアンスを実現するための取り組みの詳細を提供します。
Microsoft が責任を持つコントロールは、`type`[静的](./definition-structure.md#type)です。

> [!NOTE]
> 規制コンプライアンスはプレビュー機能です。 更新された組み込みの場合、イニシアチブ コントロールは対応するコンプライアンス基準にマップされます。 既存のコンプライアンス標準イニシアチブは、規制コンプライアンスをサポートするために更新されています。

## <a name="regulatory-compliance-defined"></a>規制コンプライアンスの定義

規制コンプライアンスは、イニシアチブ定義の[グループ化](./initiative-definition-structure.md#policy-definition-groups)に基づいて構築されています。 組み込みの場合、イニシアチブ定義の各グループは、名前 (**コントロール**)、カテゴリ (**コンプライアンス ドメイン**) を定義し、**コントロール** に関する情報を持つ [policyMetadata](./initiative-definition-structure.md#metadata-objects) オブジェクトへの参照を提供します。 規制コンプライアンスのイニシアチブ定義では、`category` プロパティを **規制コンプライアンス** に設定する必要があります。 それ以外の標準的なイニシアチブ定義として、規制コンプライアンス イニシアチブでは、動的な割り当てを作成するための[パラメーター](./initiative-definition-structure.md#parameters)がサポートされています。

顧客は、独自の規制コンプライアンス イニシアチブを作成できます。 これらの定義は、オリジナルでも、既存の組み込み定義からのコピーでも問題ありません。 組み込みの規制コンプライアンス イニシアチブの定義を参照として使用する場合は、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance)で規制コンプライアンスの定義のソースを監視することをお勧めします。

カスタムの規制コンプライアンス イニシアチブを Azure Security Center ダッシュボードにリンクするには、[Azure Security Center - カスタムセキュリティポリシーの使用](../../../security-center/custom-security-policies.md)に関するページを参照してください。

## <a name="regulatory-compliance-in-portal"></a>ポータルの規制コンプライアンス

[グループ](./initiative-definition-structure.md#policy-definition-groups)を使用してイニシアチブ定義を作成した場合、ポータルにあるそのイニシアチブの **コンプライアンス** 詳細ページに追加情報が表示されます。 

新しいタブ **[コントロール]** がページに追加されます。 フィルター処理は **コンプライアンス ドメイン** ごとに使用でき、ポリシー定義は **policyMetadata** オブジェクトの `title` フィールドによってグループ化されます。 各行は **コントロール** を表します。コンプライアンスの状態、所属する **コンプライアンス ドメイン**、責任情報、およびその **コントロール** を構成する非準拠ポリシー定義と準拠ポリシー定義の数を示します。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-overview.png" alt-text="準拠しているコントロールと非準拠のコントロールを示す NIST SP 800-53 R4 の組み込み定義に関する法令遵守の概要のスクリーンショット。":::

**コントロール** を選択すると、そのコントロールに関する詳細のページが開きます。 **概要** には、`description` と `requirements` の情報が含まれています。 **[ポリシー]** タブには、この **コントロール** に寄与する、イニシアチブに含まれる個々のポリシー定義がすべて含まれています。 **[リソース コンプライアンス]** タブには、現在表示されている **コントロール** のメンバー ポリシーによって評価される各リソースの詳細なビューが表示されます。

> [!NOTE]
> **Microsoft による管理** の評価タイプは、[静的](./definition-structure.md#type)ポリシー定義 `type` です。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-policies.png" alt-text="NIST SP 800-53 R4 組み込み定義の境界保護制御に関する規制遵守の詳細のスクリーンショット。":::

同じ **コントロール** のページで、 **[リソース コンプライアンス]** タブに変更すると、この **コントロール** のポリシー定義に含まれるすべてのリソースが表示されます。 フィルターは、名前または ID、コンプライアンスの状態、リソースの種類、場所に対して使用できます。

:::image type="content" source="../media/regulatory-compliance/regulatory-compliance-resources.png" alt-text="NIST SP 800-53 R4 組み込み定義の境界保護制御に対するリソースのコンプライアンスのスクリーンショット。":::

## <a name="regulatory-compliance-in-sdk"></a>SDK の規制コンプライアンス

イニシアチブ定義、評価スキャン レコード、イベント、およびポリシー状態で規制コンプライアンスが有効になっている場合、SDK はそれぞれの追加のプロパティを返します。 これらの追加のプロパティは、コンプライアンスの状態ごとにグループ化され、各状態にあるグループの数に関する情報を提供します。

次のコードは、`summarize` 呼び出しにより追加された結果の例です。

```json
"policyGroupDetails": [{
        "complianceState": "noncompliant",
        "count": 4
    },
    {
        "complianceState": "compliant",
        "count": 76
    }
]
```

## <a name="next-steps"></a>次のステップ

- [イニシアチブ定義の構造](./initiative-definition-structure.md)を参照してください
- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Policy の効果について](./effects.md)」を確認します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
