---
title: Azure Cosmos DB SQL API アカウントで地理空間データを扱う
description: Azure Cosmos DB と SQL API を使用した空間オブジェクトの作成、インデックス作成、クエリの方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: sngun
ms.openlocfilehash: 1654ccabab751896fcbbab09d112080ccc2f67b6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467726"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB SQL API アカウントで地理空間データと GeoJSON 位置データを使用する

この記事では、Azure Cosmos DB の地理空間機能を紹介します。 現在、地理空間データの保存とアクセスは、Cosmos DB SQL API アカウントのみでサポートされます。 この記事を読むと、次の質問に回答できるようになります。

* 空間データを Azure Cosmos DB に保存する方法
* Azure Cosmos DB 内の地理空間データを SQL や LINQ で照会する方法
* Azure Cosmos DB の空間インデックスを有効または無効にする方法

この記事では、SQL API を使用して地理空間データを扱う方法について説明します。 コード サンプルについては、こちらの [GitHub プロジェクト](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)を参照してください。

## <a name="introduction-to-spatial-data"></a>空間データの概要
空間データは、物体の空間における位置と形状を表現します。 ほとんどのアプリケーションにおける空間データは、地球上の物体、および地理空間データに対応します。 空間データを使用することで、人物の位置や、関心のある場所、都市や湖の境界を表現することができます。 代表的な使用例に近接検索クエリがあります (例: "現在地付近に存在するコーヒー ショップをすべて検索")。 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB は、[GeoJSON 仕様](https://tools.ietf.org/html/rfc7946)を使用して表現された地理空間ポイント データのインデックスとクエリをサポートしています。 GeoJSON データの構造は常に有効な JSON オブジェクトであるため、特殊なツールやライブラリがなくても、Azure Cosmos DB を使って保存とクエリを実行できます。 Azure Cosmos DB SDK には、空間データを簡単に扱うことができるヘルパー クラスとヘルパー メソッドが用意されています。 

### <a name="points-linestrings-and-polygons"></a>Point、LineString、Polygon
**Point** は、空間における一点の位置を表します。 スーパーやキオスクの所在地、自動車、都市などの位置は、地理空間データにおけるポイントによって正確に表されます。  GeoJSON (と Azure Cosmos DB) では、ポイントがその座標ペアまたは経度/緯度によって表されます。 次に示したのは、ポイントを表す JSON の例です。

**Azure Cosmos DB のポイント**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON 仕様では、まず経度を指定し、次に緯度を指定します。 他の地図作成アプリケーションと同様、経度と緯度は角度であり、度 (°) の単位で表されます。 経度値は、グリニッジ子午線を基準とする -180°～ 180.0°の範囲で測定され、緯度値は、赤道を基準とする -90.0°～ 90.0°の範囲で測定されます。 
> 
> Azure Cosmos DB では座標が WGS-84 測地系で解釈されます。 座標参照系の詳細については、以下を参照してください。
> 
> 

このデータは、Azure Cosmos DB ドキュメントに埋め込むことができます。次の例では、位置情報データを含んだユーザー プロファイルにデータを埋め込んでいます。

**Azure Cosmos DB に保存された、位置データを含むプロファイルの使用**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

GeoJSON は、Point に加え、LineString と Polygon をサポートしています。 **LineString** は、空間における一連の複数のポイントとそれらをつなぐ線分を表します。 地理空間データでは、幹線道路や川を表すために LineString がよく使用されます。 接続されたポイントが LineString を形成し、それによって囲まれた境界が **Polygon** となります。 ポリゴンは通常、湖や行政区 (市区町村、都道府県など) など、自然な形状を表す目的で使用されます。 以下は Azure Cosmos DB における Polygon の例です。 

**GeoJSON における Polygon**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 仕様では、最後に指定された座標ペアが、最初に指定された座標ペアとちょうど重なり、閉じた形状になっていることが、有効な Polygon の条件となります。
> 
> Polygon 内のポイントは、反時計回りに指定する必要があります。 時計回りに指定された Polygon は、その中の領域を逆にしたものを表します。
> 
> 

GeoJSON では、Point、LineString、Polygon に加え、複数の地理空間位置をグループ化したり、位置情報を持った任意のプロパティを **Feature**として関連付けたりする場合の表現方法も規定されています。 これらのオブジェクトは有効な JSON であるため、いずれも Azure Cosmos DB で保存および処理できます。 ただし、Azure Cosmos DB では、ポイントのインデックス自動作成のみがサポートされています。

### <a name="coordinate-reference-systems"></a>座標参照系
地球の形状は不規則であるため、地理空間データの座標は多く座標参照系 (CRS) で表され、それぞれ独自の基準系と測定単位が存在します。 たとえば、"National Grid of Britain" は、イギリスでは精度の高い座標系ですが、イギリス外では精度が下がります。 

今日使われている最も一般的な CRS は、世界測地系の [WGS-84](http://earth-info.nga.mil/GandG/wgs84/) です。 WGS-84 は、GPS 装置や、多くの地図サービス (Google マップ、Bing マップの API など) で使用されています。 Azure Cosmos DB でサポートされるのは、WGS-84 CRS を使用した地理空間データのインデックスとクエリだけです。 

## <a name="creating-documents-with-spatial-data"></a>空間データを使用したドキュメントの作成
GeoJSON の値を含んだドキュメントを作成すると、対応するコンテナーのインデックス作成ポリシーに従い、空間インデックスを使用して自動的にインデックスが作成されます。 Python や Node.js など動的に型付けされる言語で Azure Cosmos DB SDK を使用している場合は、有効な GeoJSON を作成する必要があります。

**地理空間データを含んだドキュメントの作成 (Node.js)**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API の場合は、`Microsoft.Azure.Documents.Spatial` 名前空間の `Point` クラスと `Polygon` クラスを使って、アプリケーションのオブジェクト内に位置情報を埋め込むことができます。 これらのクラスを使用して簡単に、空間データを GeoJSON にシリアル化したり、逆シリアル化したりすることができます。

**地理空間データを含んだドキュメントの作成 (.NET)**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

緯度情報と経度情報がなくても、物理的な住所や所在地名 (都市、国や地域など) があれば、Bing マップ REST サービスなどのジオコーディング サービスを使って実際の座標を検索することができます。 Bing マップのジオコーディングの詳細については、 [こちら](https://msdn.microsoft.com/library/ff701713.aspx)を参照してください。

## <a name="querying-spatial-types"></a>空間データ型のクエリ
地理空間データの挿入方法がわかったら、SQL と LINQ で Azure Cosmos DB のデータを検索してみましょう。

### <a name="spatial-sql-built-in-functions"></a>空間 SQL 組み込み関数
Azure Cosmos DB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 SQL 言語の全組み込み関数の詳細については、[Azure Cosmos DB のクエリ](how-to-sql-query.md)に関する記事をご覧ください。

|**使用方法**|**説明**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | 2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。|
|ST_WITHIN (spatial_expr, spatial_expr) | 1 つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。|
|ST_INTERSECTS (spatial_expr, spatial_expr)| 指定された 2 つの GeoJSON オブジェクト (Point、Polygon、または LineString) が重なるかどうかを示すブール式を返します。|
|ST_ISVALID| 指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。|
| ST_ISVALIDDETAILED| 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかのブール値を含んだ JSON 値を返します。無効である場合はさらに、その理由が文字列値として返されます。|

空間関数を使用すると、空間データに対して近接検索クエリを実行することができます。 指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを ST_DISTANCE 組み込み関数で取得するクエリの例を以下に示します。 

**クエリ**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

インデックス作成ポリシーに空間インデックスを含めた場合、"距離クエリ" はインデックスを使って効率的に実行されます。 空間インデックスの詳細については、以下のセクションを参照してください。 指定されたパスに空間インデックスがない場合でも、 `x-ms-documentdb-query-enable-scan` 要求ヘッダーの値を "true" に設定して指定することによって空間クエリを実行することはできます。 その場合、.NET では、省略可能な引数 **FeedOptions** を、 [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) を true に設定してクエリに渡してください。 

ポイントが Polygon 内に存在するかどうかは、ST_WITHIN を使用してチェックできます。 通常 Polygon は、郵便番号、都道府県の境界など、自然な形状の範囲を表す目的で使用されます。 インデックス作成ポリシーに空間インデックスを含めた場合、"範囲内" 検索はインデックスを使って効率的に実行されます。 

ST_WITHIN の Polygon 引数に指定できるのは、単一のリングだけです。つまり、環の内側に穴が含まれている Polygon は指定できません。 

**クエリ**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**結果**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Azure Cosmos DB クエリで型が一致しないときの動作と同様、いずれかの引数に指定された場所の値が無効であったり形式に誤りがあったりした場合、その値は**未定義**として評価され、評価対象となったドキュメントはクエリの結果からスキップされます。 クエリから結果が返されなかった場合は、ST_ISVALIDDETAILED を実行して、空間データ型が無効である理由をデバッグしてください。     
> 
> 

Azure Cosmos DB は逆クエリの実行もサポートします。つまり、Azure Cosmos DB で Polygon や Line のインデックスを作成し、指定したポイントが含まれている領域をクエリすることができます。 このパターンは物流では一般的であり、たとえば、指定された領域へのトラックの出入りを識別するために使用されています。 

**クエリ**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**結果**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

空間オブジェクトが有効であるかどうかは、ST_ISVALID と ST_ISVALIDDETAILED を使用してチェックできます。 たとえば以下のクエリでは、範囲外の緯度値 (-132.8) を指定して、ポイントの有効性をチェックしています。 ST_ISVALID で返されるのはブール値だけであるのに対し、ST_ISVALIDDETAILED では、ブール値に加え、無効と考えられる理由の文字列が返されます。

**クエリ**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**結果**

    [{
      "$1": false
    }]

これらの関数を使用して、Polygon を検証することもできます。 以下の例では、閉じた形状になっていない Polygon を ST_ISVALIDDETAILED で検証しています。 

**クエリ**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**結果**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>.NET SDK での LINQ クエリ
SQL .NET SDK には、LINQ 式の中で使用するための、`Distance()` と `Within()` というスタブ メソッドも用意されています。 これらのメソッド呼び出しは、SQL LINQ プロバイダーによって等価な SQL 組み込み関数呼び出し (それぞれ ST_DISTANCE と ST_WITHIN) に変換されます。 

以下は LINQ クエリの一例で、LINQ を使用して、Azure Cosmos DB コレクションのドキュメントのうち、指定されたポイントから半径 30 km 圏内に "location" 値があるものをすべて検索しています。

**距離検索の LINQ クエリ**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

同様に、"location" が、指定されたボックス/Polygon 内に存在するドキュメントをすべて検索するクエリを以下に示します。 

**範囲検索の LINQ クエリ**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


以上で、LINQ と SQL を使ってドキュメントを検索する方法を見てきました。今度は、Azure Cosmos DB に対して空間インデックスを構成する方法について見ていきましょう。

## <a name="indexing"></a>インデックス作成
[Azure Cosmos DB のスキーマ非依存インデックス](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)に関するホワイト ペーパーで説明されているように、Azure Cosmos DB のデータベース エンジンはスキーマを一切必要とせず、JSON を高いレベルでサポートするように設計されています。 書き込みに最適化された Azure Cosmos DB のデータベース エンジンは、GeoJSON 標準で表された空間データ (Point、Polygon、Line) をネイティブに認識できます。

簡単に言えば、ジオメトリは、測地座標系から 2D 平面に投影された後、**4 分木**を使用して段階的にセルに分割されます。 これらのセルは、その位置に基づき、**ヒルベルト空間充填曲線**内で一次元にマッピングされ、ポイントの局所性が維持されます。 さらに、位置情報データのインデックスを作成するとき、そのデータに、**テセレーション**と呼ばれるプロセスが適用されます。つまり、特定の位置と交差するすべてのセルが特定され、キーとして Azure Cosmos DB のインデックスに保存されます。 クエリの実行時、ポイントや Polygon などの引数は、同様にテセレーションを経て関連するセル ID 範囲が抽出された後、インデックスからデータを取得するために使用されます。

/* (すべてのパス) の空間インデックスを含んだインデックス作成ポリシーを指定した場合、コレクション内で検出されたすべてのポイントがインデックス化され、効率的な空間クエリ (ST_WITHIN および ST_DISTANCE) の実行が可能となります。 空間インデックスには精度値がありません。必ず既定の精度値を使用してください。

> [!NOTE]
> Azure Cosmos DB は、Point、Polygon、LineString のインデックス自動作成をサポートしています。
> 
> 

以下の JSON スニペットは、空間インデックスを有効にしたインデックス作成ポリシーを示したものです。つまり、ドキュメント内に見つかったすべての GeoJSON ポイントのインデックスが作成され、空間クエリに使用されます。 Azure Portal を使用してインデックス作成ポリシーを変更する場合、インデックス作成ポリシーで以下の JSON を指定するとコレクションの空間インデックスを有効にすることができます。

**インデックス作成ポリシーの JSON でコレクションの空間インデックスを Point と Polygon に有効にする**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

以下の .NET コードでは、ポイントを含んだすべてのパスを対象に、空間インデックスを有効にしてコレクションを作成しています。 

**空間インデックスを有効にしてコレクションを作成する**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

以下の例では、ドキュメント内に保存されたすべてのポイントで空間インデックスを利用するように既存のコレクションに変更を加えています。

**既存のコレクションの空間インデックスを有効にする**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> ドキュメント内の GeoJSON の location 値が無効であったり形式が正しくなかったりした場合、空間クエリ用のインデックスは作成されません。 ST_ISVALID と ST_ISVALIDDETAILED を使用して location 値を検証できます。
> 
> コレクションの定義にパーティション キーが含まれている場合、インデックス変換の進行状況は報告されません。 
> 
> 

## <a name="next-steps"></a>次の手順
Azure Cosmos DB の地理空間機能の基本的な使い方を学んだら、次の段階に進みましょう。

* [GitHub の地理空間 .NET コード サンプル](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)
* [Azure Cosmos DB Query Playground](https://www.documentdb.com/sql/demo#geospatial) で地理空間のクエリを実際に体験する
* [Azure Cosmos DB のクエリ](how-to-sql-query.md)の詳細を確認する
* [Azure Cosmos DB のインデックス作成ポリシー](index-policy.md)の詳細を確認する

