---
title: リソースのタグ付けのポリシー
description: タグの準拠を保証するために割り当てることができる Azure のポリシーについて説明します。
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89293739"
---
# <a name="assign-policies-for-tag-compliance"></a>タグの準拠のためのポリシーを割り当てる

タグ付けのルールと規則を強制するには、[Azure Policy](../../governance/policy/overview.md) を使用します。 ポリシーを作成することで、組織で必要とされるタグが存在しないサブスクリプションにリソースがデプロイされるというシナリオを回避します。 タグの適用や非準拠リソースの検索を手動で行う代わりに、デプロイ時に必要なタグを自動的に適用するポリシーを作成します。 また、新しい [Modify](../../governance/policy/concepts/effects.md#modify) 効果と[修復タスク](../../governance/policy/how-to/remediate-resources.md)を使用して、既存のリソースにタグを適用することもできるようになりました。 次のセクションでは、タグのポリシーの例を示します。

## <a name="policies"></a>ポリシー

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>次のステップ

* リソースにタグを付ける方法については、「[タグを使用した Azure リソースの整理](tag-resources.md)」を参照してください。
* すべてのリソースの種類で、タグがサポートされるわけではありません。 リソースの種類にタグを適用することができるかどうかを確認するには、[Azure リソースに対するタグのサポート](tag-support.md)に関する記事を参照してください。
