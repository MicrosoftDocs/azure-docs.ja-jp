---
title: チュートリアル:Apache Kafka で Apache Storm を使用する - Azure HDInsight
description: HDInsight の Apache Storm と Apache Kafka を使用してストリーミング パイプラインを作成する方法について説明します。 このチュートリアルでは、KafkaBolt コンポーネントと KafkaSpout コンポーネントを使用して Kafka からデータをストリーミングします。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 2078ba177d68cfaa0a91b79611d92e5c68e4e245
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868418"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>チュートリアル:HDInsight 上の Apache Kafka で Apache Storm を使用する

このチュートリアルでは、[Apache Storm](https://storm.apache.org/) トポロジを使用して、HDInsight の [Apache Kafka](https://kafka.apache.org/) でデータを読み書きする方法について説明します。 また、Storm クラスター上の [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 互換ストレージにデータを保持する方法についても説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Storm と Kafka
> * コードについて
> * Kafka クラスターと Storm クラスターの作成
> * トポロジの作成
> * トポロジの構成
> * Kafka トピックの作成
> * トポロジの開始
> * トポロジの停止
> * リソースをクリーンアップする

## <a name="prerequisites"></a>前提条件

* Kafka トピックの作成方法を熟知していること。 詳細については、[HDInsight の Kafka のクイックスタート](./kafka/apache-kafka-get-started.md)に関するドキュメントをご覧ください。

* Storm ソリューション (トポロジ) の作成とデプロイの方法を熟知していること。 具体的には、[Apache Storm Flux](https://storm.apache.org/releases/current/flux.html) フレームワークを使用するトポロジです。 詳細については、「[Java での Apache Storm トポロジの作成](./storm/apache-storm-develop-java-topology.md)」を参照してください。

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) 以上 HDInsight 3.5 以降には Java 8 が必要です。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* SSH クライアント (`ssh` と `scp` コマンドが必要です) - 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* `JAVA_HOME` - JDK がインストールされているディレクトリを指している必要があります。
* `PATH` - 次のパスを含む必要があります。
  
    * `JAVA_HOME` (または同等のパス)。
    * `JAVA_HOME\bin` (または同等のパス)。
    * Maven がインストールされているディレクトリ。

> [!IMPORTANT]  
> このドキュメントの手順には、HDInsight の Storm クラスターと HDInsight の Kafka クラスターの両方を含む Azure リソース グループが必要です。 これらのクラスターは両方とも、Strom クラスターが Kafka クラスターと直接通信できるように、Azure 仮想ネットワーク内に配置します。
> 
> 利便性のために、このドキュメントは、必要なすべての Azure リソースを作成できるテンプレートにリンクしています。 
>
> 仮想ネットワークでの HDInsight の使用方法の詳細については、[HDInsight 用の仮想ネットワークの計画](hdinsight-plan-virtual-network-deployment.md)に関するドキュメントを参照してください。

## <a name="storm-and-kafka"></a>Storm と Kafka

Apache Storm には、Apache Kafka を操作するためのコンポーネントがいくつか用意されています。 このチュートリアルでは、次のコンポーネントを使用します。

* `org.apache.storm.kafka.KafkaSpout`:このコンポーネントは Kafka からデータを読み取ります。 このコンポーネントは、次のコンポーネントに依存します。

    * `org.apache.storm.kafka.SpoutConfig`:スパウト コンポーネントの構成を提供します。

    * `org.apache.storm.spout.SchemeAsMultiScheme` および `org.apache.storm.kafka.StringScheme`:Kafka のデータを Storm のタプルに変換します。

* `org.apache.storm.kafka.bolt.KafkaBolt`:このコンポーネントはデータを Kafka に書き込みます。 このコンポーネントは、次のコンポーネントに依存します。

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`:書き込みの対象となるトピックを記述します。

    * `org.apache.kafka.common.serialization.StringSerializer`:データを文字列値としてシリアル化するようにボルトを構成します。

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`:Storm トポロジ内で使用されるタプル データ構造から、Kafka に格納されたフィールドにマップします。

これらのコンポーネントは、`org.apache.storm : storm-kafka` パッケージで提供されます。 Storm のバージョンに適合するパッケージ バージョンを使用してください。 HDInsight 3.6 の場合、Storm のバージョンは 1.1.0 です。
その他の Kafka コンポーネントが含まれた `org.apache.kafka : kafka_2.10` パッケージも必要です。 Kafka のバージョンに適合するパッケージ バージョンを使用してください。 HDInsight 3.6 の場合、Kafka のバージョンは 1.1.1 です。

次の XML は、[Apache Maven](https://maven.apache.org/) プロジェクトの `pom.xml` 内の依存関係宣言を示しています。

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>コードについて

このドキュメントで使用するコードは、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) で入手できます。

このチュートリアルでは、次の 2 つのトポロジが提供されます。

* Kafka-writer: ランダムな文を生成し、それらを Kafka に格納します。

* Kafka-reader: Kafka からデータを読み取り、Storm クラスターの HDFS 互換ファイル ストアに格納します。

    > [!WARNING]  
    > HDInsight で使用される HDFS 互換ストレージを Storm で操作できるようにするには、スクリプト アクションが必要です。 このスクリプトでは、Storm の `extlib` パスに複数の jar ファイルをインストールします。 このチュートリアルのテンプレートでは、クラスターの作成時に、このスクリプトが自動的に使用されます。
    >
    > Storm クラスターの作成にこのドキュメントのテンプレートを使用しない場合は、スクリプト アクションをクラスターに手動で適用する必要があります。
    >
    > このスクリプト アクションは [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) にあり、Storm クラスターのスーパーバイザー ノードと Nimbus ノードに適用されます。 スクリプト アクションの使用方法の詳細については、[スクリプト アクションを使用した HDInsight のカスタマイズ](hdinsight-hadoop-customize-cluster-linux.md)に関するドキュメントをご覧ください。

これらのトポロジは、[Flux](https://storm.apache.org/releases/current/flux.html) で定義されています。 Flux は Storm 0.10.x で導入されており、これによりトポロジ構成とコードを切り離すことができます。 Flux フレームワークを使用するトポロジの場合、YAML ファイルでトポロジを定義します。 YAML ファイルはトポロジの一部として含めることができるほか、 トポロジを送信するときに使用するスタンドアロン ファイルにもなります。 Flux では実行時の変数代入もサポートされており、以下の例ではこれを使用しています。

これらのトポロジの実行時に、次のパラメーターが設定されます。

* `${kafka.topic}`:トポロジの読み取り/書き込みの対象になる Kafka トピックの名前。

* `${kafka.broker.hosts}`:Kafka ブローカーが実行されるホスト。 ブローカー情報は、KafkaBolt が Kafka への書き込み時に使用します。

* `${kafka.zookeeper.hosts}`:Kafka クラスター上で Zookeeper が実行されるホスト。

* `${hdfs.url}`:HDFSBolt コンポーネントのファイル システム URL。 Azure Storage アカウントと Azure Data Lake Storage のどちらにデータを書き込むかを示します。

* `${hdfs.write.dir}`:データの書き込み先のディレクトリ。

Flux トポロジの詳細については、[https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) を参照してください。

### <a name="kafka-writer"></a>Kafka-writer

Kafka-writer トポロジでは、Kafka ボルト コンポーネントがパラメーターとして 2 つの文字列値を受け取ります。 これらのパラメーターは、ボルトから __キー__ 値および __メッセージ__ 値として Kafka に送信するタプル フィールドを示します。 キーは、Kafka でデータをパーティション分割するために使用されます。 メッセージは格納されるデータです。

この例では、`com.microsoft.example.SentenceSpout` コンポーネントは、`key` と `message` の 2 つのフィールドを含むタプルを生成します。 Kafka ボルトはこれらのフィールドを抽出し、フィールド内のデータを Kafka に送信します。

これらのフィールドに、`key` および `message` という名前を使用する必要はありません。 マッピングをわかりやすくするために、このプロジェクトではこれらの名前を使用しています。

次の YAML は、Kafka-writer コンポーネントの定義を示しています。

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-reader

Kafka-reader トポロジでは、スパウト コンポーネントが文字列値として Kafka からデータを読み取ります。 その後、データは、ログ コンポーネントによって Storm ログに書き込まれ、HDFS ボルト コンポーネントによって Storm クラスターの HDFS 互換ファイル システムに書き込まれます。

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>プロパティ置換

プロジェクトには、トポロジで使用されるパラメーターを渡す際に使用する `dev.properties` という名前のファイルが含まれています。 次のプロパティが定義されます。

| dev.properties ファイル | 説明 |
| --- | --- |
| `kafka.zookeeper.hosts` | Kafka クラスターの [Apache ZooKeeper](https://zookeeper.apache.org/) ホスト。 |
| `kafka.broker.hosts` | Kafka ブローカー ホスト (ワーカー ノード)。 |
| `kafka.topic` | トポロジで使用される Kafka トピック。 |
| `hdfs.write.dir` | Kafka-reader トポロジによる書き込み先のディレクトリ。 |
| `hdfs.url` | Storm クラスターで使用されるファイル システム。 Azure ストレージ アカウントの場合、値として `wasb://` を使用します。 Azure Data Lake Storage Gen2 の場合、値として `abfs://` を使用します。 Azure Data Lake Storage Gen1 の場合、値として `adl://` を使用します。 |

## <a name="create-the-clusters"></a>クラスターの作成

HDInsight の Apache Kafka では、パブリック インターネットを介した Kafka ブローカーへのアクセスは提供されていません。 Kafka を使用するものは、すべて同じ Azure 仮想ネットワークに属している必要があります。 このチュートリアルでは、Kafka クラスターと Storm クラスターを同じ Azure 仮想ネットワーク内に配置します。 

次の図に、Storm と Kafka 間の通信フローを示します。

:::image type="content" source="./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png" alt-text="Azure 仮想ネットワークにおける Strom クラスターと Kafka クラスターの図":::

> [!NOTE]  
> SSH や [Apache Ambari](https://ambari.apache.org/) など、クラスター上の他のサービスは、インターネット経由でアクセスできます。 HDInsight で使用できるパブリック ポートの詳細については、「[HDInsight で使用されるポートと URI](hdinsight-hadoop-port-settings-for-services.md)」を参照してください。

Azure 仮想ネットワークを作成し、その仮想ネットワーク内に Kafka クラスターと Storm クラスターを作成するには、次の手順に従います。

1. 次のボタンを使用して Azure にサインインし、Azure Portal でテンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Azure Resource Manager テンプレートは、 **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** にあります。 このテンプレートは次のリソースを作成します。

    * Azure リソース グループ
    * Azure Virtual Network
    * Azure ストレージ アカウント
    * HDInsight バージョン 3.6 上の Kafka (3 ワーカー ノード)
    * HDInsight バージョン 3.6 上の Storm (3 ワーカー ノード)

   > [!WARNING]  
   > HDInsight で Kafka の可用性を保証するには、クラスターに少なくとも 3 つのワーカー ノードが必要です。 このテンプレートは、3 つのワーカー ノードが含まれる Kafka クラスターを作成します。

2. 次のガイダンスに従って、 **[カスタム デプロイ]** セクションの各エントリに入力します。

   1. 次の情報に従って、 **[カスタマイズされたテンプレート]** セクションの各エントリに入力します。

      | 設定 | 値 |
      | --- | --- |
      | サブスクリプション | お使いの Azure サブスクリプション |
      | Resource group | リソースが含まれるリソース グループ。 |
      | 場所 | リソースが作成される Azure リージョン。 |
      | [Kafka Cluster Name]\(Kafka クラスター名\) | Kafka クラスターの名前。 |
      | [Storm Cluster Name]\(Storm クラスター名\) | Storm クラスターの名前。 |
      | [Cluster Login User Name]\(クラスター ログイン ユーザー名\) | クラスターの管理者ユーザー名。 |
      | [クラスター ログイン パスワード] | クラスターの管理者ユーザー パスワード。 |
      | [SSH ユーザー名] | クラスター用に作成する SSH ユーザー。 |
      | [SSH パスワード] | SSH ユーザーのパスワード。 |
   
      :::image type="content" source="./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png" alt-text="テンプレート パラメーターの画像":::

3. **使用条件** を読み、 **[上記の使用条件に同意する]** をオンにします。

4. 最後に、 **[ダッシュボードにピン留めする]** をオンにし、 **[購入]** をクリックします。

> [!NOTE]  
> クラスターの作成には最大で 20 分かかります。

## <a name="build-the-topology"></a>トポロジの作成

1. 開発環境で、[https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) からプロジェクトをダウンロードし、コマンドラインを開いてプロジェクトをダウンロードした場所にディレクトリを変更します。

2. **hdinsight-storm-java-kafka** ディレクトリで次のコマンドを使用して、プロジェクトをコンパイルしデプロイ用のパッケージを作成します。

   ```bash
   mvn clean package
   ```

    このパッケージ処理では、`target` ディレクトリに `KafkaTopology-1.0-SNAPSHOT.jar` という名前のファイルが作成されます。

3. 次のコマンドを使用して、HDInsight の Storm クラスターにパッケージをコピーします。 `sshuser` をクラスターの SSH ユーザー名に置き換えます。 `stormclustername` を __Storm__ クラスターの名前に置き換えます。

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。

## <a name="configure-the-topology"></a>トポロジの構成

1. 次のいずれかの方法を使用して、HDInsight クラスター上の **Kafka** の Kafka Broker ホストを検出します。

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > 次の Bash の例では、`$CLUSTERNAME` に __Kafka__ クラスターの名前が含まれていることを前提とします。 また、[jq](https://stedolan.github.io/jq/) バージョン 1.5 以降がインストールされているものとします。 プロンプトが表示されたら、クラスターのログイン アカウントのパスワードを入力してください。

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    次のテキストのような値が返されます。

    ```output
    wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > クラスターに 2 つ以上のブローカー ホストがある場合でも、すべてのホストの完全な一覧をクライアントに提供する必要はありません。 1 つまたは 2 つで十分です。

2. 次のいずれかのを使用して、HDInsight クラスタ上の __Kafka__ の Kafka Broker ホストを検出します。

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > 次の Bash の例では、`$CLUSTERNAME` に __Kafka__ クラスターが含まれていることを前提とします。 および [jq](https://stedolan.github.io/jq/) がインストールされいていることを前提としています。 プロンプトが表示されたら、クラスターのログイン アカウントのパスワードを入力してください。

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    次のテキストのような値が返されます。

    ```output
    zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    ```

    > [!IMPORTANT]  
    > クラスターに 2 つ以上の Zookeeper ホストがある場合でも、すべてのホストの完全な一覧をクライアントに提供する必要はありません。 1 つまたは 2 つで十分です。

    この値を保存します。これは後で使用します。

3. プロジェクトのルートにある `dev.properties` ファイルを編集します。 このファイルの該当する行に、__Kafka__ クラスターの Broker ホストと Zookeeper ホストの情報を追加します。 次の例は、前の手順のサンプルの値を使用して構成されています。

    ```bash
    kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
    kafka.topic: stormtopic
    ```

    > [!IMPORTANT]  
    > `hdfs.url` エントリは、Azure ストレージ アカウントを使用するクラスター用に構成されています。 Data Lake Storage を使用する Storm クラスターでこのトポロジを使用するには、この値を `wasb` から `adl` に変更します。

4. `dev.properties` ファイルを保存し、次のコマンドを使用して **Storm** クラスターにアップロードします。

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    **USERNAME** は、クラスターの SSH ユーザー名に置き換えます。 **BASENAME** は、クラスターの作成時に使用したベース名に置き換えます。

## <a name="create-the-kafka-topic"></a>Kafka トピックの作成

Kafka では、"_トピック_" にデータが格納されます。 Storm トポロジを開始する前に、トピックを作成する必要があります。 トポロジを作成するには、次の手順に従います。

1. 次のコマンドを使用して、SSH 経由で __Kafka__ クラスターに接続します。 `sshuser` は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 `kafkaclustername` を Kafka クラスターの名前に置き換えます。

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. Kafka トピックを作成するには、次のコマンドを使用します。 `$KAFKAZKHOSTS` を、トポロジの構成時に使用した Zookeeper ホスト情報に置き換えます。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    このコマンドでは、Kafka クラスターの Zookeeper に接続し、`stormtopic` という名前の新しいトピックを作成します。 このトピックが Storm トポロジで使用されます。

## <a name="start-the-writer"></a>ライターの起動

1. 次のコマンドを使用して、SSH 経由で **Storm** クラスターに接続します。 `sshuser` は、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 `stormclustername` を Storm クラスターの名前に置き換えます。

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    メッセージが表示されたら、クラスターの作成時に使用したパスワードを入力します。
   
    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. Storm クラスターに SSH で接続してから、次のコマンドを使用してライター トポロジを起動します。

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    このコマンドで使用されているパラメーターの意味は次のとおりです。

    * `org.apache.storm.flux.Flux`:Flux を使用してこのトポロジを構成および実行します。

    * `--remote`:トポロジを Nimbus に送信します。 トポロジは、クラスター内のワーカー ノード全体に配布されます。

    * `-R /writer.yaml`:`writer.yaml` ファイルを使用して、トポロジを構成します。 `-R` は、このリソースが jar ファイル内に含まれていることを指示しています。 リソースは jar のルートにあるため、`/writer.yaml` がリソースへのパスになります。

    * `--filter`:`dev.properties` ファイルの値を使用して、`writer.yaml` トポロジのエントリを作成します。 たとえば、ファイル内の `kafka.topic` エントリの値を使用して、トポロジの定義内の `${kafka.topic}` エントリを置き換えます。

## <a name="start-the-reader"></a>リーダーの起動

1. Storm クラスターへの SSH セッションで、次のコマンドを使用してリーダー トポロジを起動します。

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. 少し待ってから、次のコマンドを使用して、リーダー トポロジによって作成されたファイルを表示します。

    ```bash
    hdfs dfs -ls /stormdata
    ```

    出力は次のテキストのようになります。

    ```output
    Found 173 items
      -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
      -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
      -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
      ...
    ```

3. ファイルの内容を表示するには、次のコマンドを使用します。 `filename.txt` をファイル名に置き換えます。

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    次のテキストはファイルの内容の一例です。

    > 87 年前
    >
    > 白雪姫と 7 人の小人
    >
    > 私の本質は 2 才児です
    >
    > 白雪姫と 7 人の小人
    >
    > 私の本質は 2 才児です
    >
    > 87 年前
    >
    > 1 日 1 個のリンゴで医者いらず

## <a name="stop-the-topologies"></a>トポロジの停止

Storm クラスターへの SSH セッションで、次のコマンドを使用して Storm の各トポロジを停止します。

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするために、リソース グループを削除することができます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 __[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、[Apache Storm](https://storm.apache.org/) トポロジを使用して、HDInsight の [Apache Kafka](https://kafka.apache.org/) に対して書き込み/読み取りを実行する方法を説明しました。 また、HDInsight で使用される [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) 互換ストレージにデータを格納する方法も説明しました。

> [!div class="nextstepaction"]
> [Apache Kafka Producer API と Consumer API を使用する](kafka/apache-kafka-producer-consumer-api.md)
