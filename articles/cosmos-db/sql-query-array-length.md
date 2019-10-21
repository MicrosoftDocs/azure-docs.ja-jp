---
title: Azure Cosmos DB クエリ言語の ARRAY_LENGTH
description: Azure Cosmos DB での SQL システム関数 ARRAY_LENGTH について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b27b3f09212047e2e8937a4bf649fa3335e15cb2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348662"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 指定された配列式の要素の数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*arr_expr*  
   配列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ARRAY_LENGTH` を使用して、配列の長さを取得する方法を示します。  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 結果セットは次のようになります。  
  
```json
[{"len": 3}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の配列関数](sql-query-array-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
