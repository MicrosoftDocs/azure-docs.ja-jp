---
title: Azure Cosmos DB:SQL Node.js API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Node.js SDK の各バージョン間の変更など、SQL Node.js API と SDK に関するあらゆる詳細を提供します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: 8408cdada40bd3595f9bf7cf2c6a2de90040ec0b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217263"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用の Azure Cosmos DB Node.js SDK:リリース ノートとリソース
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
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
> * [SQL](./sql-query-getting-started.md)
> * [Bulk Executor - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

|リソース  |Link  |
|---------|---------|
|SDK のダウンロード  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API ドキュメント  |  [JavaScript SDK リファレンス ドキュメント](/javascript/api/%40azure/cosmos/)
|SDK のインストール手順  |  [インストール手順](https://github.com/Azure/azure-sdk-for-js)
|SDK への参加 | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| サンプル | [Node.js コード サンプル](sql-api-nodejs-samples.md)
| 概要チュートリアル | [JavaScript SDK の開始](sql-api-nodejs-get-started.md)
| Web アプリ チュートリアル | [Azure Cosmos DB を使用した Node.js Web アプリケーションの作成](sql-api-nodejs-application.md)
| 現在サポートされているプラットフォーム | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK バージョン 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK バージョン 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK バージョン 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK バージョン 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK バージョン 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK バージョン 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK バージョン 1.x.x

## <a name="release-notes"></a>リリース ノート

### <a name="310"></a><a name="3.1.0"></a>3.1.0
* 既定の ResponseContinuationTokenLimitInKB を 1kb に設定しました。 既定では、長いヘッダーを避けるために、これを 1kb に制限しています (Node.js にはグローバルなヘッダー サイズ制限があります)。 ユーザーは、このフィールドを設定して長いヘッダーを許可することができます。これは、バックエンドによるクエリ実行の最適化に役立つ場合があります。
* disableSSLVerification を削除しました。 このオプションには、[#388](https://github.com/Azure/azure-cosmos-js/pull/388) で説明されている新しい代替手段があります

### <a name="304"></a><a name="3.0.4"></a>3.0.4
* initialHeaders でパーティション キー ヘッダーを明示的に設定できるようにしました
* package.json#files を使用して、余分なファイルが公開されないようにしました
* 以前のバージョンの node+v8 でのルーティング マップの並べ替えエラーを修正しました
* ユーザーが部分再試行オプションを指定したときのバグを修正しました

### <a name="303"></a><a name="3.0.3"></a>3.0.3
* require で呼び出されたモジュールが webpack によって解決されないようにしました

### <a name="302"></a><a name="3.0.2"></a>3.0.2
* RU が集計クエリに対して常に 0 として報告されるという、長い間未処理だったバグを修正しました

### <a name="300"></a><a name="3.0.0"></a>3.0.0

🎉 v3 をリリースしました。 🎉 多くの新機能、バグ修正、いくつかの破壊的変更があります。 このリリースの主な目標:

* 主要な新機能を実装します
  * DISTINCT クエリ
  * LIMIT/OFFSET クエリ
  * ユーザーが取り消し可能な要求
* すべてのコンテナーに無制限のスケールがある最新の Cosmos REST API バージョンに更新します
* ブラウザーから Cosmos を簡単に使用できるようにします
* 新しい Azure JS SDK ガイドラインとの連携を強化します

#### <a name="migration-guide-for-breaking-changes"></a>破壊的変更のための移行ガイド
##### <a name="improved-client-constructor-options"></a>強化されたクライアント コンストラクター オプション

コンストラクター オプションが簡略化されました。

* masterKey はキーの名前が変更され、最上位レベルに移動されました
* 以前は options.auth の下にあったプロパティが最上位レベルに移動されました

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>簡素化された QueryIterator API
V2 には、クエリからの結果を反復処理または取得する方法が数多くありました。 v3 API を簡略化し、類似または重複する API を削除しようとしました。

* iterator.next() と iterator.current() を削除しました。 fetchNext() を使用して結果のページを取得します。
* iterator.forEach() を削除しました。 代わりに、非同期反復子を使用します。
* iterator.executeNext() の名前が iterator.fetchNext() に変更されました
* iterator.toArray() の名前が iterator.fetchAll() に変更されました
* ページは、プレーンな JS オブジェクトではなく、適切な応答オブジェクトになりました
* const container = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>パーティション分割されるようになった固定コンテナー
Cosmos サービスでは、以前に固定コンテナーとして作成されたものも含め、すべてのコンテナー上でパーティション キーがサポートされるようになりました。 v3 SDK は、この変更を実装する最新の API バージョンに更新されますが、破壊的ではありません。 操作用のパーティション キーを指定しない場合、既定では、既存のすべてのコンテナーとドキュメントを操作するシステム キーが使用されます。

##### <a name="upsert-removed-for-stored-procedures"></a>ストアド プロシージャの upsert の削除
以前は、パーティション分割されていないコレクションに対して upsert が許可されていましたが、API バージョンを更新すると、すべてのコレクションがパーティション分割されるので、これを完全に削除しました。

##### <a name="item-reads-will-not-throw-on-404"></a>項目の読み取りで 404 がスローされない
const container = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>既定のマルチリージョンの書き込み
SDK では、Cosmos 構成でサポートされている場合に、既定で複数のリージョンに書き込まれるようになりました。 これは、以前はオプトイン動作でした。

##### <a name="proper-error-objects"></a>適切なエラー オブジェクト
失敗した要求では、適切なエラーまたはエラーのサブクラスがスローされるようになりました。 以前は、プレーン JS オブジェクトがスローされていました。

#### <a name="new-features"></a>新機能
##### <a name="user-cancelable-requests"></a>ユーザーが取り消し可能な要求
内部でのフェッチへの移行により、ブラウザーの AbortController API を使用して、ユーザーが取り消し可能な操作をサポートできます。 複数の要求が進行中の可能性がある操作 (クロス パーティション クエリなど) の場合、操作に対するすべての要求が取り消されます。 最新ブラウザーのユーザーには、既に AbortController があります。 Node.js ユーザーはポリフィル ライブラリを使用する必要があります

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>db/コンテナー作成操作の一環としてのスループットの設定
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
ヘッダー トークンの生成が新しいライブラリ @azure/cosmos-sign に分割されました。 Cosmos REST API を直接呼び出すすべてのユーザーは、これを使用して、内部の @azure/cosmos を呼び出したのと同じコードを使用してヘッダーに署名できます。

##### <a name="uuid-for-generated-ids"></a>生成された ID の UUID
v2 には、項目 ID を生成するためのカスタム コードがありました。 既知の管理されたコミュニティ ライブラリ uuid に切り替えました。

##### <a name="connection-strings"></a>Connection strings
Azure portal からコピーした接続文字列を渡すことができるようになりました。

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>ブラウザー エクスペリエンスの向上
ブラウザー内で v2 SDK を使用することはできましたが、理想的なエクスペリエンスではありませんでした。 いくつかの node.js 組み込みライブラリをポリフィルし、webpack やパーセルなどのバンドラーを使用する必要がありました。 v3 SDK を使用すると、ブラウザー ユーザーがすぐに利用できるエクスペリエンスが大幅に向上します。

* 要求の内部構造を fetch に置き換えました (#245)
* バッファーの使用を削除しました (#330)
* ユニバーサル パッケージ/API を優先してノードの組み込みの使用法を削除しました (#328)
* node-abort-controller に切り替えました (#294)

#### <a name="bug-fixes"></a>バグの修正
* オファーの読み取りを修正し、オファーのテストを戻しました (#224)
* EnableEndpointDiscovery を修正しました (#207)
* ページ分割された結果で不足している RU を修正しました (#360)
* SQL クエリ パラメーターの種類を拡張しました (#346)
* ttl を ItemDefinition に追加しました (#341)
* CP クエリ メトリックを修正しました (#311)
* activityId を FeedResponse に追加しました (#293)
* _ts の型を文字列から数値に切り替えました (#252)(#295)
* 要求の課金の集計を修正しました (#289)
* 空の文字列パーティション キーを許可しました (#277)
* 競合するクエリの種類に文字列を追加しました (#237)
* uniqueKeyPolicy をコンテナーに追加しました (#234)

#### <a name="engineering-systems"></a>エンジニアリング システム
常に最も目立つ変更であるとは限りませんが、チームがより優れたコードをより迅速に提供するのに役立ちます。

* 運用ビルドのロールアップを使用しました (#104)
* Typescript 3.5 に更新しました (#327)
* TS プロジェクト参照に変換しました。 テスト フォルダーを抽出しました (#270)
* noUnusedLocals と noUnusedParameters を有効にしました (#275)
* CI ビルドのための Azure Pipelines YAML (#298)

### <a name="215"></a><a name="2.1.5"></a>2.1.5
* コードの変更はありません。 2\.1.4 パッケージに余分なファイルが含まれていた問題を修正しました。

### <a name="214"></a><a name="2.1.4"></a>2.1.4
* 再試行ポリシー内のリージョン内フェールオーバーを修正しました
* ChangeFeed hasMoreResults プロパティを修正しました
* 開発の依存関係を更新しました
* PolicheckExclusions.txt を追加しました

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* _ts の型を文字列から数値に切り替えました
* 既定のインデックス作成テストを修正しました
* uniqueKeyPolicy を v2 にバックポートしました
* デモとデモのデバッグを修正しました

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* v3 ブランチからオファーの修正をバックポートしました
* executeNext() 型シグネチャのバグを修正しました
* 入力ミスを修正しました

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* ビルドを再構築中です。 ビルド時に SDK のバージョンをプルできるようにします。

### <a name="210"></a><a name="2.1.0"></a>2.1.0
#### <a name="new-features"></a>新機能
* ChangeFeed サポートを追加しました (#196)
* インデックス作成用の MultiPolygon データ型を追加しました (#191)
* masterKey (#202) のエイリアスとしてコンストラクターに "key" プロパティを追加しました

#### <a name="fixes"></a>修正
* next() が反復子で間違った値を返すバグを修正しました

#### <a name="engineering-improvements"></a>エンジニアリングの強化
* Typescript 消費の統合テストを追加しました (#199)
* GitHub からの直接インストールを有効にしました (#194)

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* エージェントの種類ノードのインターフェイスを追加しました。 Typescript ユーザーは @types/node を依存関係としてインストールする必要がなくなりました
* 優先する場所が適切に処理されるようになりました
* 開発者向けドキュメントの投稿に関する機能強化
* さまざまな入力ミスの修正

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* 2\.0.3 で発生した型定義の問題を修正しました

### <a name="203"></a><a name="2.0.3"></a>2.0.3
* `big-integer` の依存関係を削除しました
* AsyncIterable 型の参照ディレクティブに切り替えました。 Typescript ユーザーは "lib" 設定をカスタマイズする必要がなくなりました。
* 入力ミスを修正しました

### <a name="202"></a><a name="2.0.2"></a>2.0.2
* readme のリンクの修正

### <a name="201"></a><a name="2.0.1"></a>2.0.1
* 再試行インターフェイスの実装を修正しました

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* JavaScript SDK の Version 2.0.0 の GA
* マルチリー ジョンの書き込みのサポートが追加されました。

### <a name="200-3"></a><a name="2.0.0-3"></a>2.0.0-3
* パブリック プレビュー用の JavaScript SDK のバージョン 2.0.0 の RC1。
* 最上位レベルの CosmosClient とメソッドが関連するデータベース、コンテナー、および項目クラスとの間で分割される、新しいオブジェクト モデル。 
* [promises](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) をサポートします。 
* TypeScript に変換された SDK。

### <a name="1144"></a><a name="1.14.4"></a>1.14.4
* npm のドキュメントを修正しました。

### <a name="1143"></a><a name="1.14.3"></a>1.14.3
* 接続に問題が発生したとき、デフォルトで再試行されるようになりました。
* コレクション変更フィードの読み取り対応になりました。
* "読み取りセッションを利用不可" を中断させるセッション整合性バグを修正しました。
* クエリ メトリック対応になりました。
* http エージェントの最大接続数を変更しました。

### <a name="1142"></a><a name="1.14.2"></a>1.14.2
* Azure DocumentDB ではなく Azure Cosmos DB を参照するように更新されたドキュメントです。
* ConnectionPolicy での proxyUrl 設定のサポートが追加されました。

### <a name="1141"></a><a name="1.14.1"></a>1.14.1
* 大文字と小文字を区別するファイル システムの軽微な修正です。

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* セッションの整合性のサポートが追加されます。
* この SDK バージョンには [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) の最新バージョンが必要です。

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* クロス パーティション クエリを分割検証しました。
* 先頭および末尾のスラッシュを含むリソース リンクのサポート (および対応するテスト) を追加します。

### <a name="1122"></a><a name="1.12.2"></a>1.12.2
*    npm のドキュメントを修正しました。

### <a name="1121"></a><a name="1.12.1"></a>1.12.1
* ドキュメントに Unicode の特殊文字 (LS、PS) が含まれている場合の executeStoredProcedure のバグを修正しました。
* パーティション キーで Unicode 文字が使用されているドキュメントを処理する際のバグを修正しました。
* 名前メディアでコレクションを作成するためのサポートを修正しました。 GitHub 問題 #114。
* アクセス許可承認トークンのサポートを修正しました。 GitHub 問題 #178。

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* ConsistentPrefix と呼ばれる新しい[一貫性レベル](consistency-levels.md)に対応するようになりました。
* UriFactory のサポートを追加しました。
* Unicode サポートのバグを修正しました。 GitHub 問題 #171。

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。
* クロス パーティション クエリの並列処理の次数を制御するオプションを追加しました。
* Azure Cosmos DB Emulator に対して実行しているときに TLS 検証を無効にするオプションを追加しました。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。
* 単一パーティション コレクションに関する継続トークンのバグを修正しました。 GitHub 問題 #107。
* 0 を単一パラメーターとして処理する際の executeStoredProcedure のバグを修正しました。 GitHub 問題 #155。

### <a name="1102"></a><a name="1.10.2"></a>1.10.2
* SDK バージョンを含めるようにユーザー エージェント ヘッダーを修正しました。
* コードの軽微なクリーンアップ。

### <a name="1101"></a><a name="1.10.1"></a>1.10.1
* SDK を使用して emulator(hostname=localhost) を対象にしているときに TLS 検証が無効になります。
* ストアド プロシージャの実行時にスクリプトのログ記録を有効することができるようになりました。

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* クロス パーティションの並列クエリのサポートを追加しました。
* パーティション分割コレクションの TOP/ORDER BY クエリのサポートを追加しました。

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* スロットルされた要求のための再試行ポリシー サポートを追加しました (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。既定では、Azure Cosmos DB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。 再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。 Azure Cosmos DB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。 この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。
* Cosmos DB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。
* ConnectionPolicy クラスの RetryOptions プロパティを公開する、RetryOptions クラスが追加されました。これは、一部の既定の再試行オプションをオーバーライドするために使用できます。

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* 複数リージョンのデータベース アカウントのサポートを追加しました。

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [パーティション分割コレクション](partitioning-overview.md)と[ユーザー定義のパフォーマンス レベル](performance-levels.md)を実装しました。

### <a name="156"></a><a name="1.5.6"></a>1.5.6
* 結果の不適切な連結のためにリンクを返していなかった RangePartitionResolver.resolveForRead のバグを修正しました。

### <a name="155"></a><a name="1.5.5"></a>1.5.5
* hashParitionResolver resolveForRead() を修正しました: 登録済みのすべてのリンクの一覧を返す代わりに、指定したパーティション キーが例外をスローしない問題を修正しました。

### <a name="154"></a><a name="1.5.4"></a>1.5.4
* 問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) を修正します - 専用 HTTPS エージェント: Azure Cosmos DB 目的用のグローバル エージェントが変更されないようにします。 lib のすべての要求に対して、専用エージェントを使用します。

### <a name="153"></a><a name="1.5.3"></a>1.5.3
* 問題 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) を修正します - メディア ID のダッシュを正しく処理します。

### <a name="152"></a><a name="1.5.2"></a>1.5.2
* 問題 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) を修正します - EventEmitter リスナー リーク警告の問題を修正します。

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* 問題 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) を修正します - 大文字小文字を区別するシステムのため、Hash フォルダーの名前を hash に変更します。

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* ハッシュと範囲パーティション リゾルバーを追加することでシャーディングのサポートを実装。

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert を実装します。 documentClient の新しい upsertXXX メソッド

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました。

### <a name="122"></a><a name="1.2.2"></a>1.2.2
* Q promises ラッパーを新しいリポジトリに分割します。
* 更新し、npm レジストリのファイルをパッケージ化します。

### <a name="121"></a><a name="1.2.1"></a>1.2.1
* ID ベースのルーティングを実装します。
* 問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) を修正します - 現在のプロパティがメソッド current() と競合します。

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 地理空間インデックスのサポートを追加しました。
* すべてのリソースの ID プロパティを検証します。 リソースの ID には ?、/、#、&#47;&#47; 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2 インデックス作成ポリシーを実装します。

### <a name="103"></a><a name="1.0.3"></a>1.0.3
* 問題 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - core および promise SDK で eslint および grunt 構成を実装しました。

### <a name="102"></a><a name="1.0.2"></a>1.0.2
* 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - promise ラッパーには、エラーのあるヘッダーが含まれません。

### <a name="101"></a><a name="1.0.1"></a>1.0.1
* readConflicts、readConflictAsync、queryConflicts を追加し、競合に関して問い合わせる機能を実装しました。
* API ドキュメントを更新しました。
* 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync エラー。

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK。

## <a name="release--retirement-dates"></a>リリース日と提供終了日

Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。 新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 7 月 26 日 |--- |
| [3.0.4](#3.0.4) |2019 年 7 月 22 日 |--- |
| [3.0.3](#3.0.3) |2019 年 7 月 17 日 |--- |
| [3.0.2](#3.0.2) |2019 年 7 月 9 日 |--- |
| [3.0.0](#3.0.0) |2019 年 6 月 28 日 |--- |
| [2.1.5](#2.1.5) |2019 年 3 月 20 日 |--- |
| [2.1.4](#2.1.4) |2019 年 3 月 15 日 |--- |
| [2.1.3](#2.1.3) |2019 年 3 月 8 日 |--- |
| [2.1.2](#2.1.2) |2019 年 1 月 28 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 5 日 |--- |
| [2.1.0](#2.1.0) |2018 年 12 月 4 日 |--- |
| [2.0.5](#2.0.5) |2018 年 11 月 7 日 |--- |
| [2.0.4](#2.0.4) |2018 年 10 月 30 日 |--- |
| [2.0.3](#2.0.3) |2018 年 10 月 30 日 |--- |
| [2.0.2](#2.0.2) |2018 年 10 月 10 日 |--- |
| [2.0.1](#2.0.1) |2018 年 9 月 25 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 24 日 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018 年 8 月 2 日 |--- |
| [1.14.4](#1.14.4) |2018 年 5 月 3 日 |2020 年 8 月 30 日 |
| [1.14.3](#1.14.3) |2018 年 5 月 3 日 |2020 年 8 月 30 日 |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |2020 年 8 月 30 日 |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |2020 年 8 月 30 日 |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |2020 年 8 月 30 日 |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |2020 年 8 月 30 日 |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |2020 年 8 月 30 日 |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |2020 年 8 月 30 日 |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |2020 年 8 月 30 日 |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |2020 年 8 月 30 日 |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |2020 年 8 月 30 日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020 年 8 月 30 日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020 年 8 月 30 日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020 年 8 月 30 日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |2020 年 8 月 30 日 |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |2020 年 8 月 30 日 |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |2020 年 8 月 30 日 |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |2020 年 8 月 30 日 |
| [1.5.2](#1.5.2) |2016 年 1 月 22 日 |2020 年 8 月 30 日 |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |2020 年 8 月 30 日 |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |2020 年 8 月 30 日 |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.2.2](#1.2.2) |2015 年 9 月 10 日 |2020 年 8 月 30 日 |
| [1.2.1](#1.2.1) |2015 年 8 月 15 日 |2020 年 8 月 30 日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020 年 8 月 30 日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020 年 8 月 30 日 |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |2020 年 8 月 30 日 |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |2020 年 8 月 30 日 |
| [1.0.1](#1.0.1) |2015 年 5 月 15 日 |2020 年 8 月 30 日 |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |2020 年 8 月 30 日 |

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。