---
title: Kafka Connect を使用して Apache Kafka と Azure Cosmos DB Cassandra API を統合する
description: DataStax Apache Kafka Connector を使用して Kafka から Azure Cosmos DB Cassandra API にデータを取り込む方法について説明します
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 12/14/2020
ms.author: abhishgu
ms.reviewer: abhishgu
ms.openlocfilehash: 25972ba2bb30c39838c4822a42af292e8d8b1dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803631"
---
# <a name="ingest-data-from-apache-kafka-into-azure-cosmos-db-cassandra-api-using-kafka-connect"></a>Kafka Connect を使用して Apache Kafka から Azure Cosmos DB Cassandra API にデータを取り込む
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

既存の Cassandra アプリケーションでは、[CQLv4 ドライバーの互換性](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)により、[Azure Cosmos DB Cassandra API](cassandra-introduction.md) を簡単に操作できます。 この機能を利用して [Apache Kafka](https://kafka.apache.org/) などのストリーミング プラットフォームと統合し、データを Azure Cosmos DB に取り込みます。

Apache Kafka (トピック) のデータは、他のアプリケーションによって使用されたり他のシステムに取り込まれたりした場合にのみ役立ちます。 [選択した言語とクライアント SDK を使用して](https://cwiki.apache.org/confluence/display/KAFKA/Clients)、[Kafka Producer/Consumer](https://kafka.apache.org/documentation/#api) API を使用してソリューションを構築することができます。 Kafka Connect には代替のソリューションがあります。 これは、Apache Kafka とその他のシステムとの間で、スケーラブルで信頼性の高い方法でデータをストリーム配信するためのプラットフォームです。 Kafka Connect は Cassandra を含む市販のコネクタをサポートしているため、Kafka を Azure Cosmos DB Cassandra API と統合するためのカスタム コードを記述する必要はありません。 

この記事では、オープンソースの [DataStax Apache Kafka Connector](https://docs.datastax.com/en/kafka/doc/kafka/kafkaIntro.html) を使用します。これは Kafka Connect フレームワーク上で動作し、Kafka トピックから 1 つ以上の Cassandra テーブルの行にレコードを取り込みます。 この例は、Docker Compose を使用して再利用可能なセットアップを示しています。 これは、1 つのコマンドで必要なすべてのコンポーネントをローカルにブートストラップできるため、非常に便利です。 これらのコンポーネントには、Kafka、Zookeeper、Kafka Connect ワーカー、およびサンプル データ ジェネレーター アプリケーションなどがあります。

ここでは、コンポーネントとそのサービス定義の詳細について説明します。完全な `docker-compose` ファイルは [GitHub リポジトリ](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/docker-compose.yaml)で参照できます。

- Kafka と Zookeeper は [debezium](https://hub.docker.com/r/debezium/kafka/) イメージを使用します。
- Docker コンテナーとして実行するために、DataStax Apache Kafka Connector は既存の Docker イメージの上に組み込まれています ([debezium/connect-base](https://github.com/debezium/docker-images/tree/master/connect-base/1.2))。 このイメージには、Kafka とその Kafka Connect ライブラリのインストールが含まれているため、カスタム コネクタの追加が非常に便利です。 [Dockerfile](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/connector/Dockerfile) を参照してください。
- `data-generator` サービスは、ランダムに生成された (JSON) データを `weather-data` Kafka トピックにシードします。 コードと `Dockerfile` は [GitHub リポジトリ](https://github.com/Azure-Samples/cosmosdb-cassandra-kafka/blob/main/data-generator/)で参照できます

## <a name="prerequisites"></a>[前提条件]

* [Azure Cosmos DB Cassandra API アカウントのプロビジョニング](create-cassandra-dotnet.md#create-a-database-account)

* [cqlsh またはホストされたシェルを使用して検証する](cassandra-support.md#hosted-cql-shell-preview)

* [Docker](https://docs.docker.com/get-docker/) と [Docker Compose](https://docs.docker.com/compose/install) のインストール

## <a name="create-keyspace-tables-and-start-the-integration-pipeline"></a>キースペース、テーブルを作成し、統合パイプラインを開始する

Azure portal を使用して、デモ アプリケーションに必要な Cassandra キースペースとテーブルを作成します。

> [!NOTE]
> 以下のように、同じキースペースとテーブル名を使用してください

```sql
CREATE KEYSPACE weather WITH REPLICATION = {'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1};

CREATE TABLE weather.data_by_state (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (state, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;

CREATE TABLE weather.data_by_station (station_id text, temp int, state text, ts timestamp, PRIMARY KEY (station_id, ts)) WITH CLUSTERING ORDER BY (ts DESC) AND cosmosdb_cell_level_timestamp=true AND cosmosdb_cell_level_timestamp_tombstones=true AND cosmosdb_cell_level_timetolive=true;
```

GitHub レポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/cosmosdb-cassandra-kafka
cd cosmosdb-cassandra-kafka
```

すべてのサービスを開始します。

```shell
docker-compose --project-name kafka-cosmos-cassandra up --build
```

> [!NOTE]
> コンテナーをダウンロードして起動するのにしばらく時間がかかる場合があります。これは、1 回だけ実行するプロセスです。

すべてのコンテナーが開始されているかどうかを確認するには、次のようにします。

```shell
docker-compose -p kafka-cosmos-cassandra ps
```

データ ジェネレーター アプリケーションにより、Kafka の `weather-data` トピックへのデータのポンプが開始されます。 また、確認のために簡易サニティ チェックを実行することもできます。 Kafka Connect ワーカーを実行している Docker コンテナーを調べます。


```bash
docker exec -it kafka-cosmos-cassandra_cassandra-connector_1 bash
```

コンテナー シェルにドロップすると、通常の Kafka コンソールのコンシューマー プロセスを開始するだけで、気象データ (JSON 形式) が流れてくるのがわかるはずです。

```bash
cd ../bin
./kafka-console-consumer.sh --bootstrap-server kafka:9092 --topic weather-data
```

## <a name="cassandra-sink-connector-setup"></a>Cassandra Sink コネクタのセットアップ

次の JSON コンテンツをファイルにコピーします (これには `cassandra-sink-config.json` という名前を付けることができます)。 セットアップに従って更新する必要があります。このセクションの残りの部分では、このトピックに関するガイダンスを提供します。

```json
{
    "name": "kafka-cosmosdb-sink",
    "config": {
        "connector.class": "com.datastax.oss.kafka.sink.CassandraSinkConnector",
        "tasks.max": "1",
        "topics": "weather-data",
        "contactPoints": "<cosmos db account name>.cassandra.cosmos.azure.com",
        "port": 10350,
        "loadBalancing.localDc": "<cosmos db region e.g. Southeast Asia>",
        "auth.username": "<enter username for cosmosdb account>",
        "auth.password": "<enter password for cosmosdb account>",
        "ssl.hostnameValidation": true,
        "ssl.provider": "JDK",
        "ssl.keystore.path": "/etc/alternatives/jre/lib/security/cacerts/",
        "ssl.keystore.password": "changeit",
        "datastax-java-driver.advanced.connection.init-query-timeout": 5000,
        "maxConcurrentRequests": 500,
        "maxNumberOfRecordsInBatch": 32,
        "queryExecutionTimeout": 30,
        "connectionPoolLocalSize": 4,
        "topic.weather-data.weather.data_by_state.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "topic.weather-data.weather.data_by_station.mapping": "station_id=value.stationid, temp=value.temp, state=value.state, ts=value.created",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.json.JsonConverter",
        "value.converter.schemas.enable": false,
        "offset.flush.interval.ms": 10000
    }
}
```

属性の概要は次のとおりです。

**基本的な接続**

- `contactPoints`: Cosmos DB Cassandra のコンタクト ポイントを入力します
- `loadBalancing.localDc`: Cosmos DB アカウントのリージョン (例: 東南アジア) を入力します
- `auth.username`: ユーザー名を入力します
- `auth.password`: パスワードを入力します
- `port`: ポート値を入力します (これは `10350` であり、`9042` ではありません。 そのままにしてください)

**SSL の構成**

Azure Cosmos DB では [SSL 経由のセキュリティで保護された接続](database-security.md)が適用され、Kafka Connect コネクタでは SSL もサポートされます。

- `ssl.keystore.path`: コンテナー内の JDK キーストアへのパス- `/etc/alternatives/jre/lib/security/cacerts/`
- `ssl.keystore.password`:JDK キーストア (既定) のパスワード
- `ssl.hostnameValidation`:独自のノードのホスト名検証を有効にします
- `ssl.provider`: SSL プロバイダーとして `JDK` が使用されます

**ジェネリック パラメーター**

- `key.converter`:文字列コンバーター `org.apache.kafka.connect.storage.StringConverter` を使用します
- `value.converter`: Kafka トピックのデータは JSON であるため、`org.apache.kafka.connect.json.JsonConverter` を使用します
- `value.converter.schemas.enable`:JSON ペイロードには関連付けられたスキーマがないため (デモ アプリの目的のため)、この属性を `false` に設定して、スキーマを検索しないように Kafka Connect に指示する必要があります。 そうしないと、エラーが発生します。

### <a name="install-the-connector"></a>コネクタをインストールする

Kafka Connect REST エンドポイントを使用してコネクタをインストールします。

```shell
curl -X POST -H "Content-Type: application/json" --data @cassandra-sink-config.json http://localhost:8083/connectors
```

状態を確認するには、次の操作を行います。

```
curl http://localhost:8080/connectors/kafka-cosmosdb-sink/status
```

すべてがうまくいけば、コネクタがその魔法を紡ぎ始めるはずです。 Azure Cosmos DB に対して認証を行い、Kafka トピック (`weather-data`) から Cassandra テーブル (`weather.data_by_state` および `weather.data_by_station`) へのデータの取り込みを開始する必要があります

これでテーブル内のデータに対してクエリを実行できるようになりました。 Azure portal に移動し、Azure Cosmos DB アカウントのホステッド CQL シェルを起動します。

:::image type="content" source="./media/cassandra-kafka-connect/cqlsh.png" alt-text="CQLSH を開く":::

## <a name="query-data-from-azure-cosmos-db"></a>Azure Cosmos DB からデータのクエリを実行する

`data_by_state` および `data_by_station` テーブルを確認します。 開始するためのサンプル クエリを以下にいくつか示します。

```sql
select * from weather.data_by_state where state = 'state-1';
select * from weather.data_by_state where state IN ('state-1', 'state-2');
select * from weather.data_by_state where state = 'state-3' and ts > toTimeStamp('2020-11-26');

select * from weather.data_by_station where station_id = 'station-1';
select * from weather.data_by_station where station_id IN ('station-1', 'station-2');
select * from weather.data_by_station where station_id IN ('station-2', 'station-3') and ts > toTimeStamp('2020-11-26');
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

* [コンテナーとデータベースのスループットのプロビジョニング](set-throughput.md) 
* [パーティション キーのベスト プラクティス](partitioning-overview.md#choose-partitionkey)
* 「[Azure Cosmos DB Capacity Planner を使用して RU/秒を見積もる](estimate-ru-with-capacity-planner.md)」の記事
