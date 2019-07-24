---
title: Azure Stream Analytics 地理空間関数の概要
description: この記事では、Azure Stream Analytics ジョブで使用される地理空間関数について説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53103134"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Stream Analytics 地理空間関数の概要

Azure Stream Analytics の地理空間関数は、地理空間データのストリーミングにおけるリアルタイム分析を可能にします。 数行のコードを使用するだけで、複雑なシナリオに対応した運用環境グレードのソリューションを開発できます。 

地理空間関数のメリットが得られるシナリオの例は次のとおりです。

* ライドシェア
* フリート管理
* 資産の追跡
* ジオフェンス
* セル サイト間での電話の追跡

Stream Analytics クエリ言語には、組み込みの地理空間関数が 7 つあります(**CreateLineString**、**CreatePoint**、**CreatePolygon**、**ST_DISTANCE**、**ST_OVERLAPS**、**ST_INTERSECTS**、**ST_WITHIN**)。

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` 関数は、複数のポイントを受け入れて GeoJSON LineString を返します。LineString はマップ上の線として描画できます。 LineString を作成するには、少なくとも 2 つのポイントが必要です。 LineString ポイントは順番に連結されます。

次のクエリでは、`CreateLineString` を使用し、3 つのポイントを使用して LineString を作成します。 最初のポイントはストリーミング入力データから作成され、他の 2 つのポイントは手動で作成されます。

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>入力例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例  

 {"type" :"LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" :"LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

詳しくは、[CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) のリファレンスをご覧ください。

## <a name="createpoint"></a>CreatePoint

`CreatePoint` 関数は、緯度と経度を受け入れて GeoJSON ポイントを返します。ポイントはマップ上に描画できます。 緯度と経度は **float** データ型である必要があります。

次の例のクエリでは、`CreatePoint` を使用し、ストリーミング入力データからの緯度と経度を使用してポイントを作成します。

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>入力例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例
  
 {"type" :"Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" :"Point", "coordinates" : [20.2321, -87.33]}  

詳しくは、[CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) のリファレンスをご覧ください。

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` 関数は、複数のポイントを受け入れて GeoJSON ポリゴン レコードを返します。 ポイントの順序は右手の指輪の向き (反時計回り) に従う必要があります。 宣言された順序で、あるポイントから別のポイントに歩くと想像してください。 左側全体がポリゴンの中心になります。

次の例のクエリでは、`CreatePolygon` を使用して 3 つのポイントからポリゴンを作成します。 最初の 2 つのポイントは手動で作成され、最後のポイントは入力データから作成されます。

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>入力例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例  

 {"type" :"Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" :"Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

詳しくは、[CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) のリファレンスをご覧ください。


## <a name="stdistance"></a>ST_DISTANCE
`ST_DISTANCE` 関数は、2 つのポイント間の距離をメートル単位で返します。 

次のクエリでは、`ST_DISTANCE` を使用して、自動車からガソリン スタンドまでの距離が 10 km 未満になったときにイベントを生成します。

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

詳しくは、[ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) のリファレンスをご覧ください。

## <a name="stoverlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` 関数は、2 つのポリゴンを比較します。 ポリゴンが重なっている場合、この関数は 1 を返します。 ポリゴンが重なっていない場合、この関数は 0 を返します。 

次のクエリでは、`ST_OVERLAPS` を使用して、洪水の危険性がある地域にビルがある場合にイベントを生成します。

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

次の例のクエリでは、自動車の進行方向の天気が荒天である場合にイベントを生成します。

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

詳しくは、[ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) のリファレンスをご覧ください。

## <a name="stintersects"></a>ST_INTERSECTS
`ST_INTERSECTS` 関数は、2 つの LineString を比較します。 LineString が交差する場合、この関数は 1 を返します。 LineString が交差しない場合、この関数は 0 を返します。

次の例のクエリでは、`ST_INTERSECTS` を使用して、舗装道路が未舗装の道路と交差しているかどうかを判断します。

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>入力例  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{"type":"LineString", "coordinates": [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{"type":"LineString", "coordinates": [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>出力例  

 1  
  
 0  

詳しくは、[ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) のリファレンスをご覧ください。

## <a name="stwithin"></a>ST_WITHIN
`ST_WITHIN` 関数は、ポイントまたはポリゴンが特定のポリゴン内にあるかどうかを判断します。 ポイントまたはポリゴンが特定のポリゴンに含まれている場合、この関数は 1 を返します。 ポイントまたはポリゴンが宣言されたポリゴン内にない場合、この関数は 0 を返します。

次の例のクエリでは、`ST_WITHIN` を使用して、配達先のポイントが指定された倉庫ポリゴン内にあるかどうかを判断します。

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>入力例  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{"type":"Point", "coordinates": [76.6, 10.1]}|{"type":"Polygon", "coordinates": [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{"type":"Point", "coordinates": [15.0, 15.0]}|{"type":"Polygon", "coordinates": [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>出力例  

 0  
  
 1  

詳しくは、[ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) のリファレンスをご覧ください。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)