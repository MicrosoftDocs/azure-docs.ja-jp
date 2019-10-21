---
title: Azure Cosmos DB クエリ言語の CONCAT
description: Azure Cosmos DB での SQL システム関数 CONCAT について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e61d61a3d64ca7d7808619159e4dfc8e8b33d68
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351268"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 2 つ以上の文字列値を連結した結果である文字列を返します。  
  
## <a name="syntax"></a>構文
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   他の値に連結する文字列式です。 `CONCAT` 関数には、少なくとも 2 つの *str_expr* 引数が必要です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定した値の連結された文字列を返します。  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 結果セットは次のようになります。  
  
```json
[{"concat": "abcdef"}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
