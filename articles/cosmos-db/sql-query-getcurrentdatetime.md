---
title: Azure Cosmos DB クエリ言語の GetCurrentDateTime
description: Azure Cosmos DB での SQL システム関数 GetCurrentDateTime について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8a2c3dcd3c8ca6dc9d751e50a7862fe98e6de510
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351025"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
 UTC (協定世界時) での現在の日付と時刻を ISO 8601 文字列として返します。
  
## <a name="syntax"></a>構文
  
```sql
GetCurrentDateTime ()
```
  
## <a name="return-types"></a>戻り値の型
  
  UTC での現在の日付と時刻を ISO 8601 文字列値として `YYYY-MM-DDThh:mm:ss.sssZ` の形式で返します。
  
  |||
  |-|-|
  |YYYY|4 桁の年|
  |mm|2 桁の月 (例: 01 = 1 月)|
  |DD|2 桁の日付 (01 から 31)|
  |T|時間要素の開始を表します|
  |hh|2 桁の時間 (00 から 23)|
  |mm|2 桁の分 (00 から 59)|
  |ss|2 桁の秒 (00 から 59)|
  |.sss|3 桁の秒の小数部|
  |Z|UTC (協定世界時) 指定子||
  
  ISO 8601 形式の詳細については、「[ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)」を参照してください

## <a name="remarks"></a>解説

  GetCurrentDateTime() は非決定論的関数です。 
  
  返される結果は UTC です。

## <a name="examples"></a>例
  
  次の例は、GetCurrentDateTime() 組み込み関数を使用して、UTC での現在の日付と時刻を取得する方法を示しています。
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 結果セットは次のようになります。
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

## <a name="next-steps"></a>次の手順

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
