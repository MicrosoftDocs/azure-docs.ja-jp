---
title: Azure Cosmos DB SQL API 用の SQL クエリ メトリック
description: Azure Cosmos DB 要求の SQL クエリをインストルメント化し、SQL クエリのパフォーマンスをデバッグする方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 019ca26143a4879efafa973299703f0abcb21162
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102488088"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Azure Cosmos DB を使用したクエリ パフォーマンスのチューニング
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、スキーマやセカンダリ インデックスを使用せず[データを照会するための SQL API](./sql-query-getting-started.md) を提供します。 この記事は、開発者を対象とした次の情報を提供します。

* Azure Cosmos DB の SQL クエリの実行構造の詳細
* クエリの要求および応答ヘッダー、およびクライアント SDK オプションの詳細
* クエリ パフォーマンスに関するヒントとベスト プラクティス
* SQL 実行の統計を利用しクエリ パフォーマンスのデバッグの例

## <a name="about-sql-query-execution"></a>SQL クエリの実行について

Azure Cosmos DB では、データは任意の[ストレージ サイズまたは要求スループット](partitioning-overview.md)に拡張できるコンテナーに格納されます。 Azure Cosmos DB は、プロビジョニング スループットの増加またはデータの増加に応じて、複数の物理パーティションにわたってデータをシームレスに拡張できます。 REST API またはサポートされている [SQL SDK](sql-api-sdk-dotnet.md) のいずれかを使用して任意のコンテナーに SQL クエリを発行できます。

パーティション分割の概要: "city" などのパーティション キーを定義すると、複数の物理パーティションにわたるデータの分割方法が決定されます。 1 つのパーティション キーに属しているデータ (たとえば、"city" == "Seattle") は、1 つの物理パーティション内に格納されますが、1 つの物理パーティションには、通常、複数のパーティション キーが存在します。 パーティションが最大記憶域容量に達すると、サービスによってパーティションは新しい 2 つのパーティションにシームレスに分割され、これらのパーティション間でパーティション キーが均等に分割されます。 パーティションは一時的なもので、API は、パーティション キー ハッシュの範囲を表す "パーティション キー範囲" の抽象化を使用します。 

Azure Cosmos DB にクエリを発行すると、SDK はこれらの論理手順を実行します。

* SQL クエリを解析してクエリ実行プランを決定します。 
* クエリに `SELECT * FROM c WHERE c.city = "Seattle"` のようなパーティション キーに対するフィルターが含まれている場合は、1 つのパーティションに転送されます。 クエリにパーティション キーに対するフィルターがない場合は、すべてのパーティションでクエリが実行され、結果はクライアント側でマージされます。
* クエリは、クライアントの構成に基づいて、各パーティション内で順次または並行して実行されます。 各パーティション内では、クエリは、クエリの複雑性、構成されたページ サイズ、コレクションにプロビジョニングされたスループットに応じて 1 回以上のラウンド トリップを実行します。 実行ごとに、クエリ実行統計で使用することもできる、クエリ実行で使用された[要求ユニット](request-units.md)の数が返されます。 
* SDK は複数のパーティションにわたるクエリ結果のサマリーを作成します。 たとえば、複数のパーティションにわたって ORDER BY が実行された場合、各パーティションからの結果がマージ、並べ替えられて、全体で並べ替えられた結果が返されます。 クエリが `COUNT` のような集計の場合、各パーティションからの count 数を合計して合計 count 数を算出します。

SDK は、クエリの実行にさまざまなオプションを提供します。 たとえば、.NET では、これらのオプションは `FeedOptions` クラスで提供されています。 次の表は、これらのオプションと、オプションがクエリ実行時間に及ぼす影響を示しています。 

| オプション | 説明 |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | 1 つ以上のパーティションにわたって実行する必要があるクエリでは true に設定する必要があります。 これは、開発時にパフォーマンスのトレードオフを意識的に評価するための明示的なフラグです。 |
| `EnableScanInQuery` | インデックスを作成しないことを選択したが、スキャンを使用してクエリを実行する必要がある場合は、true に設定する必要があります。 要求されたフィルター パスのインデックス作成が無効になっている場合のみ適用されます。 | 
| `MaxItemCount` | サーバーへのラウンド トリップあたり返される最大項目数。 -1 に設定すると、サーバーが項目数を管理します。 または、この値を下げると、ラウンド トリップあたりの取得項目数を抑えることができます。 
| `MaxBufferedItemCount` | これはクライアント側のオプションで、複数のパーティションにわたって ORDER BY を実行した場合のメモリ消費量を制限するために使用します。 値を引き上げると、パーティション間の並べ替えを待つ時間を短縮できます。 |
| `MaxDegreeOfParallelism` | Azure Cosmos データベース サービスでの並列クエリ実行中にクライアント側で実行される同時実行操作の数を取得または設定します。 プロパティに正の値を設定すると、同時実行操作の数が設定された値に制限されます。 0 未満に設定すると、同時実行操作の数はシステムによって自動的に設定されます。 |
| `PopulateQueryMetrics` | コンパイル時間、インデックス ループ時間、ドキュメント読み込み時間などのクエリ実行のさまざまな段階でかかっている時間に関する統計詳細をログに記録できます。 クエリ統計からの出力を Azure のサポートと共有して、クエリ パフォーマンスの問題を診断できます。 |
| `RequestContinuation` | クエリから返された非透過的継続トークンを渡すことによって、クエリの実行を再開できます。 継続トークンには、クエリの実行に必要なすべての状態がカプセル化されています。 |
| `ResponseContinuationTokenLimitInKb` | サーバーによって返される継続トークンの最大サイズを制限することができます。 アプリケーション ホストで応答ヘッダーのサイズが制限されている場合は、これを設定する必要があります。 これを設定すると、全体的な実行時間と、クエリで使用される RU 数が増加する場合があります。  |

たとえば、パーティション キーとして `/city` を使用し、100,000 RU/秒のスループットでプロビジョニングされているコレクションに要求されたパーティション キーのクエリがあるとします。 .NET で `CreateDocumentQuery<T>` を使用すると、このクエリは次のように要求できます。

```cs
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
```

上記の SDK スニペットは、次の REST API 要求に対応します。

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

各クエリの実行ページは、`Accept: application/query+json` ヘッダーと本文 SQL クエリを使用する REST API `POST` に対応します。 クエリごとにサーバーへ 1 つまたは複数のラウンド トリップが実行され、実行を再開するために、クライアントとサーバー間で `x-ms-continuation` トークンがエコーされます。 FeedOptions の構成オプションは、要求ヘッダーの形式でサーバーに渡されます。 たとえば、`MaxItemCount` は `x-ms-max-item-count` に対応します。 

この要求は、(読みやすくするために切り捨てられます) 次の応答を返します。

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

クエリから返されたキーの応答ヘッダー、次のとおりです。

| オプション | 説明 |
| ------ | ----------- |
| `x-ms-item-count` | 応答で返される項目の数。 これは、指定された `x-ms-max-item-count` や、応答の最大ペイロード サイズ、プロビジョニングされたスループット、およびクエリの実行時間の範囲内に収まる項目数によって異なります。 |  
| `x-ms-continuation:` | その他の結果が利用可能な場合は、クエリの実行を再開する継続トークンです。 | 
| `x-ms-documentdb-query-metrics` | 実行するクエリ統計です。 これは、クエリ実行のさまざまな段階でかかった時間の統計を含む、記号で区切られた文字列です。 `x-ms-documentdb-populatequerymetrics` が `True` に設定されている場合に返されます。 | 
| `x-ms-request-charge` | クエリによって使用される[要求ユニット](request-units.md)数。 | 

REST API の要求ヘッダーとオプションの詳細については、「[REST API を使用しているリソースを照会する](/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)」を参照してください。

## <a name="best-practices-for-query-performance"></a>クエリ パフォーマンスに関するベスト プラクティス
Azure Cosmos DB クエリのパフォーマンスに影響を与える最も一般的な要因を次に示します。 この後、この記事の中でそれぞれのトピックについて詳しく説明します。

| 要素 | ヒント | 
| ------ | -----| 
| プロビジョニング スループット | クエリごとの RU を測定し、クエリに必要なプロビジョニング スループットがあることを確認します。 | 
| パーティション分割とパーティション キー | 待ち時間を短くするために、フィルター句と一致するパーティション キー値のクエリが優先されます。 |
| SDK とクエリのオプション | 直接接続、クライアント側のクエリ実行オプションの調整など、SDK のベスト プラクティスに従ってください。 |
| インデックス作成ポリシー | クエリに必要なインデックス作成パス/ポリシーがあることを確認してください。 |
| クエリ実行メトリック | クエリ実行メトリックを分析して、クエリおよびデータ図形の書き換えの必要性を特定します。  |

### <a name="provisioned-throughput"></a>プロビジョニング スループット
Cosmos DB では、秒あたりの要求ユニット (RU) で表される予約済みスループットで各データのコンテナーを作成します。 1 KB のドキュメントの読み取りは 1 RU で、各操作 (クエリを含む) は、その複雑性に基づいて固定された RU 数に正規化されます。 たとえば、1000 RU/秒がコンテナーにプロビジョニングされており、5 RU を消費する `SELECT * FROM c WHERE c.city = 'Seattle'` のようなクエリの場合、このクエリは秒あたり、(1000 RU/秒) / (5 RU/クエリ) = 200 個のクエリを実行できます。 

1 秒あたり 200 個を超えるクエリを送信した場合、サービスは、1 秒あたり 200 クエリを超えて受け取った要求に対し速度制限を開始します。 SDK では、バックオフ/再試行を実行することによって、このような事態を自動的に処理するため、これらのクエリの待ち時間が長くなっているように見えることがあります。 プロビジョニング スループットを必要な値に引き上げることで、クエリの待ち時間やスループットが改善されます。 

要求ユニットの詳細については、 「[要求ユニット](request-units.md)」を参照してください。

### <a name="partitioning-and-partition-keys"></a>パーティション分割とパーティション キー
Azure Cosmos DB では、通常、最も実行時間が短く、効率的なクエリから、実行時間が長く、効率性が低いクエリの順番で実行されます。 

* 1 つのパーティション キーと項目キーの取得
* 1 つのパーティション キーに 1 つのフィルター句を含むクエリ
* 任意のプロパティに対して等値または範囲フィルター句を使用しないクエリ
* フィルターを使用しないクエリ

すべてのパーティションを参照する必要があるクエリでは、待ち時間が長くなり、使用する RU 数が多くなります。 各パーティションは、すべてのプロパティに対して自動インデックス作成機能があるため、この場合、インデックスからクエリを効率的に実行できます。 並行処理オプションを使用すると、複数のパーティションにまたがってクエリを実行できます。

パーティション分割とパーティション キーの詳細については、「[Azure Cosmos DB のパーティション分割](partitioning-overview.md)」をご覧ください。

### <a name="sdk-and-query-options"></a>SDK とクエリのオプション
Azure Cosmos DB でクライアント側のパフォーマンスを最適化する方法については、「[パフォーマンスに関するヒント](performance-tips.md)」と「[パフォーマンス テスト](performance-testing.md)」を参照してください。 これには、最新の SDK を使用する、既定の接続数、ガベージ コレクション頻度などのプラットフォーム固有の構成を設定すること、Direct/TCP などの軽量な接続オプションを使用することが含まれます。 


#### <a name="max-item-count"></a>最大項目数
クエリでは、`MaxItemCount` の値は、エンド ツー エンドのクエリ時間に大きな影響を与えることができます。 サーバーへの各ラウンド トリップでは、`MaxItemCount` を超える数の項目が返ることはありません (既定値は 100 項目)。 この値を大きく設定する (-1 が最大値であり推奨値です) と、サーバーとクライアント間のラウンド トリップの回数を制限することによって、全体的なクエリの実行時間が向上します。これは、結果セットが大きい場合に特に当てはまります。

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>並列処理の最大限度
クエリでは、`MaxDegreeOfParallelism` を調整して、アプリケーションに最適な構成を識別します。これは、(パーティション キー値に対するフィルターなしで) クロス プラットフォーム クエリを実行する場合に特に当てはまります。 `MaxDegreeOfParallelism` は、並列タスクの最大数、つまり並列でアクセスされるパーティションの最大数を制御します。 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

以下を仮定しましょう。
* D = 並列処理の既定の最大数 (= クライアント コンピューター上のプロセッサの総数)
* P = ユーザー指定の並列タスクの最大数
* N = クエリに応答するためにアクセスする必要があるパーティションの数

P の値が異なると、並列クエリがどのように動作するかの結果を次に示します。
* (P == 0) => シリアル モード
* (P == 1) => 最大 1 つのタスク
* (P > 1) => Min (P, N) の並列タスク 
* (P < 1) => Min (N, D) の並列タスク

SDK リリース ノート、および実装されているクラスとメソッドの詳細については、[SQL SDK](sql-api-sdk-dotnet.md) を参照してください。

### <a name="network-latency"></a>ネットワーク待ち時間
グローバル配布を設定し、最も近い地域に接続する方法については、「[Azure Cosmos DB グローバル配布](tutorial-global-distribution-sql-api.md)」を参照してください。 ネットワーク待ち時間は、複数のラウンド トリップを実行したり、クエリから大量の結果セットを取得する必要がある場合は、クエリのパフォーマンスに大きな影響を及ぼします。 

「クエリ実行メトリック」セクションに、サーバーでのクエリの実行時間を取得する方法 (`totalExecutionTimeInMs`) の説明があるので、クエリを実行するために費やされた時間とネットワークを通過するために費やされた時間を区別できます。

### <a name="indexing-policy"></a>インデックス作成ポリシー
インデックス作成のパス、種類、モード、クエリ実行への影響については、「[インデックス作成ポリシーを構成する](index-policy.md)」を参照してください 既定では、等値クエリには有効だが範囲クエリ/並べ替えクエリには有効ではない文字列に対しハッシュ インデックスを使用するインデックス作成ポリシーが採用されています。 文字列の範囲クエリが必要な場合は、すべての文字列に対し範囲インデックス タイプを指定することをお勧めします。 

既定では、Azure Cosmos DB は、自動インデックス作成をすべてのデータに適用します。 ハイ パフォーマンスの挿入シナリオの場合は、各挿入操作の RU コストを削減するために、パスを除外することを検討してください。 

## <a name="query-execution-metrics"></a>クエリ実行メトリック
省略可能な `x-ms-documentdb-populatequerymetrics`ヘッダーを渡す (`FeedOptions.PopulateQueryMetrics` .NET SDK で) ことで、クエリの実行に関する詳細なメトリックを取得できます。 `x-ms-documentdb-query-metrics` で返される値には、クエリの実行の高度なトラブルシューティングに使用できる次のキー/値ペアが含まれています。 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| メトリック | ユニット | 説明 | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | ミリ秒 | クエリ実行時間 | 
| `queryCompileTimeInMs` | ミリ秒 | クエリのコンパイル時間  | 
| `queryLogicalPlanBuildTimeInMs` | ミリ秒 | 論理的なクエリ プランの構築にかかった時間 | 
| `queryPhysicalPlanBuildTimeInMs` | ミリ秒 | 物理的なクエリ プランクエリ プランの構築にかかった時間 | 
| `queryOptimizationTimeInMs` | ミリ秒 | クエリの最適化にかかった時間 | 
| `VMExecutionTimeInMs` | ミリ秒 | クエリの実行にかかった時間 | 
| `indexLookupTimeInMs` | ミリ秒 | 物理インデックス レイヤーでかかった時間 | 
| `documentLoadTimeInMs` | ミリ秒 | ドキュメントの読み込みにかかった時間  | 
| `systemFunctionExecuteTimeInMs` | ミリ秒 | システム (組み込み) 関数の実行にかかった合計時間 (ミリ秒)  | 
| `userFunctionExecuteTimeInMs` | ミリ秒 | ユーザー定義関数の実行にかかった合計時間 (ミリ秒) | 
| `retrievedDocumentCount` | count | 取得されたドキュメントの合計数  | 
| `retrievedDocumentSize` | バイト | バイト単位で取得されたドキュメントの合計サイズ (バイト)  | 
| `outputDocumentCount` | count | 出力ドキュメントの数 | 
| `writeOutputTimeInMs` | ミリ秒 | クエリ実行時間 (ミリ秒) | 
| `indexUtilizationRatio` | 比率 (< = 1) | 読み込まれたドキュメント数に対するフィルターに一致したドキュメントの数の比率  | 

クライアント SDK は、各パーティション内でクエリを実行するために、複数のクエリ操作を内部実行する可能性があります。 合計結果が `x-ms-max-item-count` を上回った場合、クエリがパーティションにプロビジョニングされたスループットを超えた場合、またはクエリのペイロードがページあたりの最大サイズに達した場合、またはクエリがシステムによって割り当てられたタイムアウト上限に達した場合は、クライアントはパーティションあたり複数の呼び出しを実行します。 部分クエリが実行されるたびに、そのページに対し、`x-ms-documentdb-query-metrics` が返されます。 

以下は、クエリの例とクエリ実行から返されたメトリックを解釈する方法です。 

| クエリ | メトリックの例 | 説明 | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | 取得されたドキュメントの数は、TOP 句と一致した 100 + 1 になっています。 スキャンであるため、クエリ時間の大部分が `WriteOutputTime` と `DocumentLoadTime` で費やされています。 | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | RetrievedDocumentCount が高くなっています (TOP 句と一致する 500 + 1 になっています)。 | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | `/N/?` に対するインデックス参照であるため、IndexLookupTime のキー参照に約 0.9 ミリ秒かかっています。 | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | `/N/?` に対するインデックス参照であるため、範囲スキャンの IndexLookupTime では、これより少し長い 1.7 ミリ秒かかっています。 | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | `DocumentLoadTime` では前のクエリと同じ時間がかかっていますが、プロジェクションさているプロパティが 1 つのみのため、より短い `WriteOutputTime` になっています。 | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | 各値が `c.N` の UDF を実行するために、`UserDefinedFunctionExecutionTime` で約 213 ミリ秒かかっています。 |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | `IndexLookupTime` の `/Name/?` では約 0.6 ミリ秒かかっています。 `SystemFunctionExecutionTime` でのほとんどのクエリの実行時間 は最大 7 ミリ秒です。 |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | `LOWER` を使用しているため、クエリはスキャンとして実行され、2491 の取得ドキュメント中、500 ドキュメントが返されました。 |


## <a name="next-steps"></a>次のステップ
* サポートされている SQL クエリ演算子とキーワードの詳細については、「[SQL クエリ](sql-query-getting-started.md)」を参照してください。 
* 要求ユニットの詳細については、「[要求ユニット](request-units.md)」を参照してください。
* インデックス作成ポリシーの詳細については、「[インデックス作成ポリシー](index-policy.md)」を参照してください