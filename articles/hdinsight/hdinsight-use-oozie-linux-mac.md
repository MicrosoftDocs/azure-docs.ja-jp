---
title: Linux ベースの Azure HDInsight で Hadoop Oozie ワークフローを使用する
description: Linux ベースの HDInsight で Hadoop Oozie を使用します。 Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8227ff0c56e147db66c4cdc93083d671b08d1d98
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433412"
---
# <a name="use-apache-oozie-with-apache-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Apache Hadoop で Apache Oozie を使用して Linux ベースの Azure HDInsight でワークフローを定義して実行する

Azure HDInsight で Apache Oozie と Apache Hadoop を使用する方法を説明します。 Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。 Oozie は Hadoop スタックと統合されており、次のジョブをサポートしています。

* Apache Hadoop MapReduce の使用
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie を使って、Java プログラムやシェル スクリプトなどの、システムに固有のジョブをスケジュールすることもできます。

> [!NOTE]  
> HDInsight でワークフローを定義するもう 1 つのオプションは、Azure Data Factory を使う方法です。 Data Factory について詳しくは、[Data Factory での Apache Pig と Apache Hive の使用][azure-data-factory-pig-hive]に関するページを参照してください。 Enterprise セキュリティ パッケージを使用したクラスターで Oozie を使用するには、「[Enterprise セキュリティ パッケージを使用する HDInsight Hadoop クラスターで Apache Oozie を実行する](domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

* **HDInsight 上の Hadoop クラスター**。 [Linux での HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページを参照してください。

* **SSH クライアント**。 「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

* **Azure SQL Database**。  [Azure portal での Azure SQL データベースの作成](../sql-database/sql-database-get-started.md)に関するページを参照してください。  この記事では、`oozietest` という名前のデータベースを使用します。

* クラスターのプライマリ ストレージの [URI スキーム](./hdinsight-hadoop-linux-information.md#URI-and-scheme)。 Azure Storage では `wasb://`、Azure Data Lake Storage Gen2 では `abfs://`、Azure Data Lake Storage Gen1 では `adl://` です。 Azure Storage または Data Lake Storage Gen2 で安全な転送が有効になっている場合、URI はそれぞれ `wasbs://` または `abfss://` になります。[「安全な転送」](../storage/common/storage-require-secure-transfer.md)も参照してください。


## <a name="example-workflow"></a>ワークフローの例

このドキュメントで使用されるワークフローには、2 つのアクションが含まれています。 アクションは、タスク (Hive、Sqoop、MapReduce、または他のプロセスの実行など) の定義です。

![ワークフロー図][img-workflow-diagram]

1. Hive アクションは、HiveQL スクリプトを実行して、HDInsight に含まれている `hivesampletable` からレコードを抽出します。 各データ行は、特定のモバイル デバイスからのアクセスを表します。 レコードの形式は次のテキストのようになります。

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    このドキュメントで使う Hive スクリプトは、プラットフォームごと (Android や iPhone など) の合計アクセス数をカウントし、カウントしたアクセス数を新しい Hive テーブルに保存します。

    Hive の詳細については、[HDInsight での Apache Hive の使用][hdinsight-use-hive]に関するページを参照してください。

2. Sqoop アクションは、新しい Hive テーブルの内容を Azure SQL Database で作成されたテーブルにエクスポートします。 Sqoop の詳細については、[HDInsight での Apache Sqoop の使用][hdinsight-use-sqoop]に関するページを参照してください。

> [!NOTE]  
> HDInsight クラスターでサポートされている Oozie のバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。

## <a name="create-the-working-directory"></a>作業ディレクトリの作成

Oozie では、ジョブに必要なすべてのリソースを同じディレクトリに保存する必要があります。 この例では、`wasbs:///tutorials/useoozie` を使用します。 このディレクトリを作成するには、次の手順のようにします。

1. 次のコードを編集して、`sshuser` をクラスターの SSH ユーザー名に置き換えます。また、`clustername` をクラスター名に置き換えます。  それから、[SSH を使用](hdinsight-hadoop-linux-use-ssh-unix.md)して HDInsight クラスターに接続するコードを入力します。  

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

2. ディレクトリを作成するには、次のコマンドを使用します。

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]  
    > `-p` パラメーターを指定すると、すべてのディレクトリがこのパスに作成されます。 `data` ディレクトリは、`useooziewf.hql` スクリプトが使うデータを保持するために使われます。

3. 次のコードを編集して、`username` を SSH ユーザー名に置き換えます。  Oozie がユーザー アカウントを偽装できるようにするには、次のコマンドを使用します。

    ```bash
    sudo adduser username users
    ```

    > [!NOTE]  
    > ユーザーが既に `users` グループのメンバーであることを示すエラーは無視できます。

## <a name="add-a-database-driver"></a>データベース ドライバーの追加

このワークフローでは、Sqoop を使ってデータを SQL データベースにエクスポートするため、SQL データベースとの対話に使う JDBC ドライバーのコピーを提供する必要があります。 JDBC ドライバーを作業ディレクトリにコピーするには、SSH セッションから次のコマンドを使用します。

```bash
hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc*.jar /tutorials/useoozie/
```

> [!IMPORTANT]  
> `/usr/share/java/` にある実際の JDBC ドライバーを確認します。

ワークフローで他のリソース (MapReduce アプリケーションを含む jar など) を使っている場合は、これらのリソースも追加する必要があります。

## <a name="define-the-hive-query"></a>Hive クエリの定義

次の手順を使って、クエリを定義する Hive クエリ言語 (HiveQL) スクリプトを作成します。 後で示す Oozie ワークフローで、このクエリを使います。

1. SSH 接続から、次のコマンドを使用して、`useooziewf.hql` という名前のファイルを作成します。

    ```bash
    nano useooziewf.hql
    ```

3. GNU nano エディターが開いたら、ファイルの内容として次のクエリを使います。

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    このスクリプトでは、次の 2 つの変数を使用しています。

   * `${hiveTableName}`:作成されるテーブルの名前が格納されます。

   * `${hiveDataFolder}`:テーブルのデータ ファイルを保存する場所が格納されます。

     ワークフロー定義ファイル (この記事では workflow.xml) は、実行時にこの HiveQL スクリプトにこれらの値を渡します。

4. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。  

5. 次のコマンドを使用して、`useooziewf.hql` を `wasbs:///tutorials/useoozie/useooziewf.hql` にコピーします。

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    このコマンドは、クラスターの HDFS 互換ストレージに `useooziewf.hql` ファイルを保存します。

## <a name="define-the-workflow"></a>ワークフローの定義

Oozie ワークフローの定義は、XML プロセス定義言語である Hadoop プロセス定義言語 (hPDL) を使って記述します。 次の手順に従ってワークフローを定義します。

1. 次のステートメントを使用して、新しいファイルを作成し、編集します。

    ```bash
    nano workflow.xml
    ```

2. nano エディターが開いたら、ファイルの内容として次の XML を入力します。

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    このワークフローでは、2 つのアクションが定義されています。

   * `RunHiveScript`:開始アクションであり、`useooziewf.hql` Hive スクリプトを実行します。

   * `RunSqoopExport`:Sqoop を使って、作成されたデータを Hive スクリプトから SQL データベースにエクスポートします。 このアクションは、`RunHiveScript` アクションが正常に実行された場合にのみ実行されます。

     このワークフローには、`${jobTracker}` などのいくつかのエントリがあります。 これらのエントリは、ジョブ定義で使う値に置き換えます。 ジョブ定義は後で作成します。

     また、Sqoop セクションの `<archive>mssql-jdbc-7.0.0.jre8.jar</archive>` エントリにも注意してください。 このエントリは、このアクションの実行時にこのアーカイブを Sqoop で使用できるようにすることを Oozie に指示します。

3. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。  

4. 次のコマンドを使用して、`workflow.xml` ファイルを `/tutorials/useoozie/workflow.xml` にコピーします。

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-a-table"></a>テーブルを作成する

> [!NOTE]  
> SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。

1. 次のコマンドを使用して、FreeTDS を HDInsight クラスターにインストールします。

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. 次のコードを編集して、`<serverName>` を Azure SQL サーバー名に置き換え、`<sqlLogin>` を Azure SQL サーバー ログインに置き換えます。  コマンドを入力して、前提条件の SQL データベースに接続します。  プロンプトでパスワードを入力します。

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    出力は次のテキストのようになります。

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. `1>` プロンプトで、以下の行を入力します。

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 これらのステートメントにより、ワークフローで使われる `mobiledata` という名前のテーブルが作成されます。

    テーブルが作成されたことを確認するには、次のコマンドを使います。

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    次のようなテキストが出力されます。

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo             mobiledata      BASE TABLE

4. `1>` プロンプトで「`exit`」と入力して、tsql ユーティリティを終了します。

## <a name="create-the-job-definition"></a>ジョブ定義の作成

ジョブ定義には、workflow.xml の検索場所を記述します。 ワークフローで使われる他のファイル (`useooziewf.hql` など) の検索場所についても記述します。 さらに、ワークフローおよび関連するファイル内で使われるプロパティの値も定義します。

1. 既定のストレージの完全なアドレスを取得するには、次のコマンドを使います。 このアドレスは、次のステップで作成する構成ファイルで使います。

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    このコマンドでは、次の XML のような情報が返されます。

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]  
    > HDInsight クラスターで既定のストレージとして Azure Storage を使用する場合、`<value>` 要素の内容は `wasbs://`で始まります。 Azure Data Lake Storage Gen1 を使用する場合は、`adl://` で始まります。 Azure Data Lake Storage Gen2 を使用する場合は、`abfs://` で始まります。

    以下の手順で使うため、`<value>` 要素の内容を保存します。

2. 次の xml を以下のように編集します。

    |プレースホルダーの値| 置き換えられた値|
    |---|---|
    |wasbs://mycontainer\@mystorageaccount.blob.core.windows.net| 手順 1 で受け取った値。|
    |admin| admin ではない場合、HDInsight クラスターの自分のログイン名。|
    |serverName| Azure SQL データベース サーバー名。|
    |sqlLogin| Azure SQL データベース サーバー ログイン。|
    |sqlPassword| Azure SQL データベース サーバー ログインのパスワード。|

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=sqlLogin;password=sqlPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>admin</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

    このファイル内のほとんどの情報は、workflow.xml ファイルまたは ooziewf.hql ファイルで使われる値 (`${nameNode}` など) を設定するために使われます。  パスが `wasbs` パスの場合は、完全パスを使用する必要があります。 `wasbs:///` だけに短縮しないでください。 `oozie.wf.application.path` エントリでは、workflow.xml ファイルの検索場所を定義します。 このファイルには、このジョブで実行されたワークフローが格納されます。

3. Oozie ジョブ定義構成を作成するには、次のコマンドを使います。

    ```bash
    nano job.xml
    ```

4. nano エディターが開いたら、編集した XML をファイルの内容として貼り付けます。

5. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。

## <a name="submit-and-manage-the-job"></a>ジョブの送信と管理

次の手順では、Oozie コマンドを使用して、クラスターで Oozie ワークフローを送信および管理します。 Oozie コマンドは、 [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)の使いやすいインターフェイスです。

> [!IMPORTANT]  
> Oozie コマンドを使うときは、HDInsight ヘッドノードの FQDN を使う必要があります。 この FQDN にはクラスターからのみアクセスできます。クラスターが Azure 仮想ネットワーク上にある場合は、同じネットワークの他のマシンからアクセスできます。

1. Oozie サービスの URL を取得するには、次のコマンドを使います。

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    このコマンドでは、次の XML のような情報が返されます。

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` の部分が Oozie コマンドで使用する URL です。

2. コードを編集して、URL を前に返された URL に置き換えます。 URL の環境変数を作成するには次のコマンドを使うので、すべてのコマンドでこれを入力する必要はありません。

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

3. ジョブを送信するには、次のコマンドを使います。

    ```bash
    oozie job -config job.xml -submit
    ```

    このコマンドでは、`job.xml` からジョブ情報を読み込んで Oozie に送信しますが、ジョブは実行しません。

    コマンドが完了すると、ジョブの ID が返されます (例: `0000005-150622124850154-oozie-oozi-W`)。 この ID はジョブの管理に使用されます。

4. 次のコードを編集して、`<JOBID>` を前の手順で返された ID に置き換えます。  ジョブのステータスを表示するには、次のコマンドを使います。

    ```bash
    oozie job -info <JOBID>
    ```

    次のテキストのような情報が返されます。

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    このジョブの状態は `PREP` です。 この状態は、ジョブが作成されたが開始されていないことを示します。

5. 次のコードを編集して、`<JOBID>` を前に返された ID に置き換えます。  ジョブを開始するには、次のコマンドを使います。

    ```bash
    oozie job -start <JOBID>
    ```

    このコマンドの実行後に状態を確認すると、ジョブが実行中状態になり、ジョブのアクションに関する情報が返されます。  このジョブは完了までに数分かかります。

6. 次のコードを編集して、`<serverName>` を Azure SQL サーバー名に置き換え、`<sqlLogin>` を Azure SQL サーバー ログインに置き換えます。  タスクが正常に完了したら、次のコマンドを使って、データが生成され、SQL データベース テーブルにエクスポートされたことを確認できます。  プロンプトでパスワードを入力します。

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -p 1433 -D oozietest
    ```

    `1>` プロンプトで、次のクエリを入力します。

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    次のテキストのような情報が返されます。

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Oozie コマンドの詳細については、[Apache Oozie コマンドライン ツール](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)に関するページをご覧ください。

## <a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API を使うと、Oozie で動く独自のツールを作成できます。 Oozie REST API の使用に関する HDInsight 固有の情報は次のとおりです。

* **URI**:`https://CLUSTERNAME.azurehdinsight.net/oozie` を使うと、クラスターの外部から REST API にアクセスできます。

* **認証**:認証を行うには、クラスターの HTTP アカウント (admin) とパスワードで API を使います。 例:

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Oozie REST API の使い方について詳しくは、[Apache Oozie Web サービス API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html) のページをご覧ください。

## <a name="oozie-web-ui"></a>Oozie Web UI

Oozie Web UI は、クラスターでの Oozie ジョブの状態を表示する Web ベースのビューを提供します。 Web UI では、次の情報を表示できます。

   * ジョブの状態
   * ジョブ定義
   * 構成
   * ジョブのアクションのグラフ
   * ジョブのログ

また、ジョブ内のアクションの詳細を表示することもできます。

Oozie Web UI にアクセスするには、次の手順のようにします。

1. HDInsight クラスターへの SSH トンネルを作成します。 詳細については、[HDInsight での SSH トンネリングの使用](hdinsight-linux-ambari-ssh-tunnel.md)に関するページを参照してください。

2. トンネルを作成した後、URI `http://headnodehost:8080` を使用して Web ブラウザーで Ambari Web UI を開きます。

3. ページの左側で、 **[Oozie]**  >  **[クイック リンク]**  >  **[Oozie Web UI]** の順に選びます。

    ![メニューの画像](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Oozie Web UI には、実行中のワークフロー ジョブが既定で表示されます。 すべてのワークフロー ジョブを表示するには、 **[All Jobs]\(すべてのジョブ\)** を選びます。

    ![すべてのジョブの表示](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. ジョブに関する他の情報を表示するには、ジョブを選びます。

    ![[Job Info]](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. **[Job Info]\(ジョブの情報\)** タブでは、基本的なジョブ情報とジョブの個々のアクションを確認できます。 上部にあるタブを使って、 **[Job Definition]\(ジョブの定義\)** の表示、 **[Job Configuration]\(ジョブの構成\)** の表示、 **[Job Log]\(ジョブのログ\)** へのアクセス、 **[Job DAG]\(ジョブの DAG\)** でジョブの有向非巡回グラフ (DAG) の表示を行うことができます。

   * **[ジョブのログ]** :ジョブのすべてのログを取得するには、 **[Get Logs]\(ログの取得)** ボタンを選びます。ログをフィルター処理するには、 **[Enter Search Filter]\(検索フィルターの入力)** フィールドを使います。

       ![ジョブのログ](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **[Job DAG]\(ジョブの DAG)** :DAG は、ワークフローで取得されるデータ パスの概要をグラフィックで表したものです。

       ![ジョブの DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. **[Job Info]\(ジョブの情報\)** タブでアクションのいずれかを選択すると、そのアクションの情報が表示されます。 たとえば、**RunHiveScript** アクションを選びます。

    ![アクション情報](./media/hdinsight-use-oozie-linux-mac/action.png)

8. **コンソールの URL** へのリンクなど、アクションの詳細を確認できます。 このリンクを使うと、ジョブ トラッカーでのジョブの情報が表示されます。

## <a name="schedule-jobs"></a>ジョブのスケジュールを設定する

コーディネーターを使うと、ジョブの開始時刻、終了時刻、実行頻度を指定できます。 ワークフローのスケジュールを定義するには、次の手順のようにします。

1. 次のコマンドを使って、**coordinator.xml** という名前のファイルを作成します。

    ```bash
    nano coordinator.xml
    ```

    このファイルの内容として、次の XML を使用します。

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]  
    > `${...}` 変数は、実行時にジョブ定義の値に置き換えられます。 変数は次のとおりです。
    >
    > * `${coordFrequency}`:ジョブのインスタンスが実行される間隔。
    > * `${coordStart}`:ジョブの開始時刻。
    > * `${coordEnd}`:ジョブの終了時刻。
    > * `${coordTimezone}`:コーディネーター ジョブでは、(通常は UTC を使用して表される) 夏時間なしの固定タイム ゾーンを使用します。 このタイム ゾーンを、"*Oozie 処理のタイムゾーン*" と呼びます。
    > * `${wfPath}`:workflow.xml のパス。

2. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。

3. ファイルをこのジョブの作業ディレクトリにコピーするには、次のコマンドを使います。

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. 以前作成した `job.xml` ファイルを変更するには、次のコマンドを使います。

    ```bash
    nano job.xml
    ```

    次の変更を行います。

   * ワークフロー ファイルではなく、コーディネーター ファイルを実行するように Oozie に指示するには、`<name>oozie.wf.application.path</name>` を `<name>oozie.coord.application.path</name>` に変更します。

   * コーディネーターが使用する `workflowPath` 変数を設定するには、次の XML を追加します。

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       `wasbs://mycontainer@mystorageaccount.blob.core.windows` テキストを、job.xml ファイルの他のエントリで使われている値に置き換えます。

   * コーディネーターが使用する開始時刻、終了時刻、頻度を定義するには、次の XML を追加します。

        ```xml
        <property>
            <name>coordStart</name>
            <value>2018-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2018-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       これらの値によって、開始時刻が 2018 年 5 月 10 日 12:00 PM に、終了時刻が 2018 年 5 月 12 日 12:00 PM に設定されます。 このジョブの実行間隔は、毎日に設定されます。 頻度は分単位であるため、24 時間 x 60 分 = 1440 分になります。 最後に、タイムゾーンを UTC に設定しています。

5. ファイルを保存するには、Ctrl + X キー、`Y` キー、**Enter** キーの順に押します。

6. ジョブを提出して開始するには、次のコマンドを使います。

    ```bash
    oozie job -config job.xml -run
    ```

7. Oozie Web UI にアクセスし、 **[Coordinator Jobs]\(コーディネーター ジョブ\)** タブを選ぶと、次の画像のような情報が表示されます。

    ![[Coordinator Jobs]\(コーディネーター ジョブ\) タブ](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **[Next Materialization (次の実体化)]** エントリは、このジョブが次回実行される日時を示しています。

8. 前のワークフロー ジョブと同様に、Web UI でジョブ エントリを選ぶと、そのジョブの情報が表示されます。

    ![コーディネーター ジョブの情報](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]  
    > この画像には、正常に実行されたジョブのみが表示されており、スケジュールされたワークフロー内の個々のアクションについては表示されません。 個々のアクションを表示するには、 **[Action]\(アクション\)** エントリの 1 つを選びます。

    ![アクション情報](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>トラブルシューティング

Oozie UI では、Oozie のログを見ることができます。 また、Oozie UI には、ワークフローによって開始された MapReduce タスクの JobTracker ログへのリンクも含まれます。 トラブルシューティングのパターンは次のようになります。

   1. Oozie Web UI でジョブを表示します。

   2. 特定のアクションでエラーが発生した場合は、そのアクションを選択して、 **[Error Message]** フィールドにエラーの詳細が示されているかどうかを確認します。

   3. 可能な場合は、アクションから URL を使って、アクションの詳細 (JobTracker ログなど) を表示します。

発生する可能性のあるエラーとその解決方法を以下に示します。

### <a name="ja009-cannot-initialize-cluster"></a>JA009:Cannot initialize cluster (クラスターを初期化できません)

**現象**:ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、`RunHiveScript` の状態が **START_MANUAL** と示されます。 アクションを選択すると、次のエラー メッセージが表示されます。

    JA009: Cannot initialize Cluster. Please check your configuration for map

**原因**:**job.xml** ファイルで使われている Azure Blob Storage アドレスに、ストレージ コンテナー名またはストレージ アカウント名が含まれていません。 Blob Storage アドレスは、`wasbs://containername@storageaccountname.blob.core.windows.net` という形式にする必要があります。

**解決方法**:ジョブが使う Blob Storage アドレスを変更します。

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltusergt"></a>JA002:Oozie is not allowed to impersonate &lt;USER&gt; (Oozie は &lt;USER&gt; の偽装を許可されていません)

**現象**:ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、`RunHiveScript` の状態が **START_MANUAL** と示されます。 アクションを選ぶと、次のエラー メッセージが表示されます。

    JA002: User: oozie is not allowed to impersonate <USER>

**原因**:現在のアクセス許可設定で、Oozie が指定されたユーザー アカウントを偽装することを許可していません。

**解決方法**:Oozie は **users** グループのユーザーを偽装できます。 `groups USERNAME` を使用して、ユーザー アカウントがメンバーとして属するグループを確認します。 ユーザーが **users** グループのメンバーでない場合は、次のコマンドを使用して、ユーザーをこのグループに追加します。

    sudo adduser USERNAME users

> [!NOTE]  
> ユーザーがグループに追加されたことを HDInsight が認識するまで数分かかる場合があります。

### <a name="launcher-error-sqoop"></a>Launcher ERROR (ランチャー エラー) (Sqoop)

**現象**:ジョブの状態が **KILLED** に変更されます。 ジョブの詳細に、`RunSqoopExport` の状態が **ERROR** と示されます。 アクションを選ぶと、次のエラー メッセージが表示されます。

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**原因**:Sqoop が、データベースにアクセスするために必要なデータベース ドライバーを読み込むことができません。

**解決方法**:Oozie ジョブから Sqoop を使うときは、ジョブが使う他のリソース (workflow.xml など) とともにデータベース ドライバーを含める必要があります。 また、workflow.xml の `<sqoop>...</sqoop>` セクションから、データベース ドライバーが格納されたアーカイブを参照します。

たとえば、このドキュメントのジョブの場合、次の手順に従います。

1. `mssql-jdbc-7.0.0.jre8.jar` ファイルを **/tutorials/useoozie** ディレクトリにコピーします。

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. `workflow.xml` を変更して、`</sqoop>` の上の新しい行に次の XML を追加します。

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>次の手順

この記事では、Oozie ワークフローを定義する方法と Oozie ジョブを実行する方法について説明しました。 HDInsight の使用方法について詳しくは、次の記事をご覧ください。

* [HDInsight で Apache Hadoop ジョブのデータをアップロードする][hdinsight-upload-data]
* [HDInsight 上の Apache Hadoop で Apache Sqoop を使用する][hdinsight-use-sqoop]
* [HDInsight 上の Apache Hadoop で Apache Hive を使用する][hdinsight-use-hive]
* [HDInsight 上の Apache Hadoop で Apache Pig を使用する][hdinsight-use-pig]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: https://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: https://hadoop.apache.org/
[apache-oozie-400]: https://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: https://oozie.apache.org/docs/3.3.2/

[powershell-download]: https://azure.microsoft.com/downloads/
[powershell-about-profiles]: https://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/library/ee176961.aspx

[cindygross-hive-tables]: https://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie-linux-mac/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: https://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
