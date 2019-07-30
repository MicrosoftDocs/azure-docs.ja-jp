---
title: チュートリアル:Azure HDInsight で対話型クエを使用して抽出、変換、読み込み (ETL) 操作を実行する
description: チュートリアル - 生の CSV データセットからデータを抽出し、HDInsight で対話型クエリを使用してデータを変換した後、変換済みデータを Apache Sqoop を使用して Azure SQL データベースに読み込む方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 07/02/2019
ms.author: hrasheed
ms.custom: hdinsightactive,mvc
ms.openlocfilehash: fbab8502c088c2ae7a4b8e87285d7e4cac1de4c0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807401"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight で対話型クエリを使用してデータの抽出、変換、読み込みを行う

このチュートリアルでは、一般公開されているフライト データの生の CSV データ ファイルを取得して HDInsight クラスター ストレージにインポートした後、Azure HDInsight で対話型クエリを使用してデータを変換します。 データを変換したら、[Apache Sqoop](https://sqoop.apache.org/) を使用して Azure SQL データベースにデータを読み込みます。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * サンプルのフライト データをダウンロードする
> * HDInsight クラスターにデータをアップロードする
> * 対話型クエリを使用してデータを変換する
> * Azure SQL データベースでテーブルを作成する
> * Sqoop を使用して Azure SQL データベースにデータをエクスポートする

## <a name="prerequisites"></a>前提条件

* HDInsight 上の対話型クエリ クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](../hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照して、 **[クラスターの種類]** として **[対話型クエリ]** を選択します。

* Azure SQL Database。 保存先データ ストアとして Azure SQL データベースを使用します。 SQL データベースがない場合は、「[Azure Portal で Azure SQL データベースを作成する](/azure/sql-database/sql-database-single-database-get-started)」を参照してください。

* SSH クライアント 詳細については、[SSH を使用して HDInsight (Apache Hadoop) に接続する方法](../hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="download-the-flight-data"></a>フライト データのダウンロード

1. [米国運輸省研究・革新技術庁/運輸統計局](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)のページに移動します。

2. ページで、すべてのフィールドをクリアしてから、次の値を選択します。

   | EnableAdfsAuthentication | 値 |
   | --- | --- |
   | Filter Year |2019 |
   | Filter Period |January |
   | フィールド |Year、FlightDate、Reporting_Airline、DOT_ID_Reporting_Airline、Flight_Number_Reporting_Airline、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |

3. **[Download]** を選択します。 選択したデータ フィールドを含む .zip ファイルがダウンロードされます。

## <a name="upload-data-to-an-hdinsight-cluster"></a>HDInsight クラスターにデータをアップロードする

HDInsight クラスターに関連付けられたストレージにデータをアップロードする方法はたくさんあります。 このセクションでは、`scp` を使用してデータをアップロードします。 データをアップロードする他の方法については、[HDInsight へのデータのアップロード](../hdinsight-upload-data.md)に関する記事をご覧ください。

1. .zip ファイルを HDInsight クラスターのヘッド ノードにアップロードします。 `FILENAME` を .zip ファイルの名前に、`CLUSTERNAME` を HDInsight クラスターの名前に置き換えて、以下のコマンドを編集します。 その後、コマンド プロンプトを開き、ファイルの場所に作業ディレクトリを設定してから、コマンドを入力します。

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    「yes」または「no」を入力して続行するよう求められた場合は、コマンド プロンプトから「yes」と入力して Enter キーを押してください。 入力するとき、ウィンドウにテキストは表示されません。

2. アップロードが完了したら、SSH を使用してクラスターに接続します。 `CLUSTERNAME` を HDInsight クラスターの名前に置き換えて、以下のコマンドを編集します。 次のコマンドを入力します。

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. SSH 接続が確立されたら、環境変数を設定します。 `FILE_NAME`、`SQL_SERVERNAME`、`SQL_DATABASE`、`SQL_USER`、`SQL_PASWORD` を適切な値に置き換えます。 その後、コマンドを入力します。

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. 以下のコマンドを入力して、.zip ファイルを解凍します。

    ```bash
    unzip $FILENAME.zip
    ```

5. 次のコマンドを入力し、HDInsight ストレージにディレクトリを作成してから、そのディレクトリに .csv ファイルをコピーします。

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Hive クエリを使用したデータの変換

HDInsight クラスター上で Hive ジョブを実行する方法はたくさんあります。 このセクションでは、[Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell) を使用して Hive ジョブを実行します。 Hive ジョブを実行するその他の方法については、[HDInsight での Apache Hive の使用](../hadoop/hdinsight-use-hive.md)に関するページを参照してください。

Hive ジョブの一環として、.csv ファイルから **Delays** という名前の Hive テーブルにデータをインポートします。

1. HDInsight クラスター用に既に開いている SSH プロンプトから、次のコマンドを使用して **flightdelays.hql** という名前の新しいファイルを作成して編集します。

    ```bash
    nano flightdelays.hql
    ```

2. このファイルの内容として、次のテキストを使用します。

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

3. ファイルを保存するには、**Ctrl + X** キー、**Y** キー、Enter キーの順に押します。

4. Hive を起動し、**flightdelays.hql** ファイルを実行するには、次のコマンドを使用します。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. **flightdelays.hql** スクリプトの実行が完了したら、次のコマンドを使用して対話型 Beeline セッションを開きます。

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. `jdbc:hive2://localhost:10001/>` プロンプトが表示されたら、次のクエリを使用してインポートされたフライト遅延データからデータを取得します。

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

7. Beeline を終了するには、プロンプトで「 `!quit` 」と入力します。

## <a name="create-a-sql-database-table"></a>SQL データベース テーブルの作成

SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](http://www.freetds.org/) を使用します。

1. FreeTDS をインストールするには、クラスターへの開いている SSH 接続から、次のコマンドを使用します。

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. インストールが完了したら、次のコマンドを使用して SQL Database サーバーに接続します。

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    次のテキストのような出力が返されます。

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. `1>` プロンプトで、以下の行を入力します。

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    `GO` ステートメントを入力すると、前のステートメントが評価されます。 このステートメントにより、クラスター化インデックス付きの、**delays** という名前のテーブルが作成されます。

    次のクエリを使用して、テーブルが作成されたことを確認します。

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    出力は次のテキストのようになります。

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Apache Sqoop を使用して SQL データベースにデータをエクスポートする

前のセクションで、変換済みデータを `/tutorials/flightdelays/output` にコピーしました。 このセクションでは、Sqoop を使用して、`/tutorials/flightdelays/output` のデータを、Azure SQL データベースに作成したテーブルにエクスポートします。

1. 以下のコマンドを入力して、Sqoop で SQL データベースを認識できることを確認します。

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    このコマンドにより、先ほど `delays` テーブルを作成したデータベースを含む、データベースのリストが返されます。

2. 以下のコマンドを入力して、`/tutorials/flightdelays/output` から `delays` テーブルにデータをエクスポートします。

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop が、`delays` テーブルを含むデータベースに接続され、`/tutorials/flightdelays/output` ディレクトリから `delays` テーブルにデータがエクスポートされます。

3. sqoop コマンドが完了した後、以下のコマンドを入力し、tsql ユーティリティを使ってデータベースに接続します。

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    次のステートメントを使って、データが delays テーブルにエクスポートされたことを確認します。

    ```sql
    SELECT * FROM delays
    GO
    ```

    テーブル内のデータの一覧が表示されます。 テーブルには、都市の名前と、その都市のフライトの平均遅延時間が含まれます。 

    「 `exit` 」と入力して、tsql ユーティリティを終了します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

クラスターを削除するには、「[ブラウザー、PowerShell、または Azure CLI を使用して HDInsight クラスターを削除する](../hdinsight-delete-cluster.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、生の CSV データ ファイルを取得し、それを HDInsight クラスター ストレージにインポートしてから、Azure HDInsight で対話型クエリを使用してデータを変換しました。  次のチュートリアルに進んで、Apache Hive Warehouse Connector について確認してください。

> [!div class="nextstepaction"]
>[Hive Warehouse Connector を使用して Apache Spark と Apache Hive を統合する](./apache-hive-warehouse-connector.md)
