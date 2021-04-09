---
title: Apache Kafka Connect との統合 - Azure Event Hubs | Microsoft Docs
description: この記事では、Kafka 用 Azure Event Hubs で Kafka Connect を使用する方法について説明します。
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935157"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Azure Event Hubs で Apache Kafka Connect のサポートを統合する
[Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) は、Kafka クラスターを通じて、MySQL や HDFS、ファイル システムなどの外部システムに接続し、それらとの間でデータをインポート/エクスポートするためのフレームワークです。 このチュートリアルでは、Event Hubs と共に Kafka Connect フレームワークを使用する方法について説明します。

> [!WARNING]
> Apache Kafka Connect フレームワークおよびそのコネクタを共に使用することは、**Microsoft Azure 経由の製品サポートの対象外です**。
>
> Apache Kafka Connect では、その動的構成が、通常であれば無制限の保持期間を持つ圧縮されたトピックに保持されていることを前提としています。 Azure Event Hubs では[圧縮がブローカー機能として実装されていないため](event-hubs-federation-overview.md#log-projections)、保持されるイベントに時間ベースの保持期間の制限が常に課されます。これは、Azure Event Hubs が長期間のデータまたは構成ストアではなく、リアルタイムのイベント ストリーミング エンジンであるという原則から来ています。
>
> Apache Kafka プロジェクトではこれらの役割が混同されていても問題ないかもしれませんが、Azure では、このような情報は適切なデータベースまたは構成ストアで管理するのが最適であると考えられています。
>
> Apache Kafka Connect のシナリオの多くは正常に機能しますが、Apache Kafka と Azure Event Hubs の保持モデル間のこれらの概念的な違いのために、特定の構成が期待どおりに機能しなくなる可能性があります。 

このチュートリアルでは、イベント ハブに Kafka Connect を統合し、基本的な FileStreamSource および FileStreamSink コネクタをデプロイする方法について説明します。 現在、この機能はプレビュー段階にあります。 これらのコネクタは運用環境での使用を想定したものではありませんが、Azure Event Hubs が Kafka ブローカーとして機能する Kafka Connect のシナリオをエンド ツー エンドで示しています。

> [!NOTE]
> このサンプルは [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) で入手できます。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Event Hubs 名前空間を作成します
> * サンプル プロジェクトを複製する
> * Event Hubs 用に Kafka Connect を構成する
> * Kafka Connect を実行する
> * コネクタを作成する

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

- Azure のサブスクリプション。 アカウントがない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/)してください。
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka リリース (バージョン 1.1.1、Scala バージョン 2.11)。[kafka.apache.org](https://kafka.apache.org/downloads#1.1.1) から入手できます。
- [Apache Kafka 用 Event Hubs](./event-hubs-for-kafka-ecosystem-overview.md) の概要に関する記事を読む。

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 名前空間を作成します
Event Hubs サービスとの間で送受信を行うには、イベント ハブの名前空間が必要です。 名前空間とイベント ハブを作成する手順については、[イベント ハブの作成](event-hubs-create.md)に関するページを参照してください。 Event Hubs の接続文字列と完全修飾ドメイン名 (FQDN) を、後で使用するために取得します。 手順については、「[Get an Event Hubs connection string (Event Hubs の接続文字列を取得する)](event-hubs-get-connection-string.md)」を参照してください。 

## <a name="clone-the-example-project"></a>サンプル プロジェクトを複製する
Azure Event Hubs リポジトリを複製し、tutorials/connect サブフォルダーに移動します。 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Event Hubs 用に Kafka Connect を構成する
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


## <a name="run-kafka-connect"></a>Kafka Connect を実行する

この手順では、Event Hubs を使用し、Kafka Connect ワーカーをローカルから分散モードで開始して、クラスターの状態を維持します。

1. 前出の `connect-distributed.properties` ファイルをローカルに保存します。  中かっこで囲んだ値はすべて置き換えてください。
2. お使いのマシン上にある Kafka リリースの場所に移動します。
4. `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties` を実行します。  `'INFO Finished starting connectors and tasks'` が表示されれば、Connect ワーカーの REST API は対話可能な状態です。 

> [!NOTE]
> Kafka Connect は、Kafka AdminClient API を使用して、圧縮などの推奨される構成を含むトピックを自動的に作成します。 Azure portal で名前空間をざっとチェックすると、Connect ワーカーの内部的なトピックが自動的に作成されていることがわかります。
>
>Kafka Connect の内部トピックでは、**圧縮を使用する必要があります**。  Connect の内部トピックが正しく構成されていない場合、Event Hubs チームでは不適切な構成を修正する責任を負いません。

### <a name="create-connectors"></a>コネクタを作成する
このセクションでは、FileStreamSource コネクタと FileStreamSink コネクタを立ち上げる方法について説明します。 

1. 入力データ ファイルと出力データ ファイル用のディレクトリを作成します。
    ```bash
    mkdir ~/connect-quickstart
    ```

2. 2 つのファイルを作成します。FileStreamSource コネクタの読み取り元となる、シード データを含んだファイルと、この例の FileStreamSink コネクタの書き込み先となるファイルです。
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. FileStreamSource コネクタを作成します。  中かっこ内の値は、実際のホーム ディレクトリのパスに置き換えてください。
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    上記のコマンドを実行すると、Event Hubs インスタンスに Event Hub `connect-quickstart` が確認できます。
4. ソース コネクタの状態を確認します。
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    必要に応じて [Service Bus エクスプローラー](https://github.com/paolosalvatori/ServiceBusExplorer/releases)を使用して、イベントが `connect-quickstart` トピックに到着したことを確認できます。

5. FileStreamSink コネクタを作成します。  こちらも、中かっこ内の値は、実際のホーム ディレクトリのパスに置き換えてください。
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. シンク コネクタの状態を確認します。
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. ファイル間でデータがレプリケートされていることと、そのデータが両方のファイル間で一致していることを確認します。
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>クリーンアップ
Kafka Connect は、イベント ハブのトピックを作成することによって、Connect クラスターが停止した後も永続的に存在する構成、オフセット、状態を格納します。 この永続化が不要である場合は、これらのトピックを削除することをお勧めします。 このチュートリアルで作成した `connect-quickstart` イベント ハブも必要に応じて削除してかまいません。

## <a name="next-steps"></a>次のステップ

Kafka 用 Event Hubs の詳細については、次の記事を参照してください。  

- [イベント ハブ内の Kafka ブローカーをミラーリングする](event-hubs-kafka-mirror-maker-tutorial.md)
- [Apache Spark をイベント ハブに接続する](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink をイベント ハブに接続する](event-hubs-kafka-flink-tutorial.md)
- [GitHub 上でサンプルを調べる](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Akka Streams をイベント ハブに接続する](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure Event Hubs のための Apache Kafka 開発者ガイド](apache-kafka-developer-guide.md)
