---
title: Azure Cosmos DB クエリ言語の GetCurrentDateTime
description: Azure Cosmos DB での SQL システム関数 GetCurrentDateTime について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ec0b8ccaceed4abe3dd2784463f507f3bc76d890
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606961"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)

UTC (協定世界時) での現在の日付と時刻を ISO 8601 文字列として返します。
  
## <a name="syntax"></a>構文
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>戻り値の型
  
  UTC での現在の日付と時刻を ISO 8601 文字列値として `YYYY-MM-DDThh:mm:ss.fffffffZ` の形式で返します。
  
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

GetCurrentDateTime() は非決定論的関数です。 返される結果は UTC です。 有効桁数は 7 桁で、精度は 100 ナノ秒です。

このシステム関数では、インデックスは使用されません。

## <a name="examples"></a>例
  
次の例は、GetCurrentDateTime() 組み込み関数を使用して、UTC での現在の日付と時刻を取得する方法を示しています。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 結果セットは次のようになります。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
