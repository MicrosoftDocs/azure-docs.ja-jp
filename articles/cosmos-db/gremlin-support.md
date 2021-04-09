---
title: Azure Cosmos DB での Gremlin のサポートと、TinkerPop 機能との互換性
description: Apache TinkerPop の Gremlin 言語について説明します。 Azure Cosmos DB で使用できる機能と手順について説明します。また、TinkerPop Graph エンジンの互換性の違いについても説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 11/11/2020
ms.author: sngun
ms.openlocfilehash: 036338e90a3e7b466924d419400c0dcc692dec5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97630753"
---
# <a name="azure-cosmos-db-gremlin-graph-support-and-compatibility-with-tinkerpop-features"></a>Azure Cosmos DB での Gremlin グラフのサポートと、TinkerPop 機能との互換性
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB は、[Gremlin](https://tinkerpop.apache.org/docs/3.3.2/reference/#graph-traversal-steps) と呼ばれる、[Apache TinkerPop](https://tinkerpop.apache.org) のグラフ トラバーサル言語をサポートしています。 Gremlin 言語を使用して、グラフ エンティティ (頂点と辺) の作成、エンティティ内のプロパティの変更、クエリとトラバーサルの実行、エンティティの削除を行うことができます。

Azure Cosmos DB Graph エンジンは [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) のトラバーサル ステップの仕様に厳密に従っていますが、実装には Azure Cosmos DB に固有の違いがあります。 この記事では、Gremlin の簡単なチュートリアルを提供し、Gremlin API でサポートされている Gremlin の機能を紹介します。

## <a name="compatible-client-libraries"></a>互換性のあるクライアント ライブラリ

次の表に、Azure Cosmos DB に対して使用できる一般的な Gremlin ドライバーを示します。

| ダウンロード | source | 作業の開始 | サポートされているコネクタのバージョン |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.4.6/reference/#gremlin-DotNet) | [Github の Gremlin.NET](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [.NET を使用してグラフを作成する](create-graph-dotnet.md) | 3.4.6 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Java を使用してグラフを作成する](create-graph-java.md) | 3.2.0 以降 |
| [Node.js](https://www.npmjs.com/package/gremlin) | [GitHub の Gremlin-JavaScript](https://github.com/apache/tinkerpop/tree/master/gremlin-javascript) | [Node.js を使用してグラフを作成する](create-graph-nodejs.md) | 3.3.4 以降 |
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [GitHub の Gremlin-Python](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Python を使用してグラフを作成する](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Github の Gremlin-PHP](https://github.com/PommeVerte/gremlin-php) | [PHP を使用してグラフを作成する](create-graph-php.md) | 3.1.0 |
| [Go Lang](https://github.com/supplyon/gremcos/) | [Go Lang](https://github.com/supplyon/gremcos/) | | このライブラリは、外部の共同作成者によって作成されています。 Azure Cosmos DB チームは、このライブラリのサポートやメンテナンスを一切提供していません。 |
| [Gremlin コンソール](https://tinkerpop.apache.org/downloads.html) | [TinkerPop ドキュメント](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Gremlin コンソールを使用してグラフを作成する](create-graph-gremlin-console.md) | 3.2.0 以降 |

## <a name="supported-graph-objects"></a>サポートされているグラフ オブジェクト

TinkerPop は、さまざまなグラフ テクノロジに対応する標準です。 そのため、グラフ プロバイダーによって提供される機能を説明する標準的な用語があります。 Azure Cosmos DB は、複数のサーバーまたはクラスター間でパーティション分割できる、永続的でコンカレンシーの高い書き込み可能なグラフ データベースを提供します。 

次の表に、Azure Cosmos DB で実装されている TinkerPop の機能を示します。 

| カテゴリ | Azure Cosmos DB 実装 |  Notes | 
| --- | --- | --- |
| グラフ機能 | 永続化と同時アクセスが提供されます。 トランザクションをサポートするように設計されています。 | コンピューターのメソッドは、Spark コネクタを介して実装できます。 |
| 変数機能 | ブール値、整数、Byte、Double、Float、Long、文字列をサポートしています。 | プリミティブ型をサポートしています。データ モデルを介して複雑な型と互換性があります。 |
| 頂点機能 | RemoveVertices、MetaProperties、AddVertices、MultiProperties、StringIds、UserSuppliedIds、AddProperty、RemoveProperty をサポートしています。  | 頂点の作成、変更、削除をサポートしています。 |
| 頂点プロパティ機能 | StringIds、UserSuppliedIds、AddProperty、RemoveProperty、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 頂点プロパティの作成、変更、削除をサポートしています。 |
| 辺機能 | AddEdges、RemoveEdges、StringIds、UserSuppliedIds、AddProperty、RemoveProperty | 辺の作成、変更、削除をサポートしています。 |
| 辺プロパティ機能 | Properties、BooleanValues、ByteValues、DoubleValues、FloatValues、IntegerValues、LongValues、StringValues | 辺プロパティの作成、変更、削除をサポートしています。 |

## <a name="gremlin-wire-format"></a>Gremlin のワイヤ形式

Azure Cosmos DB では、Gremlin の操作から結果を返すときに JSON 形式を使用します。 Azure Cosmos DB では現在、JSON 形式がサポートされています。 たとえば、次のスニペットは、Azure Cosmos DB から "*クライアントに返される*" JSON による頂点の表現を示しています。

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

JSON 形式で使用される頂点のプロパティを次に説明します。

| プロパティ | 説明 | 
| --- | --- | --- |
| `id` | 頂点の ID。 一意である必要があります (該当する場合は、`_partition` の値との組み合わせにおいて一意である必要があります)。 値が指定されていない場合は、GUID が自動的に提供されます | 
| `label` | 頂点のラベル。 このプロパティは、エンティティの種類を示すために使用します。 |
| `type` | 頂点とグラフ以外のドキュメントを区別するために使用します。 |
| `properties` | 頂点に関連付けられているユーザー定義プロパティのバッグ。 各プロパティには複数の値を指定できます。 |
| `_partition` | 頂点のパーティション キー。 [グラフのパーティション分割](graph-partitioning.md)に使用されます。 |
| `outE` | このプロパティには、頂点からの外向きの辺のリストが含まれます。 頂点と共に隣接情報を格納することで、トラバーサルの高速実行が可能になります。 辺はラベルに基づいてグループ化されます。 |

辺には、グラフの他の部分へのナビゲーションに役立つ次の情報が含まれています。

| プロパティ | 説明 |
| --- | --- |
| `id` | 辺の ID。 一意である必要があります (該当する場合は、`_partition` の値との組み合わせにおいて一意である必要があります) |
| `label` | 辺のラベル。 このプロパティは省略可能です。関係の種類を示すために使用します。 |
| `inV` | このプロパティには、辺の頂点一覧が含まれています。 辺と共に隣接情報を格納することで、トラバーサルの高速実行が可能になります。 頂点はラベルに基づいてグループ化されます。 |
| `properties` | 辺に関連付けられているユーザー定義プロパティのバッグ。 各プロパティには複数の値を指定できます。 |

各プロパティでは、配列内に複数の値を格納できます。 

| プロパティ | 説明 |
| --- | --- |
| `value` | プロパティの値。

## <a name="gremlin-steps"></a>Gremlin のステップ

次に、Azure Cosmos DB でサポートされている Gremlin のステップを見てみましょう。 Gremlin の完全なリファレンスについては、[TinkerPop リファレンス](https://tinkerpop.apache.org/docs/3.3.2/reference)をご覧ください。

| ステップ | 説明 | TinkerPop 3.2 ドキュメント |
| --- | --- | --- |
| `addE` | 2 つの頂点の間に辺を追加します。 | [addE ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#addedge-step) |
| `addV` | グラフに頂点を追加します。 | [addV ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#addvertex-step) |
| `and` | すべてのトラバーサルが値を返すようにします。 | [and ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#and-step) |
| `as` | ステップの出力に変数を割り当てるステップ モジュレーター。 | [as ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#as-step) |
| `by` | `group` および `order` と共に使用するステップ モジュレーター。 | [by ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#by-step) |
| `coalesce` | 結果を返す最初のトラバーサルを返します。 | [coalesce ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#coalesce-step) |
| `constant` | 定数値を返します。 `coalesce` と共に使用します。| [constant ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#constant-step) |
| `count` | トラバーサルからカウントを返します。 | [count ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#count-step) |
| `dedup` | 重複を削除して値を返します。 | [dedup ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#dedup-step) |
| `drop` | 値 (頂点/辺) を破棄します。 | [drop ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#drop-step) |
| `executionProfile` | 実行された Gremlin ステップによって生成されたすべての操作の記述を作成します | [executionProfile ステップ](graph-execution-profile.md) |
| `fold` | 結果の集計を計算するバリアとして機能します。| [fold ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#fold-step) |
| `group` | 指定されたラベルに基づいて値をグループ化します。| [group ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#group-step) |
| `has` | プロパティ、頂点、辺をフィルター処理するときに使用します。 `hasLabel`、`hasId`、`hasNot`、`has` の各バリアントをサポートします。 | [has ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#has-step) |
| `inject` | 値をストリームに挿入します。| [inject ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#inject-step) |
| `is` | ブール式を使用してフィルターを実行するときに使用します。 | [is ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#is-step) |
| `limit` | トラバーサルで項目の数を制限するときに使用します。| [limit ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#limit-step) |
| `local` | サブクエリと同様に、トラバーサルのセクションをローカルでラップします。 | [local ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#local-step) |
| `not` | フィルターの否定を生成するときに使用します。 | [not ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#not-step) |
| `optional` | 指定されたトラバーサルの結果が生成された場合は、その結果を返します。それ以外の場合は、呼び出し元の要素を返します。 | [optional ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#optional-step) |
| `or` | 少なくとも 1 つのトラバーサルで値が返されるようにします。 | [or ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#or-step) |
| `order` | 指定された並べ替え順序で結果を返します。 | [order ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#order-step) |
| `path` | トラバーサルの完全なパスを返します。 | [path ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#path-step) |
| `project` | プロパティをマップとして投影します。 | [project ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#project-step) |
| `properties` | 指定されたラベルのプロパティを返します。 | [properties ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#_properties_step) |
| `range` | 値の指定された範囲にフィルターを適用します。| [range ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#range-step) |
| `repeat` | ステップを指定された回数繰り返します。 ループに使用します。 | [repeat ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#repeat-step) |
| `sample` | トラバーサルの結果をサンプリングするときに使用します。 | [sample ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#sample-step) |
| `select` | トラバーサルの結果を予想するときに使用します。 |  [select ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#select-step) |
| `store` | トラバーサルの非ブロッキング集計に使用します。 | [store ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#store-step) |
| `TextP.startingWith(string)` | 文字列フィルター処理関数。 この関数は、指定された文字列で始まるプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.endingWith(string)` |  文字列フィルター処理関数。 この関数は、指定された文字列で終わるプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.containing(string)` | 文字列フィルター処理関数。 この関数は、指定された文字列の内容を含むプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notStartingWith(string)` | 文字列フィルター処理関数。 この関数は、指定された文字列で始まらないプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notEndingWith(string)` | 文字列フィルター処理関数。 この関数は、指定された文字列で終わらないプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `TextP.notContaining(string)` | 文字列フィルター処理関数。 この関数は、指定された文字列を含んでいないプロパティを照合するための `has()` ステップの述語として使用されます。 | [TextP 述語](https://tinkerpop.apache.org/docs/3.4.0/reference/#a-note-on-predicates) |
| `tree` | 頂点からのパスを集計してツリーを形成します。 | [tree ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#tree-step) |
| `unfold` | 反復子をステップとしてアンロールします。| [unfold ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#unfold-step) |
| `union` | 複数のトラバーサルの結果をマージします。| [union ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#union-step) |
| `V` | 頂点および辺の間でのトラバーサルに必要なステップ (`V`、`E`、`out`、`in`、`both`、`outE`、`inE`、`bothE`、`outV`、`inV`、`bothV`、`otherV`) が含まれています。 | [vertex ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#vertex-steps) |
| `where` | トラバーサルの結果をフィルター処理するときに使用します。 `eq`、`neq`、`lt`、`lte`、`gt`、`gte`、`between` の各演算子をサポートします。  | [where ステップ](https://tinkerpop.apache.org/docs/3.3.2/reference/#where-step) |

Azure Cosmos DB によって提供された書き込みに最適化されたエンジンは、頂点および辺内のすべてのプロパティの自動インデックス作成を既定でサポートしています。 そのため、任意のプロパティでのフィルターを使用するクエリ、範囲クエリ、並べ替え、または集計は、インデックスに基づいて処理され、効率的に提供されます。 Azure Cosmos DB におけるインデックス作成のしくみの詳細については、[スキーマに依存しないインデックス作成](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)に関する論文をご覧ください。

## <a name="behavior-differences"></a>動作の違い

* Azure Cosmos DB Graph エンジンでは ***幅優先*** トラバーサルが実行されますが、TinkerPop Gremlin では深さ優先になります。 この動作により、Cosmos DB のように水平方向にスケーラブルなシステムでのパフォーマンスが向上します。

## <a name="unsupported-features"></a>サポートされていない機能

* ***[Gremlin バイトコード](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** は、プログラミング言語に依存しない、グラフ トラバーサルの仕様です。 Cosmos DB Graph ではまだサポートされていません。 `GremlinClient.SubmitAsync()` を使用して、トラバーサルをテキスト文字列として渡してください。

* ***`property(set, 'xyz', 1)`*** set カーディナリティは現在サポートされていません。 代わりに `property(list, 'xyz', 1)` を使用してください 詳細については、[TinkerPop での頂点プロパティ](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)に関する記事を参照してください。

* ***`match()` ステップ*** は、現在利用できません。 このステップでは、宣言型のクエリ機能が提供されます。

* 頂点または辺の ***プロパティとしてのオブジェクト*** はサポートされていません。 プロパティには、プリミティブ型または配列のみを指定できます。

* ***配列プロパティによる並べ替え*** (`order().by(<array property>)`) はサポートされていません。 プリミティブ型での並べ替えのみがサポートされています。

* ***非プリミティブ JSON 型*** はサポートされていません。 `string` 型、`number` 型、または `true`/`false` 型を使用してください。 `null` 値はサポートされていません。 

* ***GraphSONv3*** シリアライザーは現在サポートされていません。 接続構成で `GraphSONv2` シリアライザー、リーダー、およびライター クラスを使用してください。 Azure Cosmos DB Gremlin API によって返される結果は、GraphSON 形式と同じ形式ではありません。 

* **ラムダ式と関数** は現在サポートされていません。 これには、`.map{<expression>}`、`.by{<expression>}`、および `.filter{<expression>}` 関数が含まれます。 詳細について、および Gremlin の手順を使用してこれらを書き換える方法については、[ラムダ式に関する注意事項](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)に関する記事を参照してください。

* システムの分散型の性質により、***トランザクション*** はサポートされていません。  Gremlin アカウントで "自分の書き込みを読み取る" ように適切な整合性モデルを構成し、オプティミスティック同時実行制御を使用して、競合する書き込みを解決してください。

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

* [SDK を使用](create-graph-dotnet.md)してグラフ アプリケーションの構築を開始する 
* Azure Cosmos DB の[グラフ サポート](graph-introduction.md)の詳細について説明します。
