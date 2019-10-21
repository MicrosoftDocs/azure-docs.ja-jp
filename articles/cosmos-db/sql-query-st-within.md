---
title: Azure Cosmos DB クエリ言語の ST_WITHIN
description: Azure Cosmos DB の SQL システム関数 ST_WITHIN について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 40ecb26e7ac782d7831e6ef94c9d3cfc6a370cbb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349337"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 最初の引数で指定された GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の引数の GeoJSON (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   GeoJSON Point、Polygon、または LineString オブジェクトの式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール値を返します。  
  
## <a name="examples"></a>例
  
  次の例では、`ST_WITHIN` を使用して多角形内のすべての世帯ドキュメントを検索する方法を示します。  
  
```sql
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 結果セットは次のようになります。  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
