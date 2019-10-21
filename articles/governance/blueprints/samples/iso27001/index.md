---
title: ISO 27001 ブループリント サンプル - 概要
description: ISO 27001 ブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定の ISO 27001 コントロールを評価するのに役立ちます。
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/22/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: ccfd782d375de9d3a39cac20b1e48e16cc40d2f7
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297393"
---
# <a name="overview-of-the-iso-27001-blueprint-sample"></a>ISO 27001 ブループリント サンプルの概要

ISO 27001 ブループリント サンプルでは、特定の ISO 27001 コントロールの評価に役立つ、[Azure Policy](../../../policy/overview.md) を使用したガバナンス ガードレールが提供されます。 このブループリントは、ISO 27001 コントロールの実施が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイするのに役立ちます。 [基本アーキテクチャ](../iso27001-shared/index.md)と [ASE および SQL ワークロード](../iso27001-ase-sql-workload/index.md)のデプロイに役立つ追加の ISO 27001 ブループリント サンプルが 2 つ使用できます。

## <a name="control-mapping"></a>コントロール マッピング

コントロール マッピングのセクションでは、このブループリント内に含まれるポリシーについてと、それらのポリシーが ISO 27001 のさまざまなコントロールにどのように対応するかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシーに違反していないかどうかを Azure Policy によって評価されます。 詳細については、[Azure Policy](../../../policy/overview.md) に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

ISO 27001 ブループリント サンプルの概要とアーキテクチャを確認しました。
次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [ISO 27001 ブループリント - コントロール マッピング](./control-mapping.md)
> [ISO 27001 ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。
