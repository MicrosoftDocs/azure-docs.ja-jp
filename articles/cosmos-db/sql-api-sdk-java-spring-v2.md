---
title: SQL API 用 Spring Data Azure Cosmos DB v2 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Spring Data Azure Cosmos DB v2 について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 1f78d7185415b66445c78f3b4cdb54ed0d63cb0a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216617"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>コア (SQL) API 用 Spring Data Azure Cosmos DB v2:リリース ノートとリソース
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

 コア (SQL) 用 Spring Data Azure Cosmos DB バージョン 2 により、開発者は Spring アプリケーションで Azure Cosmos DB を使用できます。 Spring Data Azure Cosmos DB では、データベースとコレクションの操作、ドキュメントの操作、クエリの発行を行うための Spring Data インターフェイスを公開しています。 同期 API と非同期 (リアクティブ) API の両方とも、同じ Maven アーティファクトでサポートされています。 

[Spring Framework](https://spring.io/projects/spring-framework) は、Java アプリケーション開発を効率化するプログラミングおよび構成モデルです。 Spring では依存関係の挿入を使用して、アプリケーションの "プラミング" が効率化されます。 アプリケーションの構築とテストが簡潔になるため、多くの開発者が Spring を好んでいます。 [Spring Boot](https://spring.io/projects/spring-boot) は、Web アプリとマイクロサービスの開発を目的として、このプラミングの処理を拡張したものです。 [Spring Data](https://spring.io/projects/spring-data) は、Spring または Spring Boot アプリケーションのコンテキストから Azure Cosmos DB などのデータストアにアクセスするためのプログラミング モデルです。 

Spring Data Azure Cosmos DB は [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) アプリケーションで使用できます。

> [!IMPORTANT]  
> これらのリリース ノートは、Spring Data Azure Cosmos DB のバージョン 2 に関するものです。 [バージョン 3 のリリース ノートについてはこちら](sql-api-sdk-java-spring-v3.md)を参照してください。 
>
> Spring Data Azure Cosmos DB では、SQL API のみがサポートされています。
>
> 他の Azure Cosmos DB API での Spring Data については、次の記事を参照してください。
> * [Azure Cosmos DB での Apache Cassandra 用 Spring Data](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data Gremlin](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 急いでいますか?
> 1. この SDK を使用できるように、[サポートされている最小 Java ランタイムの JDK 8](/java/azure/jdk/) をインストールします。
> 2. [スターター](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)を使用して Spring Data Azure Cosmos DB アプリを作成します。 これは簡単にできます。
> 3. 基本的な Azure Cosmos DB の要求について説明している「[Spring Data Azure Cosmos DB 開発者ガイド](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)」を参照します。
>
> Spring Boot Starter アプリは、[Spring Initializr](https://start.spring.io/) によってすぐに利用できます。
>

## <a name="resources"></a>参照情報

| リソース | Link |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API ドキュメント** | [Spring Data Azure Cosmos DB リファレンス ドキュメント]() |
|**SDK に投稿する** | [GitHub の Spring Data Azure Cosmos DB リポジトリ](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Java 用 Azure Cosmos DB Spring Boot Starter クライアント ライブラリ](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure Cosmos DB による Spring TODO アプリ サンプル**| [App Service Linux でのエンドツーエンドの Java エクスペリエンス (パート 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**開発者ガイド** | [Spring Data Azure Cosmos DB 開発者ガイド](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Starter の使用** | [Azure Cosmos DB SQL API で Spring Boot Starter を使用する方法](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure Cosmos DB Spring Boot Starter の GitHub リポジトリ](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure App Service によるサンプル** | [App Service on Linux で Spring と Cosmos DB を使用する方法](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO アプリのサンプル](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>リリース履歴

### <a name="230-may-21-2020"></a>2.3.0 (2020 年 5 月 21 日)
#### <a name="new-features"></a>新機能
* Spring Boot のバージョンを 2.3.0 に更新。


### <a name="225-may-19-2020"></a>2.2.5 (2020 年 5 月 19 日)
#### <a name="new-features"></a>新機能
* Azure Cosmos DB バージョンを 3.7.3 に更新。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* Azure Cosmos DB SDK 3.7.3 からのメモリ リークの修正と Netty のバージョンのアップグレードを含む。

### <a name="224-april-6-2020"></a>2.2.4 (2020 年 4 月 6 日)
#### <a name="key-bug-fixes"></a>主要なバグ修正
* `CosmosDbConfig` を考慮するように `allowTelemetry` フラグを修正。
* コンテナーの `TTL` プロパティを修正。

### <a name="223-february-25-2020"></a>2.2.3 (2020 年 2 月 25 日)
#### <a name="new-features"></a>新機能
* パーティション キー API による新しい `findAll` を追加。
* Azure Cosmos DB バージョンを 3.7.0 に更新。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* `collectionName` -> `containerName` を修正。
* `entityClass` と `domainClass` -> `domainType` を修正。
* "入力エンティティの代わりにリポジトリによって保存されたエンティティ コレクションを返す" を修正。

### <a name="2110-february-25-2020"></a>2.1.10 (2020 年 2 月 25 日)
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "入力エンティティの代わりにリポジトリによって保存されたエンティティ コレクションを返す" の修正を移植。

### <a name="222-january-15-2020"></a>2.2.2 (2020 年 1 月 15 日)
#### <a name="new-features"></a>新機能
* Azure Cosmos DB バージョンを 3.6.0 に更新。
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="221-december-31-2019"></a>2.2.1 (2019 年 12 月 31 日)
#### <a name="new-features"></a>新機能
* Azure Cosmos DB SDK バージョンを 3.5.0 に更新。
* 自動コレクション作成を有効または無効にする注釈フィールドを追加。
* 例外処理を改善。 `CosmosDBAccessException` を介して `CosmosClientException` を公開。
* `ResponseDiagnostics` を介して `requestCharge` と `activityId` を公開。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* SDK 3.5.0 更新プログラムで "Cosmos DB HTTP 応答ヘッダーが 8192 バイトを超えた場合の例外"、"ConsistencyPolicy.defaultConsistencyLevel() は有界整合性制約および一貫性のあるプレフィックスで失敗する" を修正。
* `findById` メソッドの動作を修正。 以前は、エンティティが見つからなかった場合、例外をスローする代わりに、このメソッドは空の戻り値を返していました。
* `CosmosPageRequest` を使用した場合に次のページに並べ替えが適用されないバグを修正。

### <a name="219-december-26-2019"></a>2.1.9 (2019 年 12 月 26 日)
#### <a name="new-features"></a>新機能
* 自動コレクション作成を有効または無効にする注釈フィールドを追加。
#### <a name="key-bug-fixes"></a>主要なバグ修正
*  `findById` メソッドの動作を修正。 以前は、エンティティが見つからなかった場合、例外をスローする代わりに、このメソッドは空の戻り値を返していました。

### <a name="220-october-21-2019"></a>2.2.0 (2019 年 10 月 21 日)
#### <a name="new-features"></a>新機能
* 完全なリアクティブ Cosmos リポジトリのサポート。
* Azure Cosmos DB の要求診断文字列とクエリ メトリックのサポート。
* Azure Cosmos DB SDK バージョンを 3.3.1 に更新。
* Spring Framework バージョンを 5.2.0.RELEASE にアップグレード。
* Spring Data Commons バージョンを 2.2.0.RELEASE にアップグレード。
* `findByIdAndPartitionKey` および `deleteByIdAndPartitionKey` API を追加。
* azure-doumentdb から依存関係を削除。
* DocumentDB を Azure Cosmos DB に再ブランド化。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "PageSize がリポジトリ内の合計の項目数未満の場合に、並べ替えで例外がスローされる" を修正。

### <a name="218-october-18-2019"></a>2.1.8 (2019 年 10 月 18 日)
#### <a name="new-features"></a>新機能
* DocumentDB API の非推奨。
* `findByIdAndPartitionKey` および `deleteByIdAndPartitionKey` API を追加。
* `_etag` に基づくオプティミスティック ロックを追加。
* ドキュメント コレクション名での SpEL 表現を有効化。
* `ObjectMapper` の機能強化を追加。

### <a name="217-october-18-2019"></a>2.1.7 (2019 年 10 月 18 日)
#### <a name="new-features"></a>新機能
* Azure Cosmos DB SDK バージョン 3 の依存関係を追加。
* リアクティブ Cosmos リポジトリを追加。
* Azure Cosmos DB SDK バージョン 3 を使用するように `DocumentDbTemplate` の実装を更新。
* リアクティブ Cosmos リポジトリをサポートするためのその他の構成変更を追加。

### <a name="212-march-19-2019"></a>2.1.2 (2019 年 3 月 19 日)
#### <a name="key-bug-fixes"></a>主要なバグ修正
* 次の `applicationInsights` の依存関係を削除。
    * 依存関係の汚染の可能性のあるリスク。
    * Java 11 の非互換性。
    * CPU やメモリへの潜在的なパフォーマンスの影響の回避。

### <a name="207-march-20-2019"></a>2.0.7 (2019 年 3 月 20 日)
#### <a name="key-bug-fixes"></a>主要なバグ修正
* 次の `applicationInsights` の依存関係を移植。
    * 依存関係の汚染の可能性のあるリスク。
    * Java 11 の非互換性。
    * CPU やメモリへの潜在的なパフォーマンスの影響の回避。

### <a name="211-march-7-2019"></a>2.1.1 (2019 年 3 月 7 日)
#### <a name="new-features"></a>新機能
* メイン バージョンを 2.1.1 に更新。

### <a name="206-march-7-2019"></a>2.0.6 (2019 年 3 月 7 日)
#### <a name="new-features"></a>新機能
* テレメトリからのすべての例外を無視。

### <a name="210-december-17-2018"></a>2.1.0 (2018 年 12 月 17 日)
#### <a name="new-features"></a>新機能
* 問題に対処するためにバージョンを 2.1.0 に更新。

### <a name="205-september-13-2018"></a>2.0.5 (2018 年 9 月 13 日)
#### <a name="new-features"></a>新機能
* キーワード `exists` と `startsWith` を追加。
* Readme を更新。
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "エンティティに対して self href を直接呼び出すことができない" を修正。
* "コレクションが作成されていない場合、findAll が失敗する" を修正。

### <a name="204-prerelease-august-23-2018"></a>2.0.4 (プレリリース) (2018 年 8 月 23 日)
#### <a name="new-features"></a>新機能
* パッケージ名を documentdb から cosmosdb に変更。
* クエリ メソッド キーワードの新機能を追加。 SQL API の 16 個のキーワードがサポートされるようになりました。
* ページングと並べ替えによるクエリの新機能を追加。
* spring-data-cosmosdb の構成を簡略化。
* `deleteCollection` および `deleteAll` API を追加。

#### <a name="key-bug-fixes"></a>主要なバグ修正
* バグの修正と欠陥の軽減策。

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) の詳細をご覧ください。

[Spring Framework](https://spring.io/projects/spring-framework) の詳細を確認してください。

[Spring Boot](https://spring.io/projects/spring-boot) の詳細を確認してください。

[Spring Data](https://spring.io/projects/spring-data) の詳細を確認してください。