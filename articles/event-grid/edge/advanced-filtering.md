---
title: 高度なフィルター処理 - Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge での Event Grid の高度なフィルター処理。
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 64b8956c47cbdbf31bb8253dac0c1e1f12833bf7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001050"
---
# <a name="advanced-filtering"></a>高度なフィルター処理
Event Grid では、json ペイロードの任意のプロパティに対してフィルターを指定できます。 これらのフィルターは一連の `AND` 条件としてモデル化されており、外側の各条件には省略可能な内側の `OR` 条件があります。 `AND` 条件ごとに、次の値を指定します。

* `OperatorType` - 比較の種類。
* `Key` - フィルターを適用するプロパティへの json パス。
* `Value` - フィルターが実行される参照値 (または) `Values` - フィルターが実行される一連の参照値。

## <a name="json-syntax"></a>JSON 構文

高度なフィルターの JSON 構文は次のとおりです。

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>配列値に対するフィルター処理

現在、Event Grid は値の配列に対するフィルター処理をサポートしていません。 受信イベントに高度なフィルターのキーの配列値がある場合、一致する操作は失敗します。 受信イベントは、イベント サブスクリプションと一致しなくなります。

## <a name="and-or-not-semantics"></a>AND-OR-NOT セマンティクス

前述の json の例では、`AdvancedFilters` は配列です。 各 `AdvancedFilter` 配列要素は `AND` 条件と考えることができます。

複数の値 (`NumberIn`、`NumberNotIn`、`StringIn`など) をサポートする演算子の場合、各値は `OR` 条件として扱われます。 そのため、`StringBeginsWith("a", "b", "c")` は、`a` または `b` または `c` で始まる文字列値と一致します。

> [!CAUTION]
> NOT 演算子 (`NumberNotIn` および `StringNotIn`) は、`Values` フィールドに指定された各値で AND 条件として動作します。
>
> そうしないと、フィルターは Accept-All フィルターになり、フィルター処理の目的が損なわれます。

## <a name="floating-point-rounding-behavior"></a>浮動小数点の丸め動作

Event Grid では、`decimal` .NET 型を使用してすべての数値が処理されます。 イベント サブスクリプションの JSON で指定された数値は、浮動小数点の丸め動作の対象になりません。

## <a name="case-sensitivity-of-string-filters"></a>文字列フィルターの大文字と小文字の区別

すべての文字列比較で、大文字と小文字が区別されます。 現在、この動作を変更する方法はありません。

## <a name="allowed-advanced-filter-keys"></a>許可される高度なフィルター キー

`Key` プロパティには、既知の最上位レベルのプロパティか、複数のドット (各ドットは、入れ子にされた json オブジェクトへのステップインを意味します) を含む json パスを指定できます。

JSONPath の仕様とは異なり、Event Grid ではキーの `$` 文字に特別な意味がありません。

### <a name="event-grid-schema"></a>Event Grid スキーマ

Event Grid スキーマのイベントの場合:

* id
* トピック
* サブジェクト
* EventType
* DataVersion
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>カスタム イベント スキーマ

Event Grid ではペイロードにエンベロープ スキーマが適用されないため、カスタム イベント スキーマの `Key` に制限はありません。

## <a name="numeric-single-value-filter-examples"></a>数値の単一値フィルターの例

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>数値範囲の値フィルターの例

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>文字列範囲の値フィルターの例

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>ブール値の単一値フィルターの例

* BoolEquals です。

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
