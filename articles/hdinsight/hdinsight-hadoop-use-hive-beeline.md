---
title: "Beeline による HDInsight (Hadoop) での Hive の使用 | Microsoft Docs"
description: "SSH を使用して HDInsight で Hadoop クラスターに接続し、Beeline を使用して Hive クエリを対話的に実行する方法について説明します。 Beeline は、JDBC を介して HiveServer2 を使用するためのユーティリティです。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 3adfb1ba-8924-4a13-98db-10a67ab24fca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: b015e3d99c72c1840a31bacee5d9655fa9f48da7
ms.lasthandoff: 03/25/2017


---
# <a name="use-hive-with-hadoop-in-hdinsight-with-beeline"></a>Beeline による HDInsight での Hive と Hadoop の使用
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

この記事では、Secure Shell (SSH) を使用して Linux ベースの HDInsight クラスターに接続してから、 [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) コマンド ライン ツールを使用して Hive クエリを対話的に実行する方法について説明します。

> [!NOTE]
> Beeline は Hive への接続に JDBC を使用します。 Hive の JDBC 使用に関する詳細については、「 [Hive の JDBC ドライバーを使用して Azure HDInsight の Hive に接続する](hdinsight-connect-hive-jdbc-driver.md)」を参照してください。

## <a id="prereq"></a>前提条件
この記事の手順を完了するには、次のものが必要です。

* HDInsight クラスターでの Linux ベースの Hadoop

  > [!IMPORTANT]
  > Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。

* SSH クライアント SSH クライアントを備えた Linux、Unix、および Mac OS Windows ユーザーは [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのクライアントをダウンロードする必要があります。

## <a id="ssh"></a>SSH を使用した接続
SSH コマンドを使用して、HDInsight クラスターの完全修飾ドメイン名 (FQDN) に接続します。 FQDN はクラスターに指定した名前で、その後、 **.azurehdinsight.net**が続きます。 以下の例では、 **myhdinsight**という名前のクラスターに接続します。

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**HDInsight クラスターの作成時に SSH 認証に証明書キーを指定した場合は** 、クライアント システムの秘密キーの場所を指定する必要があることがあります。

    ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**HDInsight クラスターの作成時に SSH 認証のパスワードを指定した場合は** 、パスワードの入力を求められます。

SSH の使用方法の詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページをご覧ください。

## <a id="beeline"></a>Beeline コマンドを使用する
1. 接続したら、次のコマンドを使用して Beeline を開始します。
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    これにより、Beeline クライアントが起動され、JDBC の URL に接続されます。 ここでは、HiveServer2 がクラスター内の両方のヘッド ノードで実行されているため `localhost` を使用し、プライマリ ヘッドノード上で直接 Beeline を実行しています。
   
    コマンドが完了すると、`jdbc:hive2://localhost:10001/>` プロンプトが表示されます。
2. Beeline コマンドは、通常、`!` 文字で始まります。たとえば、「`!help`」でヘルプが表示されます。 ただし、多くの場合、`!` は省略できます。 たとえば、「`help`」も機能します。
   
    ヘルプを表示すると、「`!sql`」が表示されます。これは HiveQL ステートメントの実行に使用されます。 ただし、HiveQL が一般的に使用されるので、先行する `!sql` を省略できます。 次の 2 つのステートメントはまったく同じ結果となります。Hive で現在利用可能なテーブルが表示されます。
   
        !sql show tables;
        show tables;
   
    新しいクラスターでは、1 つのテーブルだけが表示されます。**hivesampletable** です。
3. hivesampletable のスキーマを表示するには、次を使用します。
   
        describe hivesampletable;
   
    次の情報が返されます。
   
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
   
    テーブルの列が表示されます。 このデータにクエリを実行できますが、代わりに、まったく新しいテーブルを作成し、Hive にデータを読み込み、スキーマを適用する方法について確認しましょう。
4. HDInsight クラスター付属のサンプル データを使用し、次のステートメントを入力して「 **log4jLogs** 」という名前の新しいテーブルを作成します。
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    これらのステートメントは次のアクションを実行します。
   
   * **DROP TABLE** - テーブルが既存の場合にテーブルとデータ ファイルを削除します。
   * **CREATE EXTERNAL TABLE** : Hive に新しく '外部' テーブルを作成します。 外部テーブルは Hive にテーブル定義のみを格納します。 データは元の場所に残されます。
   * **ROW FORMAT** - Hive にデータの形式を示します。 ここでは、各ログのフィールドは、スペースで区切られています。
   * **STORED AS TEXTFILE LOCATION** - Hive に、データの格納先 (example/data ディレクトリ) と、データはテキストとして格納されていることを示します。
   * **SELECT** - **t4** 列の値が **[ERROR]** であるすべての行の数を指定します。 ここでは、この値を含む行が 3 行あるため、 **3** という値が返されています。
   * **INPUT__FILE__NAME LIKE '%.log'** - Hive に .log で終わるファイルのデータのみを返す必要があることを示します。 通常、Hive でクエリを実行するとき、同じフォルダー内ではデータのスキーマが同じになりますが、このサンプル ログ ファイルは他のデータ形式で格納されます。
     
     > [!NOTE]
     > 基盤となるデータを外部ソースによって更新する (データの自動アップロード処理など) 場合や別の MapReduce 操作によって更新する場合に、Hive クエリで最新のデータを使用する場合は、外部テーブルを使用する必要があります。
     > 
     > 外部テーブルを削除しても、データは削除**されません**。テーブル定義のみが削除されます。
     > 
     > 
     
     このコマンドの出力は次のように表示されます。

     ```
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
5. Beeline を終了するには、 `!quit`を使用します。

## <a id="file"></a>HiveQL ファイルを実行する
Beeline を使用し、HiveQL ステートメントを含むファイルを実行することもできます。 次の手順でファイルを作成し、Beeline を使用してそれを実行します。

1. 次のコマンドを使用し、「 **query.hql**」という名前の新しいファイルを作成します。
   
        nano query.hql
2. エディターが開いたら、ファイルの内容として次を使用します。 このクエリにより「 **errorLogs**」という名前の新しい「内部」テーブルが作成されます。
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    これらのステートメントは次のアクションを実行します。
   
   * **CREATE TABLE IF NOT EXISTS** - 既存のテーブルがない場合、テーブルを作成します。 **EXTERNAL** キーワードが使用されていないため、これは内部テーブルであり、Hive のデータ保管先に格納され、完全に Hive によって管理されます。
   * **STORED AS ORC** - Optimized Row Columnar (ORC) 形式でデータを格納します。 この形式は、Hive にデータを格納するための、非常に効率的で適切な形式です。
   * **INSERT OVERWRITE ...SELECT** - **[ERROR]** を含む **log4jLogs** テーブルの列を選択し、**errorLogs** テーブルにデータを挿入します。
     
     > [!NOTE]
     > 外部テーブルとは異なり、内部テーブルを削除すると、基盤となるデータも削除されます。
     > 
     > 
3. ファイルを保存するには、**Ctrl** + **_X** キーを押し、**Y** キー、**Enter** キーの順に押します。
4. 次を使用し、Beeline でファイルを実行します。 **HOSTNAME** を以前にヘッド ノードで取得した名前に置き換え、**PASSWORD** を管理者アカウントのパスワードに置き換えます。
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin -i query.hql
   
   > [!NOTE]
   > `-i` パラメーターを使用すると、Beeline が開始されて query.hql ファイル内のステートメントが実行され、Beeline で `jdbc:hive2://localhost:10001/>` プロンプトが開いたままになります。 `-f` パラメーターを使用してファイルを実行することもできます。これにより、ファイルが処理されると Bash に戻ります。
   > 
   > 
5. **errorLogs** テーブルが作成されたことを確認するには、**errorLogs** からすべての行を返す次のステートメントを使用します。
   
        SELECT * from errorLogs;
   
    3 つのデータ行が返され、各行の t4 列には **[ERROR]** が含まれます。
   
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)

## <a name="more-about-beeline-connectivity"></a>Beeline 接続の詳細について
本ドキュメントのこの手順では、クラスター ヘッドノードで実行される HiveServer2 に `localhost` を使用して接続します。 ヘッドノードのホスト名または完全修飾ドメイン名も使用できますが、処理するための追加の手順が必要になります (ホスト名または FQDN を検索する手順)。 ヘッドノードから Beeline を使用する場合は、`localhost` を使用するだけで十分です。

クラスターにエッジ ノードがあり、Beeline がインストールされている場合は、ヘッドノードのホスト名か FQDN を使用して接続する必要があります。

クラスター外部のクライアントに Beeline がインストールされている場合は、次のコマンドを使用して接続できます。 **CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。 **PASSWORD** を管理者 (HTTP ログイン) アカウントのパスワードに替えます。

    beeline -u 'jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;ssl=true?hive.server2.transport.mode=http;hive.server2.thrift.http.path=hive2' -n admin -p PASSWORD

パラメーター/URI は、ヘッドノード上で直接実行する場合や、クラスター内のエッジ ノードから実行する場合とは異なることに注意してください。 これは、ポート 443 経由でトラフィックをルーティングするパブリックのゲートウェイを使用して、インターネットからクラスターに接続するためです。 また、他のいくつかのサービスはポート 443 のパブリックのゲートウェイを通じて公開されるため、直接接続する場合と URI が異なります。 インターネットから接続するときには、パスワードを入力してセッションを認証する必要もあります。

## <a id="summary"></a><a id="nextsteps"></a>次の手順
このように、Beeline コマンドを使用すると、HDInsight クラスターで簡単に対話的に Hive クエリを実行できます。

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

Hive で Tez を使用する場合、デバッグ情報については、次のドキュメントを参照してください。

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


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


