---
title: Azure Cosmos DB クエリ言語の ST_DISTANCE
description: Azure Cosmos DB での SQL システム関数 ST_DISTANCE について説明します。
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f01f5faf68821fe9f85657c74111efdbb02bd204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100559936"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 2 つの GeoJSON Point、Polygon、MultiPolygon、または LineString 式間の距離を返します。 詳細については、[地理空間データと GeoJSON 位置データ](sql-query-geospatial-intro.md)に関する記事を参照してください。
  
## <a name="syntax"></a>構文
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   有効な GeoJSON ポイント、多角形、または LineString オブジェクト式です。  
  
## <a name="return-types"></a>戻り値の型
  
  距離を含む数値式を返します。 これは、既定の参照システムのメートル単位で表されます。  
  
## <a name="examples"></a>例
  
  次の例では、指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを `ST_DISTANCE` 組み込み関数で取得する方法を示します。 .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 結果セットは次のようになります。  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>解説

このシステム関数は、集計を使用したクエリを除き、[地理空間インデックス](index-policy.md#spatial-indexes)の恩恵を受けます。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
