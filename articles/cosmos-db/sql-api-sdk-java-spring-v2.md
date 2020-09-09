---
title: SQL API 用 Spring Data Azure Cosmos DB v2 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Spring Data Azure Cosmos DB v2 に関する詳細を説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590510"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Spring Data Azure Cosmos DB v2: リリース ノートとリソース
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

Spring Data Azure Cosmos DB v2 for Core (SQL) により、開発者は Spring アプリケーションで Azure Cosmos DB を利用できます。 Spring Data Azure Cosmos DB では、データベースとコレクションの操作、ドキュメントの操作、クエリの発行を行うための Spring Data インターフェイスを公開しています。 同期 API と非同期 (リアクティブ) API の両方とも、同じ Maven アーティファクトでサポートされています。 

[Spring Framework](https://spring.io/projects/spring-framework) は、Java アプリケーション開発を効率化するプログラミングおよび構成モデルです。 組織の Web サイトを引き合いに出すと、Spring では依存関係の挿入を使用して、アプリケーションの "プラミング" が効率化されます。 アプリケーションの構築とテストが簡潔になるため、多くの開発者が Spring を好んでいます。 [Spring Boot](https://spring.io/projects/spring-boot) は、Web アプリとマイクロサービスの開発を目的として、このプラミングの処理の考え方を拡張したものです。 [Spring Data](https://spring.io/projects/spring-data) は、Spring または Spring Boot アプリケーションのコンテキストから Azure Cosmos DB などのデータストアにアクセスするためのプログラミング モデルです。 

Spring Data Azure Cosmos DB は [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) アプリケーションで使用できます。

> [!IMPORTANT]  
> これらのリリースノートは、Spring Data Azure Cosmos DB の v2 を対象としています。 v3 のリリース ノートは[こちら](sql-api-sdk-java-spring-v3.md)で確認できます。 
>
> Spring Data Azure Cosmos DB では SQL API のみをサポートしています。
>
> 次のガイドでは、他の Azure Cosmos DB API の Spring Data をサポートしています。
> * [Azure Cosmos DB での Apache Cassandra 用 Spring Data](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 急いでいますか?
> 1. この SDK を使用できるように、[サポートされている最小 Java ランタイムの JDK 8](/java/azure/jdk/?view=azure-java-stable) をインストールします。
> 2. スターターを使用して Spring Data Azure Cosmos DB アプリを作成します。[これは簡単です](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)。
> 3. 基本的な Azure Cosmos DB 要求について説明する [Spring Data Azure Cosmos DB 開発者ガイド](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)を参照します。
>
> Spring Boot Starter アプリは、[Spring Initializr](https://start.spring.io/) によってすぐに利用できます。
>

## <a name="helpful-content"></a>役に立つコンテンツ

| コンテンツ | Link |
|---|---|
| **SDK のダウンロード** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API ドキュメント** | [Spring Data Azure Cosmos DB リファレンス ドキュメント]() |
|**SDK への協力** | [GitHub の Spring Data Azure Cosmos DB リポジトリ](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [Java 用 Azure Cosmos DB Spring Boot Starter クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Azure Cosmos DB による Spring TODO アプリ サンプル**| [App Service Linux でのエンドツーエンドの Java エクスペリエンス (パート 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**開発者向けガイド** | [Spring Data Azure Cosmos DB 開発者ガイド](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**スターターの使用ガイド** | [Azure Cosmos DB SQL API で Spring Boot Starter を使用する方法](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure Spring Boot Starter Cosmos DB 用 GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**App Services によるサンプル** | [App Service on Linux で Spring と Cosmos DB を使用する方法](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO アプリのサンプル](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>リリース履歴

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>新機能
* Spring Boot のバージョンを 2.3.0 に更新 
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>新機能
* Azure Cosmos DB バージョンを v 3.7.3 に更新
#### <a name="key-bug-fixes"></a>主要なバグ修正
* メモリ リークの修正と Cosmos SDK v 3.7.3 からの netty のバージョンのアップグレードを含む 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* allowTelemetry フラグを CosmosDbConfig から考慮するように修正
* コンテナーの TTL プロパティを修正

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>新機能
* パーティション キー API による新しい findAll を追加
* azure-cosmos バージョンを 3.7.0 に更新
#### <a name="key-bug-fixes"></a>主要なバグ修正
* collectionName -> containerName に修正
* entityClass と domainClass -> domainType に修正
* "入力エンティティの代わりにリポジトリによって保存されたエンティティ コレクションを返す" を修正

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "入力エンティティの代わりにリポジトリによって保存されたエンティティ コレクションを返す" の修正を移植

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>新機能
* azure-cosmos バージョンを v3.6.0 に更新
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>新機能
* Cosmos DB SDK バージョンを 3.5.0 に更新
* コレクションの自動作成を有効/無効にする注釈フィールドを追加
* 例外処理の改善、CosmosDBAccessException によって CosmosClientException を公開
* ResponseDiagnostics によって requestCharge と activityId を公開
#### <a name="key-bug-fixes"></a>主要なバグ修正
* SDK 3.5.0 更新プログラムで "Cosmos DB HTTP 応答ヘッダーが 8192 バイトを超えた場合の例外"、"ConsistencyPolicy.defaultConsistencyLevel() は有界整合性制約および一貫性のあるプレフィックスで失敗する" を修正
* findById API の動作を修正し、例外をスローする代わりに、見つからない場合に空を返す
* CosmosPageRequest を使用しているときに、次のページに並べ替えが適用されなかったバグを修正

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>新機能
* コレクションの自動作成を有効/無効にする注釈フィールドを追加
#### <a name="key-bug-fixes"></a>主要なバグ修正
* findById API の動作を修正し、例外をスローする代わりに、見つからない場合に空を返す

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>新機能
* 完全なリアクティブ Cosmos リポジトリのサポート
* Cosmos DB 要求診断文字列とクエリ メトリックのサポート
* Cosmos DB SDK バージョンを 3.3.1 に更新
* Spring Framework バージョンを 5.2.0.RELEASE にアップグレード
* Spring Data Commons バージョンを 2.2.0.RELEASE にアップグレード
* findByIdAndPartitionKey、deleteByIdAndPartitionKey API を追加
* azure-doumentdb から依存関係を削除
* DocumentDb を Cosmos にブランド変更
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "PageSize がリポジトリ内の合計の項目数未満の場合に、並べ替えで例外がスローされる" を修正

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>新機能
* Document DB API を廃止
* findByIdAndPartitionKey、deleteByIdAndPartitionKey API を追加。
* _etag に基づくオプティミスティック ロックを追加
* ドキュメント コレクション名に対して SPeL 式を有効化
* ObjectMapper の機能強化。
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>新機能
* Cosmos SDK v3 依存関係を追加
* リアクティブ Cosmos リポジトリを追加
* Cosmos SDK v3 を使用するように DocumentDbTemplate の実装を更新。
* リアクティブ Cosmos リポジトリをサポートするためのその他の構成変更。
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* 次の ApplicationInsights の依存関係を削除
    * 依存関係の汚染の可能性のあるリスク
    * Java 11 の非互換性
    * CPU やメモリへの潜在的なパフォーマンスの影響の回避。

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* バックポートにより、次の applicationInsights の依存関係を削除
    * 依存関係の汚染の可能性のあるリスク
    * Java 11 の非互換性
    * CPU やメモリへの潜在的なパフォーマンスの影響の回避。

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>新機能
* マスター バージョンを 2.1.1 に更新
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>新機能
* テレメトリからのすべての例外を無視
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>新機能
* 問題に対処するためにバージョンを 2.1.0 に更新
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>新機能
* キーワード exists、startsWith を追加
* Readme を更新
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "エンティティに対して self href を直接呼び出すことができない" を修正
* "コレクションが作成されていない場合、findAll が失敗する" を修正

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (プレリリース) (2018-08-23)
#### <a name="new-features"></a>新機能
* パッケージ名を documentdb から cosmosdb に変更
* クエリ メソッド キーワードの新しい機能を追加、Sql API から 16 個のキーワードをサポート。
* ページングと並べ替えによるクエリの新機能を追加。
* spring-data-cosmosdb の構成を簡略化。
* deleteCollection と deleteAll API を追加。

#### <a name="key-bug-fixes"></a>主要なバグ修正
* バグの修正と欠陥の機能拡張。

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。

Spring Framework の詳細については、[プロジェクトのホームページ](https://spring.io/projects/spring-framework)を参照してください。

Spring Boot の詳細については、[プロジェクトのホームページ](https://spring.io/projects/spring-boot)を参照してください。

Spring Data の詳細については、[プロジェクトのホームページ](https://spring.io/projects/spring-data)を参照してください。