---
title: SQL API 用 Azure Cosmos DB Java SDK v4 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Java SDK v4 と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: af0964dceca8b862d0008d878045203983a96bda
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586217"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Java SDK v4: リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change Feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark コネクタ](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

Core (SQL) 用 Azure Cosmos DB Java SDK v4 では、非同期 API と同期 API が 1 つの Maven アーティファクトに結合されています。 v4 SDK は、Project Reactor と [Netty ライブラリ](https://netty.io/)に基づいて、強化されたパフォーマンス、新しい API 機能、非同期サポートを提供します。 ユーザーは、Azure Cosmos DB Java SDK v4 を使用することで、[Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) や [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md) よりもパフォーマンスを向上させることができます。

> [!IMPORTANT]  
> これらのリリース ノートは、Azure Cosmos DB Java SDK v4 のみを対象としています。 v4 より前のバージョンを現在使用している場合、v4 へのアップグレードについては、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイドを参照してください。
>
> 次の 3 つの手順に従って作業を進めてください。
> 1. この SDK を使用できるように、[サポートされている最小 Java ランタイムの JDK 8](/java/azure/jdk/?view=azure-java-stable) をインストールします。
> 2. [Azure Cosmos DB Java SDK v4 のクイックスタート ガイド](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)に従って、Maven アーティファクトにアクセスし、Azure Cosmos DB の基本的な要求の詳細を確認します。
> 3. Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)と[トラブルシューティング](troubleshoot-java-sdk-v4-sql.md)のガイドを読んで、SDK をお使いのアプリケーションに最適化します。
>
> [Azure Cosmos DB のワークショップとラボ](https://aka.ms/cosmosworkshop)は、Azure Cosmos DB Java SDK v4 の使用方法を学習するための別の優れたリソースです。
>

## <a name="helpful-content"></a>役に立つコンテンツ

| コンテンツ | Link |
|---|---|
|**SDK のダウンロード**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API ドキュメント** | [Java API リファレンス ドキュメント](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**SDK への協力** | [GitHub の Azure SDK for Java 中央リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**開始するには** | [クイック スタート:Azure Cosmos DB SQL API データを管理する Java アプリを作成する](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [クイック スタート コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本的なコード サンプル** | [Azure Cosmos DB: SQL API の Java のサンプル](sql-api-java-sdk-samples.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**変更フィードを使用するコンソール アプリ**| [変更フィード - Java SDK v4 サンプル](create-sql-api-java-changefeed.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web アプリのサンプル**| [Java SDK v4 を使用した Web アプリの構築](sql-api-java-application.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **パフォーマンスに関するヒント**| [Java SDK v4 のパフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)| 
| **トラブルシューティング** | [Java SDK v4 のトラブルシューティング](troubleshoot-java-sdk-v4-sql.md) |
| **以前の SDK から v4 への移行** | [Java V4 SDK への移行](migrate-java-v4-sdk.md) |
| **サポートされている最小ランタイム**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB のワークショップとラボ** |[Cosmos DB ワークショップのホーム ページ](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>リリース履歴

### <a name="440-beta1-unreleased"></a>4.4.0-beta.1 (未リリース)

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29)
#### <a name="new-features"></a>新機能
* reactor-core ライブラリ バージョンを `3.3.8.RELEASE` に更新しました。 
* reactor-netty ライブラリ バージョンを `0.9.10.RELEASE` に更新しました。 
* netty ライブラリ バージョンを `4.1.51.Final` に更新しました。 
* `partitionKey` を含む `upsertItem` 用の新しいオーバーロード API を追加しました。 
* オープン テレメトリのトレース サポートを追加しました。 
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ゲートウェイ モードで要求をキャンセルした場合、SSLException がスローされる問題を修正しました。
* ストアド プロシージャ実行でのリソース スロットルの再試行ポリシーを修正しました。
* ログ レベル デバッグ モードで SDK がハングする問題を修正しました。 
* 直接モードで待機時間が定期的に急上昇する問題を修正しました。 
* クライアントの初期化時間が長い問題を修正しました。 
* 直接モードとゲートウェイ モードでクライアントをカスタマイズすると http プロキシ バグが発生する問題を修正しました。 
* ユーザーが null オプションを渡すと発生する可能性のある NPE を修正しました。 
* 診断文字列の `requestLatency` に timeUnit を追加しました。
* 診断文字列から重複する URI 文字列を削除しました。 
* 診断文字列をポイント操作向けの正しい JSON 形式に修正しました。
* Not Found 例外でリアクター チェーンが発生する原因となっていた `.single()` 演算子の問題を修正しました。 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>新機能
* スクリプト ログ対応 API を `CosmosStoredProcedureRequestOptions` に追加しました。
* `DirectConnectionConfig` の既定の `idleEndpointTimeout` を 1h に更新し、既定の `connectTimeout` を 5s に更新しました。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* `GatewayConnectionConfig` `idleConnectionTimeout` が `DirectConnectionConfig` `idleConnectionTimeout` をオーバーライドしていた問題を修正しました。
* `CosmosQueryRequestOptions` で `responseContinuationTokenLimitInKb` の get および set API を修正しました。
* 同じ名前でコレクションを再作成するときのクエリおよび変更フィードの問題を修正しました。
* ClassCastException をスローする上位のクエリの問題を修正しました。
* NullPointerException をスローする並べ替えクエリの問題を修正しました。
* リアクター `onErrorDropped` が呼び出される、直接モードでの取り消された要求の処理の問題を修正しました。 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>新機能
* `GROUP BY` クエリのサポートを追加しました。
* DirectConnectionConfig で maxConnectionsPerEndpoint の既定値を 130 に引き上げました。
* DirectConnectionConfig で maxRequestsPerConnection の既定値を 30 に引き上げました。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* 継続トークンを使用して再開すると、order by クエリによって重複した結果が返される問題を修正しました。 
* 入れ子になったオブジェクトに対して、値のクエリによって null 値が返される問題を修正しました。
* RntbdClientChannelPool での要求マネージャーの null ポインター例外を修正しました。

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>新機能
* `QueryRequestOptions` の名前を `CosmosQueryRequestOptions` に変更しました。
* `ChangeFeedProcessorBuilder` をビルダー パターンに更新しました。
* 新しいコンテナー名と子リソースの API によって、`CosmosPermissionProperties` を更新しました。
* `CosmosClientBuilder` により多くのサンプルとエンリッチされたドキュメントを追加しました。 
* 自動スケールとオートパイロットのサポートのために、`CosmosDatabase` & `CosmosContainer` API を throughputProperties によって更新しました。 
* `CosmosClientException` の名前を `CosmosException` に変更しました。 
* `AccessCondition` & `AccessConditionType` を `ifMatchETag()` & `ifNoneMatchETag()` API に置き換えました。 
* すべての `Cosmos*AsyncResponse` & `CosmosResponse` 型を単一の `CosmosResponse` 型に統合しました。
* `CosmosResponseDiagnostics` の名前を `CosmosDiagnostics` に変更しました。  
* `CosmosDiagnostics` で `FeedResponseDiagnostics` をラップしました。 
* azure-cosmos から `jackson` 依存関係を削除し、azure-core に依存するようになりました。 
* `CosmosKeyCredential` を `AzureKeyCredential` 型に置き換えました。 
* `GatewayConnectionConfig` に `ProxyOptions` API を追加しました。 
* `OffsetDateTime` ではなく `Instant` 型を使用するように SDK を更新しました。 
* 新しい列挙型 `OperationKind` を追加しました。 
* `FeedOptions` の名前を `QueryRequestOptions` に変更しました。 
* `Cosmos*Properties` 型に `getETag()` & `getTimestamp()` API を追加しました。 
* `CosmosException` & `CosmosDiagnostics` に `userAgent` 情報を追加しました。 
* `Diagnostics` での改行文字をシステムの改行文字に更新しました。 
* `readAll*` API を削除しました。代わりに、クエリの select all API を使用します。
* `ChangeFeedProcessor` 遅延推定 API を追加しました。   
* SDK に自動スケールとオートパイロットのスループット プロビジョニングのサポートを追加しました。  
* `ConnectionPolicy` を新しい接続構成に置き換えました。 直接およびゲートウェイ モードの接続構成に対して、`CosmosClientBuilder` 経由で `DirectConnectionConfig` & `GatewayConnectionConfig` API を公開しました。
* `JsonSerializable` & `Resource` を実装パッケージに移動しました。 
* 書き込み操作での応答コンテンツ全文を無効にする `contentResponseOnWriteEnabled` API を CosmosClientBuilder に追加しました。
* 応答の種類に対する `getETag()` API を公開しました。
* `CosmosAuthorizationTokenResolver` を実装に移動しました。 
* `preferredLocations` & `multipleWriteLocations` API の名前を `preferredRegions` & `multipleWriteRegions` に変更しました。 
* `reactor-core` を 3.3.5.RELEASE、`reactor-netty` を 0.9.7.RELEASE、`netty` を 4.1.49.Final バージョンに更新しました。 
* SDK での `analyticalStoreTimeToLive` のサポートを追加しました。     
* `CosmosClientException` によって `AzureException` を拡張しています。 
* `maxItemCount` & `requestContinuationToken` API を `FeedOptions` から削除しました。代わりに、`CosmosPagedFlux` & `CosmosPagedIterable` からの `byPage()` API を使用します。
* `Permission` API のパブリック サーフェスに対して `CosmosPermissionProperties` を導入しました。
* `SqlParameterList` 型を削除して、`List` に置き換えました。
* 直接 TCP クライアントでの複数のメモリ リークを修正しました。 
* `DISTINCT` クエリのサポートを追加しました。 
* `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` での外部の依存関係を削除しました。  
* `CosmosPagedFlux` & `CosmosPagedIterable` を `utils` パッケージに移動しました。 
* netty を 4.1.45.Final に、プロジェクト リアクターを 3.3.3 バージョンに更新しました。
* パブリック rest コントラクトを `Final` クラスに更新しました。
* 浮動小数点演算に高度な診断のサポートを追加しました。
* パッケージを `com.azure.cosmos` に更新しました
* model / rest コントラクトに対して `models` パッケージを追加しました
* `CosmosPagedFlux` & `CosmosPagedIterable` 型に対して `utils` パッケージを追加しました。 
* SDK 全体にわたって `Duration` を使用するようにパブリック API を更新しました。
* すべての rest コントラクトを `models` パッケージに追加しました。
* `RetryOptions` が `ThrottlingRetryOptions` に変更されました。
* クエリ API に対して、改ページ位置の自動修正の型 `CosmosPagedFlux` & `CosmosPagedIterable` を追加しました。 
* `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)` で新しい API を使用して CosmosClients の複数のインスタンスにわたって TransportClient を共有する機能のサポートを追加しました
* 二重シリアル化/逆シリアル化の削除によるクエリの最適化。 
* 不要なコピーによる旧戻しや更新を削除することによる応答ヘッダーの最適化。 
* 中間の文字列インスタンス化を削除して、`ByteBuffer` のシリアル化/逆シリアル化を最適化しました。

#### <a name="key-bug-fixes"></a>主要なバグ修正
* ConnectionPolicy `toString()` Null ポインター例外を修正しました。
* 値の order by クエリの場合に発生する、クエリ結果の解析の問題を修正しました。 
* 直接 TCP クライアントでのソケット リークの問題を修正しました。
* 継続トークンでの `orderByQuery` のバグを修正しました。
* パーティション分割を処理する場合およびパーティションが見つからない場合の `ChangeFeedProcessor` バグの修正。
* 異なるスレッド間でリース更新を同期する場合の `ChangeFeedProcessor` バグの修正。
* StoreReader で `ArrayIndexOutOfBound` 例外を引き起こしている競合状態を修正しました

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。