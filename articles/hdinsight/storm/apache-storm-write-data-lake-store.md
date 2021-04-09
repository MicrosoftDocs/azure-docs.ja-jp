---
title: 'チュートリアル: HDInsight Apache Storm からストレージへ - Azure Data Lake'
description: チュートリアル - Apache Storm を使用して、Azure HDInsight 用の HDFS と互換性のあるストレージに書き込む方法について説明します。
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932547"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>チュートリアル: Azure HDInsight 上の Apache Storm から Apache Hadoop HDFS に書き込む

Apache Storm を使用して、HDInsight 上の Apache Storm によって使用される HDFS と互換性のあるストレージにデータを書き込む方法について説明します。 HDInsight では、HDFS と互換性のあるストレージとして Azure Storage と Azure Data Lake Storage の両方を使用できます。 Storm は、HDFS にデータを書き込む [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) コンポーネントを提供します。 このドキュメントでは、HdfsBolt から両方の種類のストレージへの書き込みに関する情報を提供します。

このドキュメントで使用されているトポロジの例は、HDInsight 上の Storm に含まれているコンポーネントによって異なります。 他の Apache Storm クラスターと共に使用された場合、Azure Data Lake Storage で動作するには、変更が必要になる可能性があります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * スクリプト アクションを使ってクラスターを構成する
> * トポロジをビルドおよびパッケージ化する
> * トポロジをデプロイおよび実行する
> * 出力データを表示する
> * トポロジを停止する

## <a name="prerequisites"></a>前提条件

* [Java Developer Kit (JDK) バージョン 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Apache に従って適切に[インストール](https://maven.apache.org/install.html)された [Apache Maven](https://maven.apache.org/download.cgi)。  Maven は Java プロジェクトのプロジェクト ビルド システムです。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

* クラスターのプライマリ ストレージの [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage で安全な転送が有効になっている場合、URI は `wasbs://` になります。  [安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページも参照してください。

### <a name="example-configuration"></a>構成例

次の YAML は、この例に含まれている `resources/writetohdfs.yaml` ファイルからの抜粋です。 このファイルは、Apache Storm の [Flux](https://storm.apache.org/releases/current/flux.html) フレームワークを使用して Storm トポロジを定義します。

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

* `syncPolicy`: ファイルがファイル システムにいつ同期/フラッシュされるかを定義します。 この例では、1000 組ごとです。
* `fileNameFormat`: ファイルを書き込むときに使用するパスとファイル名のパターンを定義します。 この例では、パスはフィルターを使用して実行時に指定され、ファイル拡張子は `.txt` です。
* `recordFormat`: 書き込まれるファイルの内部形式を定義します。 この例では、フィールドは `|` 文字で区切られます。
* `rotationPolicy`: ファイルをいつローテーションするかを定義します。 この例では、ローテーションは実行されません。
* `hdfs-bolt`: 前のコンポーネントを `HdfsBolt` クラスの構成パラメーターとして使用します。

Flux フレームワークの詳細については、「[https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html)」を参照してください。

## <a name="configure-the-cluster"></a>クラスターを構成する

既定では、HDInsight 上の Storm には、`HdfsBolt` が Storm のクラスパス内の Azure Storage または Data Lake Storage と通信するために使用するコンポーネントは含まれていません。 これらのコンポーネントをクラスター上の Storm 用の `extlib` ディレクトリに追加するには、次のスクリプト アクションを使用します。

| プロパティ | 値 |
|---|---|
|スクリプトの種類 |- Custom|
|Bash スクリプト URI |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|ノードの種類 |Nimbus、Supervisor|
|パラメーター |なし|

このスクリプトのクラスターでの使用については、「[スクリプト アクションを使用して HDInsight クラスターをカスタマイズする](./../hdinsight-hadoop-customize-cluster-linux.md)」のドキュメントを参照してください。

## <a name="build-and-package-the-topology"></a>トポロジをビルドおよびパッケージ化する

1. サンプル プロジェクトを [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) から開発環境にダウンロードします。

2. コマンド プロンプト、ターミナル、またはシェル セッションから、ダウンロードされたプロジェクトのルートにディレクトリを変更します。 トポロジを構築およびパッケージ化するには、次のコマンドを使用します。

    ```cmd
    mvn compile package
    ```

    構築およびパッケージ化が完了すると、`target` という名前の新しいディレクトリが存在し、そこには `StormToHdfs-1.0-SNAPSHOT.jar` という名前のファイルが含まれています。 このファイルには、コンパイルされたトポロジが含まれています。

## <a name="deploy-and-run-the-topology"></a>トポロジをデプロイおよび実行する

1. 次のコマンドを使用して、トポロジを HDInsight クラスターにコピーします。 `CLUSTERNAME` をクラスターの名前に置き換えます。

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. アップロードが完了したら、次のようにして、SSH を使用して HDInsight クラスターに接続します。 `CLUSTERNAME` をクラスターの名前に置き換えます。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 接続したら、次のコマンドを使用して `dev.properties` という名前のファイルを作成します。

    ```bash
    nano dev.properties
    ```

1. `dev.properties` ファイルの内容として、次のテキストを使用します。 [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)に基づいて必要に応じて修正してください。

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    ファイルを保存するには、__Ctrl + X__ キーを押してから __Y__ キー、__Enter__ キーの順に押します。 このファイル内の値により、ストレージの URL と、データが書き込まれるディレクトリ名が設定されます。

1. 次のコマンドを使用して、トポロジを開始します。

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    このコマンドは、トポロジをクラスターの Nimbus ノードに送信することによって、Flux フレームワークを使用してトポロジを開始します。 トポロジは jar に含まれる `writetohdfs.yaml` ファイルによって定義されます。 `dev.properties` ファイルはフィルターとして渡され、ファイルに含まれる値がトポロジによって読み取られます。

## <a name="view-output-data"></a>出力データを表示する

データを表示するには、次のコマンドを使用します。

  ```bash
  hdfs dfs -ls /stormdata/
  ```

このトポロジによって作成されたファイルの一覧が表示されます。 次の一覧は、上記のコマンドによって返されるデータの例です。

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>トポロジを停止する

Storm トポロジは、停止されるか、またはクラスターが削除されるまで実行されます。 トポロジを停止するには、次のコマンドを使用します。

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするために、リソース グループを削除することができます。 リソース グループを削除すると、関連付けられている HDInsight クラスター、およびリソース グループに関連付けられているその他のリソースも削除されます。

Azure Portal を使用してリソース グループを削除するには:

1. Azure Portal で左側のメニューを展開してサービスのメニューを開き、 __[リソース グループ]__ を選択して、リソース グループの一覧を表示します。
2. 削除するリソース グループを見つけて、一覧の右側にある __[詳細]__ ボタン ([...]) を右クリックします。
3. __[リソース グループの削除]__ を選択し、確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Apache Storm を使用して、HDInsight 上の Apache Storm によって使用される HDFS と互換性のあるストレージにデータを書き込む方法について説明します。

> [!div class="nextstepaction"]
> 他の [HDInsight の Apache Storm の例](apache-storm-example-topology.md)を確認