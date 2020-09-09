---
title: SQL API 用 Azure Cosmos DB Apache Spark コネクタのリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Apache Spark コネクタに関するあらゆる詳細を説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590011"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API 用 Azure Cosmos DB Apache Spark コネクタ: リリース ノートとリソース
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

Core (SQL) API 用 Azure Cosmos DB Apache Spark コネクタを使用して、ビッグ データ分析を高速化します。 Spark コネクタにより、Azure Cosmos DB に格納されているデータに対して [Spark](https://spark.apache.org/) ジョブを実行できます。 バッチ処理とストリーム処理がサポートされています。

このコネクタは、Azure 上でマネージド Spark クラスターを提供する [Azure Databricks](https://azure.microsoft.com/services/databricks) または [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) と一緒に使用できます。 サポートする Spark バージョンを次の表に示します。

| コンポーネント | Version |
|---------|-------|
| Apache Spark | 2.4.x、2.3.x、2.2.x、2.1.x |
| Scala | 2.11 |
| Azure Databricks ランタイムのバージョン | 3\.4 より新しいバージョン |

> [!WARNING]
> このコネクタは、Azure Cosmos DB のコア (SQL) API をサポートします。
> Cosmos DB for MongoDB API の場合は、[MongoDB Spark コネクタ](https://docs.mongodb.com/spark-connector/master/)を使用してください。
> Cosmos DB Cassandra API の場合は、[Cassandra Spark コネクタ](https://github.com/datastax/spark-cassandra-connector)を使用してください。
>

## <a name="helpful-content"></a>役に立つコンテンツ

| コンテンツ | Link |
|---|---|
| **SDK のダウンロード** | [Apache Spark からのダウンロード](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API ドキュメント** | [Spark コネクタのリファレンス]() |
|**SDK への協力** | [GitHub の Apache Spark 用 Azure Cosmos DB コネクタ](https://github.com/Azure/azure-cosmosdb-spark) | 
|**開始するには** | [Apache Spark-Azure Cosmos DB コネクタを使用したビッグ データ分析の高速化](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Apache Kafka と Azure Cosmos DB で Apache Spark 構造化ストリーミングを使用する](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>リリース履歴

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "id" に "ChangeFeedMaxPagesPerBatch" 構成が適用されている "|" 文字が含まれるストリーミング チェックポイント エッジのケースを修正

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新機能
* 入れ子になったパーティション キーを使用している場合の一括更新のサポートを追加
* Cosmos DB への書き込み時の Decimal データ型と Float データ型のサポートを追加。
* 値として Long (unix エポック) を使用している場合の Timestamp 型のサポートを追加
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* "WriteThroughputBudget" 構成を使用している場合の型キャストの例外を修正。

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>新機能
* 例外とログに一括エラーのエラー情報を追加。
#### <a name="key-bug-fixes"></a>主要なバグ修正

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ストリーミング チェックポイントの問題を修正。

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ノイズを減らすために、意図せずにレベル ERROR で残されていたメッセージのログ レベルを修正

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* パーティション分割時の構造化ストリーミングのバグ (一部の変更フィード レコードの欠落やチェックポイントの書き込みで Null 例外が発生する可能性がある) を修正

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* readStream に指定されたカスタム スキーマが無視されるバグを修正

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ビルド時間が 50% 増加した回帰 (シェーディングされていない JAR に、シェーディングされたすべての依存関係が含まれる) を修正

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>新機能
#### <a name="key-bug-fixes"></a>主要なバグ修正
* TCP 経由の直接転送が RequestTimeoutException で失敗する依存関係の問題を修正

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新機能
* メタデータ呼び出しの数を減らすために、接続管理と接続プーリングを改善
#### <a name="key-bug-fixes"></a>主要なバグ修正

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ

Cosmos DB の詳細については、[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) サービス ページを参照してください。

Apache Spark の詳細については、[ホームページ](https://spark.apache.org/)を参照してください。