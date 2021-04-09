---
title: Azure Security Center 用の組み込みポリシー定義
description: Azure Security Center 用の Azure Policy 組み込みポリシー定義を一覧表示します。 これらの組み込みポリシー定義は、Azure リソースを管理するための一般的な方法を示します。
ms.date: 03/17/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: d6afafef2bb09855fb50f67fcc1054043fa725ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596501"
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

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Security Center の既定のイニシアチブ (Azure セキュリティ ベンチマーク)

Security Center によって監視される組み込みのポリシーの詳細については、次の表を参照してください。

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center カテゴリ

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>次のステップ

この記事では、Security Center での Azure Policy のセキュリティ ポリシー定義について説明しました。 イニシアチブ、ポリシー、およびそれらが Security Center の推奨事項とどのように関連しているかについて詳しくは、「[セキュリティポリシー、イニシアチブ、および推奨事項とは](security-policy-concept.md)」をご覧ください。
