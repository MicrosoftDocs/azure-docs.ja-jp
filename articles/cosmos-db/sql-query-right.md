---
title: Azure Cosmos DB クエリ言語の RIGHT
description: Azure Cosmos DB の SQL システム関数 RIGHT について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5aeee91db0b1ce891d7e4090a074ddda1f15a576
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349539"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
 指定された文字数分、文字列の右側の部分を返します。  
  
## <a name="syntax"></a>構文
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字を抽出する文字列式です。  
  
*num_expr*  
   文字数を指定する数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、さまざまな長さの値の "abc" の右側の部分を返します。  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
