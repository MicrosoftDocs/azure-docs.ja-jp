---
title: 'Azure Cosmos DB: SQL API の .NET (Microsoft.Azure.Cosmos) のサンプル'
description: Azure Cosmos DB SQL API を使う一般的なタスクについては、GitHub の C# .NET V3 SDK のサンプルを参照してください。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: cfa79569ea4f2ec37eb7086d4878d3592babf18d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89015188"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB: SQL API の .NET V3 SDK (Microsoft.Azure.Cosmos) のサンプル

> [!div class="op_single_selector"]
> * [.NET V2 SDK のサンプル](sql-api-dotnet-samples.md)
> * [.NET V3 SDK のサンプル](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK のサンプル](sql-api-java-sdk-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

[azure-cosmos-dotnet-v3](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) GitHub リポジトリには、Azure Cosmos DB リソースに対する CRUD などの一般的な操作を実行する最新の .NET サンプル ソリューションがあります。 以前のバージョンの .NET SDK に慣れている方は、"コレクション" や "ドキュメント" という用語をよく目にしたかと思います。 Azure Cosmos DB では複数の API モデルをサポートしているため、.NET SDK バージョン 3.0 では、"コンテナー" と "項目" という一般的な用語が使用されています。 コンテナーは、コレクション、グラフ、またはテーブルを表します。 項目は、ドキュメント、エッジ/頂点、行など、コンテナー内の内容を表します。 この記事では、次の内容について説明します。

* 各サンプル C# プロジェクト ファイルのタスクへのリンク。
* 関連する API リファレンス コンテンツへのリンク。

## <a name="prerequisites"></a>前提条件

[Azure の開発] ワークフローがインストールされた Visual Studio 2019

- **無料の** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) をダウンロードして使用できます。 Visual Studio のセットアップ中に、必ず **[Azure の開発]** を有効にしてください。

   [Microsoft.Azure.cosmos NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

Azure サブスクリプションまたは Cosmos DB の無料試用版アカウント

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ことができます。ご利用の Visual Studio サブスクリプションにより、有料の Azure サービスに使用できるクレジットが毎月提供されます。
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> このサンプルは自己完結型で、自分で設定とクリーンアップを行います。 これが行われるたびに、ご使用のコンテナーのパフォーマンス レベルにおける 1 時間の使用料がご自分のサブスクリプションに課金されます。
> 

## <a name="database-examples"></a>データベースのサンプル

サンプルの *DatabaseManagement* プロジェクトの [RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos データベースについて知るために、「[データベース、コンテナー、アイテムの操作](databases-containers-items.md)」を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [データベースの作成](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [ID でのデータベースの読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.ReadAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [アカウントのすべてのデータベースの読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [データベースの削除](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>コンテナーの例

サンプルの *ContainerManagement* プロジェクトの [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos コンテナーについて知るために、「[データベース、コンテナー、アイテムの操作](databases-containers-items.md)」を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [コンテナーの作成](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [カスタム インデックス ポリシーを持つコンテナーの作成](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [コンテナーの構成済みのパフォーマンスの変更](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Container.ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [ID によるコンテナーの取得](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container.ReadContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [データベース内のすべてのコンテナーの読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Database.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [コンテナーの削除](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Container.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>項目の例

サンプルの *ItemManagement* プロジェクトの [RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos 項目について知るために、「[データベース、コンテナー、アイテムの操作](databases-containers-items.md)」を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [項目の作成](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet) |
| [ID での項目の読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[container.ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [項目のクエリ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [項目の置換](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[container.ReplaceItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [項目のアップサート](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[container.UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [項目の削除](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[container.DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [条件付き ETag チェックによる項目の置換](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>インデックス作成のサンプル

サンプルの *IndexManagement* プロジェクトの [RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB におけるインデックス作成について知るために、[インデックス ポリシー](index-policy.md)、[インデックスの種類](index-types.md)、[インデックスのパス](index-paths.md)に関する記事を参照してください。 

| タスク | API リファレンス |
| --- | --- |
| [インデックスからの項目の除外](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Lazy インデックス作成の使用](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [インデックスからの指定した項目パスの除外](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>クエリのサンプル

サンプルの *Queries* プロジェクトの [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) メソッドは、SQL クエリ文法、LINQ プロバイダーとクエリ、およびラムダを使用して、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB の SQL クエリ リファレンスについて知るために、「[Azure Cosmos DB の SQL クエリの例](how-to-sql-query.md)」を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [単一パーティションの項目のクエリ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [複数パーティションの項目のクエリ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [SQL ステートメントを使用するクエリ](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[container.GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>変更フィードの例

サンプルの *ChangeFeed* プロジェクトの [RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB の変更フィードについて知るために、[Azure Cosmos DB の変更フィードの読み取り](read-change-feed.md)と[変更フィード プロセッサ](change-feed-processor.md)に関する記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [基本的な変更フィードの機能](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [特定の時刻からの変更フィードの読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [最初からの変更フィードの読み取り](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [変更フィード プロセッサから V3 SDK の変更フィードへの移行](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>サーバー側プログラミングのサンプル

サンプルの *ServerSideScripts* プロジェクトの [RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) メソッドは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB のサーバー側プログラミングについて知るために、「[ストアド プロシージャ、トリガー、およびユーザー定義関数](stored-procedures-triggers-udfs.md)」を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [ストアド プロシージャの作成](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [ストアド プロシージャの実行](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [ストアド プロシージャの削除](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |
