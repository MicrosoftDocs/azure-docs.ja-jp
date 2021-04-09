---
title: Azure Cosmos DB クエリ言語の GetCurrentTimestamp
description: Azure Cosmos DB での SQL システム関数 GetCurrentTimestamp について学習します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: fa7d1ec2af12065fb7d761073cd982a561cf53c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524263"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。
  
## <a name="syntax"></a>構文
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>戻り値の型
  
Unix エポックから現在までに経過したミリ秒数を表す符号付き数値、つまり 1970 年 1 月 1 日木曜日 00 時 00 分 00 秒から経過したミリ秒数を返します。

## <a name="remarks"></a>解説

GetCurrentTimestamp() は非決定論的関数です。 返される結果は UTC (協定世界時) です。

> [!NOTE]
> このシステム関数では、インデックスは使用されません。 値を現在の時刻と比較する場合は、現在の時刻をクエリ実行前に取得して、その定数文字列値を `WHERE` 句で使用します。

## <a name="examples"></a>例
  
  次の例は、GetCurrentTimestamp() 組み込み関数を使用して、現在のタイムスタンプを取得する方法を示しています。
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 結果セットは次のようになります。
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>次のステップ

- [日付と時刻関数 (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
