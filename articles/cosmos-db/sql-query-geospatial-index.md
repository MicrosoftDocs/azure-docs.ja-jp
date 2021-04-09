---
title: Azure Cosmos DB を使用して地理空間データのインデックスを付ける
description: Azure Cosmos DB を使用して空間データのインデックスを付ける
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336216"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Azure Cosmos DB を使用して地理空間データのインデックスを付ける
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB のデータベース エンジンは、本当にスキーマに依存しないように設計されており、JSON のファースト クラスのサポートを実現しています。 書き込みに最適化された Azure Cosmos DB のデータベース エンジンは、GeoJSON 標準で表された空間データをネイティブに認識できるようになっています。

簡単に言えば、ジオメトリは、測地座標系から 2D 平面に投影された後、**4 分木** を使用して段階的にセルに分割されます。 これらのセルは、その位置に基づき、**ヒルベルト空間充填曲線** 内で一次元にマッピングされ、ポイントの局所性が維持されます。 さらに、位置情報データのインデックスを作成するとき、そのデータに、**テセレーション** と呼ばれるプロセスが適用されます。つまり、特定の位置と交差するすべてのセルが特定され、キーとして Azure Cosmos DB のインデックスに保存されます。 クエリの実行時、ポイントや Polygon などの引数は、同様にテセレーションを経て関連するセル ID 範囲が抽出された後、インデックスからデータを取得するために使用されます。

`/*` (すべてのパス) の空間インデックスを含むインデックス ポリシーを指定すると、効率的な空間クエリのために、コンテナー内で見つかったすべてのデータにインデックスが付けられます。

> [!NOTE]
> Azure Cosmos DB は、Point、LineString、Polygon、および MultiPolygon のインデックス作成をサポートしています。 これらの型のいずれかにインデックスを設定すると、他のすべての型にインデックスが自動的に設定されます。 つまり、Polygon にインデックスを設定すると、Point、LineString、MultiPolygon にもインデックスが設定されます。 新しい空間型にインデックスを設定しても、その型の有効な GeoJSON データがない限り、書き込み RU 使用量やインデックスのサイズには影響しません。

## <a name="modifying-geospatial-configuration"></a>地理空間構成の変更

コンテナーでは、**地理空間構成** によって、空間データのインデックスの作成方法が指定されます。 コンテナーごとに 1 つの **地理空間構成** (geography または geometry) を指定します。

Azure portal で、**geography** と **geometry** の間で空間型を切り替えることができます。 geometry 空間型に切り替える前に、[境界ボックスを使用した有効な空間 geometry インデックス作成ポリシー](#geometry-data-indexing-examples)を作成することが重要です。

Azure portal 内の **Data Explorer** で **地理空間構成** を設定する方法を次に示します。

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="地理空間構成の設定":::

.NET SDK で `geospatialConfig` を変更して、**地理空間構成** を調整することもできます。

指定しない場合、`geospatialConfig` は既定で geography データ型になります。 `geospatialConfig` を変更すると、コンテナー内のすべての既存の地理空間データのインデックスが再作成されます。

次の例では、`geospatialConfig` プロパティを設定し、**boundingBox** を追加することで、地理空間データ型を `geometry` に変更しています。

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>geography データのインデックス付けの例

次の JSON スニペットは、**geography** データ型に対して空間インデックスが有効なインデックス ポリシーを示しています。 これは、geography データの種類の空間データに対して有効であり、空間クエリのためにドキュメント内で見つかった GeoJSON の Point、Polygon、MultiPolygon、または LineString のインデックスが付けられます。 Azure portal を使用してインデックス作成ポリシーを変更する場合、インデックス作成ポリシーで以下の JSON を指定するとコンテナーの空間インデックスを有効にすることができます。

**geography 空間インデックスを使用したコンテナー インデックス作成ポリシー JSON**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> ドキュメント内の GeoJSON の location 値が無効であったり形式が正しくなかったりした場合、空間クエリ用のインデックスは作成されません。 ST_ISVALID と ST_ISVALIDDETAILED を使用して location 値を検証できます。

Azure CLI、PowerShell、または任意の SDK を使用して、[インデックス作成ポリシーを変更する](how-to-manage-indexing-policy.md)こともできます。

## <a name="geometry-data-indexing-examples"></a>geometry データのインデックス作成の例

**geometry** データ型では、geography データ型と同様に、関連パスとインデックスを作成する型を指定する必要があります。 さらに、インデックス作成ポリシー内に `boundingBox` を指定して、その特定のパスに対してインデックスを作成する領域を指定する必要があります。 それぞれの地理空間パスには、独自の `boundingBox` が必要です。

境界ボックスは、次のプロパティで構成されます。

- **xmin**: インデックスを作成する領域の最小 x 座標
- **ymin**: インデックスを作成する領域の最小 y 座標
- **xmax**: インデックスを作成する領域の最大 x 座標
- **ymax**: インデックスを作成する領域の最大 x 座標

幾何データで用いられる平面は無限に広がっている可能性があるため、境界ボックスが必要です。 ただし、空間インデックスでは、有限の空間が必要です。 **geography** データ型では、地球が境界であり、境界ボックスを設定する必要はありません。

データのすべて (またはほとんど) を含む境界ボックスを作成します。 空間インデックスを利用できるのは、完全に境界ボックスの内側にあるオブジェクトに対する計算を行う操作だけです。 境界ボックスを必要以上に大きくすると、クエリのパフォーマンスが低下します。

次に示すのは、**geospatialConfig** を `geometry` に設定して、**幾何** データのインデックスを作成するインデックス作成ポリシーの例です。

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

上記のインデックス作成ポリシーには、x 座標 (-10, 10)、y 座標 (-20, 20) の **境界ボックス** が使用されています。 上記のインデックス作成ポリシーが指定されているコンテナーでは、この領域内にあるすべてのポイント、ポリゴン、マルチポリゴン、およびラインストリングのインデックスが作成されます。

> [!NOTE]
> **boundingBox** を含むインデックス作成ポリシーを `geography` データ型のコンテナーに追加する操作は失敗します。 **boundingBox** を追加する前に、コンテナーの **geospatialConfig** を `geometry` に変更する必要があります。 コンテナーの地理空間データ型を選択する前または後に、データの追加や、インデックス作成ポリシーの残りの部分 (パスや種類など) の変更を行うことができます。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB の地理空間機能の基本的な使い方を学んだら、次の段階に進みましょう。

* [Azure Cosmos DB のクエリ](sql-query-getting-started.md)の詳細を確認する
* [Azure Cosmos DB を使用した空間データのクエリ](sql-query-geospatial-query.md)の詳細を確認する
* [Azure Cosmos DB で地理空間データと GeoJSON 位置データを扱う](sql-query-geospatial-intro.md)方法の詳細を確認する
