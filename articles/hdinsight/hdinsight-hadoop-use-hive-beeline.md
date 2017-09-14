---
title: "Apache Hive で Beeline を使用する - Azure HDInsight | Microsoft Docs"
description: "Beeline クライアントを使用して、HDInsight での Hadoop で Hive クエリを実行する方法について説明します。 Beeline は、JDBC を介して HiveServer2 を使用するためのユーティリティです。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: beeline hive,hive beeline
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: db5dff01c0459db746eace0c9a4535aeccd4dcfa
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="use-the-beeline-client-with-apache-hive"></a>Apache Hive で Beeline クライアントを使用する

[Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) を使用して HDInsight で Hive クエリを実行する方法について説明します。

Beeline は、HDInsight クラスターのヘッド ノードに含まれている Hive クライアントです。 Beeline は、JDBC を使用して、HDInsight クラスターでホストされる HiveServer2 サービスに接続します。 Beeline を使用して、インターネット経由でで、HDInsight での Hive にリモートでアクセスすることもできます。 次の表は、Beeline で使用する接続文字列を示します。

| Beeline の実行場所 | parameters |
| --- | --- | --- |
| ヘッド ノードまたはエッジ ノードへの SSH 接続 | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'` |
| クラスターの外部 | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

> [!NOTE]
> `admin` をクラスターのクラスター ログイン アカウントに置き換えます。
>
> `password` をクラスター ログイン アカウントのパスワードに置き換えます。
>
> `clustername` を、使用する HDInsight クラスターの名前に置き換えます。

## <a id="prereq"></a>前提条件

* HDInsight クラスターでの Linux ベースの Hadoop

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

* SSH クライアントまたはローカル Beeline クライアント。 このドキュメントのほとんどの手順では、SSH セッションからクラスターへの Beeline を使用していることを前提としています。 クラスターの外部から Beeline を実行する方法について詳しくは、「[Beeline をリモートで使用する](#remote)」をご覧ください。

    SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="beeline"></a>Beeline を使用する

1. Beeline を開始するときに、HDInsight クラスターの HiveServer2 に接続文字列を指定する必要があります。 クラスターの外部からコマンドを実行するには、クラスター ログイン アカウント名 (既定では `admin`) とパスワードも指定する必要があります。 次の表を使用して、使用する接続文字列の形式とパラメーターを見つけます。

    | Beeline の実行場所 | parameters |
    | --- | --- | --- |
    | ヘッド ノードまたはエッジ ノードへの SSH 接続 | `-u 'jdbc:hive2://headnodehost:10001/;transportMode=http'` |
    | クラスターの外部 | `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password` |

    たとえば、次のコマンドを使用して、SSH セッションからクラスターへの Beeline を開始できます。

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    このコマンドは、Beeline クライアントを起動し、クラスターのヘッド ノード上の HiveServer2 に接続します。 コマンドが完了すると、`jdbc:hive2://headnodehost:10001/>` プロンプトが表示されます。

2. Beeline コマンドは `!` 文字で始まります。たとえば、`!help` でヘルプが表示されます。 ただし、`!` は、いくつかのコマンドでは省略できます。 たとえば、`help` も機能します。

    HiveQL ステートメントを実行するために使用される `!sql` があります。 ただし、HiveQL が一般的に使用されるので、先行する `!sql` を省略できます。 次の 2 つのステートメントは同等です。

    ```hiveql
    !sql show tables;
    show tables;
    ```

    新しいクラスターでは、1 つのテーブル (**hivesampletable**) だけが表示されます。

3. 次のコマンドを使用して、hivesampletable のスキーマを表示します。

    ```hiveql
    describe hivesampletable;
    ```

    このコマンドは、次の情報を返します。

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

    この情報は、テーブル内の列を説明します。 このデータにクエリを実行できますが、代わりに、まったく新しいテーブルを作成し、Hive にデータを読み込み、スキーマを適用する方法について確認しましょう。

4. 次のステートメントを入力して、HDInsight クラスター付属のサンプル データを使用する **log4jLogs**という名前のテーブルを作成します。

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    これらのステートメントは次のアクションを実行します。

    * `DROP TABLE`: テーブルが既に存在する場合は削除されます。

    * `CREATE EXTERNAL TABLE`: Hive に**外部**テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

    * `ROW FORMAT` - データがどのように書式設定されるか。 ここでは、各ログのフィールドは、スペースで区切られています。

    * `STORED AS TEXTFILE LOCATION`: データの格納場所とファイル形式。

    * `SELECT` - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 この値を含む行が 3 行あるため、このクエリでは値 **3** が返されます。

    * `INPUT__FILE__NAME LIKE '%.log'` - Hive は、ディレクトリ内のすべてのファイルにスキーマの適用を試みます。 このケースでは、ディレクトリにスキーマに一致しないファイルが含まれています。 結果にガベージ データが含まれないように、このステートメントを使用して、.log で終わるファイルのデータのみを返す必要があることを Hive に指示します。

  > [!NOTE]
  > 基になるデータが外部ソースによって更新されると考えられる場合は、外部テーブルを使用する必要があります。 たとえば、データの自動アップロード処理や MapReduce 操作の場合です。
  >
  > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。

    このコマンドの出力は次のテキストのようになります。

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

5. Beeline を終了するには、 `!exit`を使用します。

## <a id="file"></a>Beeline を使用して HiveQL ファイルを実行する

次の手順でファイルを作成し、Beeline を使用してそれを実行します。

1. 次のコマンドを使用して、**query.hql** という名前の新しいファイルを作成します。

    ```bash
    nano query.hql
    ```

2. ファイルの内容として、次のテキストを使用します。 このクエリは、**errorLogs** という名前の新しい '内部' テーブルを作成します。

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    これらのステートメントは次のアクションを実行します。

    * **CREATE TABLE IF NOT EXISTS**: テーブルが存在しない場合は作成されます。 **EXTERNAL** キーワードが使用されていないため、このステートメントは内部テーブルを作成します。 内部テーブルは Hive データ ウェアハウスに格納され、完全に Hive によって管理されます。
    * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 ORC 形式は、Hive データを格納するための高度に最適化された効率的な形式です。
    * **INSERT OVERWRITE ...SELECT** - **[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。

    > [!NOTE]
    > 外部テーブルとは異なり、内部テーブルを削除すると基盤となるデータも削除されます。

3. ファイルを保存するには、**Ctrl** + **_X** キーを押し、**Y** キー、**Enter** キーの順に押します。

4. 次を使用し、Beeline でファイルを実行します。

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]
    > `-i` パラメーターは Beeline を開始し、query.hql ファイル内のステートメントを実行します。 クエリが完了すると、`jdbc:hive2://headnodehost:10001/>` プロンプトが表示されます。 ファイルは、`-f` パラメーターを使用して実行することもできます。この場合、クBeeline はクエリの完了後に終了します。

5. **errorLogs** テーブルが作成されたことを確認するには、**errorLogs** からすべての行を返す次のステートメントを使用します。

    ```hiveql
    SELECT * from errorLogs;
    ```

    3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a id="remote"></a>Beeline をリモートで使用する

Beeline をローカルにインストールしている場合、または [sutoiku/beeline](https://hub.docker.com/r/sutoiku/beeline/) などの Docker イメージを通して使用する場合は、次のパラメーターを使用する必要があります。

* __接続文字列__: `-u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'`

* __クラスター ログイン名__: `-n admin`

* __クラスター ログイン パスワード__: `-p 'password'`

接続文字列の `clustername` を HDInsight クラスターの名前に置き換えます。

`admin` をクラスター ログインの名前に置き換え、`password` をクラスター ログインのパスワードに置き換えます。

## <a id="sparksql"></a>Spark での Beeline の使用

Spark は独自の HiveServer2 実装を提供します。これは一般に Spark Thrift サーバーと呼ばれます。 このサービスでは、Spark SQL を使用して Hive の代わりにクエリを解決します。クエリによってはパフォーマンスが向上します。

HDInsight クラスター上の Spark の Spark Thrift サーバーに接続するには、`10001` の代わりに `10002` ポートを使用します。 たとえば、「 `beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'`」のように入力します。

> [!IMPORTANT]
> Spark Thrift サーバーには、インターネット経由で直接アクセスすることはできません。 SSH セッションから、または HDInsight クラスターと同じ Azure Virtual Network 内でのみ接続できます。

## <a id="summary"></a><a id="nextsteps"></a>次の手順

HDInsight での Hive に関する全般的な情報について詳しくは、次のドキュメントを参照してください。

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop で実行できるその他の操作について詳しくは、次のドキュメントを参照してください。

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合は、次のドキュメントを参照してください。

* [Windows ベースの HDInsight で Tez UI を使用して Tez ジョブをデバッグする](hdinsight-debug-tez-ui.md)
* [HDInsight で Ambari ビューを使用して Tez ジョブをデバッグする](hdinsight-debug-ambari-tez-view.md)

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

