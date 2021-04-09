---
title: Azure Cosmos DB クエリ言語の GetCurrentDateTime
description: Azure Cosmos DB での SQL システム関数 GetCurrentDateTime について学習します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 12ce8beab082674cd7672713325d4b3f4322aeae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587306"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

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
|Z|UTC (協定世界時) 指定子|
  
  ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください

## <a name="remarks"></a>解説

GetCurrentDateTime() は非決定論的関数です。 返される結果は UTC です。 有効桁数は 7 桁で、精度は 100 ナノ秒です。

> [!NOTE]
> このシステム関数では、インデックスは使用されません。 値を現在の時刻と比較する場合は、現在の時刻をクエリ実行前に取得して、その定数文字列値を `WHERE` 句で使用します。

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
