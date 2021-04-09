---
title: UI 定義算術関数を作成する
description: 算術演算の実行時に使用する関数について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 022e59d847a4d89b4243223637fc6fd1e73255a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094696"
---
# <a name="createuidefinition-math-functions"></a>CreateUiDefinition 算術関数

この関数を使用すると、算術演算を実行できます。

## <a name="add"></a>add

2 つの数値が加算され、結果が返されます。

次の例は、 `3`を返します。

```json
"[add(1, 2)]"
```

## <a name="ceil"></a>ceil

指定した数値以上の最小の整数が返されます。

次の例は、 `4`を返します。

```json
"[ceil(3.14)]"
```

## <a name="div"></a>div

最初の数値が 2 番目の数値で除算され、結果が返されます。 結果は必ず整数です。

次の例は、 `2`を返します。

```json
"[div(6, 3)]"
```

## <a name="floor"></a>floor

指定した数値以下の最大の整数が返されます。

次の例は、 `3`を返します。

```json
"[floor(3.14)]"
```

## <a name="max"></a>max

2 つの数値の大きい方が返されます。

次の例は、 `2`を返します。

```json
"[max(1, 2)]"
```

## <a name="min"></a>min

2 つの数値のうち、小さい方が返されます。

次の例は、 `1`を返します。

```json
"[min(1, 2)]"
```

## <a name="mod"></a>mod

最初の数値が 2 番目の数値で除算され、余りが返されます。

次の例は、 `0`を返します。

```json
"[mod(6, 3)]"
```

次の例は、 `2`を返します。

```json
"[mod(6, 4)]"
```

## <a name="mul"></a>mul

2 つの数値が乗算され、結果が返されます。

次の例は、 `6`を返します。

```json
"[mul(2, 3)]"
```

## <a name="rand"></a>rand

指定した範囲内のランダムな整数が返されます。 この関数では、暗号化で保護されたランダムな数値は生成されません。

次の例は、`42` などが返されます。

```json
"[rand(-100, 100)]"
```

## <a name="range"></a>range

指定した範囲内の整数のシーケンスが生成されます。

次の例は、 `[1,2,3]`を返します。

```json
"[range(1, 3)]"
```

## <a name="sub"></a>sub

最初の数値から 2 番目の数値が減算され、結果が返されます。

次の例は、 `1`を返します。

```json
"[sub(3, 2)]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。
