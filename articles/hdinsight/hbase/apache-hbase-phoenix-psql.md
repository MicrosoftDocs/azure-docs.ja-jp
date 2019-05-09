---
title: psql を使用した Apache Phoenix への一括読み込み - Azure HDInsight
description: psql ツールを使用して Phoenix テーブルに一括読み込みデータを読み込みます。
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 5faea45a55d69cece56137d70862d80dfe335971
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342458"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>psql を使用した Apache Phoenix へのデータの一括読み込み

[Apache Phoenix](https://phoenix.apache.org/) は、[Apache HBase](../hbase/apache-hbase-overview.md) 上に構築されるオープンソースの超並列リレーショナル データベースです。 Phoenix は、HBase に対して SQL に似たクエリを提供します。 Phoenix では、ユーザーが SQL テーブル、インデックス、ビュー、およびシーケンスを作成、削除、変更できるように、さらに行を個別にまたは一括でアップサートできるように JDBC ドライバーを使用しています。 Phoenix では、HBase 上で待機時間の短いアプリケーションを作成する場合、MapReduce を使用してクエリをコンパイルするのではなく、noSQL ネイティブ コンパイルを使用します。 Phoenix では、コプロセッサを追加して、クライアントが指定したコードの実行をサーバーのアドレス空間でサポートすることにより、データと共存したコードを実行します。 これにより、クライアント/サーバーのデータ転送が最小限に抑えられます。  HDInsight で Phoenix を使用してデータを操作するには、まずテーブルを作成し、そこにデータを読み込みます。

## <a name="bulk-loading-with-apache-phoenix"></a>Apache Phoenix を使用した一括読み込み

データを HBase で使用できるようにする方法には複数あります。たとえば、クライアント API の使用、TableOutputFormat による MapReduce ジョブの使用、HBase シェルを使用したデータの動的な入力などがあります。 Phoenix では Phoenix テーブルに CSV データを読み込む方法として 2 つがあります。1 つは `psql` という名前のクライアント読み込みツールであり、もう 1 つは MapReduce ベースの一括読み込みツールです。

`psql` ツールはシングル スレッドであり、メガバイトまたはギガバイトのデータを読み込むのに最適です。 読み込むすべての CSV ファイルに、'.csv' ファイル拡張子が必要です。  `psql` コマンド ラインで '.sql' ファイル拡張子を使用して SQL スクリプト ファイルを指定することもできます。

MapReduce では複数のスレッドが使用されるので、MapReduce による一括読み込みは、大量のデータ ボリューム (通常は実稼働のシナリオ) に対して使用されます。

データの読み込みを開始する場合は、Phoenix が有効になっていて、クエリのタイムアウト設定が正しいことを事前に確認します。  HDInsight クラスターの [Apache Ambari](https://ambari.apache.org/) ダッシュ ボードにアクセスし、[HBase] を選択し、[構成] タブを選択します。下にスクロールし、次のように Apache Phoenix が `enabled` に設定されていることを確認します。

![Apache Phoenix HDInsight クラスターの設定](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>`psql` を使用してテーブルの一括読み込みを行う

1. 新しいテーブルを作成し、クエリをファイル名 `createCustomersTable.sql` で保存します。

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. CSV ファイル (例示した内容) を `customers.csv` として `/tmp/` ディレクトリにコピーし、新しく作成したテーブルへの読み込みに備えます。  `hdfs` コマンドを使用して、目的のソースの場所に CSV ファイルをコピーします。

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. 適切に読み込まれた入力データを確認する SQL SELECT クエリを作成し、そのクエリをファイル名 `listCustomers.sql` で保存します。 任意の SQL クエリを使用することができます。
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. "*新しい*" Hadoop コマンド ウィンドウを開いて、データの一括読み込みを行います。 まず、`cd` コマンドを使用して実行ディレクトリの場所に変更し、次に `psql` ツール (Python `psql.py` コマンド) を使用します。 

    次の例では、前述の手順 2 で行ったように `hdfs` を使用してストレージ アカウントからローカルの temp ディレクトリに `customers.csv` ファイルがコピーされていることを前提とします。

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > `ZookeeperQuorum` 名を特定するには、プロパティ名 `hbase.zookeeper.quorum` が含まれているファイル `/etc/hbase/conf/hbase-site.xml` 内で文字列 [Apache ZooKeeper](https://zookeeper.apache.org/) quorum を検索します。

5. `psql` 操作が完了したら、コマンド ウィンドウにメッセージが表示されるはずです。

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>MapReduce を使用してテーブルの一括読み込みを行う

クラスター上で分散される高スループットの読み込みを実現するには、MapReduce 読み込みツールを使用します。 このローダーは、すべてのデータを HFiles に変換してから、作成した HFiles を HBase に提供します。

1. Phoenix クライアント jar を指定した `hadoop` コマンドを使用して、CSV MapReduce ローダーを起動します。

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. 前述の手順 1 で `CreateCustomersTable.sql` を使用したように、SQL ステートメントで新しいテーブルを作成します。

3. テーブルのスキーマを確認するには、`!describe inputTable` を実行します。

4. 例示した `customers.csv` ファイルなど、入力データへの場所のパスを特定します。 入力ファイルは、WASB/ADLS ストレージ アカウントにある場合があります。 このシナリオ例では、入力ファイルは `<storage account parent>/inputFolderBulkLoad` ディレクトリにあります。

5. MapReduce 一括読み込みコマンド用の実行ディレクトリに変更します。

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. プロパティ名 `hbase.zookeeper.quorum` を使用して、`/etc/hbase/conf/hbase-site.xml` 内の `ZookeeperQuorum` 値を検索します。

7. classpath を設定し、`CsvBulkLoadTool` ツール コマンドを実行します。

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Azure Data Lake Storage で MapReduce を使用するには、Data Lake Storage のルート ディレクトリ (`hbase-site.xml` の `hbase.rootdir` 値) を確認します。 次のコマンドでは、Data Lake Storage のルート ディレクトリは `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` です。 このコマンドで、Data Lake Storage の入力フォルダーと出力フォルダーをパラメーターとして指定します。

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>推奨事項

* 入力フォルダーと出力フォルダーの両方に同じストレージ メディア (Azure Storage (WASB) または Azure Data Lake Storage (ADL) のいずれか) を使用します。 Azure Storage から Data Lake Storage にデータを転送するには、`distcp` コマンドを使用できます。

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* より大きなサイズのワーカー ノードを使用します。 MapReduce 一括コピーのマップ プロセスでは大量の一時的な出力が生成され、これによって使用可能な、DFS 以外のスペースが占有されます。 大量の一括読み込みの場合は、より多くのより大きなワーカー ノードを使用します。 クラスターに割り当てるワーカー ノードの数は、処理速度に直接影響します。

* 入力ファイルを 10 GB 程のチャンクに分割します。 一括読み込みはストレージの使用率が高い操作です。このため、入力ファイルを複数のチャンクに分割すると、パフォーマンスが向上します。

* リージョン サーバーのホットスポットを回避します。 行キーが単調に増加している場合、HBase の順次書き込みによってリージョン サーバーのホットスポッティングが減少する場合があります。 行キーを "*ソルティング*" すると、順次書き込みが少なくなります。 Phoenix では、特定のテーブルについてソルティング バイトを使用して行キーを透過的にソルティングする方法を提供します。以下を参照してください。

## <a name="next-steps"></a>次の手順

* [Apache Phoenix を使用した一括データの読み込み](https://phoenix.apache.org/bulk_dataload.html)
* [HDInsight での Linux ベースの HBase クラスターによる Apache Phoenix の使用](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [ソルティングされたテーブル](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 文法](https://phoenix.apache.org/language/index.html)
