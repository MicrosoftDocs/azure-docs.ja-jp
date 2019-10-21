---
title: Azure Cosmos DB クエリ言語の POWER
description: Azure Cosmos DB での SQL システム関数 POWER について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349641"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 指定されたべき乗の指定された式の値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr1*  
   数値式です。  
  
*numeric_expr2*  
   *numeric_expr1* のべき乗です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、数値の 3 (数のキューブ) のべき乗数を示します。  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 結果セットは次のようになります。  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>次の手順

- [数学関数 (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
