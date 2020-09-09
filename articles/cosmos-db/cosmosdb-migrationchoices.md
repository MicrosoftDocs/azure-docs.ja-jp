---
title: Cosmos DB の移行オプション
description: このドキュメントでは、オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのさまざまなオプションについて説明します
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/01/2020
ms.openlocfilehash: 02a2880c42d4eda42c1a96cfaafb04c85358ccb5
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321278"
---
# <a name="options-to-migrate-your-on-premises-or-cloud-data-to-azure-cosmos-db"></a>オンプレミスまたはクラウドのデータを Azure Cosmos DB に移行するためのオプション

さまざまなデータ ソースから Azure Cosmos DB にデータを読み込むことができます。 さらに、Azure Cosmos DB は複数の API をサポートしているため、ターゲットには既存のあらゆる API を指定できます。 さまざまなソースからさまざまな Azure Cosmos DB API への移行パスをサポートする目的で、移行パスごとに特別な処理を行うソリューションが複数用意されています。 このドキュメントでは、利用できるソリューションをリストアップし、その利点と制限事項について説明します。

## <a name="factors-affecting-the-choice-of-migration-tool"></a>移行ツールの選択に影響を与える要因

移行ツールの選択は次の要因によって決定されます。
* **オンライン移行かオフライン移行:** 移行ツールの多くで、1 回限りの移行を行うためのパスが与えられます。 これは、データベースにアクセスしているアプリケーションに一定時間のダウンタイムが発生する可能性を意味します。 一部の移行ソリューションでは、ライブ移行の手段が与えられます。ライブ移行では、移行元と移行先の間にレプリケーション パイプラインが設定されます。

* **データ ソース**:既存のデータは、Oracle DB2、Datastax Cassandra、Azure SQL Database、PostgreSQL など、さまざまなデータ ソースに含まれています。データは既存の Azure Cosmos DB アカウントに含まれていることもあります。データ モデルを変更することや、パーティション キーの異なるコンテナーに含まれるデータを再パーティションすることが移行の意図になることもあります。

* **Azure Cosmos DB API**:Azure Cosmos DB の SQL API に対しては、さまざまな移行シナリオを支援するさまざまなツールが Azure Cosmos DB チームによって開発されています。 他の API にはすべて、コミュニティによって開発され、保守されている独自の特別なツール セットが含まれています。 Azure Cosmos DB ではそのような API がワイヤ プロトコル レベルでサポートされているため、Azure Cosmos DB にデータを移行する間、それらのツールもそのまま動作するはずです。 ただし、この概念は Azure Cosmos DB に固有であるため、スロットルのカスタム処理が必要になることがあります。

* **データのサイズ**:データセットが小規模の場合、ほとんどの移行ツールは非常に快適に動作します。 データセットが数百ギガ バイトを超えると、移行ツールの選択肢が限られます。 

* **予想される移行時間**:移行は消費するスループットの少ない、ゆっくりしたインクリメンタル ペースで行うように構成するか、移行先 Azure Cosmos DB コンテナーでプロビジョニングされているスループット全体を消費し、少ない時間で移行を完了するように構成できます。

## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API

|**移行の種類**|**ソリューション**|**考慮事項**|
|---------|---------|---------|
|オフライン|[データ移行ツール](import-data.md)|&bull; セットアップが簡単で、さまざまなソースをサポートします <br/>&bull; 大規模なデータセットには適していません|
|オフライン|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; セットアップが簡単で、さまざまなソースをサポートします <br/>&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適しています <br/>&bull; チェックポイントがありません - つまり、移行の途中で問題が発生した場合、移行プロセスを全部やり直す必要があります<br/>&bull; 配信不能キューがありません - つまり、エラーが含まれるファイルがいくつかあると、移行プロセス全体が停止することがあります|
|オフライン|[Azure Cosmos DB Spark コネクタ](spark-connector.md)|&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適しています <br/>&bull; カスタム Spark セットアップが必要です <br/>&bull; Spark ではスキーマの不整合に注意を要し、移行中、問題になることがあります |
|オフライン|[カスタム ツールと Cosmos DB Bulk Executor ライブラリ](migrate-cosmosdb-data.md)|&bull; 移行の回復性を向上させるチェックポイント機能や配信不能機能を提供します <br/>&bull; 非常に大規模なデータセット (10 TB 以上) に適しています  <br/>&bull; App Service として実行するこのツールのカスタム セットアップが必要になります |
|オンライン|[Cosmos DB Functions + ChangeFeed API](change-feed-functions.md)|&bull; セットアップが簡単 <br/>&bull; 移行元が Azure Cosmos DB コンテナーの場合にのみ動作します <br/>&bull; 大規模なデータセットには適していません <br/>&bull; 移行元コンテナーからの削除をキャプチャすることはありません |
|オンライン|[ChangeFeed を利用したカスタム移行サービス](https://github.com/nomiero/CosmosDBLiveETLSample)|&bull; 進捗状況を追跡します <br/>&bull; 移行元が Azure Cosmos DB コンテナーの場合にのみ動作します <br/>&bull; 大規模なデータセットでも機能します <br/>&bull; ChangeFeed プロセッサをホストする目的で App Service を設定することがユーザーに求められます <br/>&bull; 移行元コンテナーからの削除をキャプチャすることはありません|
|オンライン|[Striim](cosmosdb-sql-api-migrate-data-striim.md)|&bull; Oracle、DB2、SQL Server などのさまざまなソースで動作します <br/>&bull; ETL パイプラインの構築が簡単で、監視用のダッシュボードを提供します <br/>&bull; 大規模なデータセットをサポートします <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります|

## <a name="azure-cosmos-db-mongo-api"></a>Azure Cosmos DB Mongo API

|**移行の種類**|**ソリューション**|**考慮事項**|
|---------|---------|---------|
|オンライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します <br/>&bull; 他の MongoDB ソースでのみ機能します|
|オフライン|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適し、ライブ変更のレプリケーションを処理します <br/>&bull; 他の MongoDB ソースでのみ機能します|
|オフライン|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; セットアップが簡単で、さまざまなソースをサポートします <br/>&bull; Azure Cosmos DB Bulk Executor ライブラリを使用します <br/>&bull; 大規模なデータセットに適しています <br/>&bull; チェックポイントがなく、移行の途中で問題が発生した場合、移行プロセスを全部やり直す必要があります<br/>&bull; 配信不能キューがなく、エラーが含まれるファイルがいくつかあると、移行プロセス全体が停止することがあります <br/>&bull; 特定のデータ ソースの読み取りスループットを向上させるためのカスタム コードが必要です|
|オフライン|[既存の Mongo ツール (mongodump、mongorestore、Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; セットアップと統合が簡単 <br/>&bull; スロットルのカスタム処理が必要です|


## <a name="azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API

|**移行の種類**|**ソリューション**|**考慮事項**|
|---------|---------|---------|
|オフライン|[cqlsh COPY コマンド](cassandra-import-data.md#migrate-data-using-cqlsh-copy-command)|&bull; セットアップが簡単 <br/>&bull; 大規模なデータセットには適していません <br/>&bull; ソースが Cassandra テーブルの場合にのみ機能します|
|オフライン|[Spark でテーブルをコピーする](cassandra-import-data.md#migrate-data-using-spark) |&bull; Spark 機能を使用し、変換と取り込みを並列処理できます <br/>&bull; スロットルを処理するカスタム再試行ポリシーを含む構成が必要です|
|オンライン|[Striim (Oracle DB/Apache Cassandra からの)](cosmosdb-cassandra-api-migrate-data-striim.md)|&bull; Oracle、DB2、SQL Server などのさまざまなソースで動作します <br/>&bull; ETL パイプラインの構築が簡単で、監視用のダッシュボードを提供します <br/>&bull; 大規模なデータセットをサポートします <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります|
|オンライン|[Blitzz (Oracle DB/Apache Cassandra からの)](oracle-migrate-cosmos-db-blitzz.md)|<br/>&bull; 大規模なデータセットをサポートします <br/>&bull; これはサードパーティ製のツールであるため、マーケットプレースから購入し、ユーザーの環境にインストールする必要があります|

## <a name="other-apis"></a>その他の API

SQL API、Mongo API、Cassandra API 以外の API については、API の既存エコシステム別にさまざまなツールがサポートされています。 

**テーブル API** 

* [データ移行ツール](table-import.md#data-migration-tool)
* [AzCopy](table-import.md#migrate-data-by-using-azcopy)

**Gremlin API**

* [Graph Bulk Executor ライブラリ](bulk-executor-graph-dotnet.md)
* [Gremlin Spark](https://github.com/Azure/azure-cosmosdb-spark/blob/2.4/samples/graphframes/main.scala) 

## <a name="next-steps"></a>次のステップ

* [.NET](bulk-executor-dot-net.md) と [Java](bulk-executor-java.md) で Bulk Executor ライブラリを使用するサンプル アプリケーションを試して、さらに詳しく学習します。 
* Bulk Executor ライブラリは Cosmos DB Spark コネクタに統合されています。詳細については、[Azure Cosmos DB Spark コネクタ](spark-connector.md)に関する記事をご覧ください。  
* 大規模な移行に関して別途支援が必要な場合は、問題のタイプに "General Advisory (一般的な勧告)" を、問題のサブタイプに "Large (TB+) migrations (大規模な (TB 以上の) 移行)" を選択してサポート チケットを開き、Azure Cosmos DB 製品チームに連絡します。
