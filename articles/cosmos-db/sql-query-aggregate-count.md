---
title: Azure Cosmos DB クエリ言語の COUNT
description: Azure Cosmos DB の Count (COUNT) SQL システム関数について説明します。
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96550577"
---
# <a name="count-azure-cosmos-db"></a>COUNT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この集計関数では、式内の値の数が返されます。
  
## <a name="syntax"></a>構文
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*scalar_expr*  
   任意のスカラー式です
  
## <a name="return-types"></a>戻り値の型
  
数値式を返します。  
  
## <a name="examples"></a>例
  
次の例では、コンテナー内の項目の合計数が返されます。
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT は、入力として任意のスカラー式を取ることができます。 以下のクエリでは、同じ結果が得られます。

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>解説

このシステム関数は、クエリのフィルター内のどのプロパティに対しても[範囲のインデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の集計関数](sql-query-aggregate-functions.md)