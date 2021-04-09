---
title: UI 定義日付関数を作成する
description: 日付値の操作時に使用する関数について説明します。
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 80484fd15e51bae7036c43bd3ca8fe8167387ede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87094700"
---
# <a name="createuidefinition-date-functions"></a>CreateUiDefinition 日付関数

日付値の操作時に使用する関数。

## <a name="addhours"></a>addHours

時間を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:59:59.000Z"`を返します。

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addminutes"></a>addMinutes

分を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:00:59.000Z"`を返します。

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

## <a name="addseconds"></a>addSeconds
秒を表す整数が、指定したタイムスタンプに追加されます。

次の例は、 `"1991-01-01T00:00:00.000Z"`を返します。

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

## <a name="utcnow"></a>utcNow

ローカル コンピューターの現在の日時を表す ISO 8601 形式の文字列が返されます。

次の例は、`"1990-12-31T23:59:59.000Z"` などが返されます。

```json
"[utcNow()]"
```

## <a name="next-steps"></a>次のステップ

* Azure Resource Manager の概要については、「[Azure Resource Manager の概要](../management/overview.md)」を参照してください。
