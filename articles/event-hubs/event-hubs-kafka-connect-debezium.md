---
title: 変更データ キャプチャ用に Azure Event Hubs の Apache Kafka Connect を Debezium と統合する
description: この記事では、Kafka 用 Azure Event Hubs で Debezium を使用する方法について取り上げます。
ms.topic: how-to
author: abhirockzz
ms.author: abhishgu
ms.date: 01/06/2021
ms.openlocfilehash: 0ad1df23e71e652f7d380ffbabb542b81954e038
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935174"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-with-debezium-for-change-data-capture"></a>変更データ キャプチャ用に Azure Event Hubs の Apache Kafka Connect のサポートを Debezium と統合する

**変更データ キャプチャ (CDC)** は、作成、更新、削除操作に応答して、データベース テーブル内の行レベルの変更を追跡するために使用される手法です。 [Debezium](https://debezium.io/) は、さまざまなデータベースで使用できる変更データ キャプチャ機能 ([PostgreSQL の論理デコード](https://www.postgresql.org/docs/current/static/logicaldecoding-explanation.html)など) に基づいて構築された分散プラットフォームです。 これにより、データベース テーブル内の行レベルの変更を取得し、それを後で [Apache Kafka](https://kafka.apache.org/) に送信されるイベント ストリームに変換する一連の [Kafka Connect コネクタ](https://debezium.io/documentation/reference/1.2/connectors/index.html)が提供されます。

> [!WARNING]
> Apache Kafka Connect フレームワークと Debezium プラットフォームおよびそのコネクタを共に使用することは、**Microsoft Azure 経由の製品サポートの対象外です**。
>
> Apache Kafka Connect では、その動的構成が、通常であれば無制限の保持期間を持つ圧縮されたトピックに保持されていることを前提としています。 Azure Event Hubs では[圧縮がブローカー機能として実装されていないため](event-hubs-federation-overview.md#log-projections)、保持されるイベントに時間ベースの保持期間の制限が常に課されます。これは、Azure Event Hubs が長期間のデータまたは構成ストアではなく、リアルタイムのイベント ストリーミング エンジンであるという原則から来ています。
>
> Apache Kafka プロジェクトではこれらの役割が混同されていても問題ないかもしれませんが、Azure では、このような情報は適切なデータベースまたは構成ストアで管理するのが最適であると考えられています。
>
> Apache Kafka Connect のシナリオの多くは正常に機能しますが、Apache Kafka と Azure Event Hubs の保持モデル間のこれらの概念的な違いのために、特定の構成が期待どおりに機能しなくなる可能性があります。 

このチュートリアルでは、[Azure Event Hubs](./event-hubs-about.md?WT.mc_id=devto-blog-abhishgu) (Kafka 用)、[Azure DB for PostgreSQL](../postgresql/overview.md)、Debezium を使用して、Azure で変更データ キャプチャ ベースのシステムを設定する方法について説明します。 ここでは、PostgreSQL のデータベース変更を Azure Event Hubs の Kafka トピックにストリーミングするために [Debezium PostgreSQL コネクタ](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html)を使用します。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "*ホワイトリスト*" という用語への言及があります。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * Azure Database for PostgreSQL を設定および構成する
> * Debezium PostgreSQL コネクタを使用して Kafka Connect を構成して実行する
> * 変更データ キャプチャをテストする
> * (省略可能) `FileStreamSink` コネクタを使用して変更データ イベントを消費する

## <a name="pre-requisites"></a>前提条件
このチュートリアルを完了するには、次のものが必要になります。

- Azure のサブスクリプション。 アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- Linux/MacOS
- Kafka リリース (バージョン 1.1.1、Scala バージョン 2.11)。[kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) から入手できます。
- [Apache Kafka 用 Event Hubs](./event-hubs-for-kafka-ecosystem-overview.md) の概要に関する記事を読む。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
Event Hubs サービスとの間で送受信を行うには、イベント ハブの名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 Event Hubs の接続文字列と完全修飾ドメイン名 (FQDN) を、後で使用するために取得します。 手順については、「[Get an Event Hubs connection string (Event Hubs の接続文字列を取得する)](event-hubs-get-connection-string.md)」を参照してください。 

## <a name="setup-and-configure-azure-database-for-postgresql"></a>Azure Database for PostgreSQL を設定および構成する
[Azure Database for PostgreSQL](../postgresql/overview.md) は、オープン ソースの PostgreSQL データベース エンジンのコミュニティ バージョンに基づいたリレーショナル データベース サービスであり、2 つのデプロイ オプションである Single Server と Hyperscale (Citus) で使用できます。 Azure portal を使用して Azure Database for PostgreSQL サーバーを作成するには、[この手順](../postgresql/quickstart-create-server-database-portal.md)に従ってください。 

## <a name="setup-and-run-kafka-connect"></a>Kafka Connect を設定して実行する
このセクションでは、次のトピックについて説明します。

- Debezium コネクタのインストール
- Event Hubs 用の Kafka Connect の構成
- Debezium コネクタを使用した Kafka Connect クラスターの起動

### <a name="download-and-setup-debezium-connector"></a>Debezium コネクタをダウンロードして設定する
このコネクタをダウンロードして設定するには、[Debezium のドキュメント](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-deploying-a-connector)にある最新の手順に従ってください。

- コネクタのプラグイン アーカイブをダウンロードします。 たとえば、コネクタのバージョン `1.2.0` をダウンロードするには、リンク https://repo1.maven.org/maven2/io/debezium/debezium-connector-postgres/1.2.0.Final/debezium-connector-postgres-1.2.0.Final-plugin.tar.gz を使用します。
- JAR ファイルを抽出し、それを [Kafka Connect の plugin.path](https://kafka.apache.org/documentation/#connectconfigs) にコピーします。


### <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs 用に Kafka Connect を構成する
Kafka Connect のスループットを Kafka から Event Hubs にリダイレクトする際に、最小限の再構成が必要となります。  次の `connect-distributed.properties` サンプルは、Event Hubs 上の Kafka エンドポイントに対して認証と通信を行うように Connect を構成する方法を示しています。

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}` を Event Hubs 名前空間への接続文字列に置き換えます。 接続文字列を取得する手順については、「[Event Hubs の接続文字列の取得](event-hubs-get-connection-string.md)」を参照してください。 構成の例には、`sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";` などがあります。


### <a name="run-kafka-connect"></a>Kafka Connect を実行する
この手順では、Event Hubs を使用し、Kafka Connect ワーカーをローカルから分散モードで開始して、クラスターの状態を維持します。

1. 前出の `connect-distributed.properties` ファイルをローカルに保存します。  中かっこで囲んだ値はすべて置き換えてください。
2. お使いのマシン上にある Kafka リリースの場所に移動します。
3. `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` を実行し、クラスターが起動するまで待ちます。

> [!NOTE]
> Kafka Connect は、Kafka AdminClient API を使用して、圧縮などの推奨される構成を含むトピックを自動的に作成します。 Azure portal で名前空間をざっとチェックすると、Connect ワーカーの内部的なトピックが自動的に作成されていることがわかります。
>
> Kafka Connect の内部トピックでは、**圧縮を使用する必要があります**。  Connect の内部トピックが正しく構成されていない場合、Event Hubs チームでは不適切な構成を修正する責任を負いません。

### <a name="configure-and-start-the-debezium-postgresql-source-connector"></a>Debezium PostgreSQL ソース コネクタを構成して起動する

PostgreSQL ソース コネクタの構成ファイル (`pg-source-connector.json`) を作成し、Azure PostgreSQL インスタンスに従って値を置き換えます。

```json
{
    "name": "todo-connector",
    "config": {
        "connector.class": "io.debezium.connector.postgresql.PostgresConnector",
        "database.hostname": "<replace with Azure PostgreSQL instance name>.postgres.database.azure.com",
        "database.port": "5432",
        "database.user": "<replace with database user name>",
        "database.password": "<replace with database password>",
        "database.dbname": "postgres",
        "database.server.name": "my-server",
        "plugin.name": "wal2json",
        "table.whitelist": "public.todos"
    }
}
```

> [!TIP]
> `database.server.name` 属性は、監視される特定の PostgreSQL データベース サーバー/クラスターの名前空間を識別および提供する論理名です。 詳細については、[Debezium のドキュメント](https://debezium.io/documentation/reference/1.2/connectors/postgresql.html#postgresql-property-database-server-name)を確認してください。

コネクタのインスタンスを作成するには、Kafka Connect の REST API エンドポイントを使用します。

```bash
curl -X POST -H "Content-Type: application/json" --data @pg-source-connector.json http://localhost:8083/connectors
```

コネクタの状態を確認するには、次のようにします。

```bash
curl -s http://localhost:8083/connectors/todo-connector/status
```

## <a name="test-change-data-capture"></a>変更データ キャプチャをテストする
動作中の変更データ キャプチャを確認するには、Azure PostgreSQL データベースのレコードを作成/更新/削除する必要があります。

まず、Azure PostgreSQL データベースに接続します (次の例では [psql](https://www.postgresql.org/docs/12/app-psql.html) を使用します)。

```bash
psql -h <POSTGRES_INSTANCE_NAME>.postgres.database.azure.com -p 5432 -U <POSTGRES_USER_NAME> -W -d <POSTGRES_DB_NAME> --set=sslmode=require

e.g. 

psql -h my-postgres.postgres.database.azure.com -p 5432 -U testuser@my-postgres -W -d postgres --set=sslmode=require
```

**テーブルを作成し、レコードを挿入する**

```sql
CREATE TABLE todos (id SERIAL, description VARCHAR(50), todo_status VARCHAR(10), PRIMARY KEY(id));

INSERT INTO todos (description, todo_status) VALUES ('setup postgresql on azure', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('setup kafka connect', 'complete');
INSERT INTO todos (description, todo_status) VALUES ('configure and install connector', 'in-progress');
INSERT INTO todos (description, todo_status) VALUES ('start connector', 'pending');
```

これでコネクタが動作を開始し、`my-server.public.todos` という名前を持つ Event Hubs トピックに変更データ イベントを送信します。ここで、`my-server` は `database.server.name` の値であり、`public.todos` は (`table.whitelist` 構成に従って) 変更を追跡しているテーブルであるとします。

**Event Hubs トピックを確認する**

すべてが期待どおりに動作していることを確認するために、このトピックの内容を調べてみましょう。 次の例では [`kafkacat`](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) を使用しますが、[ここに一覧表示されているいずれかのオプションを使用してコンシューマーを作成する](apache-kafka-developer-guide.md)こともできます。

次の内容を含む `kafkacat.conf` という名前のファイルを作成します。

```
metadata.broker.list=<enter event hubs namespace>.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanisms=PLAIN
sasl.username=$ConnectionString
sasl.password=<enter event hubs connection string>
```

> [!NOTE]
> `kafkacat.conf` 内の `metadata.broker.list` および `sasl.password` 属性を Event Hubs の情報に従って更新してください。 

別のターミナルで、コンシューマーを起動します。

```bash
export KAFKACAT_CONFIG=kafkacat.conf
export BROKER=<enter event hubs namespace>.servicebus.windows.net:9093
export TOPIC=my-server.public.todos

kafkacat -b $BROKER -t $TOPIC -o beginning
```

`todos` テーブルに追加した行に応答して PostgreSQL で生成された変更データ イベントを表す JSON ペイロードが表示されます。 そのペイロードのスニペットを次に示します。


```json
{
    "schema": {...},
    "payload": {
        "before": null,
        "after": {
            "id": 1,
            "description": "setup postgresql on azure",
            "todo_status": "complete"
        },
        "source": {
            "version": "1.2.0.Final",
            "connector": "postgresql",
            "name": "fullfillment",
            "ts_ms": 1593018069944,
            "snapshot": "last",
            "db": "postgres",
            "schema": "public",
            "table": "todos",
            "txId": 602,
            "lsn": 184579736,
            "xmin": null
        },
        "op": "c",
        "ts_ms": 1593018069947,
        "transaction": null
    }
```

このイベントは `payload` とその `schema` (簡潔にするために省略しています) で構成されています。 `payload` セクションでは、作成操作 (`"op": "c"`) がどのように表されているかに注意してください。たとえば、`"before": null` は、それが新しく `INSERT` された行であったことを示し、`after` はその行内の列の値を示し、`source` は、このイベントが選択された元の PostgreSQL インスタンス メタデータを示しています。

更新または削除操作でも同じことを行い、変更データ イベントを調べてみることができます。 たとえば、`configure and install connector` (`id` を `3` とします) のタスクの状態を更新するには、次のようにします。

```sql
UPDATE todos SET todo_status = 'complete' WHERE id = 3;
```

## <a name="optional-install-filestreamsink-connector"></a>(省略可能) FileStreamSink コネクタをインストールする
これで `todos` テーブルのすべての変更が Event Hubs トピックにキャプチャされたので、FileStreamSink コネクタ (Kafka Connect では既定で使用可能) を使用して、これらのイベントを消費します。

コネクタの構成ファイル (`file-sink-connector.json`) を作成し、ファイル システムに従って `file` 属性を置き換えます。

```json
{
    "name": "cdc-file-sink",
    "config": {
        "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
        "tasks.max": "1",
        "topics": "my-server.public.todos",
        "file": "<enter full path to file e.g. /Users/foo/todos-cdc.txt>"
    }
}
```

コネクタを作成し、その状態を確認するには、次のようにします。

```bash
curl -X POST -H "Content-Type: application/json" --data @file-sink-connector.json http://localhost:8083/connectors

curl http://localhost:8083/connectors/cdc-file-sink/status
```

データベース レコードを挿入/更新/削除し、構成された出力シンク ファイルでこれらのレコードを監視します。

```bash
tail -f /Users/foo/todos-cdc.txt
```


## <a name="cleanup"></a>クリーンアップ
Kafka Connect は、イベント ハブのトピックを作成することによって、Connect クラスターが停止した後も永続的に存在する構成、オフセット、状態を格納します。 この永続化が不要である場合は、これらのトピックを削除することをお勧めします。 また、このチュートリアルの過程で作成された `my-server.public.todos` イベント ハブを削除することもできます。

## <a name="next-steps"></a>次のステップ

Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)