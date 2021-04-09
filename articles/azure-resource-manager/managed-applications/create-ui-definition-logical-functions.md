---
title: UI 定義論理関数を作成する
description: 論理演算を実行する関数について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 00d2f0eeb5d353c8ebd7ad30f6866f890d6cb42e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094698"
---
# <a name="createuidefinition-logical-functions"></a>CreateUiDefinition 論理関数

次の関数は、条件式で使用できます。 関数によっては、一部の JSON データ型をサポートしていないことがあります。

## <a name="and"></a>and

すべてのパラメーターが `true` に評価される場合、`true` が返されます。 この関数でサポートされる 2 つ以上のパラメーターは、ブール型のみです。

次の例は、 `true`を返します。

```json
"[and(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

次の例は、 `false`を返します。

```json
"[and(equals(0, 0), greater(1, 2))]"
```

## <a name="coalesce"></a>coalesce

最初の null 以外のパラメーターの値が返されます。 この関数は、すべての JSON データ型をサポートしています。

`element1` と `element2` は未定義であるものとします。 次の例は、 `"Contoso"`を返します。

```json
"[coalesce(steps('demoStep').element1, steps('demoStep').element2, 'Contoso')]"
```

この関数は、ページが読み込まれた後にユーザーのアクションによって発生するオプションの呼び出しのコンテキストで特に役立ちます。 たとえば、UI 内の 1 つのフィールドに設定されている制約が、**最初は非表示** の別のフィールドの、現在選択されている値に依存している場合です。 この場合、`coalesce()` を使用して、ユーザーがフィールドと対話するときに望ましい効果を保持しながら、ページの読み込み時に関数を構文的に有効にすることができます。

ユーザーが複数の異なるデータベースの種類から選択できるようにするこの `DropDown` を検討してください。

```
{
    "name": "databaseType",
    "type": "Microsoft.Common.DropDown",
    "label": "Choose database type",
    "toolTip": "Choose database type",
    "defaultValue": "Oracle Database",
    "visible": "[bool(steps('section_database').connectToDatabase)]"
    "constraints": {
        "allowedValues": [
            {
                "label": "Azure Database for PostgreSQL",
                "value": "postgresql"
            },
            {
                "label": "Oracle Database",
                "value": "oracle"
            },
            {
                "label": "Azure SQL",
                "value": "sqlserver"
            }
        ],
        "required": true
    },
```

このフィールドの現在選択されている値に対して別のフィールドのアクションを条件付けするには、次のように `coalesce()` を使用します。

```
"regex": "[concat('^jdbc:', coalesce(steps('section_database').databaseConnectionInfo.databaseType, ''), '.*$')]",
```

これは必要です。`databaseType` が最初は表示されず、値を持たないためです。 これにより、式全体が正しく評価されません。

## <a name="equals"></a>equals

両方のパラメーターに同じ型と値が指定されている場合、`true` が返されます。 この関数は、すべての JSON データ型をサポートしています。

次の例は、 `true`を返します。

```json
"[equals(0, 0)]"
```

次の例は、 `true`を返します。

```json
"[equals('web', 'web')]"
```

次の例は、 `false`を返します。

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

## <a name="greater"></a>greater

最初のパラメーターが厳密に 2 番目のパラメーターを超える場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `false`を返します。

```json
"[greater(1, 2)]"
```

次の例は、 `true`を返します。

```json
"[greater('9', '10')]"
```

## <a name="greaterorequals"></a>greaterOrEquals

最初のパラメーターが 2 番目のパラメーター以上の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[greaterOrEquals(2, 2)]"
```

## <a name="if"></a>if

条件が true か false かに基づいて値を返します。 最初のパラメーターは、テストする条件です。 2 番目のパラメーターは、条件が true の場合に返される値です。 3 番目のパラメーターは、条件が false の場合に返される値です。

次の例では、`yes` が返されます。

```json
"[if(equals(42, mul(6, 7)), 'yes', 'no')]"
```

## <a name="less"></a>less

最初のパラメーターが厳密に 2 番目のパラメーター未満の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[less(1, 2)]"
```

次の例は、 `false`を返します。

```json
"[less('9', '10')]"
```

## <a name="lessorequals"></a>lessOrEquals

最初のパラメーターが 2 番目のパラメーター以下の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[lessOrEquals(2, 2)]"
```

## <a name="not"></a>not

パラメーターが `false` に評価される場合、`true` が返されます。 この関数は、ブール型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[not(false)]"
```

次の例は、 `false`を返します。

```json
"[not(equals(0, 0))]"
```

## <a name="or"></a>or

少なくとも 1 つのパラメーターが `true` に評価される場合、`true` が返されます。 この関数は、複数のブール型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[or(equals(0, 0), equals('web', 'web'), less(1, 2))]"
```

次の例は、 `true`を返します。

```json
"[or(equals(0, 0), greater(1, 2))]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。
