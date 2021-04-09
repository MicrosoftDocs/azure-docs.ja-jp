---
title: パターン:イニシアティブを使用したグループ ポリシー定義
description: この Azure Policy のパターンでは、ポリシー定義をイニシアティブにグループ化する方法の例を示します。
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: aa09cafe636a4665dba6a2e746c13b95ff304895
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072919"
---
# <a name="azure-policy-pattern-group-policy-definitions"></a>Azure Policy パターン: グループ ポリシー定義

イニシアティブは、ポリシー定義のグループです。 関連するポリシー定義を 1 つのオブジェクトにグループ化することによって、1 つの割り当てを作成して、複数の割り当てを設定できます。

## <a name="sample-initiative-definition"></a>イニシアティブの定義の例

このイニシアティブでは、2 つのポリシー定義を展開します。それぞれで **tagName** と **tagValue** のパラメーターを使用します。 イニシアティブ自体には、**costCenterValue** と **productNameValue** という 2 つのパラメーターがあります。
これらのイニシアティブ パラメーターはそれぞれ、個々のグループ化されたポリシー定義に提供されます。 この設計では、既存のポリシー定義の再利用を最大化しながら、それらを実装するために作成される割り当ての数を必要に応じて制限します。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json":::

### <a name="explanation"></a>説明

#### <a name="initiative-parameters"></a>イニシアチブ パラメーター

イニシアティブでは、グループ化されたポリシー定義に渡される独自のパラメーターを定義できます。
この例では、**costCenterValue** と **productNameValue** は両方ともイニシアティブ パラメーターとして定義されています。 値は、イニシアティブが割り当てられるときに指定されます。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="5-18":::

#### <a name="includes-policy-definitions"></a>ポリシー定義を含める

ポリシー定義でパラメーターを受け取る場合は、含まれている各ポリシー定義で **policyDefinitionId** と **parameters** 配列を指定する必要があります。 次のスニペットでは、含まれているポリシー定義は、**tagName** と **tagValue** の 2 つのパラメーターを受け取ります。 **tagName** はリテラルを使用して定義されていますが、**tagValue** は、イニシアティブによって定義されたパラメーター **costCenterValue** を使用します。 この値のパススルーによって、再利用が向上します。

:::code language="json" source="~/policy-templates/patterns/pattern-group-with-initiative.json" range="30-40":::

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。