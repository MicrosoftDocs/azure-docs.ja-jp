---
title: Azure Security Center 用の組み込みポリシー定義
description: Azure Security Center 用の Azure Policy 組み込みポリシー定義を一覧表示します。 これらの組み込みポリシー定義は、Azure リソースを管理するための一般的な方法を示します。
ms.date: 01/29/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 5c1e5e1c659f001f247af1b34e46303d5ccb3ae0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096950"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Azure Security Center 用の Azure Policy 組み込み定義

このページは、Azure Security Center に関連する [Azure Policy](../governance/policy/overview.md) 組み込みポリシー定義のインデックスです。 次のポリシー定義のグループを使用できます。

- [initiatives](#azure-security-center-initiatives) グループは、"Security Center" カテゴリ内の Azure Policy イニシアチブ定義のリストです。
- [default initiative](#azure-security-center-initiatives) グループは、Security Center の既定のイニシアチブ ([Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)) の一部であるすべての Azure Policy 定義のリストです。 この Microsoft が作成し広く尊重されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。
- [category](#azure-security-center-category) グループは、"Security Center" カテゴリ内のすべての Azure Policy 定義のリストです。

セキュリティ ポリシーの詳細については、「[セキュリティ ポリシーの操作](./tutorial-security-policy.md)」を参照してください。 他のサービス用の Azure Policy 組み込みについては、[Azure Policy 組み込み定義](../governance/policy/samples/built-in-policies.md)に関するページをご覧ください。

各組み込みポリシー定義の名前は、Azure portal のポリシー定義にリンクしています。 **[バージョン]** 列のリンクを使用すると、[Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のソースを表示できます。

## <a name="azure-security-center-initiatives"></a>Azure Security Center のイニシアチブ

Security Center によって監視される組み込みのイニシアチブの詳細については、次の表を参照してください。

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="azure-security-center-default-initiative"></a>Azure Security Center の既定のイニシアチブ

Security Center によって監視される組み込みのポリシーの詳細については、次の表を参照してください。

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center カテゴリ

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>次のステップ

この記事では、Security Center での Azure Policy のセキュリティ ポリシー定義について説明しました。 詳細については、以下の記事をお読みください。

- [Azure Policy GitHub リポジトリ](https://github.com/Azure/azure-policy)のビルトインを参照します。
- 「[Azure Policy の定義の構造](../governance/policy/concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../governance/policy/concepts/effects.md)」を確認します。
- [Azure Security Center 計画および運用ガイド](./security-center-planning-and-operations-guide.md):Azure Security Center で計画し、設計上の考慮事項を理解する方法について説明しています。
- [Azure Security Center でのセキュリティ ヘルスの監視](./security-center-monitoring.md):Azure リソースの正常性を監視する方法について説明しています。
- [Azure Security Center でのセキュリティの警告の管理と対応](./security-center-managing-and-responding-alerts.md): セキュリティの警告の管理と対応の方法について説明しています。
- [Azure Security Center を使用したパートナー ソリューションの監視](./security-center-partner-integration.md): パートナー ソリューションの正常性状態を監視する方法について説明しています。
- [Azure Policy](../governance/policy/overview.md):Azure リソースの監査と管理について説明しています。
