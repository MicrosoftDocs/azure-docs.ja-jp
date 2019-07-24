---
title: Apache Hive で Apache Beeline を使用する - Azure HDInsight
description: Beeline クライアントを使用して、HDInsight での Hadoop で Hive クエリを実行する方法について説明します。 Beeline は、JDBC を介して HiveServer2 を使用するためのユーティリティです。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: 89303e5c827fc24540d345a9a2b9a0743e453a4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257129"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Apache Hive で Apache Beeline クライアントを使用する

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) を使用して HDInsight 上で Hive クエリを実行する方法について説明します。

Beeline は、HDInsight クラスターのヘッド ノードに含まれている Hive クライアントです。 Beeline は、JDBC を使用して、HDInsight クラスターでホストされる HiveServer2 サービスに接続します。 Beeline を使用して、インターネット経由でで、HDInsight での Hive にリモートでアクセスすることもできます。 次の例は、Beeline から HDInsight への接続に使用される最も一般的な接続文字列を示します。

## <a name="types-of-connections"></a>接続の種類

### <a name="from-an-ssh-session"></a>SSH セッションから

SSH セッションからクラスターのヘッドノードに接続するときは、ポート `10001` 上の `headnodehost` のアドレスに接続することができます。

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Azure 仮想ネットワーク経由

Azure 仮想ネットワーク経由でクライアントから HDInsight に接続するときは、クラスターのヘッドノードの完全修飾ドメイン名 (FQDN) を指定する必要があります。 この接続はクラスター ノードに直接行われるため、接続にはポート `10001` が使用されます。

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

`<headnode-FQDN>` をクラスター ヘッドノードの完全修飾ドメイン名に置き換えます。 ヘッドノードの完全修飾ドメイン名を検索するには、[Apache Ambari REST API を使用した HDInsight の管理](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)に関するドキュメントの情報を使用してください。

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>HDInsight Enterprise セキュリティ パッケージ (ESP) クラスターへ

Azure Active Directory (AAD) に参加している Enterprise セキュリティ パッケージ (ESP) クラスターにクライアントから接続するときは、ドメイン名 `<AAD-Domain>` と、クラスター `<username>` へのアクセス許可を付与されているドメイン ユーザー アカウントの名前も指定する必要があります:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

`<username>` を、クラスターへのアクセス許可を付与されているドメイン上のアカウントの名前で置き換えます。 `<AAD-DOMAIN>` をクラスターが参加している Azure Active Directory (AAD) の名前に置き換えます。 `<AAD-DOMAIN>` 値には大文字の文字列を使用します。そうしないと、資格情報が見つかりません。 必要に応じて `/etc/krb5.conf` で領域名を確認します。

---

### <a name="over-public-internet"></a>パブリック インターネット経由

パブリック インターネット経由で接続するときは、クラスター ログイン アカウント名 (既定は `admin`) とパスワードを指定する必要があります。 たとえば、Beeline を使用してクライアント システムから `<clustername>.azurehdinsight.net` のアドレスに接続する場合です。 この接続は、ポート `443` を経由し、SSL を使用して暗号化されます。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

`clustername` を、使用する HDInsight クラスターの名前に置き換えます。 `admin` をクラスターのクラスター ログイン アカウントに置き換えます。 `password` をクラスター ログイン アカウントのパスワードに置き換えます。

---

### <a id="sparksql"></a>Apache Spark での Beeline の使用

Apache Spark は独自の HiveServer2 実装を提供します。これは Spark Thrift サーバーとも呼ばれます。 このサービスでは、Spark SQL を使用して Hive の代わりにクエリを解決します。クエリによってはパフォーマンスが向上します。

#### <a name="over-public-internet-with-apache-spark"></a>Azure Spark でのパブリック インターネット経由

インターネット経由で接続する際に使用される接続文字列は、わずかに異なります。 `httpPath=/hive2` の代わりに `httpPath/sparkhive2` が含まれます。

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Azure Spark でのクラスター ヘッドから、または Azure Virtual Network 内

クラスターのヘッド ノード、または HDInsight クラスターと同じ Azure Virtual Network 内のリソースから直接接続する場合は、`10001` の代わりに、Spark Thrift サーバー用のポート `10002` を使用する必要があります。 次の例は、ヘッド ノードに直接接続する方法を示しています。

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>前提条件

* HDInsight 上の Hadoop クラスター。 詳細については、[Linux での HDInsight の概要](./apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* クラスターのプライマリ ストレージの [URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)に注目してください。 たとえば、Azure Storage の場合は `wasb://`、Azure Data Lake Storage Gen2 の場合は`abfs://`、Azure Data Lake Storage Gen1 の場合は `adl://` です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効な場合、URI はそれぞれ `wasbs://` または `abfss://` です。 詳細については、[安全な転送](../../storage/common/storage-require-secure-transfer.md)に関するページを参照してください。


* オプション 1:SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。 このドキュメントのほとんどの手順では、SSH セッションからクラスターへの Beeline を使用していることを前提としています。

* オプション 2:ローカルの Beeline クライアント。


## <a id="beeline"></a>Hive クエリを実行する

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

    この情報は、テーブル内の列を説明します。

5. 次のステートメントを入力して、HDInsight クラスター付属のサンプル データを使用する **log4jLogs**という名前のテーブルを作成します。([URI スキーム](../hdinsight-hadoop-linux-information.md#URI-and-scheme)に基づいて必要に応じて修正してください。)

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

    これらのステートメントは次のアクションを実行します。

    * `DROP TABLE` : テーブルが既に存在する場合は削除されます。

    * `CREATE EXTERNAL TABLE` : Hive に**外部**テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。

    * `ROW FORMAT` : データがどのように書式設定されるか。 ここでは、各ログのフィールドは、スペースで区切られています。

    * `STORED AS TEXTFILE LOCATION` : データの格納場所とファイル形式。

    * `SELECT` : **t4** 列の値が **[ERROR]** であるすべての行の数を選択します。 この値を含む行が 3 行あるため、このクエリでは値 **3** が返されます。

    * `INPUT__FILE__NAME LIKE '%.log'` : Hive は、ディレクトリ内のすべてのファイルにスキーマの適用を試みます。 このケースでは、ディレクトリにスキーマに一致しないファイルが含まれています。 結果にガベージ データが含まれないように、このステートメントを使用して、.log で終わるファイルのデータのみを返す必要があることを Hive に指示します。

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

6. Beeline を終了するには、 `!exit`を使用します。

## <a id="file"></a>HiveQL ファイルを実行する

これは前の例からの続きです。 次の手順でファイルを作成し、Beeline を使用してそれを実行します。

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
    > `-i` パラメーターは Beeline を開始し、`query.hql` ファイル内のステートメントを実行します。 クエリが完了すると、`jdbc:hive2://headnodehost:10001/>` プロンプトが表示されます。 ファイルは、`-f` パラメーターを使用して実行することもできます。この場合、クBeeline はクエリの完了後に終了します。

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




## <a id="summary"></a><a id="nextsteps"></a>次の手順

HDInsight での Hive に関する全般的な情報について詳しくは、次のドキュメントを参照してください。

* [HDInsight で Apache Hive と Apache Hadoop を使用する](hdinsight-use-hive.md)

HDInsight での Hadoop で実行できるその他の操作について詳しくは、次のドキュメントを参照してください。

* [HDInsight 上の Apache Pig で Apache Pig を使用する](hdinsight-use-pig.md)
* [HDInsight 上の Apache Hadoop で MapReduce を使用する](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
