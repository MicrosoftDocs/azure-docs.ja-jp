---
title: 動的 Azure Maps の StylesObject スキーマ リファレンス ガイド
description: 動的 Azure Maps の StylesObject スキーマと構文のリファレンス ガイド。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96903345"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>動的マップの StylesObject スキーマ リファレンス ガイド

> [!IMPORTANT]
> Azure Maps Creator サービスは、現在パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

 `StylesObject` は、stateset スタイルを表す `StyleObject` 配列です。 Azure Maps Creator (プレビュー) [Feature State サービス](/rest/api/maps/featurestate)を使用して、stateset スタイルを屋内マップ データ地物に適用します。 stateset スタイルを作成して屋内マップ機能に関連付けると、それらを使用して動的な屋内マップを作成できます。 動的な屋内マップの作成の詳細については、「[Creator の屋内マップに動的スタイル設定を実装する](indoor-map-dynamic-styling.md)」を参照してください。

## <a name="styleobject"></a>StyleObject

`StyleObject` は、次のスタイル ルールのいずれかです。

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

次の JSON は、3 種類のスタイルごとの使用例を示しています。  `BooleanTypeStyleRule` は、`occupied` プロパティが true および false である機能の動的スタイルを決定するために使用されます。  `NumericTypeStyleRule` は、`temperature` プロパティが特定の範囲内にある機能のスタイルを決定するために使用されます。 最後に、`StringTypeStyleRule` は特定のスタイルを `meetingType` に一致させるために使用されます。



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
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
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

## <a name="stringtypestylerule"></a>StringTypeStyleRule

`StringTypeStyleRule` は [`StyleObject`](#styleobject) であり、次のプロパティで構成されています:

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *state* または動的プロパティ名。  `keyName` は `StyleObject` 配列内で一意である必要があります。| はい |
| `type` | string |値は "string" です。 | はい |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N 個の *state* 値の配列。| Yes |

### <a name="stringruleobject"></a>StringRuleObject

`StringRuleObject` は、機能のプロパティの文字列の値として使用できる最大 N 個の state 値で構成されます。 機能のプロパティ値が定義されているどの state 値とも一致しない場合、その機能は動的スタイルを持ちません。 state 値が重複している場合は、最初の値が優先されます。

文字列の値の一致では、大文字と小文字が区別されます。

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `stateValue1` | string | 値文字列が stateValue1 の場合の色。 | いいえ |
| `stateValue2` | string | 値文字列が stateValue の場合の色。 | いいえ |
| `stateValueN` | string | 値文字列が stateValueN の場合の色。 | No |

### <a name="example-of-stringtypestylerule"></a>StringTypeStyleRule の例

次の JSON は、特定の会議の種類に関連付けられているスタイルを定義する `StringTypeStyleRule` を示しています。

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

`BooleanTypeStyleRule` は [`StyleObject`](#styleobject) であり、次のプロパティで構成されています:

| プロパティ | Type | 説明 | 必須 |
|-----------|----------|-------------|-------------|
| `keyName` | string |  *state* または動的プロパティ名。  `keyName` は `StyleObject` 配列内で一意である必要があります。| はい |
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