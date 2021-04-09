---
title: Azure Cosmos DB の Gremlin API についてよく寄せられる質問
description: Azure Cosmos DB の Gremlin API についてよく寄せられる質問とその回答を紹介します
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: sngun
ms.openlocfilehash: 0374886482b43eefe6df5e107fd4da371633099d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359783"
---
# <a name="frequently-asked-questions-about-the-gremlin-api-in-azure-cosmos-db"></a>Azure Cosmos DB の Gremlin API についてよく寄せられる質問
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

この記事では、Azure Cosmos DB の Gremlin API についてよく寄せられるいくつかの質問に対する回答について説明します。

## <a name="how-to-evaluate-the-efficiency-of-gremlin-queries"></a>Gremlin クエリの効率を評価する方法

**executionProfile()** というプレビュー手順を利用し、クエリ実行プランを分析できます。 次の例のように、この手順は Gremlin クエリの最後に追加する必要があります。

**クエリの例**

```
g.V('mary').out('knows').executionProfile()
```

**出力例**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

上記プロファイルの出力には、頂点オブジェクト、端オブジェクト、および作業データ セットのサイズの取得にかかった時間が表示されます。 これは Azure Cosmos DB クエリの標準コスト測定に関連します。

## <a name="other-frequently-asked-questions"></a>その他のよく寄せられる質問

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>グラフ データベースでクエリを実行するとき、RU/秒はどのように課金されますか。

グラフのオブジェクト、頂点、端はすべて、バックエンドで JSON ドキュメントとして表示されます。 1 つの Gremlin クエリで 1 つまたは複数のグラフ オブジェクトを一度に変更できるため、それに関連付けられるコストは、クエリで処理されるオブジェクトや端に直接関連します。 このプロセスは、その他すべての API に対して Azure Cosmos DB で使用されるプロセスと同じです。 詳細については、「[Azure Cosmos DB の要求ユニット](request-units.md)」をご覧ください。

RU 課金は、結果セットではなく、走査の作業データ セットに基づきます。 たとえば、結果として頂点を 1 つ得ることがクエリの目的であるが、その過程で他の複数のオブジェクトを走査する必要がある場合、結果として頂点を 1 つ計算するために必要なすべてのグラフ オブジェクトに基づいてコストが算出されます。

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Azure Cosmos DB Gremlin API でグラフ データベースに与えられる最大スケールは何ですか。

Azure Cosmos DB では、[水平方向のパーティション分割](partitioning-overview.md)を利用し、ストレージ要件とスループット要件の増加に自動的に対処します。 ワークロードの最大スループットと最大ストレージ容量は、指定されたコンテナーに関連付けられているパーティションの数により決定されます。 ただし、Gremlin API コンテナーには、大規模でも適切なパフォーマンスが得られるように、特定のガイドラインのセットがあります。 パーティション分割の詳細とベスト プラクティスについては、[Azure Cosmos DB でのパーティション分割](partitioning-overview.md)に関する記事をご覧ください。

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>C#/.NET 開発の場合、Microsoft.Azure.Graphs パッケージまたは Gremlin.NET を使用するべきですか。

Azure Cosmos DB Gremlin API では、サービスのメイン コネクタとしてオープンソースのドライバーが活用されます。 そのため、[Apache Tinkerpop でサポートされているドライバー](https://tinkerpop.apache.org/)の使用をお勧めします。

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Gremlin ドライバーを利用したインジェクション攻撃はどのように防ぎますか。

ネイティブ Apache Tinkerpop Gremlin ドライバーのほとんどでは、クエリ実行にパラメーター ディクショナリを提供できます。 この方法のサンプルは、[Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) と [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/main/app.js) にあります。

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>"Gremlin Query Compilation Error: Unable to find any method" (Gremlin クエリ コンパイル エラー:メソッドが見つかりません) エラーが表示されるのはなぜですか。

Azure Cosmos DB Gremlin API では、Gremlin セキュリティに定義されている機能サブセットが実装されます。 サポートされている手順と詳細については、「[Gremlin サポート](gremlin-support.md)」という記事を参照してください。

最良の回避策は、Gremlin の必須手順をサポートされている機能で書き直すことです。Gremlin の必須手順はすべて、Azure Cosmos DB でサポートされているためです。

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>"WebSocketException: The server returned status code '200' when status code '101' was expected"(WebSocketException: 状態コード '101' が予想されるとき、サーバーから '200' が返されました) エラーが表示されるのはなぜですか。

このエラーはおそらく、間違ったエンドポイントが使用されているときにスローされます。 このエラーを生成するエンドポイントには次のパターンがあります。

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

これは、ご利用のグラフ データベースのドキュメント エンドポイントです。  使用すべき正しいエンドポイントは Gremlin エンドポイントです。この形式は次のようになっています。

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>"RequestRateIsTooLarge" エラーが表示されるのはなぜですか。

このエラーは、秒あたりで割り当てられている要求ユニットではクエリに十分なサービスを提供できないことを意味します。 このエラーは通常、すべての頂点を取得するクエリを実行すると表示されます。

```
// Query example:
g.V()
```

このクエリでは、グラフからすべての頂点を取得するように試行されます。 そのため、このクエリのコストは、RU の観点からは、少なくとも頂点の数と等しくなります。 RU/秒の設定を調整してこのクエリに対処してください。

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Gremlin ドライバーの接続が最後には切断されます。なぜですか。

Gremlin 接続は WebSocket 接続経由で行われます。 WebSocket 接続には有効時間が指定されませんが、Azure Cosmos DB Gremlin API では、アイドル時間が 30 分間続くと接続が終了します。

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>ネイティブ Gremlin ドライバーで Fluent API 呼び出しを使用できません。なぜですか。

Fluent API 呼び出しは、Azure Cosmos DB Gremlin API ではまだサポートされていません。 Fluent API 呼び出しにはバイトコード サポートと呼ばれている内部書式設定機能が必要ですが、これは現在のところ、Azure Cosmos DB Gremlin API ではサポートされていません。 同じ理由から、最新の Gremlin-JavaScript ドライバーも現在のところサポートされていません。

## <a name="next-steps"></a>次のステップ

* [Azure Cosmos DB Gremlin のワイヤ プロトコルのサポート](gremlin-support.md)
* [Gremlin コンソール](create-graph-gremlin-console.md)を使用して Azure Cosmos DB グラフ データベースを作成、照会、および走査する
