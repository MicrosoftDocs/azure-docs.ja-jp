---
title: 'Azure Cosmos DB SQL API: Spring Data v3 のサンプル'
description: CRUD 操作など、Azure Cosmos DB SQL API を使う一般的なタスクについては、GitHub の Spring Data v3 のサンプルを参照してください。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/23/2020
ms.custom: devx-track-java
ms.author: anfeldma
ms.openlocfilehash: 378bb891c8539a6cf3d61f6511a0f58377d2bfd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93091142"
---
# <a name="azure-cosmos-db-sql-api-spring-data-azure-cosmos-db-v3-examples"></a>Azure Cosmos DB SQL API: Spring Data Azure Cosmos DB v3 のサンプル
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V2 SDK のサンプル](sql-api-dotnet-samples.md)
> * [.NET V3 SDK のサンプル](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK のサンプル](sql-api-java-sdk-samples.md)
> * [Spring Data V3 SDK のサンプル](sql-api-spring-data-sdk-samples.md)
> * [Node.js のサンプル](sql-api-nodejs-samples.md)
> * [Python のサンプル](sql-api-python-samples.md)
> * [Azure のコード サンプル ギャラリー](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

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

> [!IMPORTANT]  
>[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
>  
>- [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)ことができます。Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。
>
>[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]
>

Azure Cosmos DB リソースに対する CRUD 操作などの一般的な操作を実行する最新のサンプル アプリケーションは、[azure-spring-data-cosmos-java-sql-api-samples](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples) GitHub リポジトリにあります。 この記事では、次の内容について説明します。

* 各サンプル Spring Data Azure Cosmos DB プロジェクト ファイルのタスクへのリンク。 
* 関連する API リファレンス コンテンツへのリンク。

**前提条件**

このサンプル アプリケーションを実行するには、次のものが必要です。

* Java Development Kit 8
* Spring Data Azure Cosmos DB v3

プロジェクトで使用する最新の Spring Data Azure Cosmos DB v3 バイナリが必要な場合は、Maven を使用して取得することもできます。 必要な依存関係は Maven によって自動的に追加されます。 または、**pom.xml** ファイルに一覧表示されている依存関係を直接ダウンロードして、ビルドのパスに追加することもできます。

```bash
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-cosmos</artifactId>
    <version>LATEST</version>
</dependency>
```

**サンプル アプリケーションの実行**

サンプル リポジトリを複製します。
```bash
$ git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples

$ cd azure-spring-data-cosmos-java-sql-api-samples
```

サンプルは、IDE (Eclipse、IntelliJ、または VSCODE) を使用して実行することも、Maven を使用してコマンド ラインから実行することもできます。

**application.properties** でこれらの環境変数を設定して、

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

サンプルにアカウント、データベース、コンテナーへの読み取り/書き込みアクセス権を付与する必要があります。

ご使用の IDE に、Spring Data のサンプル コードを実行する機能が備わっている場合があります。 それ以外の場合は、次のターミナル コマンドを使用してサンプルを実行できます。

```bash
mvn spring-boot:run
```

## <a name="document-crud-examples"></a>ドキュメントの CRUD のサンプル
この[サンプル](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に、Azure Cosmos のドキュメントについて知るために、[データベース、コンテナー、アイテムの操作](account-databases-containers-items.md)に関する概念記事を参照してください。

| タスク | API リファレンス |
| --- | --- |
| [ドキュメントの作成](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L46-L47) | CosmosRepository.save |
| [ID でのドキュメントの読み取り](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L56-L58) | CosmosRepository.derivedQueryMethod |
| [すべてのドキュメントの削除](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L39-L41) | CosmosRepository.deleteAll |

## <a name="derived-query-method-examples"></a>派生クエリ メソッドのサンプル
この[サンプル](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) ファイルは、次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に、Azure Cosmos DB クエリについて知るために、[Baeldung の「Derived Query Methods in Spring」 (Spring での派生クエリ メソッド)](https://www.baeldung.com/spring-data-derived-queries)の記事を参照してください。

| [ドキュメントのクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java#L73-L77) | CosmosRepository.derivedQueryMethod |

## <a name="custom-query-examples"></a>カスタム クエリのサンプル
この[サンプル](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/SampleApplication.java) ファイルは、SQL クエリ文法を使用して次のタスクを実行する方法を示しています。 以下のサンプルを実行する前に Azure Cosmos DB の SQL クエリ リファレンスについて知るために、「[Azure Cosmos DB の SQL クエリの例](./sql-query-getting-started.md)」を参照してください。 


| タスク | API リファレンス |
| --- | --- |
| [すべてのドキュメントのクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L20-L22) | @Query 注釈 |
| [= = を使用する等値のクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L24-L26) | @Query 注釈 |
| [!= と NOT を使用する非等値のクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L28-L38) | @Query 注釈 |
| [>、<、>=、<= のような範囲演算子を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L40-L42) | @Query 注釈 |
| [文字列に対して範囲演算子を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L44-L46) | @Query 注釈 |
| [Order By を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L48-L50) | @Query 注釈 |
| [DISTINCT を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L52-L54) | @Query 注釈 |
| [集計関数を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L56-L62) | @Query 注釈 |
| [サブドキュメントの操作](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L64-L66) | @Query 注釈 |
| [ドキュメント間結合を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L68-L85) | @Query 注釈 |
| [文字列、数値、および配列の演算子を使用するクエリ](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/springexamples/quickstart/sync/UserRepository.java#L87-L97) | @Query 注釈 |