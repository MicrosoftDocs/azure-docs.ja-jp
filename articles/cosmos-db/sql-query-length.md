---
title: Azure Cosmos DB クエリ言語の LENGTH
description: Azure Cosmos DB での SQL システム関数 LENGTH について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: aa430152415b1662a73a388a03ba6d4721c730f0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349756"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 指定された文字列式の文字数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*str_expr*  
   評価される文字列式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、文字列の長さを返します。  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 結果セットは次のようになります。  
  
```json
[{"len": 3}]  
```  

## <a name="next-steps"></a>次の手順

- [文字列関数 (Azure Cosmos DB)](sql-query-string-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
