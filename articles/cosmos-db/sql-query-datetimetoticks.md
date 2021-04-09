---
title: Azure Cosmos DB クエリ言語の DateTimeToTicks
description: Azure Cosmos DB の SQL システム関数 DateTimeToTicks について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336335"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

指定した DateTime をティックに変換します。 1 ティックは、100 ナノ秒または 1,000 万分の 1 秒を表します。 

## <a name="syntax"></a>構文
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>引数
  
*DateTime*  
   形式 `YYYY-MM-DDThh:mm:ss.fffffffZ` による UTC の日付と時刻の ISO 8601 文字列値

## <a name="return-types"></a>戻り値の型

UNIX エポックから経過した現在の 100 ナノ秒数を示す符号付き数値を返します。 つまり、DateTimeToTicks は、1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過した100 ナノ秒のティック数を返します。

## <a name="remarks"></a>注釈

DateTime が有効な ISO 8601 DateTime でない場合、DateTimeDateTimeToTicks は `undefined` を返します

このシステム関数では、インデックスは使用されません。

## <a name="examples"></a>例

ティック数を返す例を次に示します。

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

秒の小数部の数を指定せずに、タイマー刻みの数を返す例を次に示します。

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
