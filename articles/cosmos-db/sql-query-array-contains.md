---
title: Azure Cosmos DB クエリ言語の ARRAY_CONTAINS
description: 配列に、指定した値が配列に含まれているかどうかを示すブール値を返す Azure Cosmos DB 内の SQL システム関数を格納する方法について説明します
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ff88d66ee6d1ceee4f1cf4e7b6501ab323dbc79e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93332663"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

配列に指定された値が含まれているかどうかを示すブール値を返します。 コマンド内でブール式を使用して、オブジェクトの部分一致または完全一致を確認できます。 

## <a name="syntax"></a>構文
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   検索対象となる配列式です。  
  
*expr*  
   検出される式です。  

*bool_expr*  
   ブール式です。 'true' と評価されていて、指定された検索値がオブジェクトである場合、コマンドでは部分一致がチェックされます (検索オブジェクトは、いずれか 1 つのオブジェクトのサブセットです)。 'false' に評価されている場合、コマンドでは配列内のすべてのオブジェクトの完全一致がチェックされます。 指定しない場合の既定値は false です。 
  
## <a name="return-types"></a>戻り値の型
  
  ブール値を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ARRAY_CONTAINS` を使用して、配列内のメンバーシップを確認する方法を示します。  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 結果セットは次のようになります。  
  
```json
[{"b1": true, "b2": false}]  
```  

次の例では、ARRAY_CONTAINS を使用して、配列内 JSON の部分一致を確認する方法を示します。  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 結果セットは次のようになります。  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>解説

このシステム関数は、[範囲インデックス](index-policy.md#includeexclude-strategy)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
