---
title: Azure Resource Graph の Advisor データ
description: Azure Resource Graph で Advisor データを照会します
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87057779"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Resource Graph エクスプローラーで Advisor データを照会する (Azure Resource Graph)

[Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/) に新たに Advisor リソースがオンボードされました。 これは、Advisor の推奨事項が求められる多くの大規模なカスタマー シナリオの礎となるものです。 以前は大規模に実行することができなかったシナリオのいくつかは、Resource Graph を使用すれば実現できます。その例を次に示します。
* Azure portal ですべてのサブスクリプションを対象とした複雑なクエリを実行できるようになります。
* カテゴリの種類 (信頼性、パフォーマンスなど) と影響の種類 (高、中、低) ごとにまとめられた推奨事項
* 特定の推奨事項タイプのすべての推奨事項
* 影響を受けるリソースの数 (推奨カテゴリごと)

![Azure Resource Graph エクスプローラーでの Advisor](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph における Advisor リソースの種類

[Resource Graph](../governance/resource-graph/index.yml) で利用できる Advisor リソースの種類は次のとおりです。Advisor リソースには、照会可能なリソースが 3 種類あります。 Resource Graph で照会できるようになった一連のリソースを次に示します。
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

これらのリソースの種類は、AdvisorResources という名前で新しいテーブルにリストされます。Azure portal から Resource Graph エクスプローラーでこれらのリソースを照会することもできます。


## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor の信頼性に関する推奨事項](advisor-high-availability-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のオペレーショナル エクセレンスに関する推奨事項](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
