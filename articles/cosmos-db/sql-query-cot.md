---
title: Azure Cosmos DB クエリ言語の COT
description: Azure Cosmos DB での SQL システム関数 COT について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d980319730e69fdcb529272ba1b8fb48d2b5b230
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71351252"
---
# <a name="cot-azure-cosmos-db"></a>COT (Azure Cosmos DB)
 数値式で指定されたラジアン単位の角度の三角関数コタンジェントを返します。  
  
## <a name="syntax"></a>構文
  
```sql
COT(<numeric_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*numeric_expr*  
   数値式です。  
  
## <a name="return-types"></a>戻り値の型
  
  数値式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、指定された角度の `COT` を計算します。  
  
```sql
SELECT COT(124.1332) AS cot  
```  
  
 結果セットは次のようになります。  
  
```json
[{"cot": -0.040311998371148884}]  
```  
  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の数学関数](sql-query-mathematical-functions.md)
- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
