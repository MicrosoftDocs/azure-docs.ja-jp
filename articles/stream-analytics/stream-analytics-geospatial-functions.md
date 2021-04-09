---
title: Azure Stream Analytics 地理空間関数の概要
description: この記事では、Azure Stream Analytics ジョブで使用される地理空間関数について説明します。
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438542"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Stream Analytics 地理空間関数の概要

Azure Stream Analytics の地理空間関数は、地理空間データのストリーミングにおけるリアルタイム分析を可能にします。 数行のコードを使用するだけで、複雑なシナリオに対応した運用環境グレードのソリューションを開発できます。 これらの関数では、すべての WKT の型と GeoJSON Point、Polygon、および LineString がサポートされています。

地理空間関数のメリットが得られるシナリオの例は次のとおりです。

* ライドシェア
* フリート管理
* 資産の追跡
* ジオフェンス
* セル サイト間での電話の追跡

Stream Analytics クエリ言語には、組み込みの地理空間関数が 7 つ (**CreateLineString**、**CreatePoint**、**CreatePolygon**、**ST_DISTANCE**、**ST_OVERLAPS**、**ST_INTERSECTS**、**ST_WITHIN**) あります。

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` 関数は、複数のポイントを受け入れて GeoJSON LineString を返します。LineString はマップ上の線として描画できます。 LineString を作成するには、少なくとも 2 つのポイントが必要です。 LineString ポイントは順番に連結されます。

次のクエリでは、`CreateLineString` を使用し、3 つのポイントを使用して LineString を作成します。 最初のポイントはストリーミング入力データから作成され、他の 2 つのポイントは手動で作成されます。

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>入力例  
  
|緯度 (latitude)|経度 (longitude)|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例  

 {"type" : "LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" : "LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

詳しくは、[CreateLineString](/stream-analytics-query/createlinestring) のリファレンスをご覧ください。

## <a name="createpoint"></a>CreatePoint

`CreatePoint` 関数は、緯度と経度を受け入れて GeoJSON ポイントを返します。ポイントはマップ上に描画できます。 緯度と経度は **float** データ型である必要があります。

次の例のクエリでは、`CreatePoint` を使用し、ストリーミング入力データからの緯度と経度を使用してポイントを作成します。

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>入力例  
  
|緯度 (latitude)|経度 (longitude)|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例
  
 {"type" : "Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" : "Point", "coordinates" : [20.2321, -87.33]}  

詳しくは、[CreatePoint](/stream-analytics-query/createpoint) のリファレンスをご覧ください。

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` 関数は、複数のポイントを受け入れて GeoJSON ポリゴン レコードを返します。 ポイントの順序は右手の指輪の向き (反時計回り) に従う必要があります。 宣言された順序で、あるポイントから別のポイントに歩くと想像してください。 左側全体がポリゴンの中心になります。

次の例のクエリでは、`CreatePolygon` を使用して 3 つのポイントからポリゴンを作成します。 最初の 2 つのポイントは手動で作成され、最後のポイントは入力データから作成されます。

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>入力例  
  
|緯度 (latitude)|経度 (longitude)|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>出力例  

 {"type" : "Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" : "Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

詳しくは、[CreatePolygon](/stream-analytics-query/createpolygon) のリファレンスをご覧ください。


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE` 関数では、2 つのジオメトリ間の距離がメートル単位で返されます。 

次のクエリでは、`ST_DISTANCE` を使用して、自動車からガソリン スタンドまでの距離が 10 km 未満になったときにイベントを生成します。

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

詳しくは、[ST_DISTANCE](/stream-analytics-query/st-distance) のリファレンスをご覧ください。

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` 関数では、2 つのジオメトリが比較されます。 ジオメトリが重なっている場合、この関数では 1 が返されます。 ジオメトリが重なっていない場合、この関数では 0 が返されます。 

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

詳しくは、[ST_OVERLAPS](/stream-analytics-query/st-overlaps) のリファレンスをご覧ください。

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS` 関数では、2 つのジオメトリが比較されます。 ジオメトリが交差する場合、この関数では 1 が返されます。 ジオメトリが互いに交差していない場合、この関数では 0 が返されます。

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

詳しくは、[ST_INTERSECTS](/stream-analytics-query/st-intersects) のリファレンスをご覧ください。

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN` 関数では、ジオメトリが別のジオメトリ内にあるかどうかが判断されます。 最初のものが最後のものに含まれている場合、この関数では 1 が返されます。 最初のジオメトリが最後のものの中にない場合、この関数では 0 が返されます。

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

詳しくは、[ST_WITHIN](/stream-analytics-query/st-within) のリファレンスをご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)
