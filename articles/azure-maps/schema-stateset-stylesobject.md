---
title: 動的 Azure Maps の StylesObject
description: 動的 Azure Maps での作成に使用される StylesObject の JSON スキーマと構文のリファレンス ガイド。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b085fbc6e330d38b59fce0c494f672b00c712b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85120490"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>動的マップの StylesObject スキーマ リファレンス ガイド

この記事は、`StylesObject` の JSON スキーマと構文のリファレンス ガイドです。 `StylesObject` は、stateset スタイルを表す `StyleObject` 配列です。 Azure Maps Creator [Feature State サービス](https://docs.microsoft.com/rest/api/maps/featurestate)を使用して、stateset スタイルを屋内マップ データ地物に適用します。 stateset スタイルを作成して屋内マップ機能に関連付けると、それらを使用して動的な屋内マップを作成できます。 動的な屋内マップの作成の詳細については、「[Creator の屋内マップに動的スタイル設定を実装する](indoor-map-dynamic-styling.md)」を参照してください。

## <a name="styleobject"></a>StyleObject

`StyleObject` は、[`BooleanTypeStyleRule`](#booleantypestylerule) 型または [`NumericTypeStyleRule`](#numerictypestylerule) 型のいずれかです。

次の JSON は、`occupied` という名前の `BooleanTypeStyleRule` と `temperature` という名前の `NumericTypeStyleRule` を示しています。

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 `NumericTypeStyleRule` は [`StyleObject`](#styleobject) であり、次のプロパティで構成されています:

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `keyName` | string | *state* または動的プロパティ名。 `keyName` は `StyleObject` 配列内で一意である必要があります。| はい |
| `type` | string | 値は "数値" です。 | はい |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 色が関連付けられている数値スタイルの範囲の配列。 各範囲には、*state* 値が範囲を満たすときに使用される色を定義します。| はい |

### <a name="numberruleobject"></a>NumberRuleObject

`NumberRuleObject` は、[`RangeObject`](#rangeobject) および `color` プロパティで構成されています。 *state* 値が範囲内にある場合、その表示色は `color` プロパティで指定された色になります。

複数の重複する範囲を定義した場合、該当する最初の範囲に定義されている色が選択されます。

次の JSON サンプルでは、*state* 値が 50 から 60 の場合、両方の範囲が true になります。 ただし、使用される色は `#343deb` です。これは、一覧の中で該当する最初の範囲だからです。

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | [RangeObject](#rangeobject) には、一連の論理的な範囲条件を定義します。これにより、`true` の場合、*state* の表示色が `color` プロパティで指定された色に変更されます。 `range` が指定されていない場合、`color` プロパティで定義された色が常に使用されます。   | いいえ |
| `color` | string | state 値が範囲内にあるときに使用する色。 `color` プロパティは、次のいずれかの形式の JSON 文字列です。 <ul><li> HTML スタイルの 16 進値 </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> yellow や blue など、定義済みの HTML の色名。</li></ul> | はい |

### <a name="rangeobject"></a>RangeObject

`RangeObject` には、[`NumberRuleObject`](#numberruleobject) の数値範囲値を定義します。 *state* 値が範囲内に収まるには、定義されているすべての条件が true である必要があります。 

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `minimum` | double | x ≥ `minimum` であるすべての数値 x。| いいえ |
| `maximum` | double | x ≤ `maximum` であるすべての数値 x。 | いいえ |
| `exclusiveMinimum` | double | x > `exclusiveMinimum` であるすべての数値 x。| いいえ |
| `exclusiveMaximum` | double | x < `exclusiveMaximum` であるすべての数値 x。| いいえ |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule の例

次の JSON は、`temperature` という名前の `NumericTypeStyleRule` *state* を示しています。 この例で、[`NumberRuleObject`](#numberruleobject) には、2 つの定義済みの温度範囲とそれらに関連付けられた色のスタイルが含まれています。 気温の範囲が 50 から 69 の場合、ディスプレイには色 `#343deb` が使用されます。  気温の範囲が 31 から 70 の場合、ディスプレイには色 `#eba834` が使用されます。

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule` は [`StyleObject`](#styleobject) であり、次のプロパティで構成されています:

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *state* または動的プロパティ名。  `keyName` は、style 配列内で一意である必要があります。| はい |
| `type` | string |値は "boolean" です。 | はい |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| `true` および `false` *state* 値の色を持つブール値のペア。| はい |

### <a name="booleanruleobject"></a>BooleanRuleObject

`BooleanRuleObject` には、`true` および `false` 値の色を定義します。

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `true` | string | *state* 値が `true` の場合に使用する色。 `color` プロパティは、次のいずれかの形式の JSON 文字列です。 <ul><li> HTML スタイルの 16 進値 </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> yellow や blue など、定義済みの HTML の色名。</li></ul>| はい |
| `false` | string | *state* 値が `false` の場合に使用する色。 | はい |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule の例

次の JSON は、`occupied` という名前の `BooleanTypeStyleRule` *state* を示しています。 [`BooleanRuleObject`](#booleanruleobject) には、`true` および `false` 値の色を定義します。

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```
