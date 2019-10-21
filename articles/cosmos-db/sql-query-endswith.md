---
title: Azure Cosmos DB クエリ言語の ENDSWITH
description: Azure Cosmos DB での SQL システム関数 ENDSWITH について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 37c988d63e597c77bc09a1d21ad391909bb55901
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351056"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
 1 つ目の文字列式が 2 つ目の文字列で終了しているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ENDSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。  
  
*str_expr2*  
   *str_expr1* の末尾と比較される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"b" と "bc" で終わる "abc" を返します。  
  
```sql
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"e1": false, "e2": true}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
