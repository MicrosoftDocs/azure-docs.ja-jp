---
title: UI 定義作成のコレクション関数
description: 配列やオブジェクトなどのコレクションを操作するときに使用する関数について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094702"
---
# <a name="createuidefinition-collection-functions"></a>CreateUiDefinition コレクション関数

次の関数は、JSON 文字列、配列、オブジェクトなどのコレクションで使用できます。

## <a name="contains"></a>contains

指定した部分文字列が文字列に含まれる場合、指定した値が配列に含まれる場合、または指定したキーがオブジェクトに含まれる場合、`true` が返されます。

### <a name="example-string-contains"></a>例: 文字列 contains

次の例は、 `true`を返します。

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>例: 配列 contains

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `false`を返します。

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>例: オブジェクト contains

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `true`を返します。

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

文字列、配列、またはオブジェクトが null か空の場合、`true` が返されます。

### <a name="example-string-empty"></a>例: 文字列 empty

次の例は、 `true`を返します。

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>例: 配列 empty

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `false`を返します。

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>例: オブジェクト empty

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `false`を返します。

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>例: null かつ未定義

`element1` は `null` または未定義であるものとします。 次の例は、 `true`を返します。

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

ラムダ関数として指定されたフィルター処理ロジックを適用した後、新しい配列を返します。 最初のパラメーターは、フィルター処理に使用する配列です。 2 番目のパラメーターは、フィルター処理ロジックを指定するラムダ関数です。

次の例では、配列 `[ { "name": "abc" } ]` を返します。

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>first

指定した文字列の最初の文字、指定した配列の最初の値、または指定したオブジェクトの最初のキーと値が返されます。

### <a name="example-string-first"></a>例: 文字列 first

次の例は、 `"c"`を返します。

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>例: 配列 first

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `1`を返します。

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>例: オブジェクト first

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `{"key1": "Linux"}`を返します。

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>last

指定した文字列の最後の文字、指定した配列の最後の値、または指定したオブジェクトの最後のキーと値が返されます。

### <a name="example-string-last"></a>例: 文字列 last

次の例は、 `"o"`を返します。

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>例: 配列 last

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `3`を返します。

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>例: オブジェクト last

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `{"key2": "Windows"}`を返します。

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

文字列の文字数、配列の値の数、またはオブジェクトのキーの数が返されます。

### <a name="example-string-length"></a>例: 文字列 length

次の例は、 `7`を返します。

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>例: 配列 length

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `3`を返します。

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>例: オブジェクト length

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `2`を返します。

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>map

指定された配列でラムダ関数を呼び出した後、新しい配列を返します。 最初のパラメーターは、ラムダ関数に使用する配列です。 2 番目のパラメーターは、ラムダ関数です。

次の例は、すべての値を 2 倍にした新しい配列を返します。 結果は `[2, 4, 6]`です。

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

次の例は、新しい配列 `["abc", "xyz"]` を返します。

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>skip

コレクション内の指定した数の要素がバイパスされ、残りの要素が返されます。

### <a name="example-string-skip"></a>例: 文字列 skip

次の例は、 `"app"`を返します。

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>例: 配列 skip

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `[3]`を返します。

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>例: オブジェクト skip

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
次の例は、 `{"key2": "Windows"}`を返します。

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

区切り記号で区切られた入力の部分文字列を含む文字列の配列を返します。

次の例では、配列 `[ "555", "867", "5309" ]` を返します。

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

文字列の先頭からの指定した数の連続文字、配列の先頭からの指定した数の連続する値、またはオブジェクトの先頭からの指定した数の連続するキーと値が返されます。

### <a name="example-string-take"></a>例: 文字列 take

次の例は、 `"web"`を返します。

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>例: 配列 take

`element1` は `[1, 2, 3]` を返すものとします。 次の例は、 `[1, 2]`を返します。

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>例: オブジェクト take

`element1` は次を返すものとします。

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

次の例は、 `{"key1": "Linux"}`を返します。

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。
