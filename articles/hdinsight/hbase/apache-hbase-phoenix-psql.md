---
title: psql を使用した Apache Phoenix への一括読み込み - Azure HDInsight
description: psql ツールを使用して Azure HDInsight の Apache Phoenix テーブルに一括読み込みデータを読み込みます。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d1ddf69acda442212500200eb6dc326dcbcb3c1b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867799"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>psql を使用した Apache Phoenix へのデータの一括読み込み

[Apache Phoenix](https://phoenix.apache.org/) は、[Apache HBase](../hbase/apache-hbase-overview.md) 上に構築されるオープンソースの超並列リレーショナル データベースです。 Phoenix は、HBase に対して SQL に似たクエリを提供します。 Phoenix では、ユーザーが SQL テーブル、インデックス、ビュー、およびシーケンスを作成、削除、変更できるように、さらに行を個別にまたは一括でアップサートできるように JDBC ドライバーを使用しています。 Phoenix では、HBase 上で待機時間の短いアプリケーションを作成する場合、MapReduce を使用してクエリをコンパイルするのではなく、noSQL ネイティブ コンパイルを使用します。 Phoenix では、コプロセッサを追加して、クライアントが指定したコードの実行をサーバーのアドレス空間でサポートすることにより、データと共存したコードを実行します。 これにより、クライアント/サーバーのデータ転送が最小限に抑えられます。  HDInsight で Phoenix を使用してデータを操作するには、まずテーブルを作成し、そこにデータを読み込みます。

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix を使用した一括読み込み

データを HBase で使用できるようにする方法には複数あります。たとえば、クライアント API の使用、TableOutputFormat による MapReduce ジョブの使用、HBase シェルを使用したデータの動的な入力などがあります。 Phoenix では Phoenix テーブルに CSV データを読み込む方法として 2 つがあります。1 つは `psql` という名前のクライアント読み込みツールであり、もう 1 つは MapReduce ベースの一括読み込みツールです。

`psql` ツールはシングル スレッドであり、メガバイトまたはギガバイトのデータを読み込むのに最適です。 読み込むすべての CSV ファイルに、'.csv' ファイル拡張子が必要です。  `psql` コマンド ラインで '.sql' ファイル拡張子を使用して SQL スクリプト ファイルを指定することもできます。

MapReduce では複数のスレッドが使用されるので、MapReduce による一括読み込みは、大量のデータ ボリューム (通常は実稼働のシナリオ) に対して使用されます。

データの読み込みを開始する場合は、Phoenix が有効になっていて、クエリのタイムアウト設定が正しいことを事前に確認します。  HDInsight クラスターの [Apache Ambari](https://ambari.apache.org/) ダッシュ ボードにアクセスし、[HBase] を選択し、[構成] タブを選択します。下にスクロールし、次のように Apache Phoenix が `enabled` に設定されていることを確認します。

:::image type="content" source="./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png" alt-text="Apache Phoenix HDInsight クラスターの設定" border="true":::

### <a name="use-psql-to-bulk-load-tables"></a>`psql` を使用してテーブルの一括読み込みを行う

1. `createCustomersTable.sql` というファイルを作成し、そのファイルに次のコードをコピーします。 ファイルを保存して閉じます。

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. `listCustomers.sql` というファイルを作成し、そのファイルに次のコードをコピーします。 ファイルを保存して閉じます。

    ```sql
    SELECT * from Customers;
    ```

1. `customers.csv` というファイルを作成し、そのファイルに次のコードをコピーします。 ファイルを保存して閉じます。

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. `customers2.csv` というファイルを作成し、そのファイルに次のコードをコピーします。 ファイルを保存して閉じます。

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. コマンド プロンプトを開き、新しく作成したファイルの場所にディレクトリを変更します。 以下の CLUSTERNAME は HBase クラスターの実際の名前に置き換えます。 次に、コードを実行してファイルをクラスターのヘッドノードにアップロードします。

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. [ssh コマンド](../hdinsight-hadoop-linux-use-ssh-unix.md)を使用してクラスターに接続します。 次のコマンドを編集して CLUSTERNAME をクラスターの名前に置き換えてから、そのコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. SSH セッションから、**psql** ツールの場所にディレクトリを変更します。 以下のコマンドを実行します。

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. データを一括読み込みします。 以下のコードでは、**Customers** テーブルの作成と、その後のデータのアップロードの両方が行われます。

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    `psql` 操作が完了すると、次のようなメッセージが表示されます。

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. 引き続き `psql` を使用して、Customers テーブルの内容を表示できます。 以下のコードを実行します。

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    または、[HBase シェル](./query-hbase-with-hbase-shell.md)か [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) を使用してデータのクエリを実行することもできます。

1. 追加のデータをアップロードします。 テーブルはすでに存在しているので、コマンドでテーブルを指定します。 以下のコマンドを実行します。

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce を使用してテーブルの一括読み込みを行う

クラスター上で分散される高スループットの読み込みを実現するには、MapReduce 読み込みツールを使用します。 このローダーは、すべてのデータを HFiles に変換してから、作成した HFiles を HBase に提供します。

1. このセクションでは、引き続き SSH セッションと前に作成したオブジェクトを使用します。 必要に応じて、上記の手順に従って **Customers** テーブルと **customers.csv** ファイルを作成します。 必要な場合は、SSH 接続を再確立します。

1. **Customers** テーブルの内容を切り詰めます。 開いている SSH セッションから以下のコマンドを実行します。

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. `customers.csv` ファイルをヘッドノードから Azure Storage にコピーします。

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. MapReduce 一括読み込みコマンド用の実行ディレクトリに変更します。

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Phoenix クライアント jar を指定した `hadoop` コマンドを使用して、CSV MapReduce ローダーを起動します。

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    アップロードが完了すると、次のようなメッセージが表示されます。

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Azure Data Lake Storage で MapReduce を使用するには、Data Lake Storage のルート ディレクトリ (`hbase-site.xml` の `hbase.rootdir` 値) を確認します。 次のコマンドでは、Data Lake Storage のルート ディレクトリは `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` です。 このコマンドで、Data Lake Storage の入力フォルダーと出力フォルダーをパラメーターとして指定します。

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. データのクエリを実行して表示する場合は、前に説明したように **psql** を使用できます。 [HBase シェル](./query-hbase-with-hbase-shell.md)または [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) を使用することもできます。

## <a name="recommendations"></a>推奨事項

* 入力フォルダーと出力フォルダーの両方に同じストレージ メディア (Azure Storage (WASB) または Azure Data Lake Storage (ADL) のいずれか) を使用します。 Azure Storage から Data Lake Storage にデータを転送するには、`distcp` コマンドを使用できます。

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* より大きなサイズのワーカー ノードを使用します。 MapReduce 一括コピーのマップ プロセスでは大量の一時的な出力が生成され、これによって使用可能な、DFS 以外のスペースが占有されます。 大量の一括読み込みの場合は、より多くのより大きなワーカー ノードを使用します。 クラスターに割り当てるワーカー ノードの数は、処理速度に直接影響します。

* 入力ファイルを 10 GB 程のチャンクに分割します。 一括読み込みはストレージの使用率が高い操作です。このため、入力ファイルを複数のチャンクに分割すると、パフォーマンスが向上します。

* リージョン サーバーのホットスポットを回避します。 行キーが単調に増加している場合、HBase の順次書き込みによってリージョン サーバーのホットスポッティングが減少する場合があります。 行キーを "*ソルティング*" すると、順次書き込みが少なくなります。 Phoenix では、特定のテーブルについてソルティング バイトを使用して行キーを透過的にソルティングする方法を提供します。以下を参照してください。

## <a name="next-steps"></a>次のステップ

* [Apache Phoenix を使用した一括データの読み込み](https://phoenix.apache.org/bulk_dataload.html)
* [HDInsight 内の Linux ベースの Apache HBase クラスターで Apache Phoenix を使用する](../hbase/apache-hbase-query-with-phoenix.md)
* [ソルティングされたテーブル](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 文法](https://phoenix.apache.org/language/index.html)
