---
title: Azure Cosmos DB クエリ言語の ST_ISVALID
description: Azure Cosmos DB の SQL システム関数 ST_ISVALID について説明します。
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349366"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。  
  
## <a name="syntax"></a>構文
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>引数
  
*spatial_expr*  
   GeoJSON Point、Polygon、または LineString 式です。  
  
## <a name="return-types"></a>戻り値の型
  
  ブール式を返します。  
  
## <a name="examples"></a>例
  
  次の例では、ST_VALID を使用してポイントが有効かどうかを確認する方法を示します。  
  
  たとえば、このポイントの緯度の値は有効な値の範囲 [-90, 90] に含まれていないので、クエリは false を返します。  
  
  GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、ポリゴンの条件となります。 ポリゴン内のポイントは、反時計回りに指定する必要があります。 時計回りに指定されたポリゴンは、その中の領域を逆にしたものを表します。  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 結果セットは次のようになります。  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>次の手順

- [Azure Cosmos DB の空間関数](sql-query-spatial-functions.md)
- [Azure Cosmos DB のシステム関数](sql-query-system-functions.md)
- [Azure Cosmos DB の概要](introduction.md)
