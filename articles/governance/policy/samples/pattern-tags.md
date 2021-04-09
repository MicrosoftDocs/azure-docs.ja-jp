---
title: パターン:ポリシー定義でのタグの使用
description: この Azure Policy パターンでは、パラメーター化されたタグを追加したり、ポリシー定義内のリソース グループからタグを継承したりする方法の例が示されています。
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 3016fc7889f68fd13e993c67ca645a4af055c651
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88545558"
---
# <a name="azure-policy-pattern-tags"></a>Azure Policy パターン: タグ

[タグ](../../..//azure-resource-manager/management/tag-resources.md)は、ご利用の Azure リソースを管理、整理、および統括する上で重要なパーツです。 Azure Policy では、[modify](../concepts/effects.md#modify) 効果と[修復タスク](../how-to/remediate-resources.md)を使用して、新規および既存のリソース上でタグを広範に構成することができます。

## <a name="sample-1-parameterize-tags"></a>サンプル 1:タグのパラメーター化

このポリシー定義では、**tagName** と **tagValue** という 2 つのパラメーターを使用して、ポリシー割り当てによってリソース グループで検索される内容を設定します。 この形式により、ポリシー定義で任意の数のタグ名とタグ値の組み合わせを使用できるようになりますが、1 つのポリシー定義のみを維持してください。

> [!NOTE]
> このポリシー定義パターンは、[パターン: パラメーター - サンプル #1](./pattern-parameters.md#sample-1-string-parameters) に示したものと似ていますが、このサンプルでは **mode** として _All_ を使用し、リソース グループをターゲットとします。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json":::

### <a name="sample-1-explanation"></a>サンプル 1:説明

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="2-8" highlight="3":::

このサンプルでは、リソース グループをターゲットとしているため、**mode** は _All_ に設定されます。 ほとんどの場合、タグを操作するときは、**mode** を _Indexed_ に設定する必要があります。 詳細については、[mode](../concepts/definition-structure.md#resource-manager-modes) に関するページを参照してください。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="26-36" highlight="7-8":::

ポリシー定義のこの部分では、`concat` を使用して、パラメーター化された **tagName** パラメーターと `tags['name']` 形式を組み合わせることで、**field** に、そのタグのパラメーター **tagValue** を評価するように指示します。
**notEquals** が使用されているため、**tags\[tagName\]** が **tagValue** と等しくない場合、**modify** 効果がトリガーされます。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-1.json" range="43-47" highlight="3-4":::

ここでは、パラメーター化されたタグ値を使用する場合と同じ形式が **addOrReplace** 操作で使用されます。これにより、タグの作成、または評価されたリソース グループで必要な値へのタグの更新が行われます。

## <a name="sample-2-inherit-tag-value-from-resource-group"></a>サンプル 2:リソース グループからタグ値を継承する

このポリシー定義では、パラメーター **tagName** を使用して、親リソース グループから継承するタグの値が決定されます。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json":::

### <a name="sample-2-explanation"></a>サンプル 2:説明

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="2-8" highlight="3":::

このサンプルでは、リソース グループから値を取得する場合でもリソース グループまたはサブスクリプションをターゲットとしないため、**mode** は _Indexed_ に設定されます。 詳細については、[mode](../concepts/definition-structure.md#resource-manager-modes) に関するページを参照してください。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="19-29" highlight="3-4,7-8":::

**policyRule.if** では、[サンプル #1](#sample-1-parameterize-tags) のような `concat` を使用して **tagName** の値が評価されますが、それを、親リソース グループ上の同じタグの値と比較する場合は `resourceGroup()` 関数が使用されます。 ここの 2 番目の句では、リソース グループのタグが値を持ち、null ではないことを確認します。

:::code language="json" source="~/policy-templates/patterns/pattern-tags-2.json" range="36-40" highlight="3-4":::

ここでは、リソースの **tagName** タグに割り当てられている値でも、親リソース グループから値を取得するために、`resourceGroup()` 関数が使用されます。 このようにして、親リソース グループからタグを継承することができます。 リソースを既に作成していているが、まだタグを追加していない場合は、これと同じポリシー定義と[修復タスク](../how-to/remediate-resources.md)で既存のリソースを更新することができます。

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。