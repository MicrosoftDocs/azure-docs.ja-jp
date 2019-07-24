---
title: Azure Cosmos DB は:SQL .NET API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB .NET SDK の各バージョン間の変更など、SQL .NET API と SDK に関するあらゆる詳細を提供します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 83a866b20d2802b7d49363b7c6451356e938eac1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57838920"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK for SQL API: ダウンロードおよびリリース ノート
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

| |  |
|---|---|
|**SDK のダウンロード**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**サンプル**|[.NET コード サンプル](sql-api-dotnet-samples.md)|
|**作業開始**|[Azure Cosmos DB .NET SDK を開始する](sql-api-get-started.md)|
|**Web アプリ チュートリアル**|[Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application.md)|
|**現在サポートされているフレームワーク**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>リリース ノート

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1 プレビュー
* パブリック プレビュー用の .NET SDK の[バージョン 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) のプレビュー 1。
* 対象は .NET Standard です。.NET Framework 4.6.1 以降および .NET Core 2.0 以降をサポートします。
* 最上位レベルの CosmosClient と関連する CosmosDatabases、CosmosContainers、および CosmosItems の各クラスとの間でメソッドが分割される新しいオブジェクト モデルです。 
* ストリームをサポートします。 
* 状態コードを返し、応答が返されない場合にのみ例外を返すようにサーバーからの CosmosResponseMessage を更新しました。 

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* 診断機能の強化

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* 環境変数設定 "POCOSerializationOnly" を追加しました。

* DocumentDB.Spatial.Sql.dll が削除され、Microsoft.Azure.Documents.ServiceInterop.dll に含まれるようになりました

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* StoredProcedure 実行呼び出しに対するフェールオーバー中の再試行ロジックの改善。

* DocumentClientEventSource シングルトンを作成しました。 

* ConnectionPolicy RequestTimeout を守っていない、GatewayAddressCache タイムアウトを修正しました。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* ダイレクト/TCP トランスポート診断のために、SDK の内部例外の種類である TransportException を追加しました。 例外メッセージ内に存在する場合、この種類は、クライアント接続の問題のトラブルシューティングに関する追加情報を出力します。

* HttpClient 要求を送信するために使用する HTTP ハンドラー スタックである HttpMessageHandler を受け取る新しいコンストラクターのオーバーロードを追加しました (HttpClientHandler など)。

* null 値を持つヘッダーが正しく処理されていなかったバグを修正しました。

* コレクション キャッシュ検証を改善しました。

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security を 4.3.2 に更新しました。

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* 診断トレースの機能強化

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* マルチリージョンの要求の一時的な障害に対する回復力を上げました。

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* マルチリージョンの書き込みがサポートされるようになりました。
* TOP と MaxBufferedItemCount でクロス パーティションのクエリ パフォーマンスが改善されました。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* 要求をキャンセルできるようになりました。
* SetCurrentLocation を ConnectionPolicy に追加しました。これにより、優先される場所がリージョンに基づいて自動的に事前設定されます。
* 個々のパーティション上のいずれのドキュメントにも一致しないフィルターや Min/Max を含むクロス パーティション クエリのバグを修正しました。
* DocumentClient のメソッドが IDocumentClient と等価になりました。
* 確立される接続数を減らすために、ダイレクト TCP トランスポート スタックを更新しました。
* 非 Windows クライアント向けにダイレクト モード TCP のサポートを追加しました。

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 要求をキャンセルできるようになりました。
* SetCurrentLocation を ConnectionPolicy に追加しました。これにより、優先される場所がリージョンに基づいて自動的に事前設定されます。
* 個々のパーティション上のいずれのドキュメントにも一致しないフィルターや Min/Max を含むクロス パーティション クエリのバグを修正しました。

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* DocumentClient のメソッドが IDocumentClient と等価になりました。
* 確立される接続数を減らすために、ダイレクト TCP トランスポート スタックを更新しました。
* 非 Windows クライアント向けにダイレクト モード TCP のサポートを追加しました。

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* ConsistencyLevel Property を FeedOptions に追加しました。
* JsonSerializerSettings を RequestOptions および FeedOptions に追加しました。
* EnableReadRequestsFallback を ConnectionPolicy に追加しました。

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* まれなケースにおけるクロス パーティション ORDER BY クエリの KeyNotFoundException を修正しました。
* LINQ クエリの SELECT 句で JsonProperty 属性が受け入れられないバグを修正しました。

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* 特定の競合状態の下で起こり、セッションの整合性レベルを使うと断続的に "Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token" (入力セッション トークンでは読み取りセッションを使用できません) というエラーが発生するバグを修正しました。

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* FeedOptions.MaxItemCount = -1 によって System.ArithmeticException: page size is negative (ページ サイズがマイナスです) がスローされた回帰が修正されました。
* 新しい ToString() 関数が QueryMetrics に追加されました。
* コレクションの読み取り時のパーティション統計情報が公開されました。
* ChangeFeedOptions に PartitionKey プロパティが追加されました。
* 軽微なバグの修正。

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* DocumentCollection の UniqueKeyPolicy プロパティを使用して、ドキュメントの一意のインデックスを指定する機能を追加します。
* 一部のクエリやストアド プロシージャの実行に対して、カスタム JsonSerializer 設定が受け入れられないバグを修正しました。

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* API リファレンス ドキュメント、アセンブリ内のメタデータ情報、および NuGet パッケージでの Azure DocumentDB から Azure Cosmos DB へのブランド名の変更。 
* 直接接続モードで送信された要求の応答からの診断情報と待ち時間の公開。 プロパティ名は ResourceResponse クラスの RequestDiagnosticsString と RequestLatency です。
* この SDK バージョンでは、 https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Microsoft フレンド アセンブリの内部変更。

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* 信頼性のための修正と機能強化がいくつか追加されました。

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* クエリの結果をパーティション キーの特定の範囲の値にスコープするための FeedOption として PartitionKeyRangeIdresults のサポートが追加されました。 
* 指定時刻以後の変更の検索を開始するための ChangeFeedOption として StartTime のサポートが追加されました。

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* スタック オーバーフローの例外を引き起こす可能性のある JsonSerializable クラスの問題を修正しました。

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   JsonSerializerSettings が DocumentClient コンストラクターの省略可能なパラメーターとして導入されたことでアプリケーションの再コンパイルを余儀なくされる問題を修正しました。
* JsonSerializerSettings パラメーターを渡す際、ConnectionPolicy パラメーターと ConsistencyLevel パラメーターに既定値を使用できるようにするために、最終パラメーターとしての JsonSerializerSettings が必須であった DocumentClient コンストラクターが古い形式としてマークされました。

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) のインスタンス化時にカスタム JsonSerializerSettings を指定するためのサポートが追加されました。

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   SSE4 命令をサポートせず、Azure Cosmos DB SQL クエリの実行時に SEHException をスローする x64 マシンに影響を与える問題を修正しました。

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。
*   個別のパーティションに対するメトリックのクエリに対応するようになりました。
*   クエリの継続トークンのサイズ制限に対応するようになりました。
*   失敗した要求の詳細なトレースに対応するようになりました。
*   SDK でのパフォーマンスが向上しています。

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* 1.13.3 と機能的には同じです。 内部的な変更がいくつか追加されました。

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* 1.13.2 と機能的には同じです。 内部的な変更がいくつか追加されました。

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* 集計クエリの FeedOptions 内に指定された PartitionKey 値が無視される問題を修正しました。
* 中間クロス パーティション Order By クエリの実行時のパーティション管理の透過的な処理の問題を修正しました。

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* いくつかの非同期 API を ASP.NET のコンテキスト内で使用する場合にデッドロックの原因となった問題を修正しました。

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* 特定の条件で自動フェールオーバーするために SDK の耐障害性を改善しました。

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* "リモート名を解決できませんでした" という WebException を時々発生させる問題を修正しました。
* ReadDocumentAsync API に新しいオーバーロードを追加することで、型指定されたドキュメントを直接読み取るサポートを追加しました。

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) の LINQ サポートを追加しました。
* イベント ハンドラーの使用によって発生した ConnectionPolicy オブジェクトのメモリ リークの問題を修正しました。
* ETag を使用した場合に UpsertAttachmentAsync が動作しなかった問題を修正しました。
* 文字列フィールドの並べ替えで、パーティションを横断する order-by クエリの継続が動作しなかった問題を修正しました。

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](how-to-sql-query.md#Aggregates)に関するトピックを参照してください。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* 32 ビット ホスト プロセスでクロス パーティション クエリの一部が失敗する問題を修正しました。
* ゲートウェイ モード中に失敗した要求のトークンでセッション コンテナーが更新されない問題を修正しました。
* プロジェクションでの UDF 呼び出しによるクエリが場合によっては失敗する問題を修正しました。
* クライアント側のパフォーマンスを修正し、要求の読み取りと書き込みのスループットを向上させました。

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* 失敗した要求のトークンでセッション コンテナーが更新されない問題を修正しました。
* 32 ビット ホスト プロセスで動作する SDK のサポートが追加されました。 クロス パーティション クエリを使用する場合は、パフォーマンスを向上させるために 64 ビット ホスト処理をお勧めします。
* IN 式で大量のパーティション キー値を使用するクエリを含むシナリオのパフォーマンスを改善しました。
* PopulateQuotaInfo 要求オプションが設定されている場合、ドキュメント コレクションの読み取り要求の ResourceResponse に、リソース クォータのさまざまな統計情報が含まれるようになりました。

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* 1.11.0 で導入された CreateDocumentCollectionIfNotExistsAsync API のパフォーマンスが少し改善されました。
* 高度な同時要求を伴うシナリオのために、SDK のパフォーマンスが改善されました。

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* コレクションに含まれるドキュメントの [Change Feed](change-feed.md) を処理する新しいクラスと方法をサポートしました。
* クロス パーティション クエリの継続と、クロス パーティション クエリの一部のパフォーマンスの向上をサポートしました。
* CreateDatabaseIfNotExistsAsync と CreateDocumentCollectionIfNotExistsAsync メソッドを追加しました。
* LINQ でシステム関数 IsDefined、IsNull、IsPrimitive がサポートされるようになりました。
* project.json ツールがあるプロジェクトの Nuget パッケージを使用する際の、Microsoft.Azure.Documents.ServiceInterop.dll と DocumentDB.Spatial.Sql.dll アセンブリのアプリケーションのビン フォルダーへの自動 Binplace 処理を修正しました。
* デバッグ シナリオで役に立つクライアント側の ETW トレースの出力をサポートしました。

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* パーティション分割コレクションへの直接接続のサポートを追加しました。
* "Bounded Staleness"一貫性レベルのパフォーマンスが向上しました。
* ジオフェンシング空間クエリに対してコレクションのインデックス作成ポリシーを指定する際の Polygon および LineString データ型を追加しました。
* 述語変換中の StringEnumConverter、IsoDateTimeConverter、および UnixDateTimeConverter に対する LINQ のサポートを追加しました。
* さまざまなバグを修正しました。

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* "The read session is not available for the input session token (入力セッション トークンでは読み取りセッションを使用できません)" という NotFoundException の原因となった問題が修正されました。 地理的に分散したアカウントの読み取り領域にクエリを実行すると、例外が発生する場合があります。
* ResourceResponse クラスで公開されている ResponseStream により、応答から基になるストリームへの直接アクセスが有効になります。

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* ResourceResponse、FeedResponse、StoredProcedureResponse、および MediaResponse クラスが変更され、テスト駆動型デプロイ (TDD) の手本にできるように、対応するパブリック インターフェイスが実装されました。
* カスタム オブジェクトの JsonSerializerSettings をデータのシリアル化に使用するときにパーティション キー ヘッダーの形式が正しくなくなる原因となっていた問題を修正しました。

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* 実行時間の長いクエリが、"Authorization token is not valid at the current time (認証トークンは現在の時刻で無効です)" というエラーのために失敗する原因となっていた問題を修正しました。
* クロス パーティションの TOP クエリおよび ORDER BY クエリから元の SqlParameterCollection を削除する問題が修正されました。

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* パーティション分割コレクションの並列クエリのサポートを追加しました。
* パーティション分割コレクションのクロス パーティションの ORDER BY および TOP クエリに対するサポートを追加しました。
* Azure Cosmos DB NuGet パッケージについての参照で、Azure Cosmos DB プロジェクトを参照するときに必要な DocumentDB.Spatial.Sql.dll と Microsoft.Azure.Documents.ServiceInterop.dll への参照の不足を修正しました。
* LINQ でのユーザー定義関数の使用時に各種パラメーターを使用する機能を修正しました。 
* Upsert コールが書き込み専用ロケーションではなく、読み取り専用ロケーションにダイレクトされる、グローバルにレプリケートされたアカウントのバグを修正しました。
* IDocumentClient インターフェイスに、不足していた次のメソッドを追加しました。 
  * mediaStream とオプションをパラメーターとして受け取る UpsertAttachmentAsync メソッド
  * オプションをパラメーターとして受け取る CreateAttachmentAsync メソッド
  * querySpec をパラメーターとして受け取る CreateOfferQuery メソッド
* IDocumentClient インターフェースで公開されているパブリック クラスのシールを解除しました。

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 複数リージョンのデータベース アカウントのサポートを追加しました。
* 調整された要求での再試行のサポートを追加しました。  ユーザーは、ConnectionPolicy.RetryOptions プロパティを構成することで、再試行の回数と最大待機時間をカスタマイズできます。
* すべての DocumentClient プロパティおよびメソッドの署名を定義できる新しい IDocumentClient インターフェイスを追加しました。  また、この変更の一環として、IQueryable と IOrderedQueryable を作成する拡張メソッドを DocumentClient クラス自体のメソッドに変更しました。
* 特定の Azure Cosmos DB エンドポイント URI の ServicePoint.ConnectionLimit を設定する構成オプションを追加しました。  ConnectionPolicy.MaxConnectionLimit を使用して既定値 (50) を変更できます。
* IPartitionResolver とその実装が非推奨になりました。  IPartitionResolver のサポートは廃止されました。 大量のストレージとスループットを必要とする場合は、パーティション分割コレクションの使用をお勧めします。

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* RequestOptions をパラメーターとして受け取る Uri ベースの ExecuteStoredProcedureAsync メソッドにオーバーロードを追加しました。

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* ドキュメントの有効期限 (TTL) サポートを追加しました。

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* .NET SDK を Azure クラウド サービス ソリューションの一部としてパッケージ化するための Nuget パッケージ化のバグを修正しました。

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* [パーティション分割コレクション](partition-data.md)と[ユーザー定義のパフォーマンス レベル](performance-levels.md)を実装しました。 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[修正済み]** Azure Cosmos DB エンドポイントのクエリを実行すると、"System.Net.Http.HttpRequestException: コンテンツをストリームにコピーしている間にエラーが発生しました" というエラーがスローされます。

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* ページング、条件式、および範囲比較用の新しい演算子を使用できるように LINQ を拡張しました。
  * LINQ で SELECT TOP 動作を有効にする Take 演算子
  * 文字列の範囲の比較を有効にする CompareTo 演算子
  * 条件付き (?) および合体演算子 (??)
* **[修正済み]** LINQ クエリでモデル プロジェクションを Where-In と組み合わせると ArgumentOutOfRangeException が発生します。 [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[修正済み]** Select が最後の式ではない場合、LINQ プロバイダーはプロジェクションがないものと想定し、SELECT * を不適切に生成しました。  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* 実装された Upsert、追加された UpsertXXXAsync メソッド
* すべての要求のパフォーマンス改善
* LINQ プロバイダーによる文字列の条件、結合、CompareTo メソッドのサポート
* **[修正済み]** LINQ プロバイダー --> List に Contains メソッドを実行し、IEnumerable や Arraymethod の場合と同じ SQL を生成
* **[修正済み]** BackoffRetryUtility は、再試行時に新規作成する代わりに、同じ HttpRequestMessage をもう一度使用します
* **[廃止]** UriFactory.CreateCollection --> 今後は UriFactory.CreateDocumentCollection を使用する必要があります

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[修正済み]** nl-NL など、非英語文化情報の使用時のローカリゼーション問題 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* ID ベースのルーティングを追加
  * ID ベースのリソース リンクの作成を支援する新しい UriFactory ヘルパー
  * URI に取り入れる DocumentClient の新しいオーバーロード
* 地理空間の LINQ で IsValid() と IsValidDetailed() を追加
* LINQ プロバイダー サポートの機能強化:
  * **数値演算** - Abs、Acos、Asin、Atan、Ceiling、Cos、Exp、Floor、Log、Log10、Pow、Round、Sign、Sin、Sqrt、Tan、Truncate
  * **文字列** - Concat、Contains、EndsWith、IndexOf、Count、ToLower、TrimStart、Replace、Reverse、TrimEnd、StartsWith、SubString、ToUpper
  * **配列** - Concat、Contains、Count
  * **IN** 演算子

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* インデックス作成ポリシーを変更するためのサポートを追加しました。
  * DocumentClient の新しい ReplaceDocumentCollectionAsync メソッド
  * インデックス ポリシー変更の進捗状況 (%) を追跡するための ResourceResponse<T> の新しい IndexTransformationProgress プロパティ
  * DocumentCollection.IndexingPolicy は変化可能になりました
* 空間インデックス作成とクエリのサポートを追加しました。
  * 点や多角形など、空間型のシリアル化/逆シリアル化するための新しい Microsoft.Azure.Documents.Spatial 名前空間
  * Cosmos DB に格納されている GeoJSON データにインデックスを作成するための新しい SpatialIndex クラス
* **[修正済み]** 間違った SQL クエリが LINQ 式から生成される [#38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Newtonsoft.Json v5.0.7 の依存関係を追加しました。
* Order By 対応になりました。
  
  * LINQ プロバイダーによる OrderBy() または OrderByDescending() のサポート
  * Order By をサポートするための IndexingPolicy 
    
    **考えられる重大な変更** 
    
    カスタム インデックス作成ポリシーを使用してコレクションをプロビジョニングする既存のコードがある場合、新しい IndexingPolicy クラスをサポートするために既存のコードを更新する必要があります。 カスタム インデックス作成ポリシーがない場合は、この変更による影響はありません。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 新しい HashPartitionResolver クラス、RangePartitionResolver クラス、IPartitionResolver を使用したデータのパーティショニングのサポートを追加しました。
* DataContract シリアル化を追加しました。
* LINQ プロバイダーの GUID サポートを追加しました。
* LINQ の UDF サポートを追加しました。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了になった SDK を使用した Azure Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.2.3](#2.2.3) |2019 年 2 月 11 日 |--- |
| [2.2.2](#2.2.2) |2019 年 2 月 5 日 |--- |
| [2.2.1](#2.2.1) |2018 年 12 月 24 日 |--- |
| [2.2.0](#2.2.0) |2018 年 12 月 7 日 |--- |
| [2.1.3](#2.1.3) |2018 年 10 月 15 日 |--- |
| [2.1.2](#2.1.2) |2018 年 10 月 4 日 |--- |
| [2.1.1](#2.1.1) |2018 年 9 月 27 日 |--- |
| [2.1.0](#2.1.0) |2018 年 9 月 21 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 7 日 |--- |
| [1.22.0](#1.22.0) |2018 年 4 月 19 日 |--- |
| [1.21.1](#1.20.1) |2018 年 3 月 9 日 |--- |
| [1.20.2](#1.20.1) |2018 年 2 月 21 日 |--- |
| [1.20.1](#1.20.1) |2018 年 2 月 5 日 |--- |
| [1.19.1](#1.19.1) |2017 年 11 月 16 日 |--- |
| [1.19.0](#1.19.0) |2017 年 11 月 10 日 |--- |
| [1.18.1](#1.18.1) |2017 年 11 月 7 日 |--- |
| [1.18.0](#1.18.0) |2017 年 10 月 17 日 |--- |
| [1.17.0](#1.17.0) |2017 年 8 月 10 日 |--- |
| [1.16.1](#1.16.1) |2017 年 8 月 7 日 |--- |
| [1.16.0](#1.16.0) |2017 年 8 月 2 日 |--- |
| [1.15.0](#1.15.0) |2017 年 6 月 30 日 |--- |
| [1.14.1](#1.14.1) |2017 年 5 月 23 日 |--- |
| [1.14.0](#1.14.0) |2017 年 5 月 10 日 |--- |
| [1.13.4](#1.13.4) |2017 年 5 月 9 日 |--- |
| [1.13.3](#1.13.3) |2017 年 5 月 6 日 |--- |
| [1.13.2](#1.13.2) |2017 年 4 月 19 日 |--- |
| [1.13.1](#1.13.1) |2017 年 3 月 29 日 |--- |
| [1.13.0](#1.13.0) |2017 年 3 月 24 日 |--- |
| [1.12.2](#1.12.2) |2017 年 3 月 20 日 |--- |
| [1.12.1](#1.12.1) |2017 年 3 月 14 日 |--- |
| [1.12.0](#1.12.0) |2017 年 2 月 15 日 |--- |
| [1.11.4](#1.11.4) |2017 年 2 月 6 日 |--- |
| [1.11.3](#1.11.3) |2017 年 1 月 26 日 |--- |
| [1.11.1](#1.11.1) |2016 年 12 月 21 日 |--- |
| [1.11.0](#1.11.0) |2016 年 12 月 08 日 |--- |
| [1.10.0](#1.10.0) |2016 年 9 月 27 日 |--- |
| [1.9.5](#1.9.5) |2016 年 9 月 1 日 |--- |
| [1.9.4](#1.9.4) |2016 年 8 月 24 日 |--- |
| [1.9.3](#1.9.3) |2016 年 8 月 15 日 |--- |
| [1.9.2](#1.9.2) |2016 年 7 月 23 日 |--- |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |--- |
| [1.7.1](#1.7.1) |2016 年 5 月 6 日 |--- |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |--- |
| [1.6.3](#1.6.3) |2016 年 4 月 8 日 |--- |
| [1.6.2](#1.6.2) |2016 年 3 月 29 日 |--- |
| [1.5.3](#1.5.3) |2016 年 2 月 19 日 |--- |
| [1.5.2](#1.5.2) |2015 年 12 月 14 日 |--- |
| [1.5.1](#1.5.1) |2015 年 11 月 23 日 |--- |
| [1.5.0](#1.5.0) |2015 年 10 月 5 日 |--- |
| [1.4.1](#1.4.1) |2015 年 8 月 25 日 |--- |
| [1.4.0](#1.4.0) |2015 年 8 月 13 日 |--- |
| [1.3.0](#1.3.0) |2015 年 8 月 5 日 |--- |
| [1.2.0](#1.2.0) |2015 年 7 月 6 日 |--- |
| [1.1.0](#1.1.0) |2015 年 4 月 30 日 |--- |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。 

