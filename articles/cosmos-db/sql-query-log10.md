---
title: Azure Cosmos DB クエリ言語の LOG10
description: Azure Cosmos DB での SQL システム関数 LOG10 について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 56f1e96e7d4ee1b5f38ee7392665e17819ae554b
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349714"
---
# <a name="log10-azure-cosmos-db"></a>LOG10 (Azure Cosmos DB)
 指定された数値式の底 10 の対数を返します。  
  
## <a name="syntax"></a>構文
  
```sql
LOG10 (<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expression*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="remarks"></a>解説
  
  LOG10 関数と POWER 関数は、互いに逆の関係になります。 たとえば、10 ^ log10 (n) = n になります。  
  
## <a name="examples"></a>例
  
  次の例では、変数を宣言し、指定した変数 (100) の LOG10 値を返します。  
  
```sql
SELECT LOG10(100) AS log10 
```  
  
 結果セットは次のようになります。  
  
```json
[{log10: 2}]  
```  

## <a name="next-steps"></a>次の手順

- [数学関数 (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
