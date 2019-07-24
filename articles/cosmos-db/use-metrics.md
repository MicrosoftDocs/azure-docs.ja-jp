---
title: Azure Cosmos DB のメトリックを使用した監視とデバッグ
description: Azure Cosmos DB のメトリックを使用して、一般的な問題をデバッグし、データベースを監視します。
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034728"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB のメトリックを使用した監視とデバッグ

Azure Cosmos DB には、スループット、ストレージ、整合性、可用性、および待機時間のメトリックが用意されています。 [Azure portal](https://portal.azure.com) には、このようなメトリックの集計ビューが用意されています。 より詳細なメトリックについては、クライアント SDK と[診断ログ](./logging.md)の両方を使用できます。

この記事では、一般的なユース ケースと、Azure Cosmos DB メトリックを使用してこれらの問題を分析およびデバッグする手順について説明します。 メトリックは 5 分間隔で収集され、7 日間保持されます。

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>成功した要求数とエラーになった要求数の把握

まず [Azure Portal](https://portal.azure.com) を開き、**[メトリック]** ブレードに移動します。 このブレードで **[1 分あたりに容量を超過した要求の数]** グラフを見つけます。 このグラフには、状態コードで区分された毎分の合計要求が表示されます。 HTTP 状態コードの詳細については、「[HTTP Status Codes for Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」(Azure Cosmos DB の HTTP 状態コード) を参照してください。

最も一般的なエラー状態コードは 429 (レート制限/調整) です。 このエラーは、Azure Cosmos DB への要求がプロビジョニングされたスループットを超えることを意味します。 この問題の最も一般的な解決策は、そのコレクションの [RU をスケール アップ](./set-throughput.md)することです。

![毎分の要求数](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>パーティション全体のスループットの分散を決める

適切なカーディナリティのパーティション キーを持つことは、スケーラブルなアプリケーションのために重要です。 パーティションごとに分けられたパーティション コンテナーのスループットの分散を決めるには、[Azure Portal](https://portal.azure.com) の **[メトリック]** ブレードに移動します。 **[スループット]** タブの **[各物理パーティションによる 1 秒あたりの最大消費 RU]** グラフにストレージの内訳が表示されます。 次の図は、パーティションが左端に偏っていることでわかるように、データの分散が不適切な例です。

![3:05 PM に 1 つのパーティションの使用量が高い](media/use-metrics/metrics-17.png)

スループット分散が不均一の場合、*ホット* パーティションが発生します。また、その結果、要求が調整され、再パーティションが必要になる可能性があります。 Azure Cosmos DB でのパーティション分割の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](./partition-data.md)」を参照してください。

## <a name="determine-the-storage-distribution-across-partitions"></a>パーティション全体のストレージの分散を決める

適切なカーディナリティのパーティションを持つことは、スケーラブルなアプリケーションのために重要です。 パーティションごとに分けられたパーティション コンテナーのストレージの分散を決めるには、[Azure portal](https://portal.azure.com) の [メトリック] ブレードに移動します。 [ストレージ] タブの [上位パーティション キーで使用されるデータとインデックスのストレージ] グラフに、ストレージの内訳が表示されます。 次の図は、パーティションが左端に偏っていることでわかるように、データ ストレージの分散が不適切なことを示しています。

![不適切なデータ分散の例](media/use-metrics/metrics-07.png)

グラフのパーティションをクリックすると、パーティション キーの分散が偏っている根本原因を確認できます。

![パーティション キーによる分散の偏り](media/use-metrics/metrics-05.png)

分散の偏りの原因となっているパーティション キーを特定した後は、必要に応じて、より分散されたパーティション キーでコンテナーを再パーティションします。 Azure Cosmos DB でのパーティション分割の詳細については、「[Azure Cosmos DB でのパーティション分割とスケーリング](./partition-data.md)」を参照してください。

## <a name="compare-data-size-against-index-size"></a>データ サイズとインデックス サイズを比較する

Azure Cosmos DB の合計使用ストレージは、データ サイズとインデックス サイズ両方の組み合わせです。 通常、インデックス サイズは、データ サイズよりもはるかに小さいサイズです。 [Azure Portal](https://portal.azure.com) の [メトリック] ブレードの [ストレージ] タブには、データとインデックスに基づくストレージ使用量の内訳が表示されます。

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

インデックス領域を節約するには、[インデックス ポリシー](index-policy.md)を調整します。

## <a name="debug-why-queries-are-running-slow"></a>クエリの実行速度が遅い原因をデバッグする

SQL API SDK の Azure Cosmos DB は、クエリ実行の統計情報を提供します。

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
 new FeedOptions
 {
 PopulateQueryMetrics = true,
 MaxItemCount = -1,
 MaxDegreeOfParallelism = -1,
 EnableCrossPartitionQuery = true
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* は、クエリの各コンポーネントが実行にかかった時間について詳細情報を提供します。 クエリの時間が長くなる最も一般的な根本原因はスキャンです。つまり、クエリがインデックスを利用できなかったことを示します。 この問題は、フィルター条件を修正することで解決できます。

## <a name="next-steps"></a>次の手順

Azure portal で提供されているメトリックを使用して、問題の監視とデバッグを行う方法について説明しました。 データベースのパフォーマンスを改善する方法については、次の記事を参照してください。

* [Azure Cosmos DB のパフォーマンスとスケールのテスト](performance-testing.md)
* [Azure Cosmos DB のパフォーマンスに関するヒント](performance-tips.md)
