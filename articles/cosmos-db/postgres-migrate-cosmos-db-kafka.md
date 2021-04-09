---
title: Apache Kafka を使用して PostgreSQL から Azure Cosmos DB Cassandra API アカウントにデータを移行する
description: Kafka Connect を使用して PostgreSQL のデータをリアルタイムで Azure Cosmos DB Cassandra API に同期する方法について説明します。
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 01/05/2021
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 0038219ee8c1721ff5ab2be76231d33d2bd9064d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98203067"
---
# <a name="migrate-data-from-postgresql-to-azure-cosmos-db-cassandra-api-account-using-apache-kafka"></a>Apache Kafka を使用して PostgreSQL から Azure Cosmos DB Cassandra API アカウントにデータを移行する
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB の Cassandra API は、次のようなさまざまな理由により、Apache Cassandra 上で実行されているエンタープライズ ワークロードに適した選択肢になりました。

* **大幅なコスト削減:** Azure Cosmos DB によってコストを節約できます。これには、VM、帯域幅、適用されるすべての Oracle ライセンスのコストが含まれます。 さらに、データ センター、サーバー、SSD ストレージ、ネットワーク、電気代を管理する必要がありません。

* **より優れたスケーラビリティと可用性:** 単一障害点がなくなり、スケーラビリティとお使いのアプリケーションの可用性が向上します。

* **管理と監視のオーバーヘッドなし:** Azure Cosmos DB は、フル マネージドのクラウド サービスとして、無数の設定を管理および監視する際のオーバーヘッドを解消します。

[Kafka Connect](https://kafka.apache.org/documentation/#connect) は、[Apache Kafka](https://kafka.apache.org/) とその他のシステムとの間で、スケーラブルで信頼性の高い方法でデータをストリーム配信するためのプラットフォームです。 いくつかの既製のコネクタがサポートされているため、外部システムを Apache Kafka と統合するのにカスタム コードは不要です。

この記事では、Kafka コネクタの組み合わせを使用して、[PostgreSQL](https://www.postgresql.org/) などのリレーショナル データベースのレコードを [Azure Cosmos DB Cassandra API](cassandra-introduction.md) に継続的に同期するためにデータ パイプラインを設定する方法を示します。

## <a name="overview"></a>概要

この記事で紹介するエンド ツー エンドのフローの概要を次に示します。

PostgreSQL テーブルのデータは、Kafka Connect **ソース** コネクタである [Debezium PostgreSQL コネクタ](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)を使用して Apache Kafka にプッシュされます。 PostgreSQL テーブル内のレコードの挿入、更新、削除は `change data` イベントとして取得され、Kafka トピックに送信されます。 [DataStax Apache Kafka コネクタ](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) (Kafka Connect **シンク** コネクタ) は、パイプラインの 2 番目の部分を形成します。 これにより、Kafka トピックの変更データ イベントが Azure Cosmos DB Cassandra API テーブルに同期されます。

> [!NOTE]
> DataStax Apache Kafka コネクタに固有の機能を使用すると、複数のテーブルにデータをプッシュできます。 この例では、当該コネクタを使用して、さまざまなクエリ要件をサポートできる 2 つの Cassandra テーブルに変更データ レコードを保持できるようにします。

## <a name="prerequisites"></a>前提条件

* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](create-cassandra-dotnet.md#create-a-database-account)
* [cqlsh またはホストされたシェルを使用して検証する](cassandra-support.md#hosted-cql-shell-preview)
* JDK 8 以上
* [Docker](https://www.docker.com/) (省略可能)

## <a name="base-setup"></a>基本的な設定

### <a name="set-up-postgresql-database-if-you-havent-already"></a>PostgreSQL データベースをまだ設定していない場合は設定します。 

これは、既存のオンプレミス データベースでも、ローカル コンピューターに[ダウンロードしてインストールしたもの](https://www.postgresql.org/download/)でも構いません。 また、[Docker コンテナー](https://hub.docker.com/_/postgres)を使用することもできます。

コンテナーを開始するには、次のようにします。

```bash
docker run --rm -p 5432:5432 -e POSTGRES_PASSWORD=<enter password> postgres
```

[`psql`](https://www.postgresql.org/docs/current/app-psql.html) クライアントを使用して PostgreSQL インスタンスに接続します。

```bash
psql -h localhost -p 5432 -U postgres -W -d postgres
```

サンプルの注文情報を格納するテーブルを作成します。

```sql
CREATE SCHEMA retail;

CREATE TABLE retail.orders_info (
    orderid SERIAL NOT NULL PRIMARY KEY,
    custid INTEGER NOT NULL,
    amount INTEGER NOT NULL,
    city VARCHAR(255) NOT NULL,
    purchase_time VARCHAR(40) NOT NULL
);
```

### <a name="using-the-azure-portal-create-the-cassandra-keyspace-and-the-tables-required-for-the-demo-application"></a>Azure portal を使用して、デモ アプリケーションに必要な Cassandra キースペースとテーブルを作成します。

> [!NOTE]
> 以下のように、同じキースペースとテーブル名を使用してください

```sql
CREATE KEYSPACE retail WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE retail.orders_by_customer (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (customer_id, purchase_time)) WITH CLUSTERING ORDER BY (purchase_time DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE retail.orders_by_city (order_id int, customer_id int, purchase_amount int, city text, purchase_time timestamp, PRIMARY KEY (city,order_id)) WITH cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

### <a name="setup-apache-kafka"></a>Apache Kafka を設定する 

この記事ではローカル クラスターを使用しますが、他のオプションを選択することもできます。 [Kafka をダウンロードして](https://kafka.apache.org/downloads)解凍し、Zookeeper と Kafka クラスターを開始します。

```bash
cd <KAFKA_HOME>/bin

#start zookeeper
bin/zookeeper-server-start.sh config/zookeeper.properties

#start kafka (in another terminal)
bin/kafka-server-start.sh config/server.properties
```

### <a name="setup-connectors"></a>コネクタを設定する

Debezium PostgreSQL および DataStax Apache Kafka のコネクタをインストールします。 Debezium PostgreSQL コネクタのプラグイン アーカイブをダウンロードします。 たとえば、(執筆時点で最新の) バージョン 1.3.0 のコネクタをダウンロードするには、[こちらのリンク](https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.3.0.Final/debezium-connector-postgres-1.3.0.Final-plugin.tar.gz)を使用します。 DataStax Apache Kafka コネクタは、[こちらのリンク](https://downloads.datastax.com/#akc)からダウンロードします。

両方のコネクタ アーカイブを解凍し、JAR ファイルを [Kafka Connect plugin.path](https://kafka.apache.org/documentation/#connectconfigs) にコピーします。


```bash
cp <path_to_debezium_connector>/*.jar <KAFKA_HOME>/libs
cp <path_to_cassandra_connector>/*.jar <KAFKA_HOME>/libs
```

> 詳細については、[Debezium](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector) および [DataStax](https://docs.datastax.com/en/kafka/doc/) のドキュメントを参照してください。

## <a name="configure-kafka-connect-and-start-data-pipeline"></a>Kafka Connect を構成してデータ パイプラインを開始する

### <a name="start-kafka-connect-cluster"></a>Kafka Connect クラスターを開始する

```bash
cd <KAFKA_HOME>/bin
./connect-distributed.sh ../config/connect-distributed.properties
```

### <a name="start-postgresql-connector-instance"></a>PostgreSQL コネクタ インスタンスを開始する

コネクタ構成 (JSON) をファイルの例 `pg-source-config.json` に保存します

```json
{
    "name": "pg-orders-source",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "localhost",
        "database.port": "5432",
        "database.user": "postgres",
        "database.password": "password",
        "database.dbname": "postgres",
        "database.server.name": "myserver",
        "plugin.name": "wal2json",
        "table.include.list": "retail.orders_info",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter"
    }
}
```

PostgreSQL コネクタ インスタンスを開始するには、次のようにします。

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-config.json http://localhost:8083/connectors
```

> [!NOTE]
> 削除するには、`curl -X DELETE http://localhost:8083/connectors/pg-orders-source` を使用します。


### <a name="insert-data"></a>データの挿入

`orders_info` テーブルには、注文 ID、顧客 ID、市区町村などの注文の詳細が含まれています。下の SQL を使用して、このテーブルにランダムなデータを設定します。

```sql
insert into retail.orders_info (
    custid, amount, city, purchase_time
)
select
    random() * 10000 + 1,
    random() * 200,
    ('{New Delhi,Seattle,New York,Austin,Chicago,Cleveland}'::text[])[ceil(random()*3)],
    NOW() + (random() * (interval '1 min'))
from generate_series(1, 10) s(i);
```

10 件のレコードがテーブルに挿入されます。 ご自分の要件に従って、下部にある `generate_series(1, 10)` のレコード数を必ず更新してください。たとえば、`100` 件のレコードを挿入するには、`generate_series(1, 100)` を使用します

確認するには、次のようにします。

```bash
select * from retail.orders_info;
```

Kafka トピックの変更データ キャプチャ イベントを確認します

> [!NOTE]
> トピック名は、[コネクタの規則](https://debezium.io/documentation/reference/1.3/connectors/postgresql.html#postgresql-topic-names)に従った `myserver.retail.orders_info` であることに注意してください

```bash
cd <KAFKA_HOME>/bin

./kafka-console-consumer.sh --topic myserver.retail.orders_info --bootstrap-server localhost:9092 --from-beginning
```

変更データ イベントが JSON 形式で表示されます。

### <a name="start-datastax-apache-kafka-connector-instance"></a>DataStax Apache Kafka コネクタ インスタンスを開始する

コネクタ構成 (JSON) をファイルの例、`cassandra-sink-config.json` に保存し、お使いの環境に合わせてプロパティを更新します。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "myserver.retail.orders_info",
        "contactPoints": "<Azure Cosmos DB account name>.cassandra.cosmos.azure.com",
        "loadBalancing.localDc": "<Azure Cosmos DB region e.g. Southeast Asia>",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "<path to JDK keystore path e.g. <JAVA_HOME>/jre/lib/security/cacerts>",
        "ssl.keystore.password": "<keystore password: it is 'changeit' by default>",
        "port": 10350,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "auth.username": "<Azure Cosmos DB user name (same as account name)>",
        "auth.password": "<Azure Cosmos DB password>",
        "topic.myserver.retail.orders_info.retail.orders_by_customer.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "topic.myserver.retail.orders_info.retail.orders_by_city.mapping": "order_id=value.orderid, customer_id=value.custid, purchase_amount=value.amount, city=value.city, purchase_time=value.purchase_time",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "transforms": "unwrap",
        "transforms.unwrap.type": "io.debezium.transforms.ExtractNewRecordState",
        "offset.flush.interval.ms": 10000
    }
}
```

コネクタ インスタンスを開始するには、次のようにします。

```bash
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

コネクタが動作を開始し、PostgreSQL から Azure Cosmos DB へのエンド ツー エンドのパイプラインが動作可能になります。

### <a name="query-azure-cosmos-db"></a>Azure Cosmos DB に対してクエリを実行する

Azure Cosmos DB の Cassandra テーブルを確認します。 試すことができるクエリをいくつか次に示します。

```sql
select count(*) from retail.orders_by_customer;
select count(*) from retail.orders_by_city;

select * from retail.orders_by_customer;
select * from retail.orders_by_city;

select * from retail.orders_by_city where city='Seattle';
select * from retail.orders_by_customer where customer_id = 10;
```

引き続き PostgreSQL にさらにデータを挿入し、レコードが Azure Cosmos DB に同期されていることを確認できます。

## <a name="next-steps"></a>次のステップ

* [Kafka Connect を使用して Apache Kafka と Azure Cosmos DB Cassandra API を統合する](cassandra-kafka-connect.md)
* [変更データ キャプチャ用に Azure Event Hubs の Apache Kafka Connect (プレビュー) を Debezium と統合する](../event-hubs/event-hubs-kafka-connect-debezium.md)
* [Blitzz を使用して Oracle から Azure Cosmos DB Cassandra API にデータを移行する](oracle-migrate-cosmos-db-blitzz.md)
* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md) 
* [パーティション キーのベスト プラクティス](partitioning-overview.md#choose-partitionkey)
* 「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事

