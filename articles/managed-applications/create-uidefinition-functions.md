---
title: Azure マネージド アプリケーションの UI 定義作成機能 | Microsoft Docs
description: Azure マネージド アプリケーションの UI 定義を作成するときに使う機能について説明します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063796"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition 関数
このセクションには、サポートされているすべての CreateUiDefinition 関数の署名が含まれています。

関数を使用するには、角かっこで宣言を囲みます。 例: 

```json
"[function()]"
```

文字列とその他の関数は、関数のパラメーターとして参照できますが、文字列は一重引用符で囲む必要があります。 例: 

```json
"[fn1(fn2(), 'foobar')]"
```

必要に応じて、ドット演算子を使用して、関数の出力のプロパティを参照できます。 例: 

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>関数の参照
次の関数を使用すると、CreateUiDefinition のプロパティまたはコンテキストの出力を参照できます。

### <a name="basics"></a>basics
基本ステップで定義されている要素の出力値が返されます。

次の例は、基本ステップの `foo` という名前の要素の出力を返します。

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
指定したステップで定義されている要素の出力値が返されます。 基本ステップの要素の出力値を取得するには、代わりに `basics()` を使用します。

次の例は、`foo` という名前のステップの、`bar` という名前の要素の出力を返します。

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
基本ステップまたは現在のコンテキストで選択されている場所が返されます。

次の例は、`"westus"` などが返されます。

```json
"[location()]"
```

## <a name="string-functions"></a>文字列関数
次の関数は、JSON 文字列でのみ使用できます。

### <a name="concat"></a>concat
1 つ以上の文字列が連結されます。

たとえば、`element1` の出力値が `"bar"` の場合、この例は文字列 `"foobar!"` を返します。

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
指定した文字列の部分文字列が返されます。 部分文字列は、指定したインデックスから開始し、指定した長さになります。

次の例は、 `"ftw"`を返します。

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
現在の文字列内で、指定した文字列のすべての出現箇所が別の文字列で置き換えられて返されます。

次の例は、 `"Everything is awesome!"`を返します。

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
グローバルに一意の文字列 (GUID) が生成されます。

次の例では、`"c7bc8bdc-7252-4a82-ba53-7c468679a511"` などが返されます。

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
小文字に変換された文字列が返されます。

次の例は、 `"foobar"`を返します。

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
大文字に変換された文字列が返されます。

次の例は、 `"FOOBAR"`を返します。

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>コレクション関数
次の関数は、JSON 文字列、配列、オブジェクトなどのコレクションで使用できます。

### <a name="contains"></a>contains
指定した部分文字列が文字列に含まれる場合、指定した値が配列に含まれる場合、または指定したキーがオブジェクトに含まれる場合、`true` が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `true`を返します。

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `false`を返します。

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

次の例は、 `true`を返します。

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
文字列の文字数、配列の値の数、またはオブジェクトのキーの数が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `6`を返します。

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `3`を返します。

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

次の例は、 `2`を返します。

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
文字列、配列、またはオブジェクトが null か空の場合、`true` が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `true`を返します。

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `false`を返します。

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

次の例は、 `false`を返します。

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>例 4: null または未定義
`element1` は `null` または未定義であるものとします。 次の例は、 `true`を返します。

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
指定した文字列の最初の文字、指定した配列の最初の値、または指定したオブジェクトの最初のキーと値が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `"f"`を返します。

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `1`を返します。

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
次の例は、 `{"key1": "foobar"}`を返します。

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
指定した文字列の最後の文字、指定した配列の最後の値、または指定したオブジェクトの最後のキーと値が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `"r"`を返します。

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `2`を返します。

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

次の例は、 `{"key2": "raboof"}`を返します。

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
文字列の先頭からの指定した数の連続文字、配列の先頭からの指定した数の連続する値、またはオブジェクトの先頭からの指定した数の連続するキーと値が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `"foo"`を返します。

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `[1, 2]`を返します。

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

次の例は、 `{"key1": "foobar"}`を返します。

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
コレクション内の指定した数の要素がバイパスされ、残りの要素が返されます。

#### <a name="example-1-string"></a>例 1: 文字列
次の例は、 `"bar"`を返します。

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>例 2: 配列
`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `[3]`を返します。

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>例 3: オブジェクト
`element1` は次を返すものとします。

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
次の例は、 `{"key2": "raboof"}`を返します。

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>論理関数
次の関数は、条件文で使用できます。 関数によっては、一部の JSON データ型をサポートしていないことがあります。

### <a name="equals"></a>equals
両方のパラメーターに同じ型と値が指定されている場合、`true` が返されます。 この関数は、すべての JSON データ型をサポートしています。

次の例は、 `true`を返します。

```json
"[equals(0, 0)]"
```

次の例は、 `true`を返します。

```json
"[equals('foo', 'foo')]"
```

次の例は、 `false`を返します。

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
最初のパラメーターが厳密に 2 番目のパラメーター未満の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[less(1, 2)]"
```

次の例は、 `false`を返します。

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
最初のパラメーターが 2 番目のパラメーター以下の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
最初のパラメーターが厳密に 2 番目のパラメーターを超える場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `false`を返します。

```json
"[greater(1, 2)]"
```

次の例は、 `true`を返します。

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
最初のパラメーターが 2 番目のパラメーター以上の場合、`true` が返されます。 この関数は、数値型および文字列型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>and
すべてのパラメーターが `true` に評価される場合、`true` が返されます。 この関数は、複数のブール型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

次の例は、 `false`を返します。

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>or
少なくとも 1 つのパラメーターが `true` に評価される場合、`true` が返されます。 この関数は、複数のブール型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

次の例は、 `true`を返します。

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
パラメーターが `false` に評価される場合、`true` が返されます。 この関数は、ブール型のパラメーターのみをサポートしています。

次の例は、 `true`を返します。

```json
"[not(false)]"
```

次の例は、 `false`を返します。

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
最初の null 以外のパラメーターの値が返されます。 この関数は、すべての JSON データ型をサポートしています。

`element1` と `element2` は未定義であるものとします。 次の例は、 `"foobar"`を返します。

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>変換関数
次の関数を使用すると、JSON データ型とエンコーディングの間で値を変換できます。

### <a name="int"></a>int
パラメーターが整数に変換されます。 この関数は、数値型および文字列型のパラメーターをサポートしています。

次の例は、 `1`を返します。

```json
"[int('1')]"
```

次の例は、 `2`を返します。

```json
"[int(2.9)]"
```

### <a name="float"></a>float
パラメーターが浮動小数点に変換されます。 この関数は、数値型および文字列型のパラメーターをサポートしています。

次の例は、 `1.0`を返します。

```json
"[float('1.0')]"
```

次の例は、 `2.9`を返します。

```json
"[float(2.9)]"
```

### <a name="string"></a>文字列
パラメーターが文字列に変換されます。 この関数は、すべての JSON データ型のパラメーターをサポートしています。

次の例は、 `"1"`を返します。

```json
"[string(1)]"
```

次の例は、 `"2.9"`を返します。

```json
"[string(2.9)]"
```

次の例は、 `"[1,2,3]"`を返します。

```json
"[string([1,2,3])]"
```

次の例は、 `"{"foo":"bar"}"`を返します。

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
パラメーターがブール値に変換されます。 この関数は、数値型、文字列型、およびブール型のパラメーターをサポートしています。 JavaScript のブール値と同様、`0` または `'false'` を除くすべての値が `true` を返します。

次の例は、 `true`を返します。

```json
"[bool(1)]"
```

次の例は、 `false`を返します。

```json
"[bool(0)]"
```

次の例は、 `true`を返します。

```json
"[bool(true)]"
```

次の例は、 `true`を返します。

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
パラメーターがネイティブ型に変換されます。 つまり、この関数は `string()` の逆です。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `1`を返します。

```json
"[parse('1')]"
```

次の例は、 `true`を返します。

```json
"[parse('true')]"
```

次の例は、 `[1,2,3]`を返します。

```json
"[parse('[1,2,3]')]"
```

次の例は、 `{"foo":"bar"}`を返します。

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
パラメーターが base 64 エンコード文字列にエンコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"Zm9vYmFy"`を返します。

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
パラメーターが base 64 エンコード文字列からデコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"foobar"`を返します。

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
パラメーターが URL エンコード文字列にエンコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"https%3A%2F%2Fportal.azure.com%2F"`を返します。

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
パラメーターが URL エンコード文字列からデコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"https://portal.azure.com/"`を返します。

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>算術関数
### <a name="add"></a>add
2 つの数値が加算され、結果が返されます。

次の例は、 `3`を返します。

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
最初の数値から 2 番目の数値が減算され、結果が返されます。

次の例は、 `1`を返します。

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
2 つの数値が乗算され、結果が返されます。

次の例は、 `6`を返します。

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
最初の数値が 2 番目の数値で除算され、結果が返されます。 結果は必ず整数です。

次の例は、 `2`を返します。

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
最初の数値が 2 番目の数値で除算され、余りが返されます。

次の例は、 `0`を返します。

```json
"[mod(6, 3)]"
```

次の例は、 `2`を返します。

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
2 つの数値の小さい方が返されます。

次の例は、 `1`を返します。

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
2 つの数値の大きい方が返されます。

次の例は、 `2`を返します。

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
指定した範囲内の整数のシーケンスが生成されます。

次の例は、 `[1,2,3]`を返します。

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
指定した範囲内のランダムな整数が返されます。 この関数では、暗号化で保護されたランダムな数値は生成されません。

次の例では、`42` などが返されます。

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
指定した数値以下の最大の整数が返されます。

次の例は、 `3`を返します。

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
指定した数値以上の最小の整数が返されます。

次の例は、 `4`を返します。

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>データ関数
### <a name="utcnow"></a>utcNow
ローカル コンピューターの現在の日時を表す ISO 8601 形式の文字列が返されます。

次の例では、`"1990-12-31T23:59:59.000Z"` などが返されます。

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
秒を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:00:00.000Z"`を返します。

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
分を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:00:59.000Z"`を返します。

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
時間を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:59:59.000Z"`を返します。

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>次の手順
* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

