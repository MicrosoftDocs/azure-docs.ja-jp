---
title: Azure Cosmos DB クエリ言語の GetCurrentTicks
description: Azure Cosmos DB での SQL システム関数 GetCurrentTicks について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2ca76d75edba6688dbe93f11a51a0ad67942677a
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606945"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過した 100 ナノ秒ティック数を返します。
  
## <a name="syntax"></a>構文
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>戻り値の型

Unix エポックから経過した現在の 100 ナノ秒ティック数を示す符号付き数値を返します。 つまり、GetCurrentTicks は、1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過した 100 ナノ秒ティック数を返します。

## <a name="remarks"></a>注釈

GetCurrentTicks() は非決定論的関数です。 返される結果は UTC (協定世界時) です。

このシステム関数では、インデックスは使用されません。

## <a name="examples"></a>例

ティック単位で測定された現在の時刻を返す例を次に示します。

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
