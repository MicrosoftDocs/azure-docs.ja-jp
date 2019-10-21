---
title: Azure Cosmos DB クエリ言語の REPLICATE
description: Azure Cosmos DB の SQL システム関数 REPLICATE について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5e0b7f29c503daa8a95dcc46238e60728c0cec50
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349577"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICATE (Azure Cosmos DB)
 文字列値を指定された回数だけ繰り返します。
  
## <a name="syntax"></a>構文
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字列式です。
  
*num_expr*  
   数値式です。 *num_expr* が負の数値である場合、または有限の数値でない場合、結果は未定義になります。
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。
  
## <a name="remarks"></a>解説
  結果の最大長は 10,000 文字、つまり (length(*str_expr*) * *num_expr*) <= 10,000 です。

## <a name="examples"></a>例
  
  次の例は、クエリ内での `REPLICATE` の使用方法を示しています。
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 結果セットは次のようになります。
  
```json
[{"replicate": "aaa"}]
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
