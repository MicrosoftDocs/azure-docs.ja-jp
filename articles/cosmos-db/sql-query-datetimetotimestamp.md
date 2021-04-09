---
title: Azure Cosmos DB クエリ言語の DateTimeToTimestamp
description: Azure Cosmos DB での SQL システム関数 DateTimeToTimestamp について学習します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 3b1cbd88b4cd6576b2c31fbeb2f3db86309c5ebf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104597625"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

指定した DateTime をタイムスタンプに変換します。
  
## <a name="syntax"></a>構文
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>引数

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

Unix エポックから現在までに経過したミリ秒数を表す符号付き数値、つまり 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。

## <a name="remarks"></a>注釈

指定した DateTime 値が無効な場合、DateTimeToTimestamp は `undefined` を返します

## <a name="examples"></a>例
  
次の例では、DateTime をタイムスタンプに変換しています。

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

別の例:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
