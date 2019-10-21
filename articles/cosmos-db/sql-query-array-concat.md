---
title: Azure Cosmos DB クエリ言語の ARRAY_CONCAT
description: Azure Cosmos DB での SQL システム関数 ARRAY_CONCAT について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348720"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 2 つ以上の配列値を連結した結果である配列を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   他の値に連結する配列式です。 `ARRAY_CONCAT` 関数には、少なくとも 2 つの *arr_expr* 引数が必要です。  
  
## <a name="return-types"></a>戻り値の型
  
  配列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、2 つの配列を結合する方法を示します。  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 結果セットは次のようになります。  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
