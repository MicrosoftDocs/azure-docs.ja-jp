---
title: Azure Cosmos DB に地理空間データを照会する
description: Azure Cosmos DB に空間データを照会します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336131"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB に地理空間データを照会する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、SQL と LINQ を使用して、Azure Cosmos DB に地理空間データを照会する方法について取り上げます。 現在、地理空間データの保存とアクセスは、Azure Cosmos DB SQL API アカウントのみでサポートされます。 Azure Cosmos DB は、以下の Open Geospatial Consortium (OGC) 組み込み関数を使った地理空間検索をサポートしています。 SQL 言語の全組み込み関数の詳細については、[Azure Cosmos DB のクエリ システム関数](sql-query-system-functions.md)に関する記事を参照してください。

## <a name="spatial-sql-built-in-functions"></a>空間 SQL 組み込み関数

以下に示したのは、Azure Cosmos DB のクエリに使用できる地理空間システム関数の一覧です。

|**使用方法**|**説明**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | 2 つの GeoJSON Point、Polygon、または LineString 式間の距離を返します。|
|ST_WITHIN (spatial_expr, spatial_expr) | 1 つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) が 2 つ目の GeoJSON オブジェクト (Point、Polygon、または LineString) 内に存在するかどうかを示すブール式を返します。|
|ST_INTERSECTS (spatial_expr, spatial_expr)| 指定された 2 つの GeoJSON オブジェクト (Point、Polygon、または LineString) が重なるかどうかを示すブール式を返します。|
|ST_ISVALID| 指定された GeoJSON Point、Polygon、または LineString 式が有効かどうかを示すブール値を返します。|
| ST_ISVALIDDETAILED| 指定された GeoJSON Point、Polygon、または LineString 式が有効であるかどうかを示すブール値を含んだ JSON 値を返します。 無効な場合は、その理由が文字列値として返されます。|

空間関数を使用すると、空間データに対して近接検索クエリを実行することができます。 指定された場所の 30 km 圏内に存在するすべての世帯ドキュメントを `ST_DISTANCE` 組み込み関数で取得するクエリの例を以下に示します。

**クエリ**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**結果**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

インデックス作成ポリシーに空間インデックスを含めた場合、"距離クエリ" はインデックスを使って効率的に実行されます。 空間インデックスの詳細については、[地理空間インデックス](sql-query-geospatial-index.md)に関するページを参照してください。 指定されたパスに空間インデックスがない場合でも、クエリによってコンテナーのスキャンは実行されます。

ポイントが Polygon 内に存在するかどうかは、`ST_WITHIN` を使用してチェックできます。 通常 Polygon は、郵便番号、都道府県の境界など、自然な形状の範囲を表す目的で使用されます。 インデックス作成ポリシーに空間インデックスを含めた場合、"範囲内" 検索はインデックスを使って効率的に実行されます。

`ST_WITHIN` の Polygon 引数に指定できるのは、単一のリングだけです。つまり、環の内側に穴が含まれている Polygon は指定できません。

**クエリ**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**結果**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Azure Cosmos DB クエリで型が一致しないときの動作と同様、いずれかの引数に指定された場所の値が無効であったり形式に誤りがあったりした場合、その値は **未定義** として評価され、評価対象となったドキュメントはクエリの結果からスキップされます。 クエリから結果が返されなかった場合は、`ST_ISVALIDDETAILED` を実行して、空間データ型が無効である理由をデバッグしてください。
>
>

Azure Cosmos DB は逆クエリの実行もサポートします。つまり、Azure Cosmos DB で Polygon や Line のインデックスを作成し、指定したポイントが含まれている領域をクエリすることができます。 このパターンは物流では一般的であり、たとえば、指定された領域へのトラックの出入りを識別するために使用されています。

**クエリ**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**結果**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

空間オブジェクトが有効であるかどうかは、`ST_ISVALID` と `ST_ISVALIDDETAILED` を使用してチェックできます。 たとえば以下のクエリでは、範囲外の緯度値 (-132.8) を指定して、ポイントの有効性をチェックしています。 `ST_ISVALID` で返されるのはブール値だけであるのに対し、`ST_ISVALIDDETAILED` では、ブール値に加え、無効と考えられる理由の文字列が返されます。

**クエリ**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**結果**

```json
    [{
      "$1": false
    }]
```

これらの関数を使用して、Polygon を検証することもできます。 以下の例では、閉じた形状になっていない Polygon を `ST_ISVALIDDETAILED` で検証しています。

**クエリ**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**結果**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>.NET SDK での LINQ クエリ

SQL .NET SDK には、LINQ 式の中で使用するための、`Distance()` と `Within()` というスタブ メソッドも用意されています。 これらのメソッド呼び出しは、SQL LINQ プロバイダーによって等価な SQL 組み込み関数呼び出し (それぞれ ST_DISTANCE と ST_WITHIN) に変換されます。

LINQ を使用して、`location` の値が、指定された地点の半径 30 km 以内にある Azure Cosmos コンテナー内のすべてのドキュメントを検索する LINQ クエリの例を次に示します。

**距離検索の LINQ クエリ**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

同様に、`location` が、指定されたボックスまたは Polygon 内に存在するドキュメントをすべて検索するクエリを以下に示します。

**範囲検索の LINQ クエリ**

```csharp
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

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の地理空間機能の基本的な使い方を学んだら、次の段階に進みましょう。

* [Azure Cosmos DB のクエリ](sql-query-getting-started.md)の詳細を確認する
* [Azure Cosmos DB で地理空間データと GeoJSON 位置データを扱う](sql-query-geospatial-intro.md)方法の詳細を確認する
* [Azure Cosmos DB で空間データのインデックスを作成する](sql-query-geospatial-index.md)方法の詳細を確認する
