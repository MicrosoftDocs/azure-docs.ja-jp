---
title: Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール
description: Azure Cognitive Search に対して使用できる Azure Policy 規制コンプライアンス コントロールの一覧を示します。 これらの組み込みポリシー定義により、Azure リソースのコンプライアンスを管理するための一般的な方法が提供されます。
ms.date: 03/17/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: b94ca010ef8b2ab4d8fb73acf1e686835453d22c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604350"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Azure Cognitive Search 用の Azure Policy 規制コンプライアンス コントロール

[Azure Policy](../governance/policy/overview.md) を使用して [Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)の推奨事項を適用している場合は、準拠していないサービスを識別して修正するためにポリシーを作成できることは、既にご存じかもしれません。 このようなポリシーは、カスタムの場合もあれば、よく知られたベスト プラクティスのためのコンプライアンス条件と適切なソリューションを提供する組み込み定義に基づく場合もあります。

Azure Cognitive Search の場合は、現在、次に示す組み込み定義が 1 つあり、ポリシーの割り当てで使用できます。 組み込みは、ログ記録と監視のためのものです。 [作成するポリシー](../governance/policy/assign-policy-portal.md)でこの組み込み定義を使用すると、システムによって[診断ログ](search-monitor-logs.md)のない検索サービスがスキャンされ、それに応じて有効にされます。

[Azure Policy の規制コンプライアンス](../governance/policy/concepts/regulatory-compliance.md)により、さまざまなコンプライアンス基準に関連する **コンプライアンス ドメイン** および **セキュリティ コントロール** に対して、"_組み込み_" と呼ばれる、Microsoft によって作成および管理されるイニシアチブ定義が提供されます。 このページでは、Azure Cognitive Search 用の **コンプライアンス ドメイン** と **セキュリティ コントロール** の一覧を示します。 **セキュリティ コントロール** の組み込みを個別に割り当てることで、Azure リソースを特定の標準に準拠させることができます。

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Policy の規制コンプライアンス](../governance/policy/concepts/regulatory-compliance.md)の詳細を確認します。
- [Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のビルトインを参照します。
