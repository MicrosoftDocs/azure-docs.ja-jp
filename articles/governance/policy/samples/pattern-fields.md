---
title: 'パターン: ポリシー定義の field プロパティ'
description: この Azure Policy パターンでは、ポリシー定義内で field プロパティを使用する方法の例を示します。
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 267c687f78f0bbb100843faee40ab6f3d3cbb64c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072970"
---
# <a name="azure-policy-pattern-field-properties"></a>Azure Policy パターン: field プロパティ

[field](../concepts/definition-structure.md#fields) 演算子は、指定したプロパティまたは[別名](../concepts/definition-structure.md#aliases)を、与えられた[条件](../concepts/definition-structure.md#conditions)に合うように、指定した値に評価します。

## <a name="sample-policy-definition"></a>ポリシー定義の例

このポリシー定義を使用すると、組織の地理的な場所の要件を満たすことができるリージョンを定義できます。 許容されるリソースは、パラメーター **listOfAllowedLocations** ("_配列_") で定義されます。 定義に一致するリソースは、[拒否](../concepts/effects.md#deny)されます。

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>説明

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

**field** 演算子は [論理演算子](../concepts/definition-structure.md#logical-operators) **allOf** 内で 3 回使用されています。

- 最初の使用では、**notIn** 条件を持つ `location` プロパティを **listOfAllowedLocations** パラメーターに評価します。 **notIn** は、"_配列_" が想定されるときに機能しますが、実際にこのパラメーターは "_配列_" です。 作成または更新されるリソースの `location` が承認済みの一覧に含まれていない場合、この要素は true に評価されます。
- 2 番目の使用でも `location` プロパティが評価されますが、**notEquals** 条件を使用して、リソースが "_グローバル_" かどうかを確認します。 作成または更新されるリソースの `location` が "_グローバル_" でない場合、この要素は true に評価されます。
- 最後の使用では、`type` プロパティを評価し、**notEquals** 条件を使用して、リソースの種類が _Microsoft.AzureActiveDirectory/b2cDirectories_ ではないことを検証します。 そうでない場合、この要素は true に評価されます。

**allOf** 論理演算子内の 3 つの条件ステートメントすべてが true に評価された場合、リソースの作成または更新は Azure Policy によってブロックされます。

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。