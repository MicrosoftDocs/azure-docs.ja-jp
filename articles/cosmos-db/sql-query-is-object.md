---
title: Azure Cosmos DB クエリ言語の IS_OBJECT
description: Azure Cosmos DB での SQL システム関数 IS_OBJECT について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8e55cda57202ce5a08257f1448adc828764ac817
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349816"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 指定した式の型が JSON オブジェクトであるかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>引数
  
*expr*  
   任意の式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`IS_OBJECT` 関数を使用して、JSON ブール値のオブジェクト、数値、文字列、null 値、オブジェクト、配列、未定義の型をチェックします。  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 結果セットは次のようになります。  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="next-steps"></a>次の手順

- [型チェック関数 (Azure Cosmos DB)](sql-query-type-checking-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
