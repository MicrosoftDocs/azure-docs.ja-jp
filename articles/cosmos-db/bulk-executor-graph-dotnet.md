---
title: Azure Cosmos DB Gremlin API を使って Graph Bulk Executor .NET ライブラリを使用する
description: Bulk Executor ライブラリを使って、Azure Cosmos DB Gremlin API コンテナーにグラフ データを大量にインポートする方法を説明します。
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 05/28/2019
ms.author: chrande
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 15e94dac02770bf28aae4cbfc4e337cb68b8be40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425326"
---
# <a name="using-the-graph-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Graph Bulk Executor .NET ライブラリを使って Azure Cosmos DB Gremlin API の一括操作を実行する
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

このチュートリアルでは、Azure CosmosDB の Bulk Executor .NET ライブラリを使って、Azure Cosmos DB Gremlin API コンテナーにグラフ オブジェクトをインポートし、更新する手順を紹介しています。 この手順では、[Bulk Executor ライブラリ](./bulk-executor-overview.md)の Graph クラスを利用してプログラムから Vertex オブジェクトと Edge オブジェクトを作成し、1 回のネットワーク要求につき複数それらを挿入します。 この動作を Bulk Executor ライブラリで構成することにより、データベース リソースとローカル メモリ リソースの両方を最大限に活用することができます。

Gremlin クエリをデータベースに送信した場合は、コマンドが 1 つずつ評価されて実行されますが、それとは対照的に Bulk Executor ライブラリでは、オブジェクトをローカルで作成して検証するよう要求されます。 オブジェクトの作成後、グラフ オブジェクトを順次データベース サービスに送信することができます。 この手法を用いることでデータ インジェストの速度を最大 100 倍に高めることができることから、初期データ移行や定期的なデータ移動の操作に最適な手段となっています。 詳細については、[Azure Cosmos DB Graph Bulk Executor サンプル アプリケーション](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)の GitHub ページを参照してください。

## <a name="bulk-operations-with-graph-data"></a>グラフ データの一括操作

[Bulk Executor ライブラリ](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph)には、グラフ オブジェクトを作成したりインポートしたりする機能を備えた `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` 名前空間が存在します。 

Gremlin API コンテナーに対してデータ移行を使用する大まかな手順は次のとおりです。
1. データ ソースからレコードを取得します。
2. 取得したレコードから `GremlinVertex` オブジェクトと `GremlinEdge` オブジェクトを構築し、それらを `IEnumerable` データ構造に追加します。 この部分に関しては、データ ソースがグラフ データベースではなかった場合を想定し、関係を検出して追加するためのロジックをアプリケーションに実装することをお勧めします。
3. [Graph BulkImportAsync メソッド](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync)を使ってグラフ オブジェクトをコレクションに挿入します。

このメカニズムは、Gremlin クライアントを使用した場合よりも、データ移行の効率が高くなります。 このように効率が改善される理由は、Gremlin を使ってデータを挿入した場合、アプリケーションは、クエリを一度に 1 つずつ送信する必要があるためです。データを作成するためには、クエリを 1 つずつ検証、評価、実行する必要があるのです。 Bulk Executor ライブラリは、アプリケーションで検証を処理し、1 回のネットワーク要求で一度に複数のグラフ オブジェクトを送信します。

### <a name="creating-vertices-and-edges"></a>頂点とエッジの作成

`GraphBulkExecutor` には、`GremlinVertex` オブジェクトまたは `GremlinEdge` オブジェクトの `IEnumerable` リストを受け取る `BulkImportAsync` メソッドがあります。どちらのオブジェクトも `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element` 名前空間に定義されています。 このサンプルでは、エッジと頂点を 2 つの BulkExecutor インポート タスクに分けています。 次の例を見てください。

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Bulk Executor ライブラリのパラメーターについて詳しくは、[Azure Cosmos DB へのデータの一括インポートに関するトピック](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account)を参照してください。

ペイロードは、`GremlinVertex` オブジェクトと `GremlinEdge` オブジェクトとしてインスタンス化する必要があります。 これらのオブジェクトの作成方法は次のとおりです。

**頂点**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**エッジ**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> Bulk Executor ユーティリティでは、エッジを追加する前に、既存の頂点の有無が自動的にはチェックされません。 この点については、BulkImport タスクを実行する前にアプリケーション側で確認する必要があります。

## <a name="sample-application"></a>サンプル アプリケーション

### <a name="prerequisites"></a>前提条件
* Visual Studio 2019 と Azure 開発ワークロード。 無料の [Visual Studio 2019 Community Edition](https://visualstudio.microsoft.com/downloads/) を使用できます。
* Azure サブスクリプション。 [こちらで無料の Azure アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db)を作成できます。 または、Azure サブスクリプションがなくても、「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」から Cosmos データベース アカウントを作成できます。
* Azure Cosmos DB Gremlin API データベース (**無制限のコレクション**)。 このガイドでは、[Azure Cosmos DB Gremlin API を .NET で使用](./create-graph-dotnet.md)する基本的な方法を紹介しています。
* Git 詳細については、[Git のダウンロード ページ](https://git-scm.com/downloads)を参照してください。

### <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製
このチュートリアルでは、GitHub にホストされている [Azure Cosmos DB Graph Bulk Executor サンプル](https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started)を使って基本的な手順を説明します。 このアプリケーションは、頂点オブジェクトとエッジ オブジェクトをランダムに生成した後、指定したグラフ データベース アカウントに一括挿入を実行を実行する .NET ソリューションとなっています。 アプリケーションを取得するために、以下の `git clone` コマンドを実行してください。

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

このリポジトリにある GraphBulkExecutor サンプルには、次のファイルが付属しています。

ファイル|説明
---|---
`App.config`|ここには、アプリケーションとデータベースに固有のパラメーターが指定されます。 出力先のデータベースとコレクションに接続するには、あらかじめこのファイルを編集しておく必要があります。
`Program.cs`| このファイルには、`DocumentClient` コレクションの作成、クリーンアップの処理、Bulk Executor 要求の送信に必要なロジックが格納されています。
`Util.cs`| このファイルには、テスト データを生成したりデータベースとコレクションが存在するかどうかを確認したりするためのロジックを含んだヘルパー クラスが格納されています。

`App.config` ファイルで指定できる構成値は次のとおりです。

設定|説明
---|---
`EndPointUrl`|Azure Cosmos DB Gremlin API データベース アカウントの [概要] ブレードに表示される **.NET SDK エンドポイント** です。 `https://your-graph-database-account.documents.azure.com:443/` という形式が使用されます。
`AuthorizationKey`|Azure Cosmos DB アカウントに表示されるプライマリ キーまたはセカンダリ キーです。 詳細については、[Azure Cosmos DB データへのアクセスのセキュリティ保護](./secure-access-to-data.md#primary-keys)に関するページを参照してください。
`DatabaseName`, `CollectionName`|**ターゲットとなるデータベースとコレクションの名前** です。 `ShouldCleanupOnStart` が `true` に設定されている場合、これらの値と `CollectionThroughput` を使用してデータベースとコレクションがドロップされ、新たに作成されます。 同様に、`ShouldCleanupOnFinish` が `true` に設定されている場合は、インジェストが完了するとすぐに、これらを使用してデータベースが削除されます。 ターゲット コレクションは、**無制限のコレクション** であることが必要です。
`CollectionThroughput`|`ShouldCleanupOnStart` オプションが `true` に設定されている場合に、新しいコレクションを作成する目的で使用されます。
`ShouldCleanupOnStart`|プログラムの実行前にデータベース アカウントとコレクションをドロップした後、`DatabaseName`、`CollectionName`、`CollectionThroughput` の各値を使って新たに作成します。
`ShouldCleanupOnFinish`|プログラムの実行後、指定された `DatabaseName` と `CollectionName` を使って、データベース アカウントとコレクションをドロップします。
`NumberOfDocumentsToImport`|サンプルで生成されるテスト データ (頂点とエッジ) の数は、この設定によって決まります。 この数値は、頂点とエッジの両方に適用されます。
`NumberOfBatches`|サンプルで生成されるテスト データ (頂点とエッジ) の数は、この設定によって決まります。 この数値は、頂点とエッジの両方に適用されます。
`CollectionPartitionKey`|テスト データ (頂点とエッジ) の作成に使用され、その際にこのプロパティが自動的に割り当てられます。 `ShouldCleanupOnStart` オプションが `true` に設定されている場合に、データベースとコレクションを再作成するときにも使用されます。

### <a name="run-the-sample-application"></a>サンプル アプリケーションの実行

1. 自分に該当する固有のデータベース構成パラメーターを `App.config` に追加します。 これは DocumentClient インスタンスの作成に使用されます。 データベースとコンテナーは、まだ作成されていなければ自動的に作成されます。
2. アプリケーションを実行します。 これにより、頂点をインポートするときとエッジをインポートするときの合わせて 2 回、`BulkImportAsync` が呼び出されます。 挿入時にエラーが発生したオブジェクトは、`.\BadVertices.txt` または `.\BadEdges.txt` に追加されます。
3. グラフ データベースにクエリを実行して結果を評価します。 `ShouldCleanupOnFinish` オプションが true に設定されている場合、データベースは自動的に削除されます。

## <a name="next-steps"></a>次のステップ

* NuGet パッケージの詳細と Bulk Executor .NET ライブラリのリリース ノートについては、[Bulk Executor SDK の詳細](sql-api-sdk-bulk-executor-dot-net.md)に関するページを参照してください。 
* Bulk Executor の使用をさらに最適化するためには、「[パフォーマンスに関するヒント](./bulk-executor-dot-net.md#performance-tips)」を参照してください。
* この名前空間に定義されているクラスとメソッドの詳細については、[BulkExecutor.Graph のリファレンス記事](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph)を参照してください。