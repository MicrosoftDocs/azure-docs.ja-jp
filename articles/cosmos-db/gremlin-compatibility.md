---
title: Azure Cosmos DB Gremlin の TinkerPop 機能との互換性
description: Graph エンジンの互換性の問題に関するリファレンス ドキュメント
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 7257246f618e3028534f3ebd60eaf6f94a3a4720
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092509"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin の互換性
Azure Cosmos DB Graph エンジンは [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) のトラバーサル ステップの仕様に厳密に従っていますが、実装には Azure Cosmos DB に固有の違いがあります。 サポートされている Gremlin のステップの一覧を確認するには、[Gremlin API でのワイヤ プロトコルのサポート](gremlin-support.md)に関する記事を参照してください。

## <a name="behavior-differences"></a>動作の違い

* Azure Cosmos DB Graph エンジンでは***幅優先***トラバーサルが実行されますが、TinkerPop Gremlin では深さ優先になります。 この動作により、Cosmos DB のように水平方向にスケーラブルなシステムでのパフォーマンスが向上します。 

## <a name="unsupported-features"></a>サポートされていない機能

* ***[Gremlin バイトコード](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** は、プログラミング言語に依存しない、グラフ トラバーサルの仕様です。 Cosmos DB Graph ではまだサポートされていません。 `GremlinClient.SubmitAsync()` を使用して、トラバーサルをテキスト文字列として渡してください。

* ***`property(set, 'xyz', 1)`*** set カーディナリティは現在サポートされていません。 代わりに `property(list, 'xyz', 1)` を使用してください 詳細については、[TinkerPop での頂点プロパティ](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)に関する記事を参照してください。

* ***`match()` ステップ***は、現在利用できません。 このステップでは、宣言型のクエリ機能が提供されます。

* 頂点または辺の***プロパティとしてのオブジェクト***はサポートされていません。 プロパティには、プリミティブ型または配列のみを指定できます。

* ***配列プロパティによる並べ替え*** (`order().by(<array property>)`) はサポートされていません。 プリミティブ型での並べ替えのみがサポートされています。

* ***非プリミティブ JSON 型***はサポートされていません。 `string` 型、`number` 型、または `true`/`false` 型を使用してください。 `null` 値はサポートされていません。 

* ***GraphSONv3*** シリアライザーは現在サポートされていません。 接続構成で `GraphSONv2` シリアライザー、リーダー、およびライター クラスを使用してください。 Azure Cosmos DB Gremlin API によって返される結果は、GraphSON 形式と同じ形式ではありません。 

* **ラムダ式と関数**は現在サポートされていません。 これには、`.map{<expression>}`、`.by{<expression>}`、および `.filter{<expression>}` 関数が含まれます。 詳細について、および Gremlin の手順を使用してこれらを書き換える方法については、[ラムダ式に関する注意事項](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)に関する記事を参照してください。

* システムの分散型の性質により、***トランザクション***はサポートされていません。  Gremlin アカウントで "自分の書き込みを読み取る" ように適切な整合性モデルを構成し、オプティミスティック同時実行制御を使用して、競合する書き込みを解決してください。

## <a name="known-limitations"></a>既知の制限事項

* **トラバーサル中の `.V()` ステップによる Gremlin クエリのインデックス使用率**:現時点では、トラバーサルの最初の `.V()` 呼び出しのみによって、アタッチされているフィルターまたは述語を解決するために、インデックスが利用されます。 後続の呼び出しでは、インデックスが参照されないため、クエリの待機時間とコストが増加する可能性があります。
    
    既定のインデックス作成を前提とすると、`.V()` ステップによって開始された通常の読み取り Gremlin クエリでは、`.has()` や `.where()` など、アタッチされたフィルター処理のステップの中でパラメーターが使用され、クエリのコストとパフォーマンスが最適化されます。 次に例を示します。

    ```java
    g.V().has('category', 'A')
    ```

    ただし、2 つ以上の `.V()` ステップが Gremlin クエリ内に含まれている場合、そのクエリのデータの解決は最適でない可能性があります。 例として、次のクエリを取り上げます。

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    このクエリでは、`category` というプロパティに基づいて、2 つの頂点のグループが返されます。 この場合、最初の呼び出しの `g.V().has('category', 'A')` のみによって、インデックスが使用され、プロパティの値に基づいて頂点が解決されます。

    このクエリでの回避策は、`.map()` および `union()` などのサブトラバーサル ステップを使用することです。 これは、以下のように例示されます。

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    [Gremlin の `executionProfile()` ステップ](graph-execution-profile.md)を使用して、クエリのパフォーマンスを確認できます。

## <a name="next-steps"></a>次のステップ
* [Cosmos DB ユーザーの声](https://feedback.azure.com/forums/263030-azure-cosmos-db)のページにアクセスして、フィードバックを共有し、お客様にとって重要な機能に取り組めるようにチームをサポートしてください。
