---
title: チュートリアル:Apache Kafka Producer と Consumer API - Azure HDInsight
description: HDInsight 上の Kafka で Apache Kafka Producer および Consumer API を使用する方法を説明します。 このチュートリアルでは、これらの API を Java アプリケーションから HDInsight 上の Kafka で使用する方法を学習します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: 260a3fbb8486a1e9eeaa87e920143615e5fae867
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681818"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>チュートリアル:Apache Kafka Producer および Consumer API の使用

HDInsight 上の Kafka で Apache Kafka Producer および Consumer API を使用する方法を説明します。

Kafka Producer API では、アプリケーションが Kafka クラスターにデータ ストリームを送信できます。 Kafka Consumer API では、アプリケーションがクラスターからデータ ストリームを読み取ることができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 前提条件
> * コードの理解
> * アプリケーションをビルドしてデプロイする
> * クラスターでアプリケーションを実行する

API の詳細については、[Producer API](https://kafka.apache.org/documentation/#producerapi) と [Consumer API](https://kafka.apache.org/documentation/#consumerapi) に関する Apache のドキュメントをご覧ください。

## <a name="prerequisites"></a>前提条件

* HDInsight クラスター上の Apache Kafka。 クラスターを作成する方法については、[HDInsight での Apache Kafka の開始](apache-kafka-get-started.md)に関する記事をご覧ください。
* [Java Developer Kit (JDK) バージョン 8](https://aka.ms/azure-jdks) または同等の JDK (OpenJDK など)。
* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。
* Putty などの SSH クライアント。 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="understand-the-code"></a>コードの理解

アプリケーションの例は、[https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) の `Producer-Consumer` サブディレクトリにあります。 **Enterprise セキュリティ パッケージ (ESP)** が有効になっている Kafka クラスターを使用している場合は、`DomainJoined-Producer-Consumer` サブディレクトリにあるアプリケーションのバージョンを使用する必要があります。

アプリケーションは、主に次の 4 つのファイルで構成されます。
* `pom.xml`:このファイルは、プロジェクトの依存関係、Java バージョン、およびパッケージ化方法を定義します。
* `Producer.java`:このファイルは、Producer API を使用して Kafka にランダムな文を送信します。
* `Consumer.java`:このファイルは、Consumer API を使用して Kafka からデータを読み取り、それを STDOUT に出力します。
* `AdminClientWrapper.java`:このファイルでは、管理 API を使用して Kafka トピックを作成、記述、および削除します。
* `Run.java`:プロデューサーおよびコンシューマーのコードの実行に使用されるコマンド ライン インターフェイスです。

### <a name="pomxml"></a>Pom.xml

`pom.xml` ファイル内で理解すべき重要な点は、次のとおりです。

* 依存関係:このプロジェクトは、`kafka-clients` パッケージによって提供される Kafka Producer および Consumer API に依存します。 次の XML コードがこの依存関係を定義します。

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}` エントリは `pom.xml` の `<properties>..</properties>` セクション内で宣言され、Kafka バージョンの HDInsight クラスターに構成されています。

* プラグイン:Maven プラグインはさまざまな機能を備えています。 このプロジェクトでは、次のプラグインが使用されます。

    * `maven-compiler-plugin`:プロジェクトで使用される Java バージョンを 8 に設定するために使用されます。 これは、HDInsight 3.6 によって使用される Java のバージョンです。
    * `maven-shade-plugin`:このアプリケーションとすべての依存関係を含む uber jar を生成するために使用されます。 また、アプリケーションのエントリ ポイントの設定にも使用されるため、メイン クラスを指定しなくても Jar ファイルを直接実行できます。

### <a name="producerjava"></a>Producer.java

プロデューサーは、Kafka ブローカー ホスト (ワーカー ノード) と通信して、Kafka トピックにデータを送信します。 次のコード スニペットは、[GitHub リポジトリ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started)の [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) ファイルの抜粋です。プロデューサーのプロパティを設定する方法が示されています。 Enterprise セキュリティが有効なクラスターの場合は、"properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");" のように、追加のプロパティを追加する必要があります。

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

コンシューマーは、Kafka ブローカー ホスト (ワーカー ノード) と通信し、ループ内のレコードを読み取ります。 次のコード スニペットは、[Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) ファイルの抜粋で、コンシューマーのプロパティを設定しています。 Enterprise セキュリティが有効なクラスターの場合は、"properties.setProperty(CommonClientConfigs.SECURITY_PROTOCOL_CONFIG, "SASL_PLAINTEXT");" のように、追加のプロパティを追加する必要があります。

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

このコードでは、コンシューマーがトピックの先頭から読み取るように構成されます (`auto.offset.reset` は `earliest` に設定されています)。

### <a name="runjava"></a>Run.java

[Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) ファイルでは、プロデューサーまたはコンシューマーのいずれかのコードを実行するコマンド ライン インターフェイスが提供されます。 パラメーターとして Kafka ブローカー ホスト情報を指定する必要があります。 必要に応じて、コンシューマー プロセスで使用されるグループ ID 値を含めることができます。 同じグループ ID を使用して複数のコンシューマー インスタンスを作成すると、それらの間でトピックからの読み取りの負荷が分散されます。

## <a name="build-and-deploy-the-example"></a>例を構築してデプロイする

### <a name="use-pre-built-jar-files"></a>事前に作成した JAR ファイルを使用する

[Kafka Get Started Azure サンプル](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars)から jar をダウンロードします。 クラスターで **Enterprise セキュリティ パッケージ (ESP)** が有効になっている場合は、kafka-producer-consumer-esp.jar を使用します。 以下のコマンドを使用して、クラスターに jar をコピーします。

```cmd
scp kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
```

### <a name="build-the-jar-files-from-code"></a>コードから JAR ファイルを作成する

この手順をスキップする場合は、あらかじめ作成済みの jar を `Prebuilt-Jars` サブディレクトリからダウンロードできます。 kafka-producer-consumer.jar をダウンロードしてください。 クラスターで **Enterprise セキュリティ パッケージ (ESP)** が有効になっている場合は、kafka-producer-consumer-esp.jar を使用します。 jar を HDInsight クラスターにコピーするには、手順 3. を実行します。

1. [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) から例をダウンロードして抽出します。

2. 現在のディレクトリを `hdinsight-kafka-java-get-started\Producer-Consumer` ディレクトリの場所に設定します。 **Enterprise セキュリティ パッケージ (ESP)** が有効になっている Kafka クラスターを使用している場合は、場所を `DomainJoined-Producer-Consumer` サブディレクトリに設定する必要があります。 次のコマンドを使用して、アプリケーションをビルドします。

    ```cmd
    mvn clean package
    ```

    このコマンドにより、`kafka-producer-consumer-1.0-SNAPSHOT.jar` というファイルを含む `target` という名前のディレクトリが作成されます。 ESP クラスターの場合、ファイルは `kafka-producer-consumer-esp-1.0-SNAPSHOT.jar` です

3. `sshuser` は、クラスターの SSH ユーザーに置き換えます。また、`CLUSTERNAME` はクラスターの名前に置き換えます。 次のコマンドを入力して、HDInsight クラスターに `kafka-producer-consumer-1.0-SNAPSHOT.jar` ファイルをコピーします。 メッセージが表示されたら、SSH ユーザーのパスワードを入力します。

    ```cmd
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

## <a name="run-the-example"></a><a id="run"></a>例を実行する

1. `sshuser` は、クラスターの SSH ユーザーに置き換えます。また、`CLUSTERNAME` はクラスターの名前に置き換えます。 次のコマンドを入力して、クラスターとの SSH 接続を開きます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kafka ブローカー ホストを取得するには、次のコマンドの `<clustername>` と `<password>` に値を代入し、コマンドを実行します。 `<clustername>` には Azure portal に表示されているのと同じ大文字小文字の使い方をしてください。 `<password>` をクラスターのログイン パスワードで置き換えてから、次のコマンドを実行します。

    ```bash
    sudo apt -y install jq
    export clusterName='<clustername>'
    export password='<password>'
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > このコマンドでは、Ambari アクセスが必要です。 クラスターが NSG の背後にある場合は、Ambari にアクセスできるコンピューターからこのコマンドを実行します。

1. 次のコマンドを入力して、Kafka トピック `myTest` を作成します。

    ```bash
    java -jar kafka-producer-consumer.jar create myTest $KAFKABROKERS
    ```

1. プロデューサーを実行し、トピックにデータを書き込むには、次のコマンドを使用します。

    ```bash
    java -jar kafka-producer-consumer.jar producer myTest $KAFKABROKERS
    ```

1. プロデューサーが完了したら、次のコマンドを使用してトピックから読み取ります。

    ```bash
    java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS
    scp ./target/kafka-producer-consumer*.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```

    読み取られたレコードが、レコードの件数とともに表示されます。

1. __Ctrl+C__ キーを使用してコンシューマーを終了します。

### <a name="multiple-consumers"></a>複数のコンシューマー

Kafka コンシューマーは、レコードを読み取る際に、コンシューマー グループを使用します。 複数のコンシューマーで同じグループを使用すると、トピックからの読み取りの負荷が分散されます。 グループ内の各コンシューマーは、レコードの一部を受け取ります。

コンシューマー アプリケーションは、グループ ID として使用されるパラメーターを受け取ります。 たとえば、次のコマンドは `myGroup` のグループ ID を使用して、コンシューマーを起動します。

```bash
java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup
```

__Ctrl+C__ キーを使用してコンシューマーを終了します。

このプロセスを実際に確認するには、次のコマンドを使用します。

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; split-window -h 'java -jar kafka-producer-consumer.jar consumer myTest $KAFKABROKERS myGroup' \
\; attach
```

このコマンドは、`tmux` を使用してターミナルを 2 つの列に分割します。 同じグループ ID 値を持つ各列でコンシューマーが開始されます。 コンシューマーが読み取りを完了した後は、それぞれがレコードの一部だけを読み取ることに注意してください。 __Ctrl + C__ を 2 回使用して、`tmux` を終了します。

同じグループ内のクライアントによる消費は、トピックのパーティションを介して処理されます。 このコード サンプルでは、先ほど作成した `test` トピックに、8 つのパーティションがあります。 8 つのコンシューマーを開始すると、各コンシューマーはトピックの 1 つのパーティションからレコードを読み取ります。

> [!IMPORTANT]  
> コンシューマー グループに、パーティションの数よりも多いコンシューマー インスタンスを含めることはできません。 この例では、トピック内のパーティション数が 8 であるため、1 つのコンシューマー グループに最大 8 個のコンシューマーを含めることができます。 または、それぞれコンシューマーが 8 個以下の複数のコンシューマー グループを存在させることができます。

Kafka に格納されたレコードは、受信した順番でパーティション内に格納されます。 *パーティション内*のレコードの順次配信順を実現するには、コンシューマー インスタンスの数がパーティションの数と同じコンシューマー グループを作成します。 *トピック内*のレコードの順次配信を実現するには、コンシューマー インスタンスが 1 つのみのコンシューマー グループを作成します。

## <a name="common-issues-faced"></a>発生する一般的な問題

1. **トピックの作成が失敗する** クラスターで Enterprise Security Pack が有効になっている場合は、[プロデューサーとコンシューマー用に事前に作成した JAR ファイルを使用](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Prebuilt-Jars/kafka-producer-consumer-esp.jar)します。 ESP jar は、[`DomainJoined-Producer-Consumer` サブディレクトリ](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer)のコードから作成できます。 プロデューサーとコンシューマーのプロパティには、ESP が有効なクラスター用の追加のプロパティ `CommonClientConfigs.SECURITY_PROTOCOL_CONFIG` があることに注意してください。

2. **ESP が有効なクラスターで問題が発生する** 生成と消費の操作が失敗し、ESP が有効なクラスターを使用している場合は、すべての Ranger ポリシーにユーザー `kafka` が存在していることを確認してください。 存在しない場合は、すべての Ranger ポリシーに追加してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするために、リソース グループを削除することができます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 __[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、HDInsight 上の Kafka で Apache Kafka Producer および Consumer API を使用する方法について説明しました。 次の各ドキュメントを参考に、Kafka の使用の詳細を確認してください。

* [Kafka REST プロキシを使用する](rest-proxy.md)
* [Apache Kafka ログを分析する](apache-kafka-log-analytics-operations-management.md)
