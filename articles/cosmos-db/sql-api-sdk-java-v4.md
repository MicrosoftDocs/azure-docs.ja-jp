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
ms.openlocfilehash: e23a1ed348f2a627181e0e4e4c20477ee9ba1ff8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102210548"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Java SDK v4: リリース ノートとリソース
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

Core (SQL) 用 Azure Cosmos DB Java SDK v4 では、非同期 API と同期 API が 1 つの Maven アーティファクトに結合されています。 v4 SDK は、Project Reactor と [Netty ライブラリ](https://netty.io/)に基づいて、強化されたパフォーマンス、新しい API 機能、非同期サポートを提供します。 ユーザーは、Azure Cosmos DB Java SDK v4 を使用することで、[Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) や [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md) よりもパフォーマンスを向上させることができます。

> [!IMPORTANT]  
> これらのリリース ノートは、Azure Cosmos DB Java SDK v4 のみを対象としています。 v4 より前のバージョンを現在使用している場合、v4 へのアップグレードについては、[Azure Cosmos DB Java SDK v4 への移行](migrate-java-v4-sdk.md)ガイドを参照してください。
>
> 次の 3 つの手順に従って作業を進めてください。
> 1. この SDK を使用できるように、[サポートされている最小 Java ランタイムの JDK 8](/java/azure/jdk/) をインストールします。
> 2. [Azure Cosmos DB Java SDK v4 のクイックスタート ガイド](./create-sql-api-java.md)に従って、Maven アーティファクトにアクセスし、Azure Cosmos DB の基本的な要求の詳細を確認します。
> 3. Azure Cosmos DB Java SDK v4 の[パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)と[トラブルシューティング](troubleshoot-java-sdk-v4-sql.md)のガイドを読んで、SDK をお使いのアプリケーションに最適化します。
>
> [Azure Cosmos DB のワークショップとラボ](https://aka.ms/cosmosworkshop)は、Azure Cosmos DB Java SDK v4 の使用方法を学習するための別の優れたリソースです。
>

## <a name="helpful-content"></a>役に立つコンテンツ

| コンテンツ | Link |
|---|---|
|**SDK のダウンロード**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API ドキュメント** | [Java API リファレンス ドキュメント](/java/api/overview/azure/cosmosdb/client) |
|**SDK への協力** | [GitHub の Azure SDK for Java 中央リポジトリ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**開始するには** | [クイック スタート:Azure Cosmos DB SQL API データを管理する Java アプリを作成する](./create-sql-api-java.md) <br> [クイック スタート コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**基本的なコード サンプル** | [Azure Cosmos DB: SQL API の Java のサンプル](sql-api-java-sdk-samples.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**変更フィードを使用するコンソール アプリ**| [変更フィード - Java SDK v4 サンプル](create-sql-api-java-changefeed.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Web アプリのサンプル**| [Java SDK v4 を使用した Web アプリの構築](sql-api-java-application.md) <br> [サンプル コードを含む GitHub リポジトリ](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **パフォーマンスに関するヒント**| [Java SDK v4 のパフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)| 
| **トラブルシューティング** | [Java SDK v4 のトラブルシューティング](troubleshoot-java-sdk-v4-sql.md) |
| **以前の SDK から v4 への移行** | [Java V4 SDK への移行](migrate-java-v4-sdk.md) |
| **サポートされている最小ランタイム**|[JDK 8](/java/azure/jdk/) | 
| **Azure Cosmos DB のワークショップとラボ** |[Cosmos DB ワークショップのホーム ページ](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos/CHANGELOG.md)]

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)] 

## <a name="next-steps"></a>次のステップ
Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。