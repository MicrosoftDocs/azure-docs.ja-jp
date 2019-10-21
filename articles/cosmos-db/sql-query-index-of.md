---
title: Azure Cosmos DB クエリ言語の INDEX_OF
description: Azure Cosmos DB での SQL システム関数 INDEX_OF について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 50e489fdf57398d486b07944782ecbb3fd1d6a43
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350989"
---
# <a name="index_of-azure-cosmos-db"></a>INDEX_OF (Azure Cosmos DB)
 1 つ目に指定された文字列式内で 2 つ目の文字列式が最初に出現する箇所の開始位置を返します。文字列が見つからない場合は -1 を返します。  
  
## <a name="syntax"></a>構文
  
```sql
INDEX_OF(<str_expr1>, <str_expr2> [, <numeric_expr>])  
```  
  
## <a name="arguments"></a>引数
  
*str_expr1*  
   検索対象となる文字列式です。  
  
*str_expr2*  
   検索する文字列式です。  

*numeric_expr* 検索の開始位置を設定省略可能な数値式です。 *str_expr1* の最初の位置は 0 です。 
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、"abc" 内のさまざまな部分文字列のインデックスを返します。  
  
```sql
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 結果セットは次のようになります。  
  
```json
[{"i1": 0, "i2": 1, "i3": -1}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の文字列関数](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
