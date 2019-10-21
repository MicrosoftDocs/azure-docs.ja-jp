---
title: Azure Cosmos DB クエリ言語の IS_NULL
description: Azure Cosmos DB での SQL システム関数 IS_NULL について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 20486b8f8c0436d264135bb09952345548222216
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349865"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
 指定した式の型が null であるかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`IS_NULL` 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null 値、オブジェクト、配列、未定義の型をチェックします。  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 結果セットは次のようになります。  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="next-steps"></a>次の手順

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
