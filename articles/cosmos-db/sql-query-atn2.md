---
title: Azure Cosmos DB クエリ言語の ATN2
description: Azure Cosmos DB での SQL システム関数 ATN2 について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43bf2f6e27d093b72560b87349150268e0f58a60
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350204"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 ラジアン単位で表される y/x のアーク タンジェントの主値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例は、指定された x と y コンポーネントの ATN2 を計算します。  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 結果セットは次のようになります。  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="next-steps"></a>次の手順

- [数学関数 (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
