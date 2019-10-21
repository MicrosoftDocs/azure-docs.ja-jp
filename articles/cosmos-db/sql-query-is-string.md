---
title: Azure Cosmos DB クエリ言語の IS_STRING
description: Azure Cosmos DB での SQL システム関数 IS_STRING について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e105394765ccd8a495a0b44b0c29e0c4c0b9f59a
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349790"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
 指定した式の型が文字列であるかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`IS_STRING` 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null 値、オブジェクト、配列、未定義の型をチェックします。  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 結果セットは次のようになります。  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="next-steps"></a>次の手順

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
