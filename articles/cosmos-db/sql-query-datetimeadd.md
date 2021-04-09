---
title: Azure Cosmos DB クエリ言語の DateTimeAdd
description: Azure Cosmos DB の SQL システム関数 DateTimeAdd について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: c04c63a5ec72f08807b1702f74db39e00662656f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597659"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

指定の数値 (符号付き整数値として) を指定の DateTime 文字列に追加することで生成される DateTime 文字列値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>引数
  
*DateTimePart*  
   DateTimeAdd によって整数が追加される日付の一部。 この表には、有効な DateTimePart 引数をすべて一覧表示しています。

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

*numeric_expr*  
   指定の DateTime の DateTimePart に追加される符号付き整数値です。

*DateTime*  
   形式 `YYYY-MM-DDThh:mm:ss.fffffffZ` による UTC の日付と時刻の ISO 8601 文字列値。内訳は以下のとおりです。
  
|Format|説明|
|-|-|
|YYYY|4 桁の年|
|mm|2 桁の月 (例: 01 = 1 月)|
|DD|2 桁の日付 (01 から 31)|
|T|時間要素の開始を表します|
|hh|2 桁の時間 (00 から 23)|
|mm|2 桁の分 (00 から 59)|
|ss|2 桁の秒 (00 から 59)|
|.fffffff|秒の 7 桁の小数部|
|Z|UTC (協定世界時) 指定子|
  
  ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください

## <a name="return-types"></a>戻り値の型

UTC での日付と時刻を ISO 8601 文字列値として `YYYY-MM-DDThh:mm:ss.fffffffZ` の形式で返します。内訳は以下のとおりです。
  
|Format|説明|
|-|-|
|YYYY|4 桁の年|
|mm|2 桁の月 (例: 01 = 1 月)|
|DD|2 桁の日付 (01 から 31)|
|T|時間要素の開始を表します|
|hh|2 桁の時間 (00 から 23)|
|mm|2 桁の分 (00 から 59)|
|ss|2 桁の秒 (00 から 59)|
|.fffffff|秒の 7 桁の小数部|
|Z|UTC (協定世界時) 指定子|

## <a name="remarks"></a>解説

DateTimeAdd からは次の理由で `undefined` が返されます。

- 指定された DateTimePart 値が無効です
- 指定された numeric_expr は有効な整数ではありません
- 引数または結果の DateTime が有効な ISO 8601 DateTime ではありません。

## <a name="examples"></a>例
  
次の例では DateTime `2020-07-09T23:20:13.4575530Z` に 1 か月が追加されます。

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

次の例では DateTime `2020-07-09T23:20:13.4575530Z` から 2 時間が減算されます。

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
