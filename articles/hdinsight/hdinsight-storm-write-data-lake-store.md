---
title: "Azure HDInsight で Apache Storm によって Azure Data Lake Store を使用する"
description: "HDInsight 上の Apache Storm トポロジから Azure Data Lake Store にデータを書き込む方法について説明します。 このドキュメントおよび関連する例では、Data Lake Store への書き込みに HdfsBolt コンポーネントを使用する方法をデモンストレーションします。"
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/03/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 514319dfcb532ab3708352b2467c095d7775b714
ms.lasthandoff: 03/25/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>HDInsight で Apache Storm によって Azure Data Lake Store を使用する (Java)

Azure Data Lake Store は、データの高スループット、可用性、耐久性、および信頼性を提供する、HDFS 互換のクラウド ストレージ サービスです。 このドキュメントでは、Java ベースの Storm トポロジを使用して Azure Data Lake Store にデータを書き込む方法について説明します。 このドキュメントで示す手順は、Apache Storm の一部として提供されている [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントです。

> [!IMPORTANT]
> このドキュメントで使用されるトポロジの例は、HDInsight クラスターの Storm に含まれているコンポーネントに依存しており、他の Apache Storm クラスターで使用する場合には Azure Data Lake Store を動作させるための修正が必要になることがあります。

## <a name="how-to-work-with-azure-data-lake-store"></a>Azure Data Lake Store の操作方法

Data Lake Store は、HDFS 互換ファイル システムとして HDInsight に表示されるため、Storm-HDFS ボルトを使用して書き込むことができます。 HDInsight から Azure Data Lake を使用する場合は、`adl://` というファイル スキームを使用できます。

* Data Lake Storage がクラスターのプライマリ ストレージである場合は、`adl:///` を使用します。 これは Azure Data Lake のクラスター ストレージのルートです。 これは、Data Lake Storage アカウントの /clusters/CLUSTERNAME のパスに変換されます。
* Data Lake Storage がクラスターのセカンダリ ストレージである場合は、`adl://DATALAKEACCOUNT.azuredatalakestore.net/` を使用します。 この URI は、データが書き込まれる Data Lake Storage アカウントを指定します。 データは Data Lake Store のルートから書き込まれます。

    > [!NOTE]
    > この URI 形式を使用して、クラスターのプライマリ ストレージを含む Data Lake Store アカウントにデータを保存することもできます。 これにより、HDInsight を含むディレクトリ パスの外部のデータを保存することができます。

次の Java コードは、`MYDATALAKE` という名前の Data Lake Store アカウントで、Storm-HDFS ボルトを使用して、`/stormdata` という名前のディレクトリにデータを書き込む方法を示しています。

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

次の YAML は、Flux フレームワークの Storm-HDFS ボルトを使用する方法を示しています。

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
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
```

> [!NOTE]
> このドキュメントの例では、Flux フレームワークを使用します。

## <a name="prerequisites"></a>前提条件

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 以上 HDInsight 3.5 では、Java 8 が必要です。

* [Maven 3.x](https://maven.apache.org/download.cgi)

* HDInsight クラスター バージョン 3.5 の Storm。 HDInsight クラスターに新しい Storm を作成するには、 [Azure での HDInsight と Data Lake Store の使用](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) に関するドキュメントの手順に従います。

### <a name="configure-environment-variables"></a>環境変数を構成する

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。 たとえば、Unix や Linux ディストリビューションの場合は、 `/usr/lib/jvm/java-8-oracle`のような値になります。 Windows の場合は、 `c:\Program Files (x86)\Java\jre1.8`のような値になります。
* **PATH** - 次のパスを含む必要があります。
  
  * **JAVA\_HOME** または同等のパス
  * **JAVA\_HOME\bin** または同等のパス
  * Maven がインストールされているディレクトリ

## <a name="topology-implementation"></a>トポロジの実装

このドキュメントで使用される例は、Java で記述され、以下のコンポーネントを使用します。

* **TickSpout**: トポロジ内の他のコンポーネントによって使用されるデータを生成します。
* **PartialCount**: TickSpout によって生成されたイベントをカウントします。
* **FinalCount**: PartialCount のカウント データを集計します。
* **ADLStoreBolt**: [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントを使用して Azure Data Lake Store にデータを書き込みます。

このトポロジを含むプロジェクトは、 [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store)からダウンロードして利用できます。

## <a name="build-and-package-the-topology"></a>トポロジをビルドおよびパッケージ化する

1. サンプル プロジェクトを [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) から開発環境にダウンロードします。

2. エディターで `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` ファイルを開き、`.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")` という記述がある行を見つけます。 **MYDATALAKE** を、HDInsight サーバーの作成時に使用した Azure Data Lake Store の名前に変更します。

3. コマンド プロンプト、ターミナル、またはシェル セッションで、ダウンロードしたプロジェクトのルート ディレクトリに移動し、次のコマンドを実行してトポロジをビルドおよびパッケージ化します。
   
        mvn compile
        mvn package
   
    ビルドとパッケージ化が完了すると、`target` という名前の新しいディレクトリが作成されます。このディレクトリには、`StormToDataLakeStore-1.0-SNAPSHOT.jar` という名前のファイルが含まれています。 このファイルに、コンパイルされたトポロジが含まれています。

## <a name="deploy-and-run-the-topology"></a>トポロジをデプロイおよび実行する

1. 次のコマンドを使用して、トポロジを HDInsight クラスターにコピーします。 **USER** を、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **CLUSTERNAME** は、クラスターの名前に置き換えます。
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。 パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
   
   > [!NOTE]
   > 開発に Windows クライアントを使用している場合、`scp` コマンドがないことがあります。 その場合は、`pscp` を使用できます。このコマンドは、[http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) で入手できます。

2. アップロードが完了したら、次のようにして、SSH を使用して HDInsight クラスターに接続します。 **USER** を、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **CLUSTERNAME** は、クラスターの名前に置き換えます。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。 パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
   
   詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 接続したら、次のコマンドを使用して `dev.properties` という名前のファイルを作成します。

        nano dev.properties

4. `dev.properties` ファイルの内容として、次のテキストを使用します。

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    ファイルを保存するには、__Ctrl + X__ キーを押してから __Y__ キー、__Enter__ キーの順に押します。 このファイル内の値により、Data Lake ストアの URL とデータが書き込まれるディレクトリ名が設定されます。

3. 次のコマンドを使用して、トポロジを開始します。
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    このコマンドにより、Flux フレームワークを使用してトポロジが開始されます。 トポロジは jar に含まれる `datalakewriter.yaml` ファイルによって定義されます。 `dev.properties` ファイルはフィルターとして渡され、ファイルに含まれる値がトポロジによって読み取られます。

## <a name="view-output-data"></a>出力データを表示する

データを表示するには、次のコマンドを使用します。

    hdfs dfs -ls /stormdata/

これにより、トポロジによって作成されたファイルの一覧が表示されます。

Data Lake Store がクラスターの既定のストレージでない場合は、次のコマンドを使用してデータを表示します。

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

上記のコマンドで、__MYDATALAKE__ を Data Lake Store アカウントに置き換えます。

次の一覧は、上記のコマンドによって返されるデータの例です。

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>トポロジを停止する

Storm トポロジは、停止されるか、クラスターが削除されるまで実行され続けます。 トポロジを停止するには、次の情報を使用します。

**Linux ベースの HDInsight の場合**:

クラスターへの SSH セッションで、次のコマンドを使用します。

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次のステップ

ここでは、Storm を使用して Azure Data Lake Store に書き込む方法を学習しました。他の [HDInsight 用 Storm サンプル](hdinsight-storm-example-topology.md)もご覧ください。


