---
title: Azure Cosmos DB でインデックス作成ポリシーを管理する
description: Azure Cosmos DB でインデックス作成ポリシーを管理する方法について説明します
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: thweiss
ms.openlocfilehash: b80a4b8697544a0f7fe7cee99b666a513f53a0d6
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104844"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB でインデックス作成ポリシーを管理する

Azure Cosmos DB では、コンテナーごとに定義された[インデックス作成ポリシー](index-policy.md)に従ってデータのインデックスが作成されます。 新しく作成したコンテナーの既定のインデックス作成ポリシーでは、文字列または数値に範囲インデックスが適用されます。 このポリシーは、独自のカスタム インデックス作成ポリシーでオーバーライドできます。

## <a name="indexing-policy-examples"></a>インデックス作成ポリシーの例

JSON 形式で示されたインデックス作成ポリシーの例をいくつか紹介します。Azure portal では、これらがこの形式で公開されます。 同じパラメーターは、Azure CLI のほか、任意の SDK で設定することができます。

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>一部のプロパティ パスを選択的に除外するオプトアウト ポリシー

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

このインデックス作成ポリシーは、```kind```、```dataType```、```precision``` を既定値に手動で設定する下のポリシーと同じです。 これらのプロパティは明示的に設定する必要がなく、インデックス作成ポリシーから完全に除外できます (上記の例を参照)。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>一部のプロパティ パスを選択的に包含するオプトイン ポリシー

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

このインデックス作成ポリシーは、```kind```、```dataType```、```precision``` を既定値に手動で設定する下のポリシーと同じです。 これらのプロパティは明示的に設定する必要がなく、インデックス作成ポリシーから完全に除外できます (上記の例を参照)。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE] 
> 一般的に、モデルに追加される新しいプロパティのインデックスを Azure Cosmos DB がプロアクティブに作成できるよう、**オプトアウト** インデックス作成ポリシーの使用をお勧めします。

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>特定のプロパティ パスに対してのみ空間インデックスを使用する

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
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a>複合インデックス作成ポリシーの例

個々のプロパティのパスを含めたり除外したりするほかに、複合インデックスを指定することもできます。 複数のプロパティを対象とする 1 つの `ORDER BY` 句を使用したクエリを実行したい場合は、これらのプロパティに対する[複合インデックス](index-policy.md#composite-indexes)が必要になります。 さらに、複合インデックスには、さまざまなプロパティにフィルターや ORDER BY 句が与えられているクエリでパフォーマンス上の長所があります。

### <a name="composite-index-defined-for-name-asc-age-desc"></a>(name asc, age desc) に対して定義された複合インデックス:

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

名前と年齢に対する上記の複合インデックスは、クエリ #1 とクエリ #2 で必要になります。

クエリ #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

クエリ #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

この複合インデックスでは、クエリ #3 とクエリ #4 で長所があり、フィルターが最適化されます。

クエリ #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

クエリ #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>(name ASC, age ASC) と (name ASC, age DESC) に定義されている複合インデックス:

同じインデックス作成ポリシー内で、異なる複数の複合インデックスを定義できます。

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>(name ASC, age ASC) に定義されている複合インデックス:

順序の指定は任意です。 指定されていない場合、順序は昇順です。

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>インデックス作成をアクティブ状態に保ちながらすべてのプロパティ パスを除外する

このポリシーは、[Time-to-Live (TTL) 機能](time-to-live.md)がアクティブであるものの、(Azure Cosmos DB を純粋なキー/値ストアとして使用するための) セカンダリ インデックスは不要である状況で使用できます。

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>インデックス作成なし

このポリシーによってインデックス作成がオフになります。 `indexingMode` が `none` に設定されている場合、コンテナーで TTL を設定することはできません。

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>インデックス作成ポリシーの更新

Azure Cosmos DB では、インデックス作成ポリシーは下のいずれかの方法で更新できます。

- Azure portal を使用する
- Azure CLI を使用する
- SDK のいずれかを使用する

[インデックス作成ポリシーの更新](index-policy.md#modifying-the-indexing-policy)により、インデックスの変換がトリガーされます。 この変換の進行状況は、SDK から追跡することもできます。

> [!NOTE]
> インデックス作成ポリシーを更新するとき、Azure Cosmos DB への書き込みが中断されることはありません。 インデックスの再作成中、インデックスが更新されているとき、クエリから部分的な結果が返されることがあります。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

Azure Cosmos のコンテナーには、そのインデックス作成ポリシーが Azure portal で直接編集できる JSON ドキュメントとして格納されます。

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. 新しい Azure Cosmos アカウントを作成するか、既存のアカウントを選択します。

1. **[データ エクスプローラー]** ウィンドウを開いて、操作の対象となるコンテナーを選択します。

1. **[Scale & Settings]\(スケールと設定\)** をクリックします。

1. インデックス作成ポリシーの JSON ドキュメントに変更を加えます ([以下](#indexing-policy-examples)の例を参照)。

1. 完了したら、 **[保存]** をクリックします。

![Azure portal を使用してインデックス作成を管理する](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure CLI で [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) を使用すると、コンテナーのインデックス作成ポリシーの JSON 定義を置き換えることができます。

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>.NET SDK V2 の使用

[.NetSDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) の `DocumentCollection` オブジェクトでは、`IndexingMode` を変更し、`IncludedPaths` および `ExcludedPaths` を追加または削除できる `IndexingPolicy` プロパティを公開しています。


```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

インデックス変換の進行状況を追跡するには、`PopulateQuotaInfo` プロパティを `true` に設定する `RequestOptions` オブジェクトを渡します。

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-net-sdk-v3"></a>.NET SDK V3 の使用

[.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) の `ContainerProperties` オブジェクト (その使用法については[こちらのクイックスタート](create-sql-api-dotnet.md)を参照) では `IndexingPolicy` プロパティを公開しています。これを使用すると、`IndexingMode` を変更したり `IncludedPaths` や `ExcludedPaths` を追加または削除したりすることができます。


```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

インデックス変換の進行状況を追跡するには、`PopulateQuotaInfo` プロパティを `true` に設定する `RequestOptions` オブジェクトを渡したうえで、その値を `x-ms-documentdb-collection-index-transformation-progress` 応答ヘッダーから取得します。

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

新しいコンテナーの作成中にカスタム インデックス作成ポリシーを定義すると、SDK V3 の fluent API によって、この定義を簡潔かつ効率的な方法で記述できます。

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```

## <a name="use-the-java-sdk"></a>Java SDK の使用

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) の `DocumentCollection` オブジェクト (その使用法については[こちらのクイック スタート](create-sql-api-java.md)を参照) では、`getIndexingPolicy()` および `setIndexingPolicy()` メソッドを公開しています。 これらによって操作される `IndexingPolicy` オブジェクトを使用すると、インデックス作成モードを変更したり、対象のパスと対象外のパスを追加または削除したりすることができます。

コンテナーの詳細を取得する

```java
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();
```

インデックス作成モードを同期に設定する

```java
indexingPolicy.setIndexingMode(IndexingMode.Consistent);
```

対象パスを追加する

```java
Collection<IncludedPath> includedPaths = new ArrayList<>();
ExcludedPath includedPath = new IncludedPath();
includedPath.setPath("/age/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);
```

対象外パスを追加する

```java
Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);
```

空間インデックスを追加する

```java
Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);          
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

```

複合インデックスを追加する

```java
Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name/*");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age/*");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);
```

変更に従ってコンテナーを更新する

```java
 client.replaceCollection(container, null);
```

コンテナーに対するインデックス変換の進行状況を追跡するには、クォータ情報の読み込みを要求する `RequestOptions` オブジェクトを渡したうえで、その値を `x-ms-documentdb-collection-index-transformation-progress` 応答ヘッダーから取得します。

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Node.js SDK の使用

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) の `ContainerDefinition` インターフェイス (その使用法については[こちらのクイック スタート](create-sql-api-nodejs.md)を参照) では `indexingPolicy` プロパティを公開しています。これを使用すると、`indexingMode` を変更したり `includedPaths` や `excludedPaths` を追加または削除したりすることができます。

コンテナーの詳細を取得する

```javascript
const containerResponse = await client.database('database').container('container').read();
```

インデックス作成モードを同期に設定する

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

空間インデックスを含む対象パスを追加する

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

対象外パスを追加する

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

変更に従ってコンテナーを更新する

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

コンテナーに対するインデックス変換の進行状況を追跡するには、`populateQuotaInfo` プロパティを `true` に設定する `RequestOptions` オブジェクトを渡したうえで、その値を `x-ms-documentdb-collection-index-transformation-progress` 応答ヘッダーから取得します。

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Python SDK の使用

[Python SDK](https://pypi.org/project/azure-cosmos/) (その使用法については[こちらのクイック スタート](create-sql-api-python.md)を参照) を使用する場合、コンテナーの構成がディクショナリとして管理されます。 このディクショナリから、インデックス作成ポリシーとそのすべての属性にアクセスすることができます。

コンテナーの詳細を取得する

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

インデックス作成モードを同期に設定する

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

管理対象パスと空間インデックスと共にインデックス作成ポリシーを定義する

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

非管理対象パスと共にインデックス作成ポリシーを定義する

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

複合インデックスを追加する

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

変更に従ってコンテナーを更新する

```python
response = client.ReplaceContainer(containerPath, container)
```

## <a name="next-steps"></a>次の手順

以下の記事で、インデックス作成についての詳細を参照してください。

- [インデックス作成の概要](index-overview.md)
- [インデックス作成ポリシー](index-policy.md)
