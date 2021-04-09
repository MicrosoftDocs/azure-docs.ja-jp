---
title: SQL API 用 Cosmos DB Apache Spark コネクタのリリース ノートとリソース
description: リリース日、提供終了日、Azure Cosmos DB SQL Async Java SDK の各バージョン間の変更など、SQL API 用 Azure Cosmos DB Apache Spark コネクタについて説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 7ff75bf9d45803dd96927bcf7c70e7c7912db979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097160"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>コア (SQL) API 用 Azure Cosmos DB Apache Spark コネクタ:リリース ノートとリソース
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

コア (SQL) 用 Azure Cosmos DB Apache Spark コネクタを使用すると、ビッグ データ分析を高速化できます。 Spark コネクタにより、Azure Cosmos DB に格納されているデータに対して [Spark](https://spark.apache.org/) ジョブを実行できます。 バッチ処理とストリーム処理がサポートされています。

このコネクタは、Azure 上でマネージド Spark クラスターを提供する [Azure Databricks](https://azure.microsoft.com/services/databricks) または [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) と一緒に使用できます。 サポートされているバージョンを次の表に示します。

| コンポーネント | Version |
|---------|-------|
| Apache Spark | 2.4.*x*、2.3.*x*、2.2.*x*、および 2.1.*x* |
| Scala | 2.11 |
| Azure Databricks (ランタイムのバージョン) | 3\.4 より後 |

> [!WARNING]
> このコネクタは、Azure Cosmos DB のコア (SQL) API をサポートします。
> MongoDB 用の Cosmos DB API の場合は、[Spark 用 MongoDB コネクタ](https://docs.mongodb.com/spark-connector/master/)を使用します。
> Cosmos DB Cassandra API の場合は、[Cassandra Spark コネクタ](https://github.com/datastax/spark-cassandra-connector)を使用してください。
>

## <a name="resources"></a>参照情報

| リソース | Link |
|---|---|
| **SDK のダウンロード** | [最新の .jar](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG)、[Maven](https://search.maven.org/search?q=a:azure-cosmosdb-spark_2.4.0_2.11) のダウンロード |
|**API ドキュメント** | [Spark コネクタのリファレンス]() |
|**SDK に投稿する** | [GitHub の Apache Spark 用 Azure Cosmos DB コネクタ](https://github.com/Azure/azure-cosmosdb-spark) | 
|**開始するには** | [Apache Spark-Azure Cosmos DB コネクタを使用したビッグ データ分析の高速化](./spark-connector.md#bk_working_with_connector) <br> [Apache Kafka と Azure Cosmos DB で Apache Spark 構造化ストリーミングを使用する](../hdinsight/apache-kafka-spark-structured-streaming-cosmosdb.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>リリース履歴

### <a name="330"></a>3.3.0
#### <a name="new-features"></a>新機能
- 新しい構成オプション `changefeedstartfromdatetime` を追加します。このオプションを使用すると、changefeed の処理の開始時刻を指定できます。 詳細については、[構成オプション](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)に関する記事を参照してください。

### <a name="320"></a>3.2.0
#### <a name="key-bug-fixes"></a>主要なバグ修正
- 大規模な結果セット (行数が何百万にも及ぶ場合など) で実行プログラムのメモリ消費が過大となり、最終的にエラー `java.lang.OutOfMemoryError: GC overhead limit exceeded` が発生した原因となった回帰を修正します。

### <a name="311"></a>3.1.1
#### <a name="key-bug-fixes"></a>主要なバグ修正
* `ID` にパイプ文字 (|) が含まれていて `ChangeFeedMaxPagesPerBatch` 構成が適用されているストリーミング チェックポイントのエッジ ケースを修正。

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>新機能
* 入れ子になったパーティション キーが使用されている場合の一括更新のサポートを追加。
* Azure Cosmos DB への書き込み時の Decimal データ型と Float データ型のサポートを追加。
* 値として Long (Unix エポック) を使用している場合の Timestamp 型のサポートを追加。

### <a name="308"></a>3.0.8
#### <a name="key-bug-fixes"></a>主要なバグ修正
* `WriteThroughputBudget` 構成が使用されている場合に発生する型キャスト例外を修正。

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>新機能
* 例外とログに一括エラーのエラー情報を追加。

### <a name="306"></a>3.0.6
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ストリーミング チェックポイントの問題を修正。

### <a name="305"></a>3.0.5
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ノイズを減らすために、意図せずにレベル ERROR で残されていたメッセージのログ レベルを修正。

### <a name="304"></a>3.0.4
#### <a name="key-bug-fixes"></a>主要なバグ修正
* パーティション分割中の構造化ストリーミングのバグを修正。 このバグによって、一部の変更フィード レコードが見つからないか、チェックポイントの書き込みで Null 例外が発生する可能性があります。

### <a name="303"></a>3.0.3
#### <a name="key-bug-fixes"></a>主要なバグ修正
* readStream に指定されたカスタム スキーマが無視されるバグを修正。

### <a name="302"></a>3.0.2
#### <a name="key-bug-fixes"></a>主要なバグ修正
* ビルド時間が 50% 増加した回帰 (シェーディングされていない JAR に、シェーディングされたすべての依存関係が含まれる) を修正。

### <a name="301"></a>3.0.1
#### <a name="key-bug-fixes"></a>主要なバグ修正
* TCP 経由の直接転送が RequestTimeoutException で失敗する依存関係の問題を修正。

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>新機能
* メタデータ呼び出しの数を減らすために、接続管理と接続プーリングを改善。

## <a name="faq"></a>よく寄せられる質問
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>次のステップ

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) の詳細をご覧ください。

詳しくは、[Apache Spark](https://spark.apache.org/) に関するページをご覧ください。
