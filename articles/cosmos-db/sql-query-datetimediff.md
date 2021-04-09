---
title: Azure Cosmos DB クエリ言語の DateTimeDiff
description: Azure Cosmos DB での SQL システム関数 DateTimeDiff について学習します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595642"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
*StartDate* と *EndDate* で指定された 2 つの日付間の差を、指定された DateTimePart 境界の数で (符号付き整数値として) で返します。
  
## <a name="syntax"></a>構文
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
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

*StartDate*  
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

*EndDate*  
   形式 `YYYY-MM-DDThh:mm:ss.fffffffZ` による UTC の日付と時刻の ISO 8601 文字列値

## <a name="return-types"></a>戻り値の型

符号付き整数値を返します。

## <a name="remarks"></a>解説

DateTimeDiff からは次の理由で `undefined` が返されます。

- 指定された DateTimePart 値が無効です
- StartDate または EndDate が有効な ISO 8601 DateTime ではありません

DateTimeDiff からは常に符号付き整数値が返されます。また、時間間隔ではなく、通過した DateTimePart 境界の数が計測されます。

## <a name="examples"></a>例
  
次の例では、`2020-01-01T01:02:03.1234527Z` と `2020-01-03T01:02:03.1234567Z` の間で通過した日の境界の数が計算されます。

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

次の例では、`2028-01-01T01:02:03.1234527Z` と `2020-01-03T01:02:03.1234567Z` の間で通過した年の境界の数が計算されます。

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

次の例では、`2020-01-01T01:00:00.1234527Z` と `2020-01-01T01:59:59.1234567Z` の間で通過した時間の境界の数が計算されます。 これらの DateTime 値に 0.99 時間以上の隔たりがあっても、時間の境界が通過されていないため、`DateTimeDiff` からは 0 が返されます。

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
