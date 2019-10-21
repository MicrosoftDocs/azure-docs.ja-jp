---
title: Azure Cosmos DB クエリ言語の ST_ISVALIDDETAILED
description: Azure Cosmos DB の SQL システム関数 ST_ISVALIDDETAILED について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e640c223c2fef844b9b53e1f4afa3a5d398c8c0
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349341"
---
# <a name="st_isvaliddetailed-azure-cosmos-db"></a>ST_ISVALIDDETAILED (Azure Cosmos DB)
 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
## <a name="syntax"></a>構文
  
```sql
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   GeoJSON のポイントまたはポリゴンの式です。  
  
## <a name="return-types"></a>戻り値の型
  
  指定された GeoJSON ポイントまたはポリゴン式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。  
  
## <a name="examples"></a>例
  
  次の例では、`ST_ISVALIDDETAILED` を使用して有効性 (詳細) を確認する方法を示します。  
  
```sql
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 結果セットは次のようになります。  
  
```json
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
