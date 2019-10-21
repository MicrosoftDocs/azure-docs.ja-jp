---
title: Azure Cosmos DB クエリ言語の IS_NUMBER
description: Azure Cosmos DB での SQL システム関数 IS_NUMBER について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 624f5c91a9d2a0eb4744d310120050d0d5ccef4e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349846"
---
# <a name="is_number-azure-cosmos-db"></a>IS_NUMBER (Azure Cosmos DB)
 指定した式の型が数値であるかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_NUMBER(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`IS_NUMBER` 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null 値、オブジェクト、配列、未定義の型をチェックします。  
  
```sql
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 結果セットは次のようになります。  
  
```json
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  

## <a name="next-steps"></a>次の手順

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
