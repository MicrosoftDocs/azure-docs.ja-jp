---
title: Azure Cosmos DB SQL Python API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB Python SDK の各バージョン間の変更など、SQL Python API と SDK に関するあらゆる詳細を提供します。
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: e9f9daea2c0d570efb81603784ee730b11668426
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585986"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用の Azure Cosmos DB Python SDK:リリース ノートとリソース

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
> * [バルク エグゼキューター - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK のダウンロード**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API ドキュメント**|[Python の API リファレンス ドキュメント](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK のインストール手順**|[Python SDK のインストール手順](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**開始するには**|[Python SDK の開始](create-sql-api-python.md)|
|**現在サポートされているプラットフォーム**|[Python 2.7](https://www.python.org/downloads/) と [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>リリース履歴

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10)

- "遅延" インデックス作成モードの非推奨の警告を追加しました。 バックエンドでは、このモードでコンテナーを作成できなくなり、代わりに一貫性に設定されます。

**新機能**
- 新しいコンテナーを作成するときに、分析ストレージの TTL を設定する機能が追加されました。

**バグの修正**
- Get_client API の入力としての dicts のサポートが修正されました。
- クエリ反復子での Python 2/3 の互換性の問題が修正されました。
- 型のヒント エラーが修正されました (問題 #12570)。
- オプション ヘッダーが upsert_item 関数に追加されなかったバグが修正されました。 問題 #11791 - @aalapatirvbd さん、ご協力ありがとうございました。
- 文字列以外の ID が項目で使用されている場合に発生するエラーが修正されました。 AttributeError ではなく TypeError (問題 #11793) が発生するようになりました。

### <a name="400"></a>4.0.0

* 安定版リリース。
* HttpLoggingPolicy をパイプラインに追加し、要求ヘッダーと応答ヘッダーのカスタム ロガーを渡すことができるようになりました。

### <a name="400b6"></a>4.0.0b6

* メディア API の synchronized_request のバグを修正しました。
* メディア要求はサポートされていないため、ConnectionPolicy から MediaReadMode と MediaRequestTimeout が削除されました。

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors モジュールが非推奨になり、azure.cosmos.exceptions に置き換えられました。
* アクセス条件パラメーター (`access_condition`、`if_match`、`if_none_match`) は非推奨になり、個別の `match_condition` および `etag` パラメーターが優先されるようになりました。
* ルーティング マップ プロバイダーのバグを修正しました。
* クエリの Distinct、Offset、および Limit のサポートを追加しました。
* 既定のドキュメント クエリの実行コンテキストが、以下に対して使用されるようになりました

  * ChangeFeed クエリ
  * 単一パーティション クエリ (partitionkey、partitionKeyRangeId はオプションに存在します)
  * ドキュメント以外のクエリ

* [enable cross partition query]\(クロス パーティション クエリを有効にする\) が true に設定され、"value" キーワードが存在しない場合、複数のパーティションで集約のエラーが発生します。
* クエリ プランをフェッチする他のシナリオ用のクエリ プラン エンドポイントをヒットします。
* Cosmos エンティティ オブジェクトの `__repr__` サポートを追加しました。
* ドキュメントを更新しました。

### <a name="400b4"></a>4.0.0b4

* すべての操作に `timeout` キーワード引数のサポートが追加され、操作を完了する必要がある期間を示す絶対タイムアウト (秒) を指定できるようになりました。 タイムアウト値を超えた場合は、`azure.cosmos.errors.CosmosClientTimeoutError` が発生します。

* HTTP 接続エラー時の再試行動作を管理するための新しい `ConnectionRetryPolicy` を追加しました。

* 新しいコンストラクターおよび操作ごとの構成のキーワード引数を次のとおり追加しました。

  * `retry_total` - 最大再試行回数。
  * `retry_backoff_max` - 再試行の最大待機時間 (秒)。
  * `retry_fixed_interval` - 固定の再試行間隔 (ミリ秒)。
  * `retry_read` - ソケット読み取りの最大再試行回数。
  * `retry_connect` - 接続エラー時の最大再試行回数。
  * `retry_status` - エラー状態コードに対する最大再試行回数。
  * `retry_on_status_codes` - 再試行対象の特定の状態コードのリスト。
  * `retry_backoff_factor` - 再試行間の待機時間を計算するための係数。

### <a name="400b3"></a>4.0.0b3

* `create_database_if_not_exists()` と `create_container_if_not_exists` の機能をそれぞれ CosmosClient と Database に追加しました。

### <a name="400b2"></a>4.0.0b2

* バージョン 4.0.0 b2 は、Python 言語のベスト プラクティスに適合したクライアント ライブラリを構築するための取り組みにおける 2 番目のイテレーションです。

**重大な変更**

* クライアント接続は、`azure.core.pipeline` で定義されている HTTP パイプラインを使用するように調整されました。

* 対話型オブジェクトの名前がプロキシに変更されました。 これには次のものが含まれます

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* `CosmosClient` のコンストラクターが次のように更新されました。

  * `auth` パラメーターの名前が `credential` に変更され、認証の種類を直接取得するようになりました。 これにより、マスター キーの値、リソース トークンのディクショナリ、またはアクセス許可のリストを渡すことができます。 ただし、以前のディクショナリ形式は引き続きサポートされます。

  * `connection_policy` パラメーターはキーワードのみのパラメーターになりました。これは引き続きサポートされていますが、ポリシーの個々の各属性を次のような明示的なキーワード引数として渡すことができるようになりました。

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* 新しいコンストラクターが `CosmosClient` に追加され、Azure portal から取得した接続文字列を使用した作成が可能になりました。

* 次のように、いくつかの `read_all` 操作が `list` 操作に名前変更されました。

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* `request_options` または `feed_options` パラメーターを取るすべての操作は、キーワードのみのパラメーターに移行されました。 また、これらのオプションのディクショナリは引き続きサポートされますが、ディクショナリ内の個々の各オプションは、明示的なキーワード引数としてサポートされるようになりました。

* エラー階層は `azure.core.AzureError` から継承されるようになりました。

  * `HTTPFailure` の名前が `CosmosHttpResponseError` に変更されました
  * `JSONParseFailure` が削除され、`azure.core.DecodeError` に置き換えられました
  * 特定の応答コードに関する以下のエラーを追加しました。
    * `CosmosResourceNotFoundError`: 404 状態の場合
    * `CosmosResourceExistsError`: 409 状態の場合
    * `CosmosAccessConditionFailedError`: 412 状態の場合

* `CosmosClient` をコンテキスト マネージャーで実行して、クライアント接続のクローズ処理を実行できるようになりました。

* 反復可能な応答 (クエリ応答やリスト応答など) が `azure.core.paging.ItemPaged` 型になりました。 メソッド `fetch_next_block` は、`by_page` メソッドによってアクセスされる 2 番目の反復子に置き換えられました。

### <a name="400b1"></a>4.0.0b1

バージョン 4.0.0 b1 は、Python 言語のベスト プラクティスに適合した、ユーザー フレンドリなクライアント ライブラリを作成するための取り組みの最初のプレビューです。 これについての詳細、および他の Azure SDK ライブラリのプレビュー リリースの詳細については、 https://aka.ms/azure-sdk-preview1-python を参照してください。

**破壊的変更:新しい API 設計**

* 操作が特定のクライアントにスコープ設定されるようになりました。

  * `CosmosClient`:このクライアントは、アカウント レベルの操作を処理します。 これには、サービス プロパティの管理や、アカウント内のデータベースの一覧表示などが含まれます。
  * `Database`:このクライアントは、データベース レベルの操作を処理します。 これには、コンテナー、ユーザー、ストアド プロシージャの作成と削除が含まれます。 これには、CosmosClient のインスタンスから名前でアクセスできます。
  * `Container`:このクライアントは、特定のコンテナーの操作を処理します。 これには、アイテムのクエリと挿入、およびプロパティの管理が含まれます。
  * `User`:このクライアントは、特定のユーザーの操作を処理します。 これには、アクセス許可の追加と削除、およびユーザー プロパティの管理が含まれます。

    これらのクライアントにアクセスするには、`get_<child>_client` メソッドを使用してクライアント階層を下に移動します。 新しい API の詳細については、[リファレンス ドキュメント](https://aka.ms/azsdk-python-cosmos-ref)を参照してください。

* クライアントは、ID ではなく名前でアクセスされます。 リンクを作成するために文字列を連結する必要はありません。

* 個々のモジュールから型とメソッドをインポートする必要はなくなりました。 パブリック API アクセス領域を `azure.cosmos` パッケージで直接使用できます。

* 個別の `RequestOptions` インスタンスを構築せずに、個々の要求プロパティをキーワード引数として指定できます。

### <a name="302"></a>3.0.2

* MultiPolygon データ型のサポートを追加しました
* セッション読み取り再試行ポリシーでのバグ修正
* ベース 64 文字列のデコード中における不適切なパディングの問題のバグ修正

### <a name="301"></a>3.0.1

* LocationCache のバグ修正
* エンドポイントの再試行ロジックのバグ修正
* ドキュメントを修正しました

### <a name="300"></a>3.0.0

* マルチリージョンの書き込みのサポートを追加しました
* 名前の変更
  * DocumentClient から CosmosClient へ
  * コレクションからコンテナーへ
  * ドキュメントからアイテムへ
  * パッケージ名が "azure-cosmos" に更新されました
  * 名前空間が "azure.cosmos" に更新されました

### <a name="233"></a>2.3.3

* プロキシのサポートを追加しました
* 変更フィードの読み取りのサポートを追加しました
* コレクション クォータ ヘッダーのサポートを追加しました
* 大規模なセッション トークンの問題のバグ修正
* ReadMedia API のバグ修正
* パーティション キー範囲のキャッシュのバグ修正

### <a name="232"></a>2.3.2

* 接続に問題が発生したとき、デフォルトで再試行されるようになりました。

### <a name="231"></a>2.3.1

* Azure DocumentDB ではなく Azure Cosmos DB を参照するように更新されたドキュメントです。

### <a name="230"></a>2.3.0

* この SDK バージョンでは、 https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。

### <a name="221"></a>2.2.1

* 集計辞書のバグ修正
* リソース リンクのスラッシュのトリミングのバグ修正
* Unicode エンコーディングのテスト

### <a name="220"></a>2.2.0

* 1 分あたりの要求ユニット (RU/分) 機能のサポートが追加されました。
* ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。

### <a name="210"></a>2.1.0

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。
* DocumentDB Emulator に対しての実行時に、SSL 検証を無効にするためのオプションを追加しました。
* 依存する要求モジュールが必ず 2.10.0 でなければならないという制限をなくしました。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。
* ストアド プロシージャの実行時にスクリプトのログ記録を有効することができるようになりました。
* REST API バージョンが、このリリースで "2017-01-19" に上がりました。

### <a name="201"></a>2.0.1

* ドキュメントのコメントを編集しました。

### <a name="200"></a>2.0.0

* Python 3.5 のサポートを追加しました。
* 要求モジュールを使用する接続プールのサポートを追加しました。
* Session 一貫性のサポートを追加しました。
* パーティション分割コレクションの TOP/ORDERBY クエリのサポートを追加しました。

### <a name="190"></a>1.9.0

* スロットルされた要求のための再試行ポリシー サポートを追加しました (スロットルされた要求は、要求レートが大きすぎるという例外をエラー コード 429 で受信します)。既定では、DocumentDB は、エラー コード 429 が発生した場合に、応答ヘッダーの retryAfter 回数に従って要求ごとに 9 回再試行します。
  再試行の間にサーバーによって返される retryAfter 回数を無視する場合、固定の再試行間隔の回数を、ConnectionPolicy オブジェクトの RetryOptions プロパティの一部としてここで設定できます。
  DocumentDB では、(再試行の回数に関係なく) スロットルされる要求ごとに最大 30 秒待機できるようになり、エラー コード 429 と共に応答を返すようになりました。
  この回数は、ConnectionPolicy オブジェクトの RetryOptions プロパティでオーバーライドすることもできます。

* DocumentDB は、スロットルの再試行の回数と再試行の間の要求の累積待機時間を表すために、すべての要求の応答ヘッダーとして x-ms-throttle-retry-count と x-ms-throttle-retry-wait-time-ms を返すようになりました。

* document_client クラスで公開されている RetryPolicy クラスとそれに対応するプロパティ (retry_policy) が削除され、代わりに、既定の一部の再試行オプションをオーバーライドするために使用できる ConnectionPolicy クラスの RetryOptions プロパティを公開する RetryOptions クラスが導入されました。

### <a name="180"></a>1.8.0

* geo レプリケートされたデータベース アカウントのサポートを追加しました。
* グローバル ホストと masterKey を個々のテスト クラスに移動するテストの修正。

### <a name="170"></a>1.7.0

* ドキュメントの有効期限 (TTL) 機能のサポートを追加しました。

### <a name="161"></a>1.6.1

* パーティション キーのパスでの特殊文字の使用を許可するためのサーバー側のパーティション分割に関連するバグの修正。

### <a name="160"></a>1.6.0

* サーバー側のパーティション分割コレクション機能のサポートを追加しました。

### <a name="150"></a>1.5.0

* クライアント側のシャーディング フレームワークを SDK に追加しました。 HashPartionResolver と RangePartitionResolver のクラスを実装しました。

### <a name="142"></a>1.4.2

* Upsert を実装します。 Upsert 機能をサポートするために新しい UpsertXXX メソッドが追加されました。
* ID ベースのルーティングを実装します。 パブリック API の変更なし、すべて内部の変更。

### <a name="130"></a>1.3.0

* 他の SDK とバージョン番号をそろえるため、このリリースはスキップされました

### <a name="120"></a>1.2.0

* 地理空間インデックスをサポートします
* すべてのリソースの ID プロパティを検証します。 リソースの ID には `?, /, #, \\` の文字を使えず、終わりの文字をスペースにできません。
* ResourceResponse に新しいヘッダーの「インデックス変換の進行状況」を追加します。

### <a name="110"></a>1.1.0

* V2 インデックス作成ポリシーを実装する

### <a name="101"></a>1.0.1

* プロキシ接続をサポートします

## <a name="release--retirement-dates"></a>リリース日と提供終了日

Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。 新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。

> [!WARNING]
> 2022 年 8 月 31 日以降、Azure Cosmos DB では、Azure Cosmos DB Python SDK for SQL API バージョン 1.x または 2.x に対するバグ修正、新機能の追加、サポートの提供は行われません。 アップグレードしない場合でも、SDK バージョン 1.x および 2.x から送信される要求は、引き続き Azure Cosmos DB サービスによって処理されます。

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [4.0.0](#400) |2020 年 5 月 20 日 |--- |
| [3.0.2](#302) |2018 年 11 月 15 日 |--- |
| [3.0.1](#301) |2018 年 10 月 4 日 |--- |
| [2.3.3](#233) |2018 年 9 月 8 日 |2020 年 8 月 30 日 |
| [2.3.2](#232) |2018 年 5 月 8 日 |2020 年 8 月 30 日 |
| [2.3.1](#231) |2017 年 12 月 21 日 |2020 年 8 月 30 日 |
| [2.3.0](#230) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [2.2.1](#221) |2017 年 9 月 29 日 |2020 年 8 月 30 日 |
| [2.2.0](#220) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [2.1.0](#210) |2017 年 5 月 1 日 |2020 年 8 月 30 日 |
| [2.0.1](#201) |2016 年 10 月 30 日 |2020 年 8 月 30 日 |
| [2.0.0](#200) |2016 年 9 月 29 日 |2020 年 8 月 30 日 |
| [1.9.0](#190) |2016 年 7 月 7 日 |2020 年 8 月 30 日 |
| [1.8.0](#180) |2016 年 6 月 14 日 |2020 年 8 月 30 日 |
| [1.7.0](#170) |2016 年 4 月 26 日 |2020 年 8 月 30 日 |
| [1.6.1](#161) |2016 年 4 月 8 日 |2020 年 8 月 30 日 |
| [1.6.0](#160) |2016 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.5.0](#150) |2016 年 1 月 3 日 |2020 年 8 月 30 日 |
| [1.4.2](#142) |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| 1.4.1 |2015 年 10 月 6 日 |2020 年 8 月 30 日 |
| [1.2.0](#120) |2015 年 8 月 6 日 |2020 年 8 月 30 日 |
| [1.1.0](#110) |2015 年 7 月 9 日 |2020 年 8 月 30 日 |
| [1.0.1](#101) |2015 年 5 月 25 日 |2020 年 8 月 30 日 |
| 1.0.0 |2015 年 4 月 7 日 |2020 年 8 月 30 日 |
| 0.9.4-prelease |2015 年 1 月 14 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2014 年 12 月 9 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 11 月 25 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 9 月 23 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 8 月 21 日 |2016 年 2 月 29 日 |

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ

Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。 
