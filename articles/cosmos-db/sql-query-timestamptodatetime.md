---
title: Azure Cosmos DB クエリ言語の TimestampToDateTime
description: Azure Cosmos DB での SQL システム関数 TimestampToDateTime について学習します。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9d4b5179ea08d5d6eca03422db7dfc7c8c4b5c3e
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88608661"
---
# <a name="timestamptodatetime-azure-cosmos-db"></a>TimestampToDateTime (Azure Cosmos DB)

指定したタイムスタンプ値を DateTime に変換します。
  
## <a name="syntax"></a>構文
  
```sql
TimestampToDateTime (<Timestamp>)
```

## <a name="arguments"></a>引数

*Timestamp*  

Unix エポックから経過した現在の 100 ミリ秒数である符号付き数値です。 つまり、1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数です。

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
  |Z|UTC (協定世界時) 指定子||
  
  ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください

## <a name="remarks"></a>解説

指定したタイムスタンプ値が無効な場合、TimestampToDateTime は `undefined` を返します。

## <a name="examples"></a>例
  
次の例では、タイムスタンプを DateTime に変換しています。

```sql
SELECT TimestampToDateTime(1594227912345) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-08T17:05:12.3450000Z"
    }
]
```  

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
