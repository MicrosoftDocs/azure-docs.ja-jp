---
title: Azure Cosmos DB は:SQL Node.js API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Node.js SDK の各バージョン間の変更など、SQL Node.js API と SDK に関するあらゆる詳細を提供します。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 1cb6889305e5f6bce5728039712a1834dc2e9353
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033589"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用の Azure Cosmos DB Node.js SDK:リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|リソース  |Link  |
|---------|---------|
|SDK のダウンロード  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API ドキュメント  |  [JavaScript SDK リファレンス ドキュメント](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK のインストール手順  |  [インストール手順](https://github.com/Azure/azure-cosmos-js#installation)
|SDK への参加 | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| サンプル | [Node.js コード サンプル](sql-api-nodejs-samples.md)
| 概要チュートリアル | [JavaScript SDK の開始](sql-api-nodejs-get-started.md)
| Web アプリ チュートリアル | [Azure Cosmos DB を使用した Node.js Web アプリケーションの作成](sql-api-nodejs-application.md)
| 現在サポートされているプラットフォーム | [Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK バージョン 2.0.0 以上が必要です。<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>リリース ノート

### <a name="2.0.5"/>2.0.5</a>
* エージェントの種類ノードのインターフェイスを追加しました。 Typescript ユーザーは @types/node を依存関係としてインストールする必要がなくなりました
* 優先する場所が適切に処理されるようになりました
* 開発者向けドキュメントの投稿に関する機能強化
* さまざまな入力ミスの修正

### <a name="2.0.4"/>2.0.4</a>
* 2.0.3 で発生した型定義の問題を修正しました

### <a name="2.0.3"/>2.0.3</a>
* `big-integer` の依存関係を削除しました
* AsyncIterable 型の参照ディレクティブに切り替えました。 Typescript ユーザーは "lib" 設定をカスタマイズする必要がなくなりました。
* 入力ミスを修正しました

### <a name="2.0.2"/>2.0.2</a>
* readme のリンクの修正

### <a name="2.0.1"/>2.0.1</a>
* 再試行インターフェイスの実装を修正しました

### <a name="2.0.0"/>2.0.0</a>
* JavaScript SDK の Version 2.0.0 の GA
* マルチリー ジョンの書き込みのサポートが追加されました。

### <a name="2.0.0-3"/>2.0.0-3</a>
* パブリック プレビュー用の JavaScript SDK のバージョン 2.0.0 の RC1。
* 最上位レベルの CosmosClient とメソッドが関連するデータベース、コンテナー、および項目クラスとの間で分割される、新しいオブジェクト モデル。 
* [promises](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises) をサポートします。 
* TypeScript に変換された SDK。

### <a name="1.14.4"/>1.14.4</a>
* npm のドキュメントを修正しました。

### <a name="1.14.3"/>1.14.3</a>
* 接続に問題が発生したとき、デフォルトで再試行されるようになりました。
* コレクション変更フィードの読み取り対応になりました。
* "読み取りセッションを利用不可" を中断させるセッション整合性バグを修正しました。
* クエリ メトリック対応になりました。
* http エージェントの最大接続数を変更しました。

### <a name="1.14.2"/>1.14.2</a>
* Azure DocumentDB ではなく Azure Cosmos DB を参照するように更新されたドキュメントです。
* ConnectionPolicy での proxyUrl 設定のサポートが追加されました。

### <a name="1.14.1"/>1.14.1</a>
* 大文字と小文字を区別するファイル システムの軽微な修正です。

### <a name="1.14.0"/>1.14.0</a>
* セッションの整合性のサポートが追加されます。
* この SDK バージョンでは、 https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。

### <a name="1.13.0"/>1.13.0</a>
* クロス パーティション クエリを分割検証しました。
* 先頭および末尾のスラッシュを含むリソース リンクのサポート (および対応するテスト) を追加します。

### <a name="1.12.2"/>1.12.2</a>
*   npm のドキュメントを修正しました。

### <a name="1.12.1"/>1.12.1</a>
* ドキュメントに Unicode の特殊文字 (LS、PS) が含まれている場合の executeStoredProcedure のバグを修正しました。
* パーティション キーで Unicode 文字が使用されているドキュメントを処理する際のバグを修正しました。
* 名前メディアでコレクションを作成するためのサポートを修正しました。 GitHub 問題 #114。
* アクセス許可承認トークンのサポートを修正しました。 GitHub 問題 #178。

### <a name="1.12.0"/>1.12.0</a>
* ConsistentPrefix と呼ばれる新しい[一貫性レベル](consistency-levels.md)に対応するようになりました。
* UriFactory のサポートを追加しました。
* Unicode サポートのバグを修正しました。 GitHub 問題 #171。

### <a name="1.11.0"/>1.11.0</a>
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。
* クロス パーティション クエリの並列処理の次数を制御するオプションを追加しました。
* Azure Cosmos DB エミュレーターに対しての実行時に、SSL 検証を無効にするためのオプションを追加しました。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。
* 単一パーティション コレクションに関する継続トークンのバグを修正しました。 GitHub 問題 #107。
* 0 を単一パラメーターとして処理する際の executeStoredProcedure のバグを修正しました。 GitHub 問題 #155。

### <a name="1.10.2"/>1.10.2</a>
* SDK バージョンを含めるようにユーザー エージェント ヘッダーを修正しました。
* コードの軽微なクリーンアップ。

### <a name="1.10.1"/>1.10.1</a>
* SDK を使用して emulator(hostname=localhost) を対象とするときに SSL 検証が無効になります。
* ストアド プロシージャの実行時にスクリプトのログ記録を有効することができるようになりました。

### <a name="1.10.0"/>1.10.0</a>
* クロス パーティションの並列クエリのサポートを追加しました。
* パーティション分割コレクションの TOP/ORDER BY クエリのサポートを追加しました。

### <a name="1.9.0"/>1.9.0</a>
* スロットルされた要求のための再試行ポリシー サポートを追加しました  (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。既定では、Azure Cosmos DB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。 再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。 Azure Cosmos DB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。 この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。
* Cosmos DB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。
* ConnectionPolicy クラスの RetryOptions プロパティを公開する、RetryOptions クラスが追加されました。これは、一部の既定の再試行オプションをオーバーライドするために使用できます。

### <a name="1.8.0"/>1.8.0</a>
* 複数リージョンのデータベース アカウントのサポートを追加しました。

### <a name="1.7.0"/>1.7.0</a>
* ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="1.6.0"/>1.6.0</a>
* [パーティション分割コレクション](partition-data.md)と[ユーザー定義のパフォーマンス レベル](performance-levels.md)を実装しました。

### <a name="1.5.6"/>1.5.6</a>
* 結果の不適切な連結のためにリンクを返していなかった RangePartitionResolver.resolveForRead のバグを修正しました。

### <a name="1.5.5"/>1.5.5</a>
* hashParitionResolver resolveForRead() を修正しました: 登録済みのすべてのリンクの一覧を返す代わりに、指定したパーティション キーが例外をスローしない問題を修正しました。

### <a name="1.5.4"/>1.5.4</a>
* 問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) を修正します - 専用 HTTPS エージェント: Azure Cosmos DB 目的用のグローバル エージェントが変更されないようにします。 lib のすべての要求に対して、専用エージェントを使用します。

### <a name="1.5.3"/>1.5.3</a>
* 問題 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) を修正します - メディア ID のダッシュを正しく処理します。

### <a name="1.5.2"/>1.5.2</a>
* 問題 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) を修正します - EventEmitter リスナー リーク警告の問題を修正します。

### <a name="1.5.1"/>1.5.1</a>
* 問題 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) を修正します - 大文字小文字を区別するシステムのため、Hash フォルダーの名前を hash に変更します。

### <a name="1.5.0"/>1.5.0</a>
* ハッシュと範囲パーティション リゾルバーを追加することでシャーディングのサポートを実装。

### <a name="1.4.0"/>1.4.0</a>
* Upsert を実装します。 documentClient の新しい upsertXXX メソッド

### <a name="1.3.0"/>1.3.0</a>
* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました。

### <a name="1.2.2"/>1.2.2</a>
* Q promises ラッパーを新しいリポジトリに分割します。
* 更新し、npm レジストリのファイルをパッケージ化します。

### <a name="1.2.1"/>1.2.1</a>
* ID ベースのルーティングを実装します。
* 問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) を修正します - 現在のプロパティがメソッド current() と競合します。

### <a name="1.2.0"/>1.2.0</a>
* 地理空間インデックスのサポートを追加しました。
* すべてのリソースの id プロパティを検証します。 リソースの ID には ?、/、#、&#47;&#47; 文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="1.1.0"/>1.1.0</a>
* V2 インデックス作成ポリシーを実装します。

### <a name="1.0.3"/>1.0.3</a>
* 問題 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - core および promise SDK で eslint および grunt 構成を実装しました。

### <a name="1.0.2"/>1.0.2</a>
* 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - promise ラッパーには、エラーのあるヘッダーが含まれません。

### <a name="1.0.1"/>1.0.1</a>
* readConflicts、readConflictAsync、queryConflicts を追加し、競合に関して問い合わせる機能を実装しました。
* API ドキュメントを更新しました。
* 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync エラー。

### <a name="1.0.0"/>1.0.0</a>
* GA SDK。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018 年 8 月 2 日 |--- |
| [1.14.4](#1.14.4) |2018 年 5 月 3 日 |--- |
| [1.14.3](#1.14.3) |2018 年 5 月 3 日 |--- |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |--- |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |--- |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |--- |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |--- |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |--- |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |--- |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |--- |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |--- |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |--- |
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |--- |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |--- |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |--- |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |--- |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |--- |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |--- |
| [1.5.2](#1.5.2) |2016 年 1 月 22 日 |--- |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |--- |
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |--- |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |--- |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |--- |
| [1.2.2](#1.2.2) |2015 年 9 月 10 日 |--- |
| [1.2.1](#1.2.1) |2015 年 8 月 15 日 |--- |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |--- |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |--- |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |--- |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |--- |
| [1.0.1](#1.0.1) |2015 年 5 月 15 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。

