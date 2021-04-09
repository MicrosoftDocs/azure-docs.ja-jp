---
title: Azure Cosmos DB クエリ言語の DateTimePart
description: Azure Cosmos DB での SQL システム関数 DateTimePart について学習します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 97eaff405086190b60279ac7d5cf8bf441c8f840
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336369"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

指定した DateTime 間の指定した DateTimePart の値を返します。
  
## <a name="syntax"></a>構文
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>引数
  
*DateTimePart*  
   DateTimePart によって値が返される日付の部分。 この表には、有効な DateTimePart 引数をすべて一覧表示しています。

| DateTimePart | 省略形        |
| ------------ | -------------------- |
| 年         | "year"、"yyyy"、"yy" |
| Month        | "month"、"mm"、"m"   |
| 日          | "day"、"dd"、"d"     |
| 時         | "hour"、"hh"         |
| 分       | "minute"、"mi"、"n"  |
| 秒       | "second"、"ss"、"s"  |
| Millisecond  | "millisecond"、"ms"  |
| Microsecond  | "microsecond"、"mcs" |
| Nanosecond   | "nanosecond"、"ns"   |

*DateTime*  
   形式 `YYYY-MM-DDThh:mm:ss.fffffffZ` による UTC の日付と時刻の ISO 8601 文字列値

## <a name="return-types"></a>戻り値の型

正の整数値を返します。

## <a name="remarks"></a>注釈

DateTimePart からは次の理由で `undefined` が返されます。

- 指定された DateTimePart 値が無効です
- DateTime は有効な ISO 8601 DateTime ではありません

このシステム関数では、インデックスは使用されません。

## <a name="examples"></a>例

次に、月の整数値を返す例を示します。

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

マイクロ秒数を返す例を次に示します。

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
