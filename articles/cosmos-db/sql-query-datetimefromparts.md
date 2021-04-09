---
title: Azure Cosmos DB クエリ言語の DateTimeFromParts
description: Azure Cosmos DB の SQL システム関数 DateTimeFromParts について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592097"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

入力値から構築された文字列 DateTime 値を返します。
  
## <a name="syntax"></a>構文
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>引数
  
*numberYear* 形式 `YYYY` による年の整数値

*numberMonth*  
   形式 `MM` による月の整数値

*numberDay*  
   形式 `DD` による日の整数値

*numberHour* (省略可能) 形式 `hh` による時間の整数値

*numberMinute* (省略可能) 形式 `mm` による分の整数値

*numberSecond* (省略可能) 形式 `ss` による秒の整数値

*numberOfFractionsOfSecond* (省略可能) 形式 `.fffffff` による秒の端数の整数値

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
  
 ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください

## <a name="remarks"></a>解説

指定の整数で無効な DateTime が生成される場合、DateTimeFromParts からは `undefined` が返されます。

省略可能引数が指定されていない場合、その値は 0 になります。

## <a name="examples"></a>例

次の例には DateTime を構築するために必要な引数のみが含まれます。

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

もう 1 つ例を挙げます。こちらでは DateTime を構築するために省略可能引数がいくつか使用されています。

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

もう 1 つ例を挙げます。こちらでは DateTime を構築するために省略可能引数がすべて使用されています。

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
