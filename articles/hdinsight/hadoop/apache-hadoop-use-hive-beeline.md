---
title: Apache Hive で Apache Beeline を使用する - Azure HDInsight
description: Beeline クライアントを使用して、HDInsight での Hadoop で Hive クエリを実行する方法について説明します。 Beeline は、JDBC を介して HiveServer2 を使用するためのユーティリティです。
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943201"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive で Apache Beeline クライアントを使用する

この記事では、コマンド ライン [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) クライアントを使用して、SSH 接続を介して Apache Hive クエリを作成および実行する方法について説明します。

## <a name="background"></a>バックグラウンド

Beeline は、HDInsight クラスターのヘッド ノードに含まれている Hive クライアントです。 HDInsight クラスターにインストールされている Beeline クライアントに接続したり、Beeline をローカルにインストールしたりするには、[Apache Beeline への接続またはインストール](connect-install-beeline.md)に関する記事を参照してください。 Beeline は、JDBC を使用して、HDInsight クラスターでホストされる HiveServer2 サービスに接続します。 Beeline を使用して、インターネット経由でで、HDInsight での Hive にリモートでアクセスすることもできます。 次の例は、Beeline から HDInsight への接続に使用される最も一般的な接続文字列を示します。

## <a name="prerequisites-for-examples"></a>例の前提条件

* HDInsight 上の Hadoop クラスター。 [Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* クラスターのプライマリ ストレージの URI スキームに注目してください。 たとえば、Azure Storage の場合は `wasb://`、Azure Data Lake Storage Gen2 の場合は`abfs://`、Azure Data Lake Storage Gen1 の場合は `adl://` です。 Azure Storage で安全な転送が有効になっている場合、URI は `wasbs://` になります。 詳細については、[安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページを参照してください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 このドキュメントのほとんどの手順では、SSH セッションからクラスターへの Beeline を使用していることを前提としています。 ローカルの Beeline クライアントを使用することもできますが、この記事では、その手順については説明しません。

## <a name="run-a-hive-query"></a>Hive クエリを実行する

この例は、SSH 接続からの Beeline クライアントの使用に基づいています。

1. 次のコードを使用して、クラスターとの SSH 接続を開きます。 `sshuser` は、クラスターの SSH ユーザーに置き換えます。また、`CLUSTERNAME` はクラスターの名前に置き換えます。 メッセージが表示されたら、SSH ユーザー アカウントのパスワードを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 次のコマンドを入力して、開いている SSH セッションから Beeline クライアントを使用して HiveServer 2 に接続します。

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline コマンドは `!` 文字で始まります。たとえば、`!help` でヘルプが表示されます。 ただし、`!` は、いくつかのコマンドでは省略できます。 たとえば、`help` も機能します。

    HiveQL ステートメントを実行するために使用される `!sql` があります。 ただし、HiveQL が一般的に使用されるので、先行する `!sql` を省略できます。 次の 2 つのステートメントは同等です。

    ```hiveql
    !sql show tables;
    show tables;
    ```

    新しいクラスターでは、1 つのテーブル (**hivesampletable**) だけが表示されます。

4. 次のコマンドを使用して、hivesampletable のスキーマを表示します。

    ```hiveql
    describe hivesampletable;
    ```

    このコマンドは、次の情報を返します。

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    この情報は、テーブル内の列を説明します。

5. 次のステートメントを入力して、HDInsight クラスター付属のサンプル データを使用する **log4jLogs** という名前のテーブルを作成します。(URI スキームに基づいて必要に応じて修正してください。)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    これらのステートメントによって次のアクションが実行されます。

    |ステートメント |説明 |
    |---|---|
    |DROP TABLE|テーブルが存在する場合は削除されます。|
    |CREATE EXTERNAL TABLE|Hive に **外部** テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。|
    |ROW FORMAT|データがどのように書式設定されるか。 ここでは、各ログのフィールドは、スペースで区切られています。|
    |STORED AS TEXTFILE LOCATION|データの格納場所とファイル形式。|
    |SELECT|**t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 この値を含む行が 3 行あるため、このクエリでは値 **3** が返されます。|
    |INPUT__FILE__NAME LIKE '%.log'|Hive によって、ディレクトリ内のすべてのファイルにスキーマの適用が試みられます。 このケースでは、ディレクトリにスキーマに一致しないファイルが含まれています。 結果にガベージ データが含まれないように、このステートメントを使用して、.log で終わるファイルのデータのみを返す必要があることを Hive に指示します。|

   > [!NOTE]  
   > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や MapReduce 操作の場合です。
   >
   > 外部テーブルを削除しても、データは削除 **されません**。テーブル定義のみが削除されます。

    このコマンドの出力は次のテキストのようになります。

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Beeline を終了します。

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>HiveQL ファイルを実行する

この例は、前の例からの続きです。 次の手順でファイルを作成し、Beeline を使用してそれを実行します。

1. 次のコマンドを使用して、**query.hql** という名前の新しいファイルを作成します。

    ```bash
    nano query.hql
    ```

1. ファイルの内容として、次のテキストを使用します。 このクエリは、**errorLogs** という名前の新しい '内部' テーブルを作成します。

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    これらのステートメントによって次のアクションが実行されます。

    |ステートメント |説明 |
    |---|---|
    |CREATE TABLE IF NOT EXISTS|テーブルが存在しない場合は作成されます。 **EXTERNAL** キーワードが使用されていないため、このステートメントは内部テーブルを作成します。 内部テーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。|
    |STORED AS ORC|Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC 形式は、Hive データを格納するための高度に最適化された効率的な形式です。|
    |INSERT OVERWRITE ...SELECT|**[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。|

    > [!NOTE]  
    > 外部テーブルとは異なり、内部テーブルを削除すると基盤となるデータも削除されます。

1. ファイルを保存するには、**Ctrl**+**X** キーを押し、**Y** キー、**Enter** キーの順に押します。

1. 次を使用し、Beeline でファイルを実行します。

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i` パラメーターは Beeline を開始し、`query.hql` ファイル内のステートメントを実行します。 クエリが完了すると、`jdbc:hive2://headnodehost:10001/>` プロンプトが表示されます。 ファイルは、`-f` パラメーターを使用して実行することもできます。この場合、クBeeline はクエリの完了後に終了します。

1. **errorLogs** テーブルが作成されたことを確認するには、**errorLogs** からすべての行を返す次のステートメントを使用します。

    ```hiveql
    SELECT * from errorLogs;
    ```

    3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>次のステップ

* HDInsight での Hive について詳しくは、[HDInsight 上の Apache Hadoop での Apache Hive の使用](hdinsight-use-hive.md)に関するページをご覧ください。

* HDInsight での Hadoop のその他の使用方法について詳しくは、[HDInsight 上の Apache Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)に関するページを参照してください。
