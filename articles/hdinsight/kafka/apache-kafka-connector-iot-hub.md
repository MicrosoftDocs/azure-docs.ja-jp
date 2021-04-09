---
title: HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する
description: HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する方法を説明します。 Kafka Connect Azure IoT Hub プロジェクトは、Kafka にソース コネクタとシンク コネクタを提供します。 ソース コネクタは IoT Hub からデータを読み取ることができ、シンク コネクタは IoT Hub に書き込みます。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: feada70c7a461bb4a9cd621c76b5606a7f0e19d5
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865283"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>HDInsight 上の Apache Kafka を Azure IoT Hub と共に使用する

[Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) コネクタを使用して HDInsight 上の Apache Kafka と Azure IoT Hub の間でデータを移動する方法を学習します。 このドキュメントでは、クラスター内のエッジ ノードから IoT Hub コネクタを実行する方法を説明します。

Kafka Connect API では、Kafka へのデータのプルまたは Kafka から別のシステムへのデータのプッシュを継続的に行うコネクタを実装できます。 [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) は、Azure IoT Hub から Kafka にデータをプルするコネクタです。 Kafka から IoT Hub にデータをプッシュすることもことできます。

IoT Hub からプルする場合は、__ソース__ コネクタを使用します。 IoT Hub にプッシュする場合は、__シンク__ コネクタを使用します。 IoT Hub コネクタでは、ソース コネクタとシンク コネクタの両方が提供されます。

次の図は、コネクタを使用するときの Azure IoT Hub と HDInsight 上の Kafka の間のデータ フローを示しています。

:::image type="content" source="./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png" alt-text="コネクタを通じた IoT Hub から Kafka へのデータ フローを示すイメージ" border="false":::

Connect API について詳しくは、[https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) をご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight 上の Apache Kafka クラスター 詳細については、[HDInsight の Kafka のクイックスタート](apache-kafka-get-started.md)に関するドキュメントをご覧ください。

* Kafka クラスター内のエッジ ノード。 詳しくは、[HDInsight でのエッジノードの使用](../hdinsight-apps-use-edge-node.md)に関するドキュメントをご覧ください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* Azure IoT Hub とデバイス この記事では、[Raspberry Pi オンライン シミュレーターの Azure IoT Hub への接続](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md)を検討するようお勧めします。

* [Scala ビルド ツール](https://www.scala-sbt.org/)

## <a name="build-the-connector"></a>コネクタを作成します

1. コネクタのソースを [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) から開発環境にダウンロードします。

2. コマンドプロンプトから、`toketi-kafka-connect-iothub-master` ディレクトリに移動します。 次に、次のコマンドを使用して、プロジェクトをビルドおよびパッケージ化します。

    ```cmd
    sbt assembly
    ```

    このビルドは完了までに数分かかります。 このコマンドにより、プロジェクトの `toketi-kafka-connect-iothub-master\target\scala-2.11` ディレクトリに `kafka-connect-iothub-assembly_2.11-0.7.0.jar` というファイルが作成されます。

## <a name="install-the-connector"></a>コネクタをインストールする

1. .jar ファイルを HDInsight クラスター上の Kafka のエッジ ノードにアップロードします。 以下のコマンドを編集して、`CLUSTERNAME` をクラスターの実際の名前に置き換えます。 以下では、SSH ユーザーアカウントの既定値と [エッジノード](../hdinsight-apps-use-edge-node.md#access-an-edge-node) の名前が使用されています。必要に応じて変更してください。

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. ファイルのコピーが完了したら、SSH を使用してエッジ ノードに接続します。

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kafka の `libs` ディレクトリにコネクタをインストールするには、次のコマンドを使用します。

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

残りの手順では、SSH 接続を有効のままにしておきます。

## <a name="configure-apache-kafka"></a>Apache Kafka を構成する

エッジ ノードへの SSH 接続から、次の手順を使用して、コネクタをスタンドアロン モードで実行するように Kafka を構成します。

1. パスワード変数を設定します。 PASSWORD をクラスターのログインパスワードに置き換え、次のコマンドを入力します。

    ```bash
    export password='PASSWORD'
    ```

1. [jq](https://stedolan.github.io/jq/) ユーティリティをインストールします。 jq は、Ambari クエリから返された JSON ドキュメントの処理をしやすくします。 次のコマンドを入力します。

    ```bash
    sudo apt -y install jq
    ```

1. Kafka ブローカーのアドレスを取得します。 クラスターには多くのブローカーがある場合がありますが、参照する必要があるのは 1 つか 2 つだけです。 2 つのブローカー ホストのアドレスを取得するには、次のコマンドを使用します。

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    後で使用するために値をコピーします。 次のテキストのような値が返されます。

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Apache ZooKeeper ノードのアドレスを取得します。 クラスターには複数の Zookeeper ノードがありますが、参照する必要があるのは 1 つか 2 つだけです。 次のコマンドを使用して、変数 `KAFKAZKHOSTS`にアドレスを格納します。

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. コネクタをスタンドアロン モードで実行している場合は、Kafka ブローカーと通信するために `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` ファイルが使用されます。 `connect-standalone.properties` ファイルを編集するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. 次の編集を行います。

    |現在の値 |新しい値 | 解説 |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|`localhost:9092` 値を前の手順のブローカー ホストに置き換えます。|エッジ ノードのスタンドアロン構成を設定して、Kafka ブローカーを検索します。|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|この変更により、Kafka に含まれているコンソール プロデューサーを使用してテストすることができます。 他のプロデューサーとコンシューマーには別のコンバーターが必要な場合があります。 他のコンバーター値の使用について、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|上記と同じです。|
    |該当なし|`consumer.max.poll.records=10`|ファイルの末尾に追加します。 この変更により、一度に 10 個のレコードに限定することによって、シンク コネクタでのタイムアウトを防ぎます。 詳細については、「[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)」を参照してください。|

1. ファイルを保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

1. コネクタで使用されるトピックを作成するには、次のコマンドを使用します。

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

     1. IoT ハブに移動し、__[エンドポイント]__ を選択します。
     2. __[組み込みのエンドポイント]__ から、__[イベント]__ を選択します。
     3. __[プロパティ]__ から、次のフィールドの値をコピーします。

         * __イベント ハブ互換の名前__
         * __Event Hub と互換性があるエンドポイント__
         * __パーティション__

        > [!IMPORTANT]  
        > ポータルからのエンドポイント値には、この例では必要のない余分なテキストが含まれていることがあります。 このパターン `sb://<randomnamespace>.servicebus.windows.net/` に一致するテキストを抽出します。

   * __[Azure CLI](/cli/azure/get-started-with-azure-cli) で__、次のコマンドを使用します。

       ```azurecli
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

        1. __[共有アクセス ポリシー]__ を選択してから、__[サービス]__ を選択します。
        2. __[主キー]__ の値をコピーします。
        3. __[接続文字列 -- 主キー]__ の値をコピーします。

    * __[Azure CLI](/cli/azure/get-started-with-azure-cli) で__、次のコマンドを使用します。

        1. 主キーの値を取得するには、次のコマンドを使用します。

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            `myhubname` は、お使いの IoT ハブの名前に置き換えます。 応答は、このハブの `service` ポリシーの主キーです。

        2. `service` ポリシーの接続文字列を取得するには、次のコマンドを使用します。

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            `myhubname` は、お使いの IoT ハブの名前に置き換えます。 応答は `service` ポリシー用の接続文字列です。

## <a name="configure-the-source-connection"></a>ソース接続の構成

IoT ハブを使用するようにソースを構成するには、エッジ ノードへの SSH 接続から次のアクションを実行します。

1. `/usr/hdp/current/kafka-broker/config/` ディレクトリに `connect-iot-source.properties` ファイルのコピーを作成します。 toketi-kafka-connect-iothub プロジェクトからファイルをダウンロードするには、次のコマンドを使用します。

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. `connect-iot-source.properties` ファイルを編集し、IoT ハブ情報を追加するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. エディターで、次のエントリを検索し、変更します。

    |現在の値 |編集|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|`PLACEHOLDER` を `iotin` で置き換え IoT ハブから受信したメッセージは `iotin` トピックに配置されます。|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|`PLACEHOLDER` をイベント ハブ互換の名前に置き換えます。|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|`PLACEHOLDER` をイベント ハブ互換エンドポイントに置き換えます。|
    |`IotHub.AccessKeyName=PLACEHOLDER`|`PLACEHOLDER` を `service` で置き換え|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|`PLACEHOLDER` を `service` ポリシーの主キーに置き換えます。|
    |`IotHub.Partitions=PLACEHOLDER`|`PLACEHOLDER` を前の手順のパーティション数に置き換えます。|
    |`IotHub.StartTime=PLACEHOLDER`|`PLACEHOLDER` を UTC 日付に置き換えます。 この日付は、コネクタがメッセージの検査を開始した日です。 日付の形式は `yyyy-mm-ddThh:mm:ssZ` です。|
    |`BatchSize=100`|`100` を `5` で置き換え この変更により、コネクタは IoT ハブに 5 つの新しいメッセージが入った後にメッセージを Kafka に読み取ります。|

    構成例については、「[Kafka Connect Source Connector for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)」を参照してください。

1. 変更を保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

コネクタ ソースの構成について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) をご覧ください。

## <a name="configure-the-sink-connection"></a>シンク接続の構成

IoT ハブを使用するようにシンク接続を構成するには、エッジ ノードへの SSH 接続から次のアクションを実行します。

1. `/usr/hdp/current/kafka-broker/config/` ディレクトリに `connect-iothub-sink.properties` ファイルのコピーを作成します。 toketi-kafka-connect-iothub プロジェクトからファイルをダウンロードするには、次のコマンドを使用します。

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. `connect-iothub-sink.properties` ファイルを編集し、IoT ハブ情報を追加するには、次のコマンドを使用します。

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. エディターで、次のエントリを検索し、変更します。

    |現在の値 |編集|
    |---|---|
    |`topics=PLACEHOLDER`|`PLACEHOLDER` を `iotout` で置き換え `iotout` トピックに書き込まれたメッセージが IoT ハブに転送されます。|
    |`IotHub.ConnectionString=PLACEHOLDER`|`PLACEHOLDER` を `service` ポリシーの接続文字列に置き換えます。|

    構成例については、「[Kafka Connect Source Connector for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)」を参照してください。

1. 変更を保存するには、__Ctrl + X__ キー、__Y__ キー、__Enter__ キーの順に押します。

コネクタ シンクの構成について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

## <a name="start-the-source-connector"></a>ソース コネクタの開始

1. ソース コネクタを開始するには、エッジ ノードへの SSH 接続から次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    コネクタが開始された後で、デバイスから IoT ハブにメッセージを送信します。 コネクタは、IoT ハブからのメッセージを読み取り、Kafka トピックに格納するときに、コンソールに情報を記録します。

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > コネクタが開始されると、複数の警告が表示されることがあります。 これらの警告は、IoT ハブからのメッセージの受信で問題が発生する原因にはなりません。

1. **Ctrl + C** を2回押して、数分後にコネクタを停止します。 コネクタが停止するまで数分かかります。

## <a name="start-the-sink-connector"></a>シンク コネクタの開始

エッジ ノードへの SSH 接続から、次のコマンドを使用して、シンク コネクタをスタンドアロン モードで開始します。

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

コネクタが実行されると、次のテキストのような情報が表示されます。

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> コネクタが開始されると、複数の警告が表示されることがあります。 これらは無視してかまいません。

## <a name="send-messages"></a>メッセージを送信する

コネクタを通じてメッセージを送信するには、次の手順を使用します。

1. Kafka クラスターへの *2 番目* の SSH セッションを開きます。

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 新しい SSH セッションの Kafka ブローカーのアドレスを取得します。 PASSWORD をクラスターのログインパスワードに置き換え、次のコマンドを入力します。

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. `iotout` トピックにメッセージを送信するには、次のコマンドを使用します。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    このコマンドでは、通常の Bash プロンプトに戻りません。 代わりに、キーボード入力を `iotout` トピックに送信します。

1. デバイスにメッセージを送信するには、`kafka-console-producer` の SSH セッションに JSON ドキュメントを貼り付けます。

    > [!IMPORTANT]  
    > `"deviceId"` エントリの値をデバイスの ID に設定する必要があります。 次の例では、デバイスの名前は `myDeviceId` です。

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    この JSON ドキュメントのスキーマについては、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) で詳しく説明されています。

    シミュレートされた Raspberry Pi デバイスを使用し、それが実行されている場合は、デバイスによって次のメッセージがログに記録されます。

    ```output
    Receive message: Turn On
    ```

    JSON ドキュメントを再送信しますが、`"message"` エントリの値は変更します。 新しい値がデバイスによってログに記録されます。

シンク コネクタの使用について詳しくは、[https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) をご覧ください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Apache Kafka Connect API を使用して HDInsight 上の IoT Kafka Connector を開始する方法について説明しました。 次のリンクを使用することで、Kafka のその他の活用方法を知ることができます。

* [HDInsight 上の Apache Kafka で Apache Spark を使用する](../hdinsight-apache-spark-with-kafka.md)
* [HDInsight 上の Apache Kafka で Apache Storm を使用する](../hdinsight-apache-storm-with-kafka.md)
