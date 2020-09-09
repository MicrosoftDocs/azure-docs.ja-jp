---
title: FedRAMP (中) ブループリント サンプルの概要
description: FedRAMP (中) ブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定の FedRAMP (中) コントロールを評価するのに役立ちます。
ms.date: 07/31/2020
ms.topic: sample
ms.openlocfilehash: 6d0ec128a09f3426dbcec63353f04395ed92996d
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87495796"
---
# <a name="overview-of-the-fedramp-moderate-blueprint-sample"></a>FedRAMP (中) ブループリント サンプルの概要

FedRAMP (中) ブループリント サンプルでは、特定の FedRAMP (中) コントロールの評価に役立つ、[Azure Policy](../../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、FedRAMP (中) コントロールの実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。

## <a name="control-mapping"></a>コントロール マッピング

コントロール マッピングのセクションでは、このブループリント内に含まれるポリシーについてと、それらのポリシーが FedRAMP (中) のさまざまなコントロールにどのように対応するかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシーに違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../../policy/overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

FedRAMP (中) ブループリント サンプルの概要を確認しました。 次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [FedRAMP (中) ブループリント - コントロール マッピング](./control-mapping.md)
> [FedRAMP (中) ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。