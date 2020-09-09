---
title: DoD 影響レベル 5 ブループリント サンプルの概要
description: DoD 影響レベル 5 サンプルの概要。 このブループリント サンプルは、お客様が特定の DoD 影響レベル 5 コントロールを評価するのに役立ちます。
ms.date: 06/30/2020
ms.topic: sample
ms.openlocfilehash: 0889da5f0eb9d177589d8ebc6a9a17b61fec624b
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2020
ms.locfileid: "85804375"
---
# <a name="overview-of-the-dod-impact-level-5-blueprint-sample"></a>DoD 影響レベル 5 ブループリント サンプルの概要

国防総省影響レベル 5 (DoD IL5) のブループリント サンプルでは、特定の DoD 影響レベル 5 コントロールの評価に役立つ、[Azure Policy](../../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、DoD 影響レベル 5 コントロールの実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。 DoD 影響レベル 5 の承認を満たす Azure クラウドとサービスの最新情報については、「[FedRAMP および DoD CC SRG 監査スコープ別の Azure サービス](../../../../azure-government/compliance/azure-services-in-fedramp-auditscope.md)」を参照してください。

> [!NOTE]
> このブループリント サンプルは、Azure Government から入手できます。

## <a name="control-mapping"></a>コントロール マッピング

コントロール マッピングのセクションでは、このブループリント内に含まれるポリシーと、それらのポリシーが DoD 影響レベル 5 のさまざまなコントロールにどのように対応するかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシーに違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../../policy/overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

DoD 影響レベル 5 のブループリント サンプルの概要を確認しました。 次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [DoD 影響レベル 5 のブループリント - コントロール マッピング](./control-mapping.md)
> [DoD 影響レベル 5 のブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。