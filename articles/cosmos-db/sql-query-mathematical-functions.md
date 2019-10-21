---
title: Azure Cosmos DB クエリ言語の数学関数
description: Azure Cosmos DB での数学的な SQL システム関数について学習します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: fe4a84db3f2cbcfc2d9841caf520404afec2a297
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349679"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>数学関数 (Azure Cosmos DB)  

各数学関数は、引数として提供された入力値に基づいて計算を実行し、数値を返します。

次の例のようなクエリを実行できます。

```sql
    SELECT VALUE ABS(-4)
```

結果は次のとおりです。

```json
    [4]
```

## <a name="functions"></a>Functions

一般に、次のサポートされている組み込みの数学関数では、入力引数に基づいて計算を実行し、数値式を返します。
  
||||  
|-|-|-|  
|[ABS](sql-query-abs.md)|[ACOS](sql-query-acos.md)|[ASIN](sql-query-asin.md)|  
|[ATAN](sql-query-atan.md)|[ATN2](sql-query-atn2.md)|[CEILING](sql-query-ceiling.md)|  
|[COS](sql-query-cos.md)|[COT](sql-query-cot.md)|[DEGREES](sql-query-degrees.md)|  
|[EXP](sql-query-exp.md)|[FLOOR](sql-query-floor.md)|[LOG](sql-query-log.md)|  
|[LOG10](sql-query-log10.md)|[PI](sql-query-pi.md)|[POWER](sql-query-power.md)|  
|[RADIANS](sql-query-radians.md)|[RAND](sql-query-rand.md)|[ROUND](sql-query-round.md)|
|[SIGN](sql-query-sign.md)|[SIN](sql-query-sin.md)|[SQRT](sql-query-sqrt.md)|
|[SQUARE](sql-query-square.md)|[TAN](sql-query-tan.md)|[TRUNC](sql-query-trunc.md)||  
  
RAND を除く、すべての数学関数が決定論的関数です。 これは、特定の入力値のセットを使用して呼び出されるたびに、同じ結果を返すことを意味します。

## <a name="next-steps"></a>次の手順

- [システム関数 (Azure Cosmos DB)](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
- [ユーザー定義関数](sql-query-udfs.md)
- [集計](sql-query-aggregates.md)
