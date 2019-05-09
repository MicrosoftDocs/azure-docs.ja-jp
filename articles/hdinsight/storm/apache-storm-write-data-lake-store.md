---
title: Storage/Data Lake Storage への Apache Storm 書き込み - Azure HDInsight
description: Apache Storm を使用して、HDInsight 用の HDFS と互換性のあるストレージに書き込む方法について説明します。
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.openlocfilehash: 34056b3a562d3110569e05c5c50ae4d9dae2375a
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868138"
---
# <a name="write-to-apache-hadoop-hdfs-from-apache-storm-on-hdinsight"></a>HDInsight 上の Apache Storm から Apache Hadoop HDFS に書き込む

[Apache Storm](https://storm.apache.org/) を使用して、HDInsight 上の Apache Storm によって使用されている HDFS と互換性のあるストレージにデータを書き込む方法について説明します。 HDInsight では、HDFS と互換性のあるストレージとして Azure Storage と Azure Data Lake Storage の両方を使用できます。 Storm は、HDFS にデータを書き込む [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントを提供します。 このドキュメントでは、HdfsBolt から両方の種類のストレージへの書き込みに関する情報を提供します。 

> [!IMPORTANT]  
> このドキュメントで使用されているトポロジの例は、HDInsight 上の Storm に含まれているコンポーネントによって異なります。 他の Apache Storm クラスターと共に使用された場合、Azure Data Lake Storage で動作するには、変更が必要になる可能性があります。

## <a name="get-the-code"></a>コードの入手

このトポロジを含むプロジェクトは、[https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) からダウンロードして入手できます。

このプロジェクトをコンパイルするには、開発環境に次の構成が必要です。

* [Java JDK 1.8](https://aka.ms/azure-jdks) 以上 HDInsight 3.5 以降には Java 8 が必要です。

* [Maven 3.x](https://maven.apache.org/download.cgi)

開発用ワークステーションに Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。 ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* `JAVA_HOME` - JDK がインストールされているディレクトリを指している必要があります。
* `PATH` - 次のパスを含む必要があります。
  
    * `JAVA_HOME` (または同等のパス)。
    * `JAVA_HOME\bin` (または同等のパス)。
    * Maven がインストールされているディレクトリ。

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>HDInsight で HdfsBolt を使用する方法

> [!IMPORTANT]  
> HDInsight 上の Storm で HdfsBolt を使用する前に、まずスクリプト アクションを使用して、必要な jar ファイルを Storm 用の `extpath` にコピーする必要があります。 詳細については、「クラスターを構成する」のセクションを参照してください。

HdfsBolt は、ユーザーが指定したファイル スキームを使用して、HDFS に書き込む方法を理解します。 HDInsight では、次のいずれかのスキームを使用します。

* `wasb://`:Azure Storage アカウントで使用されます。
* `abfs://`:Azure Data Lake Storage Gen2 で使用されます。
* `adl://`:Azure Data Lake Storage Gen1 で使用されます。

次の表は、さまざまなシナリオでファイル スキームを使用する例を示しています。

| スキーム | メモ |
| ----- | ----- |
| `wasb:///` | 既定のストレージ アカウントは、Azure Storage アカウント内の BLOB コンテナーです。 |
| `abfs:///` | 既定のストレージ アカウントは、Azure Data Lake Storage Gen2 アカウント内のディレクトリです |
| `adl:///` | 既定のストレージ アカウントは、Azure Data Lake Storage Gen1 内のディレクトリです。 クラスターの作成中に、そのクラスターの HDFS のルートである Data Lake Storage 内のディレクトリを指定します。 たとえば、`/clusters/myclustername/` ディレクトリです。 |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | クラスターに関連付けられている既定以外の (追加の) Azure Storage アカウント。 |
| `abfs://CONTAINER@ACCOUNT.dfs.core.windows.net/` | クラスターに関連付けられている既定以外の (追加の) Azure Storage アカウント。 |
| `adl://STORENAME/` | クラスターによって使用される Data Lake Storage のルート。 このスキームを使用すると、クラスター ファイル システムを含むディレクトリの外部にあるデータにアクセスできます。 |

詳細については、Apache.org にある [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) のリファレンスを参照してください。

### <a name="example-configuration"></a>構成の例

次の YAML は、この例に含まれている `resources/writetohdfs.yaml` ファイルからの抜粋です。 このファイルは、Apache Storm の [Flux](https://storm.apache.org/releases/1.1.2/flux.html) フレームワークを使用して Storm トポロジを定義します。

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

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

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


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
```

この YAML は、次の項目を定義します。

* `syncPolicy`:ファイルがファイル システムにいつ同期/フラッシュされるかを定義します。 この例では、1000 組ごとです。
* `fileNameFormat`:ファイルを書き込むときに使用するパスとファイル名のパターンを定義します。 この例では、パスはフィルターを使用して実行時に指定され、ファイル拡張子は `.txt` です。
* `recordFormat`:書き込まれるファイルの内部形式を定義します。 この例では、フィールドは `|` 文字で区切られます。
* `rotationPolicy`:ファイルをいつローテーションするかを定義します。 この例では、ローテーションは実行されません。
* `hdfs-bolt`:前のコンポーネントを `HdfsBolt` クラスの構成パラメーターとして使用します。

Flux フレームワークの詳細については、「[https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)」を参照してください。

## <a name="configure-the-cluster"></a>クラスターを構成する

既定では、HDInsight 上の Storm には、HdfsBolt が Storm のクラスパス内の Azure Storage または Data Lake Storage と通信するために使用するコンポーネントは含まれていません。 これらのコンポーネントをクラスター上の Storm 用の `extlib` ディレクトリに追加するには、次のスクリプト アクションを使用します。

* スクリプト URI: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* 適用先のノード:Nimbus、Supervisor
* パラメーター:なし

このスクリプトのクラスターでの使用については、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](./../hdinsight-hadoop-customize-cluster-linux.md)」のドキュメントを参照してください。

## <a name="build-and-package-the-topology"></a>トポロジをビルドおよびパッケージ化する

1. サンプル プロジェクトを [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) から開発環境にダウンロードします。

2. コマンド プロンプト、ターミナル、またはシェル セッションから、ダウンロードされたプロジェクトのルートにディレクトリを変更します。 トポロジを構築およびパッケージ化するには、次のコマンドを使用します。
   
        mvn compile package
   
    構築およびパッケージ化が完了すると、`target` という名前の新しいディレクトリが存在し、そこには `StormToHdfs-1.0-SNAPSHOT.jar` という名前のファイルが含まれています。 このファイルには、コンパイルされたトポロジが含まれています。

## <a name="deploy-and-run-the-topology"></a>トポロジをデプロイおよび実行する

1. 次のコマンドを使用して、トポロジを HDInsight クラスターにコピーします。 **USER** を、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。 パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
   
   > [!NOTE]  
   > HDInsight での `scp` の使用の詳細については、「[HDInsight で SSH を使用する](../hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

2. アップロードが完了したら、次のようにして、SSH を使用して HDInsight クラスターに接続します。 **USER** を、クラスターの作成時に使用した SSH ユーザー名に置き換えます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    メッセージが表示されたら、クラスターの SSH ユーザーの作成時に使用したパスワードを入力します。 パスワードではなく公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーのパスを指定することが必要な場合があります。
   
   詳細については、[HDInsight での SSH の使用](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

3. 接続したら、次のコマンドを使用して `dev.properties` という名前のファイルを作成します。

        nano dev.properties

4. `dev.properties` ファイルの内容として、次のテキストを使用します。

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]  
    > この例では、クラスターが既定のストレージとして Azure Storage アカウントを使用することを前提にしています。 クラスターで Azure Data Lake Storage Gen2 を使用する場合は、代わりに `hdfs.url: abfs:///` を使用します。 クラスターで Azure Data Lake Storage Gen1 を使用する場合は、代わりに `hdfs.url: adl:///` を使用します。
    
    ファイルを保存するには、__Ctrl + X__ キーを押してから __Y__ キー、__Enter__ キーの順に押します。 このファイル内の値により、Data Lake Storage の URL とデータが書き込まれるディレクトリ名が設定されます。

3. 次のコマンドを使用して、トポロジを開始します。
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    このコマンドは、トポロジをクラスターの Nimbus ノードに送信することによって、Flux フレームワークを使用してトポロジを開始します。 トポロジは jar に含まれる `writetohdfs.yaml` ファイルによって定義されます。 `dev.properties` ファイルはフィルターとして渡され、ファイルに含まれる値がトポロジによって読み取られます。

## <a name="view-output-data"></a>出力データを表示する

データを表示するには、次のコマンドを使用します。

    hdfs dfs -ls /stormdata/

このトポロジによって作成されたファイルの一覧が表示されます。

次の一覧は、上記のコマンドによって返されるデータの例です。

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>トポロジを停止する

Storm トポロジは、停止されるか、またはクラスターが削除されるまで実行されます。 トポロジを停止するには、次のコマンドを使用します。

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>クラスターを削除する

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>次の手順

ここまでで、Apache Storm を使用して Azure Storage および Azure Data Lake Storage に書き込む方法を学習したので、他の [HDInsight 用の Apache Storm の例](apache-storm-example-topology.md)を見つけてください。

## <a name="see-also"></a>関連項目
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
