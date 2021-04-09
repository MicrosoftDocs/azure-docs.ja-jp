---
title: Azure Cosmos DB クエリ言語の ROUND
description: Azure Cosmos DB の SQL システム関数 ROUND について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7dc4d78f7af1086f9a4de9aa7392acb388df966e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590672"
---
# <a name="round-azure-cosmos-db"></a>ROUND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 最も近い整数値に丸められた数値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="remarks"></a>解説
  
実行される丸め操作では、中点はゼロから離れる方向に丸められます。 入力が 2 つの整数のちょうど真ん中になる数値式の場合、結果はゼロより遠い側で最も近い整数値になります。 このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。
  
|<numeric_expr>|丸めの結果|
|-|-|
|-6.5000|-7|
|-0.5|-1|
|0.5|1|
|6.5000|7|
  
## <a name="examples"></a>例
  
次の例では、最も近い整数に次の正と負の数値を丸めます。  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
結果セットは次のようになります。  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
