---
title: Azure Cosmos DB:SQL .NET Core API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB .NET Core SDK の各バージョン間の変更など、SQL .NET Core API と SDK に関するあらゆる詳細を提供します。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: c39db870e44d4e810817b70e2793b8805088180e
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932545"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用の Azure Cosmos DB .NET Core SDK:リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk executor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK のダウンロード**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API ドキュメント**|[.NET API リファレンス ドキュメント](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**サンプル**|[.NET コード サンプル](sql-api-dotnet-samples.md)|
|**作業開始**|[Azure Cosmos DB .NET を開始する](sql-api-sdk-dotnet.md)|
|**Web アプリ チュートリアル**|[Azure Cosmos DB を使用した Web アプリケーションの開発](sql-api-dotnet-application.md)|
|**現在サポートされているフレームワーク**|[.NET Standard 1.6 および .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>リリース ノート

> [!NOTE]
> .NET Core を使用している場合は、.NET Standard をターゲットとする [.NET SDK](sql-api-sdk-dotnet-standard.md) の最新バージョン 3.x を参照してください。 

### <a name="a-name260260"></a><a name="2.6.0"/>2.6.0

* ConnectionPolicy に PortReusePolicy を追加しました
* UWP アプリで SDK が使用されるときの ntdll!RtlGetVersion TypeLoadException の問題を修正しました

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* SDK の System.Net.Http バージョンが、NuGet パッケージで定義されているものと一致するようになりました。
* 元の書き込み要求が失敗した場合に別のリージョンにフォールバックすることが許可されます。
* 書き込み要求のセッションの再試行ポリシーが追加されます。

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* 空のページの原因となったクエリの競合状態のトレースを修正します

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* LINQ クエリの小数点以下有効桁数のサイズが増えました。
* 新しいクラス CompositePath、CompositePathSortOrder、SpatialSpec、SpatialType、PartitionKeyDefinitionVersion が追加されました
* DocumentCollection に TimeToLivePropertyPath が追加されました
* IndexPolicy に CompositeIndexes と SpatialIndexes が追加されました
* PartitionKeyDefinition に Version が追加されました
* PartitionKey に None が追加されました

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * IdleTcpConnectionTimeout、OpenTcpConnectionTimeout、MaxRequestsPerTcpConnection、MaxTcpConnectionsPerEndpoint が、ConnectionPolicy に追加されました。
 
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

* 診断トレーシング機能を強化しました。

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

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* ConsistencyLevel Property を FeedOptions に追加しました。
* JsonSerializerSettings を RequestOptions および FeedOptions に追加しました。
* EnableReadRequestsFallback を ConnectionPolicy に追加しました。

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* まれなケースにおけるクロス パーティション ORDER BY クエリの KeyNotFoundException を修正しました。
* LINQ クエリの SELECT 句で JsonProperty 属性が受け入れられないバグを修正しました。

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* 特定の競合状態の下で起こり、セッションの整合性レベルを使うと断続的に "Microsoft.Azure.Documents.NotFoundException: The read session is not available for the input session token" (入力セッション トークンでは読み取りセッションを使用できません) というエラーが発生するバグを修正しました。

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* FeedOptions.MaxItemCount = -1 によって System.ArithmeticException: page size is negative (ページ サイズがマイナスです) がスローされた回帰が修正されました。
* 新しい ToString() 関数が QueryMetrics に追加されました。
* コレクションの読み取り時のパーティション統計情報が公開されました。
* ChangeFeedOptions に PartitionKey プロパティが追加されました。
* 軽微なバグの修正。

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * DocumentCollection の UniqueKeyPolicy プロパティを使用して、ドキュメントの一意なインデックスを指定する機能を追加します。
 * 一部のクエリやストアド プロシージャの実行に対して、カスタム JsonSerializer 設定が受け入れられないバグを修正しました。

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * API リファレンス ドキュメント、アセンブリ内のメタデータ情報、および NuGet パッケージでの Azure DocumentDB から Azure Cosmos DB へのブランド名の変更。
 * 直接接続モードで送信された要求の応答からの診断情報と待ち時間の公開。 プロパティ名は ResourceResponse クラスの RequestDiagnosticsString と RequestLatency です。
 * この SDK バージョンでは、 https://aka.ms/cosmosdb-emulator からダウンロードできる Azure Cosmos DB エミュレーターの最新バージョンが必要です。
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* 信頼性のための修正と機能強化がいくつか追加されました。

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet) のサポートに関連する内部変更

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* クエリの結果をパーティション キーの特定の範囲の値にスコープするための FeedOption として PartitionKeyRangeIdresults のサポートが追加されました。
* 指定時刻以後の変更の検索を開始するための ChangeFeedOption として StartTime のサポートが追加されました。

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   スタック オーバーフローの例外を引き起こす可能性のある JsonSerializable クラスの問題を修正しました。

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) のインスタンス化時にカスタム JsonSerializerSettings を指定するためのサポートが追加されました。

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   ターゲット フレームワークの 1 つとして .NET Standard 1.5 をサポートします。

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   SSE4 命令をサポートせず、Azure Cosmos DB クエリの実行時に SEHException をスローする x64 マシンに影響を与える問題を修正しました。

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   ConsistentPrefix と呼ばれている新しい一貫性レベルに対応するようになりました。
*   個別のパーティションに対するメトリックのクエリに対応するようになりました。
*   クエリの継続トークンのサイズ制限に対応するようになりました。
*   失敗した要求の詳細なトレースに対応するようになりました。
*   SDK でのパフォーマンスが向上しています。

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* 集計クエリの FeedOptions 内に指定された PartitionKey 値が無視される問題を修正しました。
* 中間クロス パーティション Order By クエリの実行時のパーティション管理の透過的な処理の問題を修正しました。

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* いくつかの非同期 API を ASP.NET のコンテキスト内で使用する場合にデッドロックの原因となった問題を修正しました。

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* 特定の条件で自動フェールオーバーするために SDK の耐障害性を改善しました。

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* "リモート名を解決できませんでした" という WebException を時々発生させる問題を修正しました。
* ReadDocumentAsync API に新しいオーバーロードを追加することで、型指定されたドキュメントを直接読み取るサポートを追加しました。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) の LINQ サポートを追加しました。
* イベント ハンドラーの使用によって発生した ConnectionPolicy オブジェクトのメモリ リークの問題を修正しました。
* ETag を使用した場合に UpsertAttachmentAsync が動作しなかった問題を修正しました。
* 文字列フィールドの並べ替えで、パーティションを横断する order-by クエリの継続が動作しなかった問題を修正しました。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* 集計クエリ (COUNT、MIN、MAX、SUM、および AVG) のサポートを追加しました。 [集計のサポート](sql-query-aggregates.md)に関するトピックを参照してください。
* パーティション分割コレクションの最小スループットが 10,100 RU/秒から 2,500 RU/秒になりました。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。 Azure Cosmos DB .NET Core SDK の最新リリースは、[Xamarin](https://www.xamarin.com) との完全な互換性を持ち、iOS、Android、Mono (Linux) を対象とするアプリケーションの構築に使用されます。  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview

Azure Cosmos DB .NET Core Preview SDK を使用すると、Windows、Mac、Linux で実行できるクロスプラットフォーム対応の高速な [ASP.NET Core](https://www.asp.net/core) アプリと [.NET Core](https://www.microsoft.com/net/core#windows) アプリを構築できるようになります。

Azure Cosmos DB .NET Core Preview SDK には最新バージョンの [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) と同等の機能が備わっており、次の機能がサポートされます。
* すべての[接続モード](performance-tips.md#networking):ゲートウェイ モード、ダイレクト TCP、ダイレクト HTTP。
* すべての[一貫性レベル](consistency-levels.md):強固、セッション、有界整合性制約、最終的。
* [パーティション分割コレクション](partition-data.md)。
* [複数リージョンのデータベース アカウントと geo レプリケーション](distribute-data-globally.md)。

この SDK に関する質問がある場合は、[StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb) に投稿するか、[GitHub リポジトリ](https://github.com/Azure/azure-documentdb-dotnet/issues)に問題を報告してください。

## <a name="release--retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月**前に通知します。

新しい機能と最適化は現在の SDK にのみ追加されます。そのため、常に可能な限り最新の SDK バージョンにアップグレードすることが推奨されます。 

提供終了になった SDK を使用した Azure Cosmos DB への要求は、サービスによって拒否されます。

> [!WARNING]
> .NET Core SDK for SQL API の **1.x** バージョンはすべて、**2020 年 8 月 30 日**に廃止されます。
> 
>
<br/>


| Version | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.6.0](#2.6.0) |2019 年 8 月 30 日 |--- |
| [2.5.1](#2.5.1) |2019 年 7 月 2 日 |--- |
| [2.4.1](#2.4.1) |2019 年 6 月 20 日 |--- |
| [2.4.0](#2.4.0) |2019 年 5 月 5 日 |--- |
| [2.3.0](#2.3.0) |2019 年 4 月 4 日 |--- |
| [2.2.3](#2.2.3) |2019 年 3 月 11 日 |--- |
| [2.2.2](#2.2.2) |2019 年 2 月 6 日 |--- |
| [2.2.1](#2.2.1) |2018 年 12 月 24 日 |--- |
| [2.2.0](#2.2.0) |2018 年 12 月 7 日 |--- |
| [2.1.3](#2.1.3) |2018 年 10 月 15 日 |--- |
| [2.1.2](#2.1.2) |2018 年 10 月 4 日 |--- |
| [2.1.1](#2.1.1) |2018 年 9 月 27 日 |--- |
| [2.1.0](#2.1.0) |2018 年 9 月 21 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 7 日 |--- |
| [1.9.1](#1.9.1) |2018 年 3 月 9 日 |2020 年 8 月 30 日 |
| [1.8.2](#1.8.2) |2018 年 2 月 21 日 |2020 年 8 月 30 日 |
| [1.8.1](#1.8.1) |2018 年 2 月 5 日 |2020 年 8 月 30 日 |
| [1.7.1](#1.7.1) |2017 年 11 月 16 日 |2020 年 8 月 30 日 |
| [1.7.0](#1.7.0) |2017 年 11 月 10 日 |2020 年 8 月 30 日 |
| [1.6.0](#1.6.0) |2017 年 10 月 17 日 |2020 年 8 月 30 日 |
| [1.5.1](#1.5.1) |2017 年 10 月 02 日 |2020 年 8 月 30 日 |
| [1.5.0](#1.5.0) |2017 年 8 月 10 日 |2020 年 8 月 30 日 | 
| [1.4.1](#1.4.1) |2017 年 8 月 7 日 |2020 年 8 月 30 日 |
| [1.4.0](#1.4.0) |2017 年 8 月 2 日 |2020 年 8 月 30 日 |
| [1.3.2](#1.3.2) |2017 年 6 月 12 日 |2020 年 8 月 30 日 |
| [1.3.1](#1.3.1) |2017 年 5 月 23 日 |2020 年 8 月 30 日 |
| [1.3.0](#1.3.0) |2017 年 5 月 10 日 |2020 年 8 月 30 日 |
| [1.2.2](#1.2.2) |2017 年 4 月 19 日 |2020 年 8 月 30 日 |
| [1.2.1](#1.2.1) |2017 年 3 月 29 日 |2020 年 8 月 30 日 |
| [1.2.0](#1.2.0) |2017 年 3 月 25 日 |2020 年 8 月 30 日 |
| [1.1.2](#1.1.2) |2017 年 3 月 20 日 |2020 年 8 月 30 日 |
| [1.1.1](#1.1.1) |2017 年 3 月 14 日 |2020 年 8 月 30 日 |
| [1.1.0](#1.1.0) |2017 年 2 月 16 日 |2020 年 8 月 30 日 |
| [1.0.0](#1.0.0) |2016 年 12 月 21 日 |2020 年 8 月 30 日 |
| [0.1.0-preview](#0.1.0-preview) |2016 年 11 月 15 日 |2016 年 12 月 31 日 |

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。

