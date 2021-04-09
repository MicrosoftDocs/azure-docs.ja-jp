---
title: パターン:ポリシー定義内のパラメーター
description: この Azure Policy パターンの場合、ポリシー定義で文字列と配列のパラメーターを使用する方法の例と、エフェクトをパラメーター化する方法が提供されます。
ms.date: 08/17/2020
ms.topic: sample
ms.openlocfilehash: 7bb991c43a1deee39dd047aa7d814c124712fe69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89649941"
---
# <a name="azure-policy-pattern-parameters"></a>Azure Policy パターン: パラメーター

[パラメーター](../concepts/definition-structure.md#parameters)を使用してポリシー定義を動的に作成することで、必要なポリシー定義の数を減らすことができます。 パラメーターは、ポリシーを割り当てる際に定義します。 パラメーターにはあらかじめ定義された一連のプロパティがあり、そこでパラメーターとその使用方法が説明されます。

## <a name="sample-1-string-parameters"></a>サンプル 1:文字列パラメーター

このポリシー定義では、**tagName** と **tagValue** という 2 つのパラメーターを使用して、ポリシー割り当てによってリソースで検索される内容を設定します。 この形式により、ポリシー定義で任意の数のタグ名とタグ値の組み合わせを使用できるようになりますが、1 つのポリシー定義のみを維持してください。

> [!NOTE]
> **mode** "_all_" を使用して、リソース グループを操作するタグのサンプルについては、[パターン: タグ - サンプル 1](./pattern-tags.md#sample-1-parameterize-tags) を参照してください。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json":::

### <a name="sample-1-explanation"></a>サンプル 1:説明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="8-13":::

ポリシー定義のこの部分では、**tagName** パラメーターは "_文字列_" として定義され、その使用方法が説明されています。

次に、このパラメーターを **policyRule.if** ブロックで使用して、ポリシーを動的なものにします。 ここでは評価対象のフィールドを定義するために使用します。これは **tagName** の値を持つタグです。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-1.json" range="22-27" highlight="3":::

## <a name="sample-2-array-parameters"></a>サンプル 2:配列パラメーター

このポリシー定義では、1 つのパラメーター **listOfBandwidthinMbps** を使用して、Express Route 回線リソースで、帯域幅の設定が、承認された値のいずれかに構成されているかどうかを確認します。 一致しない場合、リソースの作成または更新が[拒否](../concepts/effects.md#deny)されます。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json":::

### <a name="sample-2-explanation"></a>サンプル 2:説明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="6-12":::

ポリシー定義のこの部分では、**listOfBandwidthinMbps** パラメーターは "_配列_" として定義され、その使用方法が説明されています。 "_配列_" には一致する値が複数あります。

次に、このパラメーターを **policyRule.if** ブロックで使用します。 "_配列_" パラメーターなので、"_配列_" 
[条件](../concepts/definition-structure.md#conditions)である **in** または **notIn** を使用する必要があります。
ここでは、定義済みの値の 1 つとして、**serviceProvider.bandwidthInMbps** エイリアスに対して使用されています。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-2.json" range="21-24" highlight="3":::

## <a name="sample-3-parameterized-effect"></a>サンプル 3: パラメーター化された効果

ポリシー定義を再利用するには、一般的にその効果そのものをパラメーター化します。 この例では、1 つのパラメーターである **effect** を使用します。 効果をパラメーター化すると、異なる効果を持つ異なるスコープに同じ定義を割り当てることができます。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json":::

### <a name="sample-3-explanation"></a>サンプル 3: 説明

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="11-25":::

ポリシー定義のこの部分では、**effect** パラメーターは "_文字列_" として定義されています。 このポリシー定義によって、割り当ての既定値が "_監査_" に設定され、他のオプションが "_無効_" および "_拒否_" に制限されます。

次に、このパラメーターを **policyRule.then** ブロックで "_効果_" として使用します。

:::code language="json" source="~/policy-templates/patterns/pattern-parameters-3.json" range="38-40" highlight="2":::

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。