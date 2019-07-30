---
title: Azure Cosmos DB:SQL Async Java API、SDK、およびリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL Async Java API と SDK に関するあらゆる詳細を提供します。
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 3cafa4d5aecaa4c8f3863c3269ec02793340e3e6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509265"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API 用 Azure Cosmos DB Async Java SDK:リリース ノートとリソース
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET Change Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST リソース プロバイダー](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Async Java SDK は、[Netty ライブラリ](https://netty.io/)をサポートした非同期操作が可能であるという点で、SQL API Java SDK とは異なります。 既存の [SQL API Java SDK](sql-api-sdk-java.md) では、非同期操作はサポートされません。 

| |  |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API ドキュメント** |[Java API リファレンス ドキュメント](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**SDK への協力** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**作業開始** | [Async Java SDK の開始](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Code sample** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **パフォーマンスに関するヒント**| [GitHub の Readme](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **サポートされている最小ランタイム**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>リリース ノート

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* TCP モードが既定で有効になりました
* クロス パーティションのクエリ メトリックで、すべてのパーティションが返されるようになりました
* グローバルな "厳密" が正しく機能するようになりました
* マルチマスターの場合、クエリのフェールオーバーが正しく再試行されません
* セキュリティ修正プログラムの依存関係のバンプ

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* ハッシュ V2 のサポートのバグ修正

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* client#close() におけるリソース リークのバグ修正 ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88))。

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* クロス パーティション クエリの継続トークンのサポートを追加しました。

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* 直接モードのバグをいくつか修正しました。
* 直接モードでのログ記録を強化しました。
* 接続管理を強化しました。

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* 直接モード接続は、一般提供 (GA) になっています。 直接モード接続を使用するサンプルについては、[azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) GitHub リポジトリをご覧ください。
* QueryMetrics のサポートが追加されました。
* 代わりに java.util.List を受け付けるために重要な順序に合わせて、java.util.Collection を受け付ける API を変更しました。 ConnectionPolicy#getPreferredLocations()、JsonSerialization、および PartitionKey(.) でリストを受け付けるようになりました。

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* 直接モード接続のサポートが追加されました。
* 代わりに java.util.List を受け付けるために重要な順序に合わせて、java.util.Collection を受け付ける API を変更しました。
  ConnectionPolicy#getPreferredLocations()、JsonSerialization、および PartitionKey(.) でリストを受け付けるようになりました。
* ゲートウェイ モードでのドキュメント クエリのセッション バグを修正しました。
* 依存関係をアップグレードしました (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79)、RxJava 1.3.8)。

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* 非常に大規模なクエリ応答の処理を修正しました。
* クライアントをインスタンス化するときのリソース トークン処理を修正しました ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78))。
* 脆弱性のある依存関係 jackson-databind をアップグレードしました ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77))。

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* リソース リークのバグを修正しました。
* MultiPolygon のサポートを追加しました
* RequestOptions でのカスタム ヘッダーのサポートを追加しました。

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* パッケージのバグを修正しました。

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* 書き込み再試行パスで NPE バグを修正しました。
* エンドポイント管理で NPE バグを修正しました。
* 脆弱性のある依存関係をアップグレードしました ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68))。
* Netty ネットワーク ログのトラブルシューティング用のサポートが追加されました。

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* マルチリー ジョンの書き込みがサポートされるようになりました。

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* プロキシのサポートを追加しました。
* リソース トークンの承認のサポートを追加しました。
* 大規模なパーティション キーの処理でバグを修正しました ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63))。
* ドキュメントを改善しました。
* SDK はより詳細なモジュールを再構築しました。

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* 英語以外のロケールのバグを修正しました ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51))。
* 競合リソースにヘルパー メソッドを追加しました。

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* パフォーマンス上の理由とライセンス上の理由により、org.json 依存関係を jackson に置き換えました ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29))。
* 非推奨の OfferV2 クラスを削除しました。
* スループット コンテンツに対応するために Offer クラスにアクセサー メソッドを追加しました。
* org.json 型を返すドキュメント/リソース内のメソッドが jackson オブジェクト型を返すように変更されました。
* JsonSerializable を拡張するクラスの getObject(.) メソッドが jackson ObjectNode 型を返すように変更されました。
* ObjectNode のコレクションを返すように getCollection(.) メソッドが変更されました。
* org.json.JSONObject 引数を持つ、JsonSerializable サブクラスのコンストラクターを削除しました。
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) では、インデントに 2 つのスペースを使用するようになりました。
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* 一意なインデックス ポリシーのサポートが追加されました。
* フィード オプションに、応答継続トークンのサイズを制限するためのサポートが追加されました。
* クロス パーティション クエリに、パーティション分割のサポートが追加されました。
* Json のタイムスタンプ シリアル化のバグを修正しました ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32))。
* Json 列挙型のシリアル化のバグを修正しました。
* サイズが 2 MB のドキュメントを管理する際のバグを修正しました ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33))。
* バグのため、依存関係 com.fasterxml.jackson.core:jackson-databind が 2.9.5 にアップグレードされました ([jackson-databind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* バグのため、rxjava-extras への依存関係が 0.8.0.17 にアップグレードされました ([rxjava-extras: GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30))。
* pom ファイル内のメタデータの記述が、ドキュメントの残りの部分とインラインとなるように更新されました。
* 構文の改善 ([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41))、([GitHub #40](https://github.com/Azure/azure-cosmosdb-java/issues/40))。

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* クエリでのバック プレッシャーのサポートが追加されました。
* クエリでのパーティション キーの範囲 ID のサポートが追加されました。
* 要求ヘッダーでより大規模な継続トークンを許可できるように修正しました (bugfix GitHub #24)。
* メイン スレッドの終了後、JVM が確実にシャットダウンされるように、Netty 依存関係を 4.1.22.Final にアップグレードしました。
* マスター ソースを読み取るときに、セッション トークンを渡さないように修正しました。
* サンプルをさらに追加しました。
* ベンチマーク シナリオをさらに追加しました。
* 適切な java doc 生成のために Java ヘッダー ファイルを修正しました。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* [Netty ライブラリ](https://netty.io/)をゲートウェイ モードで使用し、非ブロッキング IO をエンド ツー エンドでサポートする GA SDK。 

## <a name="release-and-retirement-dates"></a>リリース日と提供終了日
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、SDK の提供終了を少なくともその **12 か月** 前に通知します。

新しい機能や機能性、および最適化は、現在の SDK にのみ追加されます。 そのため、常にできるだけ早く最新の SDK バージョンにアップグレードすることをお勧めします。

提供終了の SDK を使用した Cosmos DB への要求は、サービスによって拒否されます。

<br/>

| バージョン | リリース日 | 提供終了日 |
| --- | --- | --- |
| [2.4.3](#2.4.3) |2019 年 3 月 5 日|--- |
| [2.4.2](#2.4.2) |2019 年 3 月 1 日|--- |
| [2.4.1](#2.4.1) |2019 年 2 月 20 日|--- |
| [2.4.0](#2.4.0) |2019 年 2 月 8 日|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |2019 年 2 月 4 日|--- |
| [2.3.1](#2.3.1) |2019 年 1 月 15 日|--- |
| [2.3.0](#2.3.0) |2018 年 11 月 29 日|--- |
| [2.2.2](#2.2.2) |2018 年 11 月 8 日|--- |
| [2.2.1](#2.2.1) |2018 年 11 月 2 日|--- |
| [2.2.0](#2.2.0) |2018 年 9 月 22 日|--- |
| [2.1.0](#2.1.0) |2018 年 9 月 5 日|--- |
| [2.0.1](#2.0.1) |2018 年 8 月 16 日|--- |
| [2.0.0](#2.0.0) |2018 年 6 月 20 日|--- |
| [1.0.2](#1.0.2) |2018 年 5 月 18 日|--- |
| [1.0.1](#1.0.1) |2018 年 4 月 20 日|--- |
| [1.0.0](#1.0.0) |2018 年 2 月 27 日|--- |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>関連項目
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページをご覧ください。

