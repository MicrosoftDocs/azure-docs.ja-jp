---
title: "HDInsight での Hive を使用したフライトの遅延データの分析 - Azure | Microsoft Docs"
description: "Linux ベースの HDInsight で Hive を使用してフライト データを分析し、Sqoop を使用して SQL Database にデータをエクスポートする方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 0c23a079-981a-4079-b3f7-ad147b4609e5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f333354311b16c00a0d43a691f139f5f80383d1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-flight-delay-data-by-using-hive-on-linux-based-hdinsight"></a>Linux ベースの HDInsight 上の Hive を使用したフライト遅延データの分析

Linux ベースの HDInsight で Hive を使用してフライト遅延データを分析する方法、および Sqoop を使用して Azure SQL Database にデータをエクスポートする方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、Azure HDInsight バージョン 3.4 以降で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* **HDInsight クラスター**。 Linux ベースの新しい HDInsight クラスターを作成する手順については、[HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

* **Azure SQL データベース**。 保存先データ ストアとして Azure SQL Database を使用します。 SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](../sql-database/sql-database-get-started.md)」を参照してください。

* **Azure CLI**。 Azure CLI をインストールしていない場合は、「[Azure CLI 1.0 のインストール](../cli-install-nodejs.md)」を参照してください。

## <a name="download-the-flight-data"></a>フライト データのダウンロード

1. [米国運輸省研究・革新技術庁/運輸統計局][rita-website]のページに移動します。

2. このページで、次の値を選択します。

   | 名前 | 値 |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | フィールド |Year、FlightDate、UniqueCarrier、Carrier、FlightNum、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |
   その他のフィールドはすべてクリアします。 

3. **[Download]** を選択します。

## <a name="upload-the-data"></a>データのアップロード

1. 次のコマンドを使用して、HDInsight クラスター ヘッドノードに .zip ファイルをアップロードします。

    ```
    scp FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

    *FILENAME* を .zip ファイルの名前に置き換えます。 *USERNAME* を HDInsight クラスターの SSH ログインに置き換えます。 *CLUSTERNAME* を HDInsight クラスターの名前に置き換えます。

   > [!NOTE]
   > パスワードを使用して SSH ログインを認証する場合は、パスワードを入力するよう求められます。 公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーへのパスを指定することが必要な場合があります。 たとえば、「 `scp -i ~/.ssh/id_rsa FILENAME.zip USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`」のように入力します。

2. アップロードが完了したら、SSH を使用してクラスターに接続します。

    ```ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net```

    詳細については、「[SSH を使用して HDInsight (Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」を参照してください。

3. 次のコマンドを使用して .zip ファイルを解凍します。

    ```
    unzip FILENAME.zip
    ```

    このコマンドで、約 60 MB の .csv ファイルが抽出されます。

4. 次のコマンドを使用して HDInsight のストレージにディレクトリを作成し、そのディレクトリにファイルをコピーします。

    ```
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="create-and-run-the-hiveql"></a>HiveQL の作成と実行

次の手順に従って、.csv ファイルから **Delays** という名前の Hive テーブルにデータをインポートします。

1. 次のコマンドを使用して、 **flightdelays.hql** という名前の新しいファイルを作成し、編集します。

    ```
    nano flightdelays.hql
    ```

    このファイルの内容として、次のテキストを使用します。

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

2. ファイルを保存するには、Ctrl+X キーを押し、次に Y キーを押します。

3. Hive を起動し、**flightdelays.hql** ファイルを実行するには、次のコマンドを使用します。

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

   > [!NOTE]
   > HiveServer2 が実行されている HDInsight クラスターのヘッド ノードに接続していることから、この例では `localhost` を使用しています。

4. __flightdelays.hql__ スクリプトの実行が完了したら、次のコマンドを使用して対話型 Beeline セッションを開きます。

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

5. `jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のクエリを使用してインポートされたフライト遅延データからデータを取得します。

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    このクエリにより、悪天候による遅延が発生した都市の一覧と平均遅延時間が取得され、`/tutorials/flightdelays/output` に保存されます。 その後、Sqoop がこの場所からデータを読み取り、Azure SQL Database にエクスポートします。

6. Beeline を終了するには、プロンプトで「 `!quit` 」と入力します。

## <a name="create-a-sql-database"></a>SQL Database の作成

SQL データベースが既にある場合は、サーバー名を入手する必要があります。 [Azure Portal](https://portal.azure.com) でサーバー名を見つけるには、**[SQL データベース]** を選択し、使用するデータベースの名前にフィルターを掛けます。 サーバー名は **[サーバー]** 列に表示されます。

まだ SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](../sql-database/sql-database-get-started.md)」の情報を使用して作成します。 データベースに使用したサーバー名を保存します。

## <a name="create-a-sql-database-table"></a>SQL データベース テーブルの作成

> [!NOTE]
> SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。


1. SSH を使用して Linux ベースの HDInsight クラスターに接続し、SSH セッションから次の手順を実行します。

2. 次のコマンドを使用して FreeTDS をインストールします。

    ```
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

3. インストールが完了したら、次のコマンドを使用して SQL Database サーバーに接続します。 **serverName** は SQL Database サーバー名に置き換えてください。 **adminLogin** と **adminPassword** は SQL Database のログイン情報に置き換えてください。 **databaseName** はデータベース名に置き換えてください。

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    次のテキストのような出力が返されます。

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to sqooptest
    1>
    ```

4. `1>` プロンプトで、以下の行を入力します。

    ```
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED   
    ([origin_city_name] ASC))
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 このクエリにより、クラスター化インデックス付きの、**delays** という名前のテーブルが作成されます。

    次のクエリを使用して、テーブルが作成されたことを確認します。

    ```
    SELECT * FROM information_schema.tables
    GO
    ```

    出力は次のテキストのようになります。

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo     delays      BASE TABLE
    ```

5. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="export-data-with-sqoop"></a>Sqoop を使用したデータのエクスポート

1. 次のコマンドを使用して、Sqoop が SQL データベースを認識できることを確認します。

    ```
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    このコマンドにより、先ほど delays テーブルを作成したデータベースを含むデータベースの一覧が返されます。

2. 次のコマンドを使って、hivesampletable から delays テーブルにデータをエクスポートします。

    ```
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=<databaseName>' --username <adminLogin> --password <adminPassword> --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop は delays テーブルを含むデータベースに接続して、`/tutorials/flightdelays/output` ディレクトリから delays テーブルにデータをエクスポートします。

3. sqoop コマンドが完了したら、tsql ユーティリティを使ってデータベースに接続します。

    ```
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D <databaseName>
    ```

    次のステートメントを使って、データが delays テーブルにエクスポートされたことを確認します。

    ```
    SELECT * FROM delays
    GO
    ```

    テーブル内のデータの一覧が表示されます。 「 `exit` 」と入力して、tsql ユーティリティを終了します。

## <a name="next-steps"></a>次のステップ

HDInsight でのデータ操作の詳細については、次の記事を参照してください。

* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Oozie の使用][hdinsight-use-oozie]
* [HDInsight での Sqoop の使用][hdinsight-use-sqoop]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]
* [HDInsight 用 Python ストリーミング MapReduce プログラムの開発][hdinsight-develop-streaming]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-streaming-python.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
