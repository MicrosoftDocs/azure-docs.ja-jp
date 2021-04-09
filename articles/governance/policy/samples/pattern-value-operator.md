---
title: 'パターン: ポリシー定義の value 演算子'
description: この Azure Policy パターンでは、ポリシー定義内で value 演算子を使用する方法の例を示します。
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 8392c69ff3d63ff4ecad2a26d5d914b4766147b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072885"
---
# <a name="azure-policy-pattern-the-value-operator"></a>Azure Policy pattern: value 演算子

[value](../concepts/definition-structure.md#value) 演算子は、[パラメーター](../concepts/definition-structure.md#parameters)、[サポートされているテンプレート関数](../concepts/definition-structure.md#policy-functions)、またはリテラルを、与えられた[条件](../concepts/definition-structure.md#conditions)に合うように、指定した値に評価します。

> [!WARNING]
> _テンプレート関数_ の結果がエラーの場合、ポリシーの評価は失敗します。 評価の失敗は、暗黙的な **deny** です。 詳細については、「[テンプレート エラーの回避](../concepts/definition-structure.md#avoiding-template-failures)」を参照してください。

## <a name="sample-policy-definition"></a>ポリシー定義の例

このポリシー定義では、パラメーター **tagName** ("_文字列_") で指定されているタグをリソースに追加または置換します。また、**tagName** の値はリソースが含まれるリソース グループから継承します。 この評価は、リソースが作成または更新されるときに行われます。 [modify](../concepts/effects.md#modify) 効果として、[修復タスク](../how-to/remediate-resources.md)を通じて既存のリソースに対して修復が実行される場合があります。

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json":::

### <a name="explanation"></a>説明

:::code language="json" source="~/policy-templates/patterns/pattern-value-operator.json" range="20-30" highlight="7,8":::

**value** 演算子は、**properties** 内の **policyRule.if** ブロックで使用されます。 この例では、[論理演算子](../concepts/definition-structure.md#logical-operators) **allOf** を使用して、**modify** 効果がトリガーされるためには、両方の条件ステートメントが true である必要があることを示しています。

**value** によって、テンプレート関数 [resourceGroup ()](../../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup) の結果が、空白の値を持つ **notEquals** 条件に評価されます。 親リソース グループの **tagName** に指定されたタグ名が存在する場合、条件は true に評価されます。

## <a name="next-steps"></a>次のステップ

- その他の[パターンと組み込みの定義](./index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。