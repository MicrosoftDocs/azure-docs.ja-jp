---
title: Azure Cosmos DB Gremlin API の実行プロファイル関数を使用してクエリを評価する
description: 実行プロファイル ステップを使用して Gremlin クエリのトラブルシューティングと向上を行う方法について説明します。
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 2f3967c64e79b2bc7b01b35eff26f5ac0d4e3db4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287576"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>実行プロファイル ステップを使用して Gremlin のクエリを評価する方法

この記事では、Azure Cosmos DB Gremlin API グラフ データベースの実行プロファイル ステップを使用する方法の概要を示します。 このステップでは、トラブルシューティングとクエリの最適化に関連する情報が提供され、Cosmos DB Gremlin API アカウントに対して実行できるすべての Gremlin クエリと互換性があります。

このステップを使用するには、Gremlin クエリの最後に `executionProfile()` 関数の呼び出しを追加するだけです。 **Gremlin クエリが実行され**、操作の結果でクエリ実行プロファイルを含む JSON 応答オブジェクトが返されます。

例: 

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

`executionProfile()` ステップを呼び出した後の応答は、実行された Gremlin ステップ、かかった合計時間、ステートメントの結果の Cosmos DB ランタイム演算子の配列が含まれる、JSON オブジェクトになります。

> [!NOTE]
> 実行プロファイルのこの実装は、Apache Tinkerpop の仕様では定義されていません。 それは、Azure Cosmos DB Gremlin API の実装に固有です。


## <a name="response-example"></a>応答の例

以下に示すのは、返される出力の注釈付きの例です。

> [!NOTE]
> この例には、応答の一般的な構造を説明するコメントで注釈が付けられています。 実際の executionProfile の応答には、コメントは含まれません。

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. Depending on the query, these might be directly adjacent to a set of vertices, or separate, in the case of an E() query.
        // The metrics include: time, percentTime of total execution time, resultCount, fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from the preceding graph operations.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> executionProfile ステップでは、Gremlin クエリが実行されます。 これには `addV` または `addE` ステップが含まれ、これらでは、作成が行われ、クエリで指定されている変更がコミットされます。 その結果、Gremlin クエリによって生成される要求ユニットの課金も行われます。

## <a name="execution-profile-response-objects"></a>実行プロファイルの応答オブジェクト

executionProfile() 関数の応答では、次の構造を持つ JSON オブジェクトの階層が生成されます。
  - **Gremlin 操作オブジェクト**: 実行された Gremlin 操作全体を表します。 次のプロパティが含まれます。
    - `gremlin`:実行された明示的な Gremlin ステートメント。
    - `totalTime`:ステップの実行にかかった時間 (ミリ秒単位)。 
    - `metrics`:クエリを満たすために実行された各 Cosmos DB ランタイム演算子が含まれる配列。 このリストは、実行の順序で並べられています。
    
  - **Cosmos DB ランタイム演算子**: Gremlin 操作全体の各コンポーネントを表します。 このリストは、実行の順序で並べられています。 各オブジェクトには次のプロパティが含まれます。
    - `name`:演算子の名前。 これは、評価および実行されたステップの種類です。 詳しくは、後の表をご覧ください。
    - `time`:特定の演算子にかかった時間 (ミリ秒単位)。
    - `annotations`:実行された演算子に固有の追加情報が含まれます。
    - `annotations.percentTime`:特定の演算子の実行にかかった合計時間の割合。
    - `counts`:この演算子によって、ストレージ レイヤーから返されたオブジェクトの数。 これは、`counts.resultCount` スカラー値に含まれます。
    - `storeOps`:1 つまたは複数のパーティションにまたがることができるストレージ操作を表します。
    - `storeOps.fanoutFactor`:この特定のストレージ操作でアクセスされたパーティションの数を表します。
    - `storeOps.count`:このストレージ操作で返された結果の数を表します。
    - `storeOps.size`:特定のストレージ操作の結果のサイズを表します (バイト単位)。

Cosmos DB Gremlin ランタイム演算子|説明
---|---
`GetVertices`| このステップでは、永続化レイヤーから基にされたオブジェクトのセットが取得されます。 
`GetEdges`| このステップでは、頂点のセットに隣接するエッジが取得されます。 このステップは、1 つまたは複数のストレージ操作になる可能性があります。
`GetNeighborVertices`| このステップでは、エッジのセットに接続された頂点が取得されます。 エッジには、ソース頂点とターゲット頂点両方のパーティション キーと ID が含まれます。
`Coalesce`| このステップでは、`coalesce()` Gremlin ステップが実行されるときは常に、2 つの操作の評価が考慮されます。
`CartesianProductOperator`| このステップでは、2 つのデータセット間のデカルト積が計算されます。 通常、述語 `to()` または `from()` が使用されると常に実行されます。
`ConstantSourceOperator`| このステップでは、式が計算され、結果として定数値が生成されます。
`ProjectOperator`| このステップでは、それ以前の操作の結果を使用して、応答が準備されてシリアル化されます。
`ProjectAggregation`| このステップでは、集計操作の応答が準備されてシリアル化されます。

> [!NOTE]
> この一覧は、新しい演算子が追加されると更新されます。

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>実行プロファイルの応答を分析する方法の例

実行プロファイルの応答を使用して特定できる一般的な最適化の例を次に示します。
  - ブラインド ファンアウト クエリ。
  - フィルター処理なしクエリ。

### <a name="blind-fan-out-query-patterns"></a>ブラインド ファンアウト クエリのパターン

**パーティション分割されたグラフ**からの次のような実行プロファイルの応答を想定します。

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

次のような結論が得られます。
- Gremlin ステートメントはパターン `g.V('id')` に従っているため、クエリは単一 ID 参照です。
- `time` メトリックから判断して、[1 つのポイント読み取り操作が 10 ミリ秒より長い](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide)ので、このクエリの待機時間は長いと思われます。
- `storeOps` オブジェクトを見ると、`fanoutFactor` が `5` であることを確認でき、これは [5 つのパーティション](https://docs.microsoft.com/azure/cosmos-db/partition-data)がこの操作によってアクセスされたことを意味します。

この分析の結論として、最初のクエリが必要以上に多くのパーティションにアクセスしていると判断できます。 これは、述語としてクエリでパーティション キーを指定することにより解決できます。 これにより、待機時間が短縮され、クエリあたりのコストが減ります。 詳しくは、[グラフのパーティション分割](graph-partitioning.md)に関する記事をご覧ください。 より最適なクエリは、`g.V('tt0093640').has('partitionKey', 't1001')` です。

### <a name="unfiltered-query-patterns"></a>フィルター処理なしクエリのパターン

次の 2 つの実行プロファイルの応答を比較します。 わかりやすくするため、これらの例では 1 つのパーティション分割されたグラフを使用しています。

この最初のクエリでは、ラベル `tweet` の付いたすべての頂点が取得された後、隣接する頂点が取得されます。

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

同じクエリですが、隣接する頂点を調べる前にフィルター `has('lang', 'en')` が追加されている場合のプロファイルに注意してください。

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

これら 2 つのクエリは同じ結果になりますが、1 つ目のクエリでは、隣接する項目のクエリを実行する前に、大規模な初期データセットを反復処理する必要があるため、より多くの要求ユニットが必要になります。 両方の応答から次のパラメーターを比較すると、この動作のインジケーターを確認できます。
- 最初の応答の方が `metrics[0].time` の値が高く、これはこの 1 つのステップの解決に長くかかったことを示します。
- `metrics[0].counts.resultsCount` の値も最初の応答の方が高く、初期作業データセットが大きかったことを示します。

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB で[サポートされている Gremlin の機能](gremlin-support.md)について確認します。 
* [Azure Cosmos DB での Gremlin API](graph-introduction.md) の詳細について確認します。
