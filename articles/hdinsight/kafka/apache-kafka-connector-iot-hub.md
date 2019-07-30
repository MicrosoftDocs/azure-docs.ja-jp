---
title: HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する
description: HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する方法を説明します。 Kafka Connect Azure IoT Hub プロジェクトは、Kafka にソース コネクタとシンク コネクタを提供します。 ソース コネクタは IoT Hub からデータを読み取ることができ、シンク コネクタは IoT Hub に書き込みます。
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 5559d243573ea04400007cdce0e71009dc91e27a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446435"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する

[Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) コネクタを使用して HDInsight 上の Apache Kafka と Azure IoT Hub の間でデータを移動する方法を学習します。 このドキュメントでは、クラスター内のエッジ ノードから IoT Hub コネクタを実行する方法を説明します。

Kafka Connect API では、Kafka へのデータのプルまたは Kafka から別のシステムへのデータのプッシュを継続的に行うコネクタを実装できます。 [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) は、Azure IoT Hub から Kafka にデータをプルするコネクタです。 Kafka から IoT Hub にデータをプッシュすることもことできます。 

IoT Hub からプルする場合は、__ソース__ コネクタを使用します。 IoT Hub にプッシュする場合は、__シンク__ コネクタを使用します。 IoT Hub コネクタでは、ソース コネクタとシンク コネクタの両方が提供されます。

次の図は、コネクタを使用するときの Azure IoT Hub と HDInsight 上の Kafka の間のデータ フローを示しています。

![コネクタを通じた IoT Hub から Kafka へのデータ フローを示すイメージ](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Connect API について詳しくは、[https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

* HDInsight クラスター上の Kafka。 詳細については、[HDInsight の Kafka のクイックスタート](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

* Kafka クラスター内のエッジ ノード。 詳しくは、[HDInsight でのエッジノードの使用](../hdinsight-apps-use-edge-node.md)に関するドキュメントをご覧ください。

* Azure IoT Hub。 この記事では、「[Raspberry Pi オンライン シミュレーターの Azure IoT Hub への接続](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started)」ドキュメントをお勧めします。

* SSH クライアント このドキュメントの手順では、SSH を使って、クラスターに接続します。 詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

## <a name="install-the-connector"></a>コネクタをインストールする

1. Azure IoT Hub 用の Kafka Connect の最新のリリースを [https://github.com/Azure/toketi-kafka-connect-iothub/releases/](https://github.com/Azure/toketi-kafka-connect-iothub/releases) からダウンロードします。

2. HDInsight クラスター上の Kafka のエッジ ノードに .jar ファイルをアップロードするには、次のコマンドを使用します。

    > [!NOTE]  
    > `sshuser` を HDInsight クラスターの SSH ユーザー アカウントに置き換えます。 `new-edgenode` をエッジ ノード名に置き換えます。 `clustername` をクラスター名に置き換えます。 エッジ ノード用の SSH エンドポイントについて詳しくは、[HDInsight でのエッジ ノードの使用](../hdinsight-apps-use-edge-node.md#access-an-edge-node)に関するドキュメントをご覧ください。

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. ファイルのコピーが完了したら、SSH を使用してエッジ ノードに接続します。

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するドキュメントを参照してください。

4. Kafka の `libs` ディレクトリにコネクタをインストールするには、次のコマンドを使用します。

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]  
> ビルド済みの .jar ファイルを使用している場合に、このドキュメントの残りの手順で問題が発生する場合は、ソースからパッケージをビルドしてみてください。
>
> コネクタをビルドするには、[Scala ビルド ツール](https://www.scala-sbt.org/)がある Scala 開発環境が必要です。
>
> 1. コネクタのソースを [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) から開発環境にダウンロードします。
>
> 2. プロジェクト ディレクトリのコマンド プロンプトから、次のコマンドを使用してプロジェクトをビルドおよびパッケージ化します。
>
>    ```bash
>    sbt assembly
>    ```
>
>    このコマンドで、プロジェクトの `target/scala-2.11` ディレクトリに `kafka-connect-iothub-assembly_2.11-0.6.jar` というファイルが作成されます。

## <a name="configure-apache-kafka"></a>Apache Kafka を構成する

エッジ ノードへの SSH 接続から、次の手順を使用して、コネクタをスタンドアロン モードで実行するように Kafka を構成します。

1. クラスター名を変数に保存します。 変数を使用すると、このセクションの他のコマンドのコピー/貼り付けが簡単になります。

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. `jq` ユーティリティをインストールします。 このユーティリティは、Ambari クエリから返される JSON ドキュメントを処理しやすくします。

    ```bash
    sudo apt -y install jq
    ```

3. Kafka ブローカーのアドレスを取得します。 クラスターには多くのブローカーがある場合がありますが、参照する必要があるのは 1 つか 2 つだけです。 2 つのブローカー ホストのアドレスを取得するには、次のコマンドを使用します。

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    プロンプトが表示されたら、クラスターのログイン (管理者) アカウントのパスワードを入力します。 次のテキストのような値が返されます。

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Apache ZooKeeper ノードのアドレスを取得します。 クラスターには複数の Zookeeper ノードがありますが、参照する必要があるのは 1 つか 2 つだけです。 2 つの Zookeeper ノードのアドレスを取得するには、次のコマンドを使用します。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. コネクタをスタンドアロン モードで実行している場合は、Kafka ブローカーと通信するために `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` ファイルが使用されます。 `connect-standalone.properties` ファイルを編集するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Kafka ブローカーを検索するためにエッジ ノードに対してスタンドアロン構成を構成するには、`bootstrap.servers=` で始まる行を探します。 `localhost:9092` 値を前の手順のブローカー ホストに置き換えます。

    * `key.converter=` と `value.converter=` の行を次の値に変更します。

        ```ini
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]  
        > この変更により、Kafka に含まれているコンソール プロデューサーを使用してテストすることができます。 他のプロデューサーとコンシューマーには別のコンバーターが必要な場合があります。 他のコンバーター値の使用について、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

    * ファイルの最後に次のテキストを含む行を追加します。

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]  
        > この変更により、一度に 10 個のレコードに限定することによって、シンク コネクタでのタイムアウトを防ぎます。 詳細については、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) を参照してください。

6. ファイルを保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

7. コネクタで使用されるトピックを作成するには、次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` および `iotout` トピックが存在することを確認するには、次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` トピックは、IoT ハブからメッセージを受信するために使用されます。 `iotout` トピックは、IoT ハブにメッセージを送信するために使用されます。

## <a name="get-iot-hub-connection-information"></a>IoT ハブ接続情報の取得

コネクタによって使用される IoT ハブ情報を取得するには、次の手順を使用します。

1. IoT ハブのイベント ハブ互換エンドポイントとイベント ハブ互換エンドポイント名を取得します。 この情報を取得するには、次のいずれかの方法を使用します。

   * __[Azure Portal](https://portal.azure.com/) で__、次の手順に従います。

     1. IoT ハブに移動し、 __[エンドポイント]__ を選択します。
     2. __[組み込みのエンドポイント]__ から、 __[イベント]__ を選択します。
     3. __[プロパティ]__ から、次のフィールドの値をコピーします。

         * __イベント ハブ互換の名前__
         * __イベント ハブ互換エンドポイント__
         * __パーティション__

        > [!IMPORTANT]  
        > ポータルからのエンドポイント値には、この例では必要のない余分なテキストが含まれていることがあります。 このパターン `sb://<randomnamespace>.servicebus.windows.net/` に一致するテキストを抽出します。

   * __[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) で__、次のコマンドを使用します。

       ```azure-cli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       `myhubname` は、お使いの IoT ハブの名前に置き換えます。 応答は次のテキストのようになります。

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. __共有アクセス ポリシー__ と __キー__ を取得します。 たとえば、__サービス__ キーを使用します。 この情報を取得するには、次のいずれかの方法を使用します。

    * __[Azure Portal](https://portal.azure.com/) で__、次の手順に従います。

        1. __[共有アクセス ポリシー]__ を選択してから、 __[サービス]__ を選択します。
        2. __[主キー]__ の値をコピーします。
        3. __[接続文字列 -- 主キー]__ の値をコピーします。

    * __[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) で__、次のコマンドを使用します。

        1. 主キーの値を取得するには、次のコマンドを使用します。

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            `myhubname` は、お使いの IoT ハブの名前に置き換えます。 応答は、このハブの `service` ポリシーの主キーです。

        2. `service` ポリシーの接続文字列を取得するには、次のコマンドを使用します。

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            `myhubname` は、お使いの IoT ハブの名前に置き換えます。 応答は `service` ポリシー用の接続文字列です。

## <a name="configure-the-source-connection"></a>ソース接続の構成

IoT ハブを使用するようにソースを構成するには、エッジ ノードへの SSH 接続から次のアクションを実行します。

1. `/usr/hdp/current/kafka-broker/config/` ディレクトリに `connect-iot-source.properties` ファイルのコピーを作成します。 toketi-kafka-connect-iothub プロジェクトからファイルをダウンロードするには、次のコマンドを使用します。

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. `connect-iot-source.properties` ファイルを編集し、IoT ハブ情報を追加するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    エディターで、次のエントリを検索し、変更します。

   * `Kafka.Topic=PLACEHOLDER`:`PLACEHOLDER` を `iotin` で置き換え IoT ハブから受信したメッセージは `iotin` トピックに配置されます。
   * `IotHub.EventHubCompatibleName=PLACEHOLDER`:`PLACEHOLDER` をイベント ハブ互換の名前に置き換えます。
   * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`:`PLACEHOLDER` をイベント ハブ互換エンドポイントに置き換えます。
   * `IotHub.Partitions=PLACEHOLDER`:`PLACEHOLDER` を前の手順のパーティション数に置き換えます。
   * `IotHub.AccessKeyName=PLACEHOLDER`:`PLACEHOLDER` を `service` で置き換え
   * `IotHub.AccessKeyValue=PLACEHOLDER`:`PLACEHOLDER` を `service` ポリシーの主キーに置き換えます。
   * `IotHub.StartType=PLACEHOLDER`:`PLACEHOLDER` を UTC 日付に置き換えます。 この日付は、コネクタがメッセージの検査を開始した日です。 日付の形式は `yyyy-mm-ddThh:mm:ssZ` です。
   * `BatchSize=100`:`100` を `5` で置き換え この変更により、コネクタは IoT ハブに 5 つの新しいメッセージが入った後にメッセージを Kafka に読み取ります。

     構成例については、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) をご覧ください。

3. 変更を保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

コネクタ ソースの構成について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) をご覧ください。

## <a name="configure-the-sink-connection"></a>シンク接続の構成

IoT ハブを使用するようにシンク接続を構成するには、エッジ ノードへの SSH 接続から次のアクションを実行します。

1. `/usr/hdp/current/kafka-broker/config/` ディレクトリに `connect-iothub-sink.properties` ファイルのコピーを作成します。 toketi-kafka-connect-iothub プロジェクトからファイルをダウンロードするには、次のコマンドを使用します。

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. `connect-iothub-sink.properties` ファイルを編集し、IoT ハブ情報を追加するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    エディターで、次のエントリを検索し、変更します。

   * `topics=PLACEHOLDER`:`PLACEHOLDER` を `iotout` で置き換え `iotout` トピックに書き込まれたメッセージが IoT ハブに転送されます。
   * `IotHub.ConnectionString=PLACEHOLDER`:`PLACEHOLDER` を `service` ポリシーの接続文字列に置き換えます。

     構成例については、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

3. 変更を保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

コネクタ シンクの構成について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

## <a name="start-the-source-connector"></a>ソース コネクタの開始

ソース コネクタを開始するには、エッジ ノードへの SSH 接続から次のコマンドを使用します。

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

コネクタが開始された後で、デバイスから IoT ハブにメッセージを送信します。 コネクタは、IoT ハブからのメッセージを読み取り、Kafka トピックに格納するときに、コンソールに情報を記録します。

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]  
> コネクタが開始されると、複数の警告が表示されることがあります。 これらの警告は、IoT ハブからのメッセージの受信で問題が発生する原因にはなりません。

コネクタを停止するには、__Ctrl+C__ キーを使用します。

## <a name="start-the-sink-connector"></a>シンク コネクタの開始

エッジ ノードへの SSH 接続から、次のコマンドを使用して、シンク コネクタをスタンドアロン モードで開始します。

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

コネクタが実行されると、次のテキストのような情報が表示されます。

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> コネクタが開始されると、複数の警告が表示されることがあります。 これらは無視してかまいません。

コネクタを通じてメッセージを送信するには、次の手順を使用します。

1. Kafka クラスターへの別の SSH セッションを開きます。

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. `iotout` トピックにメッセージを送信するには、次のコマンドを使用します。

    > [!WARNING]  
    > これは新しい SSH 接続であるため、`$KAFKABROKERS` 変数には情報が含まれていません。 これを設定するには、次のいずれかの方法を使用します。
    >
    > * [Apache Kafka の構成](#configure-apache-kafka)に関するセクションの最初の 3 つの手順を使用します。
    > * 前の SSH 接続の `echo $KAFKABROKERS` を使用し、次のコマンドの `$KAFKABROKERS` を実際の値に置き換えます。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    このコマンドは、通常の Bash プロンプトに戻りません。 代わりに、キーボード入力を `iotout` トピックに送信します。

3. デバイスにメッセージを送信するには、`kafka-console-producer` の SSH セッションに JSON ドキュメントを貼り付けます。

    > [!IMPORTANT]  
    > `"deviceId"` エントリの値をデバイスの ID に設定する必要があります。 次の例では、デバイスの名前は `fakepi` です。

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    この JSON ドキュメントのスキーマについては、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) で詳しく説明されています。

    シミュレートされた Raspberry Pi デバイスを使用し、それが実行されている場合は、デバイスによって次のメッセージがログに記録されます。

    ```text
    Receive message: Turn On
    ```

    JSON ドキュメントを再送信しますが、`"message"` エントリの値は変更します。 新しい値がデバイスによってログに記録されます。

シンク コネクタの使用について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Apache Kafka Connect API を使用して HDInsight 上の IoT Kafka Connector を開始する方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
