---
title: SQL API 用 Spring Data Azure Cosmos DB v3 のリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Spring Data Azure Cosmos DB v3 について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/15/2021
ms.author: kuthapar
ms.custom: devx-track-java
ms.openlocfilehash: 3c740aa00b158c7ddbca3e4f61d79e37978223c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493613"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>コア (SQL) API 用 Spring Data Azure Cosmos DB v3:リリース ノートとリソース
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

コア (SQL) 用 Spring Data Azure Cosmos DB バージョン 3 により、開発者は Spring アプリケーションで Azure Cosmos DB を使用できます。 Spring Data Azure Cosmos DB では、データベースとコレクションの操作、ドキュメントの操作、クエリの発行を行うための Spring Data インターフェイスが公開されています。 同期 API と非同期 (リアクティブ) API の両方とも、同じ Maven 成果物でサポートされています。 

> [!IMPORTANT]
> Spring Data Azure Cosmos DB は、Spring Data フレームワークに依存しています。
> 
> azure-spring-data-cosmos バージョン 3.0.0 ～ 3.4.0 では、Spring Data version 2.2 および 2.3 がサポートされています。
> 
> azure-spring-data-cosmos バージョン 3.5.0 以降では、Spring Data version 2.4.3 以降がサポートされています。
>

[Spring Framework](https://spring.io/projects/spring-framework) は、Java アプリケーション開発を効率化するプログラミングおよび構成モデルです。 Spring では依存関係の挿入を使用して、アプリケーションの "プラミング" が効率化されます。 アプリケーションの構築とテストが簡潔になるため、多くの開発者が Spring を好んでいます。 [Spring Boot](https://spring.io/projects/spring-boot) は、Web アプリとマイクロサービスの開発を目的として、このプラミングの処理を拡張したものです。 [Spring Data](https://spring.io/projects/spring-data) は、Spring または Spring Boot アプリケーションのコンテキストから Azure Cosmos DB などのデータストアにアクセスするためのプログラミング モデルおよびフレームワークです。 

Spring Data Azure Cosmos DB は [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) アプリケーションで使用できます。

> [!IMPORTANT]  
> これらのリリース ノートは、Spring Data Azure Cosmos DB のバージョン 3 に関するものです。 [バージョン 2 のリリース ノートについてはこちら](sql-api-sdk-java-spring-v2.md)を参照してください。 
>
> Spring Data Azure Cosmos DB では、SQL API のみがサポートされています。
>
> 他の Azure Cosmos DB API での Spring Data については、次の記事を参照してください。
> * [Azure Cosmos DB での Apache Cassandra 用 Spring Data](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data Gremlin](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="get-started-fast"></a>すぐに開始

  [Spring Boot Starter ガイド](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)に従って Spring Data Azure Cosmos DB を起動して実行します。 Spring Boot Starter のアプローチが、Spring Data Azure Cosmos DB コネクタの使用を開始するのにお勧めの方法です。

  または、下に示すように、Spring Data Azure Cosmos DB の依存関係をお使いの `pom.xml` ファイルに追加できます。

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>役に立つコンテンツ

| コンテンツ | Link |
|---|---|
|**SDK のダウンロード**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API ドキュメント** | [Java API リファレンス ドキュメント](/java/api/com.azure.spring.data.cosmos) |
|**SDK への協力** | [GitHub の Azure SDK for Java 中央リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**開始するには** | [クイックスタート: Spring Data Azure Cosmos DB アプリを構築して Azure Cosmos DB SQL API データを管理する](./create-sql-api-spring-data.md) <br> [クイック スタート コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**基本的なコード サンプル** | [Azure Cosmos DB: SQL API 用の Spring Data Azure Cosmos DB の例](sql-api-spring-data-sdk-samples.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **パフォーマンスに関するヒント**| [Java SDK v4 のパフォーマンスに関するヒント (Spring Data に適用可能)](performance-tips-java-sdk-v4-sql.md)| 
| **トラブルシューティング** | [Java SDK v4 のトラブルシューティング (Spring Data に適用可能)](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB のワークショップとラボ** |[Cosmos DB ワークショップのホーム ページ](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>その他のメモ

* Spring Data Azure Cosmos DB では、Java JDK 8 と Java JDK 11 がサポートされています。
* Spring Data 2.3 は現在サポートされていますが、Spring Data 2.4 は現在サポートされていません。

## <a name="faq"></a>よく寄せられる質問

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) の詳細をご覧ください。

[Spring Framework](https://spring.io/projects/spring-framework) の詳細を確認してください。

[Spring Boot](https://spring.io/projects/spring-boot) の詳細を確認してください。

[Spring Data](https://spring.io/projects/spring-data) の詳細を確認してください。