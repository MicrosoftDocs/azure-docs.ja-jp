---
title: UI 定義文字列関数を作成する
description: Azure Managed Applications の UI 定義を作成するときに使用する文字列機能について説明します
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: c662948542c36cd93f889ca045ee245c15c7bb11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094694"
---
# <a name="createuidefinition-string-functions"></a>CreateUiDefinition 文字列関数

これらは JSON 文字列で使用する関数です。

## <a name="concat"></a>concat

1 つ以上の文字列が連結されます。

たとえば、`element1` の出力値が `"Contoso"` の場合、この例は文字列 `"Demo Contoso app"` を返します。

```json
"[concat('Demo ', steps('step1').element1, ' app')]"
```

## <a name="endswith"></a>endsWith

文字列が特定の値で終わるかどうかを判断します。

次のサンプルでは true が返されます。

```json
"[endsWith('tuvwxyz', 'xyz')]"
```

## <a name="guid"></a>guid

グローバルに一意の文字列 (GUID) が生成されます。

次の例では、`"c7bc8bdc-7252-4a82-ba53-7c468679a511"` のような値が返されます。

```json
"[guid()]"
```

## <a name="indexof"></a>indexOf

文字列内の値の最初の位置を返します。見つからない場合は -1 を返します。

次のサンプルでは 2 が返されます。

```json
"[indexOf('abcdef', 'cd')]"
```

## <a name="lastindexof"></a>lastIndexOf

文字列内の値の最後の位置を返します。見つからない場合は -1 を返します。

次のサンプルでは 3 が返されます。

```json
"[lastIndexOf('test', 't')]"
```

## <a name="replace"></a>replace

現在の文字列内で、指定した文字列のすべての出現箇所が別の文字列で置き換えられて返されます。

次の例は、 `"Contoso.com web app"`を返します。

```json
"[replace('Contoso.net web app', '.net', '.com')]"
```

## <a name="startswith"></a>startsWith

文字列が特定の値で始まるかどうかを判断します。

次のサンプルでは true が返されます。

```json
"[startsWith('abcdefg', 'ab')]"
```

## <a name="substring"></a>substring

指定した文字列の部分文字列が返されます。 部分文字列は、指定したインデックスから開始し、指定した長さになります。

次の例は、 `"web"`を返します。

```json
"[substring('Contoso.com web app', 12, 3)]"
```

## <a name="tolower"></a>toLower

小文字に変換された文字列が返されます。

次の例は、 `"contoso"`を返します。

```json
"[toLower('CONTOSO')]"
```

## <a name="toupper"></a>toUpper

大文字に変換された文字列が返されます。

次の例は、 `"CONTOSO"`を返します。

```json
"[toUpper('contoso')]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。

