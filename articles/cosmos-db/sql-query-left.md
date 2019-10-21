---
title: Azure Cosmos DB クエリ言語の LEFT
description: Azure Cosmos DB での SQL システム関数 LEFT について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2e175e1ed62a4afb2a532add161dd2ab63ba9b1c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349777"
---
# <a name="left-azure-cosmos-db"></a>LEFT (Azure Cosmos DB)
 指定された文字数分、文字列の左側の部分を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   文字を抽出する文字列式です。  
  
*num_expr*  
   文字数を指定する数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  文字列式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、さまざまな長さの値の "abc" の左側の部分を返します。  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 結果セットは次のようになります。  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="next-steps"></a>次の手順

- [文字列関数 (Azure Cosmos DB)](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
