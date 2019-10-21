---
title: Azure Cosmos DB クエリ言語の STARTSWITH
description: Azure Cosmos DB の SQL システム関数 STARTSWITH について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a7822425f17d6e121dfcb20d8766f0b3bc7032a2
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349317"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 1 つ目の文字列式が 2 つ目の文字列で始まっているかどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   文字列式です。
  
*str_expr2*  
   *str_expr1* の先頭と比較される文字列式です。

## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、文字列 "abc" が "b" および "a" で始まるかどうかを確認します。  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 結果セットは次のようになります。  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
