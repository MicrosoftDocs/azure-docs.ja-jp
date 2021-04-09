---
title: MongoDB 用 Azure Cosmos DB API を使用する場合のクエリの問題のトラブルシューティング
description: Azure Cosmos DB の MongoDB 用 API のクエリに関する問題を特定、診断、およびトラブルシューティングする方法について説明します。
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 5302cb7bb3f4683d200f6f9ea106991bb934fc17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659904"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>MongoDB 用 Azure Cosmos DB API を使用する場合のクエリの問題のトラブルシューティング
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

この記事では、Azure Cosmos DB のクエリのトラブルシューティングに関する一般的な推奨アプローチについて説明します。 この記事で説明されている手順により、クエリで発生する可能性がある問題を完全に防ぐことができると考えてはいけませんが、パフォーマンスに関する最も一般的なヒントを示してあります。 この記事は、Azure Cosmos DB の MongoDB 用 API の低速クエリまたはコストの高いクエリのトラブルシューティングのための出発点として使用してください。 Azure Cosmos DB Core (SQL) API を使用している場合は、[SQL API クエリのトラブルシューティング ガイド](troubleshoot-query-performance.md)に関する記事を参照してください。

Azure Cosmos DB でのクエリの最適化は、次のように大きく分類されています。

- クエリの要求ユニット (RU) 使用量を削減する最適化
- クエリの待機時間を短縮するだけの最適化

クエリの RU 使用量を減らすと、一般的に待機時間も短くなります。

この記事では、[栄養データセット](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json)を使用して再作成できる例を示します。

> [!NOTE] 
> この記事では、バージョン 3.6 以降の MongoDB アカウントを対象とする Azure Cosmos DB の API を使用していることを前提としています。 バージョン 3.2 でパフォーマンスが良くなかった一部のクエリは、バージョン 3.6 以上で大幅に改善されています。 [サポート リクエスト](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)を提出して、バージョン 3.6 にアップグレードします。

## <a name="use-explain-command-to-get-metrics"></a>$explain コマンドを使用してメトリックを取得する

Azure Cosmos DB でクエリを最適化する場合、最初の手順は常にクエリの[ RU 使用率を取得する](find-request-unit-charge-mongodb.md)ことです。 大まかなガイドラインとして、使用率が 50 RU を超えるクエリでは、RU 使用率を下げる方法を探る必要があります。 

RU 使用率を取得するだけでなく、`$explain` コマンドを使用して、クエリとインデックスの使用状況メトリックを取得する必要があります。 クエリを実行し、`$explain` コマンドを使用してクエリとインデックスの使用状況メトリックを表示する例を次に示します。

**$explain コマンド:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**出力:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`$explain` コマンドの出力は長く、クエリの実行に関する詳細情報が含まれています。 しかし、一般的には、クエリのパフォーマンスを最適化する際に注目すべきセクションは多くありません。

| メトリック | 説明 | 
| ------ | ----------- |
| `timeInclusiveMS` | バックエンド クエリの待機時間 |
| `pathsIndexed` | クエリで使用されたインデックスを表示します | 
| `pathsNotIndexed` | 使用可能であった場合に、クエリで使用された可能性があるインデックスを表示します | 
| `shardInformation` | 特定の[物理パーティション](./partitioning-overview.md#physical-partitions)のクエリ パフォーマンスの概要 | 
| `retrievedDocumentCount` | クエリ エンジンによって読み込まれたドキュメントの数 | 
| `outputDocumentCount` | クエリ結果で返されたドキュメントの数 | 
| `estimatedDelayFromRateLimitingInMilliseconds` | レート制限による追加のクエリ待機時間の推定 | 

クエリ メトリックを取得した後、クエリの `retrievedDocumentCount` と `outputDocumentCount` を比較します。 この比較により、この記事で確認する関連セクションを特定します。 `retrievedDocumentCount` は、クエリ エンジンで読み込む必要があったドキュメントの数です。 `outputDocumentCount` は、クエリの結果に必要だったドキュメントの数です。 `retrievedDocumentCount` が `outputDocumentCount` より大幅に多い場合は、クエリの少なくとも一部でインデックスを使用できず、スキャンを実行する必要がありました。

シナリオに関連するクエリの最適化について理解するには、以下のセクションを参照してください。

### <a name="querys-ru-charge-is-too-high"></a>クエリの RU 使用量が高すぎる

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を大幅に超えている

- [必要なインデックスを含める](#include-necessary-indexes)

- [インデックスを使用する集計操作を理解する](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数とほぼ等しい

- [クロス パーティション クエリを最小化する。](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>クエリの RU 使用量は許容されるが、待機時間は依然として長すぎる

- [近接性を向上させる。](#improve-proximity)

- [プロビジョニングされたスループットを増やす。](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>取得したドキュメント数が出力したドキュメント数を超えるクエリ

 `retrievedDocumentCount` は、クエリ エンジンが読み込む必要があったドキュメントの数です。 `outputDocumentCount` は、クエリによって返されたドキュメントの数です。 `retrievedDocumentCount` が `outputDocumentCount` より大幅に多い場合は、クエリの少なくとも 1 つの部分でインデックスを使用できず、スキャンを実行する必要がありました。

インデックスによって完全には処理されなかったスキャン クエリの例を次に示します。

**$explain コマンド:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**出力:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount` (8618) は、`outputDocumentCount` (1) を大幅に上回っており、このクエリの結果としてドキュメントのスキャンが行われたことを示しています。 

### <a name="include-necessary-indexes"></a>必要なインデックスを含める

`pathsNotIndexed` 配列を確認して、これらのインデックスを追加する必要があります。 この例では、`foodGroup` と `description` のパスにインデックスを付ける必要があります。

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Azure Cosmos DB の MongoDB 用 API におけるインデックスのベストプラクティスは、MongoDB とは異なります。 Azure Cosmos DB の MongoDB 用 API では、複合インデックスは複数のプロパティで効率的に並べ替える必要があるクエリでのみ使用されます。 複数のプロパティに対するフィルターを持つクエリがある場合は、これらのプロパティごとに 1 つのフィールド インデックスを作成する必要があります。 クエリ述語では、複数の単一フィールド インデックスを使用できます。

[ワイルドカード インデックス](mongodb-indexing.md#wildcard-indexes)を使用すると、インデックス作成を簡単に行うことができます。 MongoDB とは異なり、ワイルドカード インデックスはクエリ述語内の複数のフィールドをサポートできます。 プロパティごとに個別のインデックスを作成する代わりに 1 つのワイルドカード インデックスを使用しても、クエリのパフォーマンスに違いはありません。 すべてのクエリを最適化する最も簡単な方法は、すべてのプロパティにワイルドカード インデックスを追加することです。

書き込みまたは読み取りの可用性に影響を与えることなく、いつでも新しいインデックスを追加できます。 [インデックス変換の進行状況を追跡](./how-to-manage-indexing-policy.md#dotnet-sdk)できます。

### <a name="understand-which-aggregation-operations-use-the-index"></a>インデックスを使用する集計操作を理解する

ほとんどの場合、Azure Cosmos DB の MongoDB 用 API による集計操作では、インデックスが部分的に使用されます。 通常、クエリ エンジンによって、等値および範囲フィルターが最初に適用され、インデックスが使用されます。 これらのフィルターを適用した後で、クエリ エンジンは追加のフィルターを評価し、必要に応じて残りのドキュメントを読み込んで集計を計算できます。 

次に例を示します。

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

この場合、インデックスを使用することで `$match` ステージを最適化できます。 `foodGroup` にインデックスを追加すると、クエリのパフォーマンスが大幅に向上します。 MongoDB の場合と同様に、インデックスを最大限に活用するには、集計パイプラインのできるだけ早い段階で `$match` を配置する必要があります。

Azure Cosmos DB の MongoDB 用 API では、実際の集計にはインデックスは使用されません (この場合は `$max`)。 `version` にインデックスを追加しても、クエリのパフォーマンスは向上しません。

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>取得したドキュメント数が出力したドキュメント数と等しいクエリ

`retrievedDocumentCount` が `outputDocumentCount` とほぼ同じ場合、クエリ エンジンは多くの不要なドキュメントをスキャンする必要がありませんでした。

### <a name="minimize-cross-partition-queries"></a>クロス パーティション クエリを最小化する

Azure Cosmos DB では[パーティション分割](partitioning-overview.md)を使用して、要求ユニットとデータ ストレージのニーズの増加に応じて個々のコンテナーをスケーリングします。 各物理パーティションには、区切られて独立したインデックスがあります。 クエリにコンテナーのパーティション キーと一致する等値フィルターがある場合、確認する必要があるのは関連するパーティションのインデックスのみです。 この最適化により、クエリが必要とする RU の合計数が減少します。 [詳しくは、インパーティション クエリとクロスパーティション クエリの相違点に関するページを参照してください。](how-to-query-container.md)

プロビジョニングされた RU 使用量の数が多い (3 万以上) 場合や、大量のデータ (約 100 GB 以上) が格納されている場合は、おそらく、クエリ RU の使用量の大幅な削減を確認するのに十分な大きさなのコンテナーがあります。 

`shardInformation` 配列を確認して、個々の物理パーティションのクエリ メトリックを理解することができます。 一意の `shardKeyRangeId` 値の数は、クエリを実行する必要がある物理パーティションの数です。 この例では、クエリは 4 つの物理パーティションで実行されています。 実行は、インデックスの使用とは完全に独立していることを理解しておくことが重要です。 言い換えると、クロスパーティション クエリでもインデックスを使用できるということです。

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>クエリの待機時間を短縮する最適化

多くの場合、クエリの待機時間がまだ長すぎるときは、RU 使用量が許容される可能性があります。 以下のセクションでは、クエリの待機時間を短縮するためのヒントの概要を説明します。 同じクエリを同じデータセットに対して複数回実行すると、一般的には毎回同じ RU 使用量が発生します。 ただし、クエリの待機時間はクエリの実行間隔によって異なる場合があります。

### <a name="improve-proximity"></a>近接性の向上

Azure Cosmos DB アカウントとは異なるリージョンから実行されるクエリでは、同じリージョン内で実行された場合よりも待機時間が長くなります。 たとえば、デスクトップ コンピューターでコードを実行していた場合、クエリが Azure Cosmos DB と同じ Azure リージョン内の仮想マシンからのものである場合よりも、待機時間は数十ミリ秒または数百ミリ秒 (またはそれ以上) 分長くなることが予想されます。 データをアプリの近くに配置できるように、[Azure Cosmos DB にデータをグローバルに分散する](tutorial-global-distribution-mongodb.md)ことは簡単です。

### <a name="increase-provisioned-throughput"></a>プロビジョニングされたスループットの増加

Azure Cosmos DB では、プロビジョニングされたスループットは要求ユニット (RU) で測定されます。 5 RU のスループットを使用するクエリがあると仮定します。 たとえば、1,000 RU をプロビジョニングする場合、そのクエリは 1 秒あたり 200 回実行できます。 スループットが十分でないときにクエリを実行しようとすると、Azure Cosmos DB によって要求に対するレート制限がかかります。 短時間待機した後、Azure Cosmos DB の MongoDB 用 API によって、このクエリが自動的に再試行されます。 スロットルされた要求にはさらに時間がかかるため、プロビジョニングされたスループットを増やすとクエリの待機時間が改善します。

`estimatedDelayFromRateLimitingInMilliseconds` の値は、スループットを向上させた場合に改善する潜在的な待機時間を示します。

## <a name="next-steps"></a>次の手順

* [クエリ パフォーマンスのトラブルシューティング (SQL API)](troubleshoot-query-performance.md)
* [Azure Cosmos DB の MongoDB 用 API でのインデックス作成を管理する](mongodb-indexing.md)
