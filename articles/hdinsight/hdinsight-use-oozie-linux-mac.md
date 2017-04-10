---
title: "Linux ベースの HDInsight での Hadoop Oozie ワークフローの使用 | Microsoft Docs"
description: "Linux ベースの HDInsight で Hadoop Oozie を使用します。 Oozie ワークフローを定義し、Oozie ジョブを送信する方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: e4f38a061129b2bb8a840d4eec8d224bcbc22e83
ms.lasthandoff: 03/25/2017


---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-hdinsight"></a>Hadoop で Oozie を使用して Linux ベースの HDInsight でワークフローを定義して実行する

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Apache Oozie を使用して、Hive と Sqoop を使用するワークフローを定義し、Linux ベースの HDInsight クラスターでワークフローを実行する方法について説明します。

Apache Oozie は Hadoop ジョブを管理するワークフローおよび調整システムです。 Hadoop スタックと統合されていて、Apache MapReduce、Apache Pig、Apache Hive、Apache Sqoop の Hadoop ジョブをサポートしています。 Java プログラムやシェル スクリプトなど、システムに固有のジョブのスケジュールを設定する際にも使用できます。

> [!NOTE]
> HDInsight でワークフローを定義するもう 1 つのオプションは、Azure Data Factory です。 Azure Data Factory の詳細については、「[Data Factory で Pig と Hive を使用する][azure-data-factory-pig-hive]」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure CLI**: [Azure CLI のインストール](../cli-install-nodejs.md)

* **HDInsight クラスター**: [Linux での HDInsight の使用](hdinsight-hadoop-linux-tutorial-get-started.md)

  > [!IMPORTANT]
  > このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* **Azure SQL Database**: このドキュメントの手順を使用して作成します。

## <a name="example-workflow"></a>ワークフローの例

このドキュメントで使用されるワークフローには、2 つのアクションが含まれています。 アクションはタスク (Hive、Sqoop、MapReduce、または他のプロセスの実行など) の定義です。

![ワークフロー図][img-workflow-diagram]

1. Hive アクションでは、HiveQL スクリプトを実行して、HDInsight に含まれている **hivesampletable** からレコードを抽出します。 各データ行は、特定のモバイル デバイスからのアクセスを表します。 レコードの形式は次のようになります。

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    このドキュメントで使用する Hive スクリプトは、プラットフォームごと (Android や iPhone など) の合計アクセス数をカウントし、カウントしたアクセス数を新しい Hive テーブルに保存します。

    Hive の詳細については、[HDInsight での Hive の使用][hdinsight-use-hive]に関するページを参照してください。

2. Sqoop アクションでは、新しい Hive テーブルの内容を Azure SQL Database のテーブルにエクスポートします。 Sqoop の詳細については、「[HDInsight での Hadoop Sqoop の使用][hdinsight-use-sqoop]」を参照してください。

> [!NOTE]
> HDInsight クラスターでサポートされている Oozie のバージョンについては、「[HDInsight で提供される Hadoop クラスター バージョンの新機能][hdinsight-versions]」を参照してください。

## <a name="create-the-working-directory"></a>作業ディレクトリの作成

Oozie では、ジョブに必要なリソースを同じディレクトリに保存する必要があります。 この例では **wasbs:///tutorials/useoozie** を使用します。 次のコマンドを使用して、このディレクトリと、このワークフローで作成される新しい Hive テーブルを保持する data ディレクトリを作成します。

```
hdfs dfs -mkdir -p /tutorials/useoozie/data
```

> [!NOTE]
> `-p` パラメーターを指定すると、ディレクトリがまだ存在しない場合に、すべてのディレクトリがこのパスに作成されます。 **data** ディレクトリは、**useooziewf.hql** スクリプトで使用するデータを保持するために使用されます。

次のコマンドを実行して、Hive ジョブと Sqoop ジョブの実行時に Oozie がユーザー アカウントを偽装できるようにします。 **USERNAME** をログイン名に置き換えます。

```
sudo adduser USERNAME users
```

ユーザーが既に users のメンバーであることを示すエラーが返された場合は無視してかまいません。

## <a name="add-a-database-driver"></a>データベース ドライバーの追加

このワークフローでは、Sqoop を使用してデータを SQL Database にエクスポートするため、SQL Database との対話に使用する JDBC ドライバーのコピーを提供する必要があります。 次のコマンドを使用して、ドライバーを作業ディレクトリにコピーします。

```
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

ワークフローで他のリソース (MapReduce アプリケーションを含む jar など) を使用する場合は、これらのリソースも追加する必要があります。

## <a name="define-the-hive-query"></a>Hive クエリの定義

次の手順に従って、クエリを定義する HiveQL スクリプトを作成します。これは、このドキュメントで後述する Oozie ワークフローで使用します。

1. SSH を使用してクラスターに接続します。 次のコマンドは、`ssh` コマンドの使用例です。 __USERNAME__ をクラスターの SSH ユーザーに置き換えます。 __CLUSTERNAME__ を HDInsight クラスターの名前に置き換えます。

    ```
    ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH 接続から、次のコマンドを使用して、新しいファイルを作成します。

    ```
    nano useooziewf.hql
    ```

3. nano エディターが開いたら、ファイルの内容として次のスクリプトを使用します。

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    このスクリプトでは、次の 2 つの変数を使用しています。

    * **${hiveTableName}**: 作成されるテーブルの名前が含まれます。

    * **${hiveDataFolder}**: テーブルのデータ ファイルを保存する場所が含まれます。

    ワークフロー定義ファイル (このチュートリアルでは workflow.xml) は、実行時にこの HiveQL スクリプトにこれらの値を渡します。

4. Ctrl + X キーを押してエディターを終了します。 メッセージが表示されたら、**Y** を選択してファイルを保存し、**Enter** キーを押して、ファイル名として **useooziewf.hql** を使用します。
5. 次のコマンドを使用して、**useooziewf.hql** を **wasbs:///tutorials/useoozie/useooziewf.hql** にコピーします。

    ```
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    これらのコマンドにより、このクラスターに関連付けられている Azure ストレージ アカウントに **useooziewf.hql** ファイルが保存されるため、クラスターが削除されてもファイルは保持されます。 これにより、ジョブとワークフローを維持しながら、使用されていないクラスターを削除することでコストを削減できます。

## <a name="define-the-workflow"></a>ワークフローの定義

Oozie ワークフロー定義は hPDL (XML プロセス定義言語) で書かれています。 次の手順に従ってワークフローを定義します。

1. 次のステートメントを使用して、新しいファイルを作成し、編集します。

    ```
    nano workflow.xml
    ```

2. nano エディターが開いたら、ファイルの内容として次のコードを入力します。

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
            <archive>sqljdbc41.jar</archive>
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

   * **RunHiveScript**: 開始アクションです。このアクションで **useooziewf.hql Hive** スクリプトを実行します。

   * **RunSqoopExport**: Sqoop を使用して、作成されたデータを Hive スクリプトから SQL Database にエクスポートします。 このアクションは、**RunHiveScript** アクションが正常に実行された場合にのみ実行されます。

     > [!NOTE]
     > Oozie ワークフローとワークフロー アクションの使用の詳細については、[Apache Oozie 4.0 のドキュメント][apache-oozie-400] (HDInsight バージョン 3.0 の場合) または [Apache Oozie 3.3.2 のドキュメント][apache-oozie-332] (HDInsight バージョン 2.1 の場合) を参照してください。

     このワークフローには、このドキュメントで後述するジョブ定義で使用する値に置き換えられる複数のエントリ ( `${jobTracker}`など) が含まれています。

     また、Sqoop セクションの `<archive>sqljdbc4.jar</arcive>` エントリにも注意してください。 このエントリは、このアクションの実行時にこのアーカイブを Sqoop で使用できるようにすることを Oozie に指示します。

3. Ctrl + X キーを押した後、**Y** キーと **Enter** キーを押してファイルを保存します。

4. 次のコマンドを使用して、**workflow.xml** ファイルを **/tutorials/useoozie/workflow.xml** にコピーします。

    ```
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>データベースの作成

[SQL Database の作成](../sql-database/sql-database-get-started.md)に関するドキュメントに記載されている手順に従って、新しいデータベースを作成します。 データベースを作成するときに、データベース名として **oozietest** を使用します。 この名前はデータベース サーバーで使用されます。これは次のセクションで必要となるため、メモしておいてください。

### <a name="create-the-table"></a>テーブルを作成する

> [!NOTE]
> SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。


1. 次のコマンドを使用して、FreeTDS を HDInsight クラスターにインストールします。

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. FreeTDS がインストールされると、次のコマンドを使用して、以前に作成した SQL Database サーバーに接続できます。

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    次のような出力が返されます。

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. `1>` プロンプトで、以下の行を入力します。

    ```
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 これにより、Sqoop による書き込み先となる **mobiledata** という名前の新しいテーブルが作成されます。

    次を使用して、テーブルが作成されたことを確認します。

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    次のような出力が表示されます。

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    oozietest       dbo     mobiledata      BASE TABLE
    ```

4. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="create-the-job-definition"></a>ジョブ定義の作成

ジョブ定義では、workflow.xml とワークフローで使用される他のファイル (useooziewf.hql など) の検索場所を記述します。また、ワークフローおよび関連するファイル内で使用されるプロパティの値も定義します。

1. 次のコマンドを使用して、既定のストレージの完全な WASB アドレスを取得します。 これは構成ファイルですぐに使用されます。

    ```
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    次のような情報が返されます。

    ```xml
    <name>fs.defaultFS</name>
    <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > HDInsight クラスターで既定のストレージとして Azure Storage を使用する場合、`<value>` 要素の内容は `wasbs://`で始まります。 Azure Data Lake Store を使用する場合は、`adl://` で始まります。

    以下のステップで使用するため、`<value>` 要素の内容を保存します。

2. 次のコマンドを使用して、クラスターのヘッド ノードの FQDN を取得します。 これは、クラスターの JobTracker アドレスに使用されます。 これは構成ファイルですぐに使用されます。

    ```
    hostname -f
    ```

    次のような情報が返されます。

    ```hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net```

    JobTracker に使用するポートは 8050 であるため、JobTracker に使用する完全なアドレスは `hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050` です。

3. 次のコマンドを使用して、Oozie ジョブ定義構成を作成します。

    ```
    nano job.xml
    ```

4. nano エディターが開いたら、ファイルの内容として次のスクリプトを使用します。

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>JOBTRACKERADDRESS</value>
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
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * **wasbs://mycontainer@mystorageaccount.blob.core.windows.net** のすべてのインスタンスを、既定のストレージで返された値に置き換えます。

     > [!WARNING]
     > パスが `wasb` パスの場合は、完全パスを使用する必要があります。 `wasb:///` だけに短縮しないでください。

   * **JOBTRACKERADDRESS** を、前に返された JobTracker/ResourceManager のアドレスに置き換えます。
   * **YourName** を HDInsight クラスターのログイン名に置き換えます。
   * **serverName**、**adminLogin**、**adminPassword** を Azure SQL Database の情報に置き換えます。

     このファイル内のほとんどの情報は、workflow.xml ファイルまたは ooziewf.hql ファイルで使用される値 (${nameNode} など) を設定するために使用されます。

     > [!NOTE]
     > **oozie.wf.application.path** エントリは、このジョブで実行されるワークフローが含まれた workflow.xml ファイルの検索場所を定義します。

5. Ctrl + X キーを押した後、**Y** キーと **Enter** キーを押してファイルを保存します。

## <a name="submit-and-manage-the-job"></a>ジョブの送信と管理

次の手順では、Oozie コマンドを使用して、クラスターで Oozie ワークフローを送信および管理します。 Oozie コマンドは、 [Oozie REST API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)の使いやすいインターフェイスです。

> [!IMPORTANT]
> Oozie コマンドを使用するときは、HDInsight ヘッドノードの FQDN を使用する必要があります。 この FQDN にはクラスターからのみアクセスできます。クラスターが Azure Virtual Network 上にある場合は、同じネットワーク上の他のマシンからアクセスできます。


1. 次のコマンドを使用して、Oozie サービスの URL を取得します。

    ```
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    次のような情報が返されます。

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` の部分が Oozie コマンドで使用する URL です。

2. 次のコマンドを使用して、URL の環境変数を作成します。すべてのコマンドでこれを入力する必要があるわけではありません。

    ```
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    URL を前に返された URL に置き換えます。
3. 次のコマンドを使用してジョブを送信します。

    ```
    oozie job -config job.xml -submit
    ```

    このコマンドでは、 **job.xml** からジョブ情報を読み込んで Oozie に送信しますが、ジョブは実行しません。

    コマンドが完了すると、ジョブの ID が返されます。 たとえば、「 `0000005-150622124850154-oozie-oozi-W`」のように入力します。 これはジョブの管理に使用されます。

4. 次のコマンドを使用して、ジョブの状態を表示します。 前のコマンドによって返されたジョブ ID を入力します。

    ```
    oozie job -info <JOBID>
    ```

    次のような情報が返されます。

    ```
    Job ID : 0000005-150622124850154-oozie-oozi-W
    ------------------------------------------------------------------------------------------------------------------------------------
    Workflow Name : useooziewf
    App Path      : wasbs:///tutorials/useoozie
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
    ```

    このジョブの状態は `PREP` です。これは、ジョブは送信済みですが、まだ開始されていないことを示しています。

5. 次のコマンドを使用してジョブを開始します。

    ```
    oozie job -start JOBID
    ```

    このコマンドの実行後に状態を確認すると、ジョブが実行中状態になり、ジョブのアクションに関する情報が返されます。

6. タスクが正常に完了したら、次のコマンドを使用して、データが生成され、SQL Database テーブルにエクスポートされたことを確認できます。

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    `1>` プロンプトで次のコマンドを入力します。

    ```
    SELECT * FROM mobiledata
    GO
    ```

    次のような情報が返されます。

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Oozie コマンドの詳細については、 [Oozie コマンド ライン ツール](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html)に関するページをご覧ください。

## <a name="oozie-rest-api"></a>Oozie REST API

Oozie REST API では、Oozie を使用する独自のツールを構築できます。 Oozie REST API の使用に関する HDInsight 固有の情報は次のとおりです。

* **URI**: `https://CLUSTERNAME.azurehdinsight.net/oozie` でクラスターの外部から REST API にアクセスできます。

* **認証**: クラスターの HTTP アカウント (admin) とパスワードを使用して、API に対して認証する必要があります。 次に例を示します。

    ```
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Oozie REST API の使用方法の詳細については、 [Oozie Web サービス API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html)に関するページをご覧ください。

## <a name="oozie-web-ui"></a>Oozie Web UI

Oozie Web UI は、クラスターでの Oozie ジョブの状態を表示する Web ベースのビューを提供します。 Oozie Web UI を使用して、ジョブの状態、ジョブ定義、構成、ジョブのアクションのグラフ、ジョブのログを表示できます。 また、ジョブのアクションの詳細を表示することもできます。

Oozie Web UI にアクセスするには、次の手順に従います。

1. HDInsight クラスターへの SSH トンネルを作成します。 これを実行する方法の詳細については、「[SSH トンネリングを使用して Ambari Web UI、ResourceManager、JobHistory、NameNode、Oozie、およびその他の Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)」を参照してください。

2. トンネルが作成されたら、Web ブラウザーで Ambari Web UI を開きます。 Ambari サイトの URI は **https://CLUSTERNAME.azurehdinsight.net** です。 **CLUSTERNAME** を、Linux ベースの HDInsight クラスターの名前に置き換えます。

3. ページの左側で **[Oozie]** を選択し、**[Quick Links]**、**[Oozie Web UI]** の順にクリックします。

    ![メニューの画像](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. Oozie Web UI では、実行中のワークフロー ジョブが既定で表示されます。 すべてのワークフロー ジョブを表示するには、 **[All Jobs]**を選択します。

    ![すべてのジョブの表示](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. ジョブを選択すると、そのジョブの詳細が表示されます。

    ![[Job Info]](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. [Job Info] タブでは、基本的なジョブ情報とジョブの個々のアクションを確認できます。 上部にあるタブを使用して、ジョブ定義の表示、ジョブ構成の表示、ジョブ ログへのアクセス、ジョブの有向非巡回グラフ (DAG) の表示を行うことができます。

   * **[Job Log]**: ジョブのすべてのログを取得するには、**[GetLogs]** ボタンを選択します。ログをフィルター処理するには、**[Enter Search Filter]** フィールドを使用します。

       ![ジョブ ログ](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **[Job DAG]**: DAG は、ワークフローで取得されるデータ パスの概要をグラフィックで表したものです。

       ![ジョブの DAG](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. **[Job Info (ジョブ情報)]** タブでアクションのいずれかを選択すると、そのアクションの情報が表示されます。 たとえば、 **RunHiveScript** アクションを選択します。

    ![アクション情報](./media/hdinsight-use-oozie-linux-mac/action.png)

8. ジョブの JobTracker 情報を表示する際に使用できる **Console URL**へのリンクなど、アクションの詳細を確認できます。

## <a name="scheduling-jobs"></a>ジョブのスケジュール設定

コーディネーターを使用すると、ジョブの開始時刻、終了時刻、発生頻度を指定して、ジョブを特定の時間にスケジュールできます。

ワークフローのスケジュールを定義するには、次の手順に従います。

1. 次のコマンドを使用して、 **coordinator.xml**という名前の新しいファイルを作成します。

    ```
    nano coordinator.xml
    ```

    このファイルの内容として、次のコードを使用します。

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    `${...}` 変数に注意してください。これらは、実行時にジョブ定義の値に置き換えられます。 変数は次のとおりです。

   * **${coordFrequency}**: ジョブの実行されるインスタンスの間隔。

   * **${coordStart}**: ジョブの開始時刻。

   * **${coordEnd}**: ジョブの終了時刻。

   * **${coordTimezone}**: コーディネーター ジョブでは、(通常は UTC を使用して表される) 夏時間なしの固定タイム ゾーンを使用します。 このタイム ゾーンを、「Oozie 処理のタイムゾーン」と呼びます。

   * **${wfPath}**: workflow.xml のパス。

2. Ctrl + X キーを押した後、**Y** キーと **Enter** キーを押してファイルを保存します。

3. 次のコマンドを使用して、ファイルをこのジョブの作業ディレクトリにコピーします。

    ```
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. 次のコマンドを使用して、 **job.xml** ファイルを変更します。

    ```
    nano job.xml
    ```

    次の変更を行います。

   * `<name>oozie.wf.application.path</name>` を `<name>oozie.coord.application.path</name>` に変更します。 これにより、ワークフロー ファイルではなく、コーディネーター ファイルを実行するよう Oozie に指示します。

   * 次のコードを追加して、coordinator.xml で使用される変数を、workflow.xml の場所を参照するように設定します。

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasbs://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       `wasbs://mycontainer@mystorageaccount.blob.core.windows` テキストを、job.xml ファイルの他のエントリで使用されている値に置き換えます。

   * 次のコードを追加して、coordinator.xml ファイルで使用する開始時刻、終了時刻、頻度を定義します。

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-02-07T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-02-09T12:00Z</value>
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

       これらは、開始時刻を 2017 年 2 月 7 日 12:00 PM に、終了時刻を 2017 年 2 月 9 日 12:00 PM に、このジョブの実行間隔を毎日に設定します。 頻度は分単位であるため、24 時間 x 60 分 = 1440 分になります。 最後に、タイムゾーンを UTC に設定しています。

5. Ctrl + X キーを押した後、**Y** キーと **Enter** キーを押してファイルを保存します。

6. ジョブを実行するには、次のコマンドを使用します。

    ```
    oozie job -config job.xml -run
    ```

    これにより、ジョブが送信され、開始されます。

7. Oozie Web UI にアクセスし、 **[Coordinator Jobs (コーディネーター ジョブ)]** タブを選択すると、次のような情報が表示されます。

    ![[Coordinator Jobs] タブ](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    **[Next Materialization (次の実体化)]** エントリに注意してください。これは、このジョブが次回実行される日時を示しています。

8. 以前のワークフロー ジョブと同様に、Web UI でジョブ エントリを選択すると、そのジョブの情報が表示されます。

    ![コーディネーター ジョブ情報](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    これは、スケジュールされたワークフロー内の個々のアクションではなく、ジョブの正常な実行のみを示しています。 個々のアクションを表示するには、 **[Action]** エントリのいずれかを選択します。 これにより、以前のワークフロー ジョブで取得された情報と同様の情報が表示されます。

    ![アクション情報](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>トラブルシューティング

Oozie UI では、Oozie ログと、Hive クエリなどの MapReduce タスクの JobTracker ログへのリンクの両方を簡単に表示できるので、Oozie ジョブに関する問題のトラブルシューティングを行うときに非常に役立ちます。 通常、トラブルシューティングのパターンは次のようになります。

1. Oozie Web UI でジョブを表示します。

2. 特定のアクションでエラーが発生した場合は、そのアクションを選択して、 **[Error Message]** フィールドにエラーの詳細が示されているかどうかを確認します。

3. 使用できる場合は、アクションから URL を使用して、アクションの詳細 (JobTracker ログなど) を表示します。

発生する可能性のあるエラーとその解決方法を以下に示します。

### <a name="ja009-cannot-initialize-cluster"></a>JA009: Cannot initialize cluster (クラスターを初期化できません)

**現象**: ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、RunHiveScript の状態が **START_MANUAL** と示されます。 アクションを選択すると、次のエラー メッセージが表示されます。

    JA009: Cannot initialize Cluster. Please check your configuration for map

**原因**: **job.xml** ファイルで使用される WASB アドレスに、ストレージ コンテナー名またはストレージ アカウント名が含まれていません。 WASB アドレスの形式は、 `wasbs://containername@storageaccountname.blob.core.windows.net`である必要があります。

**解決方法**: ジョブで使用する WASB アドレスを変更します。

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie is not allowed to impersonate &lt;USER> (Oozie は &lt;USER> の偽装を許可されていません)

**現象**: ジョブの状態が **SUSPENDED** に変更されます。 ジョブの詳細に、RunHiveScript の状態が **START_MANUAL** と示されます。 アクションを選択すると、次のエラー メッセージが表示されます。

    JA002: User: oozie is not allowed to impersonate <USER>

**原因**: 現在の権限設定で、Oozie が指定されたユーザー アカウントを偽装することを許可していません。

**解決方法**: Oozie が **users** グループのユーザーを偽装することを許可します。 `groups USERNAME` を使用して、ユーザー アカウントがメンバーとして属するグループを確認します。 ユーザーが **users** グループのメンバーでない場合は、次のコマンドを使用して、ユーザーをこのグループに追加します。

    sudo adduser USERNAME users

> [!NOTE]
> ユーザーがグループに追加されたことを HDInsight が認識するまで数分かかる場合があります。

### <a name="launcher-error-sqoop"></a>Launcher ERROR (ランチャー エラー) (Sqoop)

**現象**: ジョブの状態が **KILLED** に変更されます。 ジョブの詳細に、RunSqoopExport の状態が **ERROR**と示されます。 アクションを選択すると、次のエラー メッセージが表示されます。

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**原因**: Sqoop が、データベースにアクセスするために必要なデータベース ドライバーを読み込むことができません。

**解決方法**: Oozie ジョブから Sqoop を使用する場合、ジョブで使用するその他のリソース (workflow.xml など) とともにデータベース ドライバーを含める必要があります。

また、workflow.xml の `<sqoop>...</sqoop>` セクションから、データベース ドライバーが格納されたアーカイブを参照する必要があります。

たとえば、このドキュメントのジョブの場合、次の手順に従います。

1. sqljdbc4.1.jar ファイルを /tutorials/useoozie ディレクトリにコピーします。

    ```
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. workflow.xml を変更して、 `</sqoop>`の上の新しい行に次の内容を追加します。

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Oozie ワークフローを定義する方法と Oozie ジョブを実行する方法について説明しました。 HDInsight の使用方法の詳細については、次の記事をご覧ください。

* [HDInsight での時間ベースの Oozie コーディネーターの使用][hdinsight-oozie-coordinator-time]
* [HDInsight での Hadoop ジョブ用データのアップロード][hdinsight-upload-data]
* [HDInsight の Hadoop での Sqoop の使用][hdinsight-use-sqoop]
* [HDInsight での Hive と Hadoop の使用][hdinsight-use-hive]
* [HDInsight での Pig と Hadoop の使用][hdinsight-use-pig]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/data-factory-data-transformation-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

