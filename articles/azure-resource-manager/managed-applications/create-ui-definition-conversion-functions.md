---
title: UI 定義作成の変換関数
description: データ型とエンコーディングの間で値を変換するときに使用する関数について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: b69cd35b27b343da08727b4c4ee9b4fd025e1df7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094701"
---
# <a name="createuidefinition-conversion-functions"></a>CreateUiDefinition 変換関数

次の関数を使用すると、JSON データ型とエンコーディングの間で値を変換できます。

## <a name="bool"></a>[bool]

パラメーターをブール値に変換します。 この関数は、数値型、文字列型、およびブール型のパラメーターをサポートしています。 JavaScript のブール値と同様、`0` または `'false'` を除くすべての値が `true` を返します。

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

## <a name="decodebase64"></a>decodeBase64

パラメーターが base 64 エンコード文字列からデコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"Contoso"`を返します。

```json
"[decodeBase64('Q29udG9zbw==')]"
```

## <a name="decodeuricomponent"></a>decodeUriComponent

パラメーターが URL エンコード文字列からデコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"https://portal.azure.com/"`を返します。

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="encodebase64"></a>encodeBase64

パラメーターが base 64 エンコード文字列にエンコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"Q29udG9zbw=="`を返します。

```json
"[encodeBase64('Contoso')]"
```

## <a name="encodeuricomponent"></a>encodeUriComponent

パラメーターが URL エンコード文字列にエンコードされます。 この関数は、文字列型のパラメーターのみをサポートしています。

次の例は、 `"https%3A%2F%2Fportal.azure.com%2F"`を返します。

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

## <a name="float"></a>float

パラメーターが浮動小数点に変換されます。 この関数は、数値型および文字列型のパラメーターをサポートしています。

次の例は、 `1.0`を返します。

```json
"[float('1.0')]"
```

次の例は、 `2.9`を返します。

```json
"[float(2.9)]"
```

## <a name="int"></a>INT

パラメーターが整数に変換されます。 この関数は、数値型および文字列型のパラメーターをサポートしています。

次の例は、 `1`を返します。

```json
"[int('1')]"
```

次の例は、 `2`を返します。

```json
"[int(2.9)]"
```

## <a name="parse"></a>parse

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

次の例は、 `{"type":"webapp"}`を返します。

```json
"[parse('{\"type\":\"webapp\"}')]"
```

## <a name="string"></a>string

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

次の例は、 `"{"type":"webapp"}"`を返します。

```json
"[string({\"type\":\"webapp\"})]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。
