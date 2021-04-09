---
title: チュートリアル:Azure HDInsight を使用してデータの抽出、変換、読み込みを行う
description: このチュートリアルでは、生の CSV データセットからデータを抽出し、Azure HDInsight の Apache Hive を使用してデータを変換した後、Sqoop を使用して変換済みデータを Azure SQL Database に読み込む方法について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f8210c3bc0437180ace110f8decd9f83e18650ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98661935"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>チュートリアル:Azure HDInsight を使用してデータの抽出、変換、読み込みを行う

このチュートリアルでは、ETL (データの抽出、変換、読み込み) 操作を実行します。 生の CSV データ ファイルを取得して Azure HDInsight クラスターにインポートした後、Apache Hive を使用して変換し、Apache Sqoop を使用して Azure SQL Database に読み込みます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データを抽出し、HDInsight クラスターにアップロードする。
> * Apache Hive を使用してデータを変換する。
> * Sqoop を使用して Azure SQL Database にデータを読み込む。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* **HDInsight 用に構成された Azure Data Lake Storage Gen2 ストレージ アカウント**

    「[Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)」 (Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する) を参照してください。

* **HDInsight での Linux ベースの Hadoop クラスター**

    「[クイック スタート:Azure portal を使用して Azure HDInsight で Apache Hadoop と Apache Hive を使用する](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)」を参照してください。

* **Azure SQL Database**:保存先データ ストアとして Azure SQL Database を使用します。 SQL Database のデータベースがない場合は、[Azure portal での Azure SQL Database のデータベースの作成](../../azure-sql/database/single-database-create-quickstart.md)に関するページを参照してください。

* **Azure CLI**:Azure CLI をインストールしていない場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

* **Secure Shell (SSH) クライアント**:詳細については、[SSH を使用した HDInsight (Hadoop) への接続](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="download-the-flight-data"></a>フライト データのダウンロード

1. [米国運輸省研究・革新技術庁/運輸統計局](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)のページに移動します。

2. このページで、次の値を選択します。

   | 名前 | 値 |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | フィールド |Year、FlightDate、Reporting_Airline、IATA_CODE_Reporting_Airline、Flight_Number_Reporting_Airline、OriginAirportID、Origin、OriginCityName、OriginState、DestAirportID、Dest、DestCityName、DestState、DepDelayMinutes、ArrDelay、ArrDelayMinutes、CarrierDelay、WeatherDelay、NASDelay、SecurityDelay、LateAircraftDelay。 |
   
   その他のフィールドはすべてクリアします。

3. **[Download]** を選択します。 選択したデータ フィールドを含む .zip ファイルがダウンロードされます。

## <a name="extract-and-upload-the-data"></a>データの抽出とアップロード

このセクションでは、HDInsight クラスターにデータをアップロードしてから、そのデータを自分の Data Lake Storage Gen2 アカウントにコピーします。

1. コマンド プロンプトを開き、次の Secure Copy (Scp) コマンドを使用して HDInsight クラスターのヘッド ノードに .ZIP ファイルをアップロードします。

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * `<file-name>` プレースホルダーを .ZIP ファイルの名前に置き換えます。
   * `<ssh-user-name>` プレースホルダーを HDInsight クラスターの SSH ログインに置き換えます。
   * `<cluster-name>` プレースホルダーを HDInsight クラスターの名前に置き換えます。

   パスワードを使用して SSH ログインを認証する場合は、パスワードを入力するよう求められます。

   公開キーを使用している場合は、`-i` パラメーターを使用して、対応する秘密キーへのパスを指定することが必要な場合があります。 たとえば、「 `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:` 」のように入力します。

2. アップロードが完了したら、SSH を使用してクラスターに接続します。 コマンド プロンプトで次のコマンドを入力します。

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. 次のコマンドを使用して .zip ファイルを解凍します。

   ```bash
   unzip <file-name>.zip
   ```

   このコマンドにより **.csv** ファイルが抽出されます。

4. 次のコマンドを使用して、Data Lake Storage Gen2 コンテナーを作成します。

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   `<container-name>` プレースホルダーを、ご自身のコンテナーに付ける名前に置き換えます。

   `<storage-account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

5. 次のコマンドを使用して、ディレクトリを作成します。

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. 次のコマンドを使用して *.csv* ファイルをディレクトリにコピーします。

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   ファイル名にスペースや特殊文字が含まれる場合は、ファイル名を引用符で囲んでください。

## <a name="transform-the-data"></a>データの変換

このセクションでは、Beeline を使用して Apache Hive ジョブを実行します。

Apache Hive ジョブの一環として、.csv ファイルから **delays** という名前の Apache Hive テーブルにデータをインポートします。

1. HDInsight クラスター用に既に開いている SSH プロンプトから、次のコマンドを使用して **flightdelays.hql** という名前の新しいファイルを作成して編集します。

   ```bash
   nano flightdelays.hql
   ```

2. 次のテキストに変更を加えます。`<container-name>` と `<storage-account-name>` のプレースホルダーを実際のコンテナーとストレージ アカウントの名前に置き換えてください。 マウスの右ボタンをクリックしたまま Shift キーを押して、そのテキストを nano コンソールにコピーして貼り付けます。

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. Ctrl キーを押しながら X キーを押し、確認を求められたら「`Y`」と入力してファイルを保存します。

4. Hive を起動し、**flightdelays.hql** ファイルを実行するには、次のコマンドを使用します。

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. __flightdelays.hql__ スクリプトの実行が完了したら、次のコマンドを使用して対話型 Beeline セッションを開きます。

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

   このクエリにより、悪天候による遅延が発生した都市の一覧と平均遅延時間が取得され、`abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` に保存されます。 その後、Sqoop がこの場所からデータを読み取り、Azure SQL Database にエクスポートします。

7. Beeline を終了するには、プロンプトで「 `!quit` 」と入力します。

## <a name="create-a-sql-database-table"></a>SQL データベース テーブルの作成

この操作には、SQL Database のサーバー名が必要になります。 サーバー名を確認するには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。

2. **[SQL データベース]** を選択します。

3. 使用するデータベースの名前でフィルター処理します。 サーバー名は **[サーバー名]** 列に表示されます。

4. 使用するデータベースの名前でフィルター処理します。 サーバー名は **[サーバー名]** 列に表示されます。

    ![Azure SQL サーバーの詳細を取得する](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Azure SQL サーバーの詳細を取得する")

    SQL Database に接続してテーブルを作成するには、多くの方法があります。 次の手順では、HDInsight クラスターから [FreeTDS](https://www.freetds.org/) を使用します。

5. FreeTDS をインストールするには、クラスターへの SSH 接続から次のコマンドを使用します。

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. インストールが完了したら、次のコマンドを使用して SQL Database に接続します。

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * `<server-name>` プレースホルダーを論理 SQL サーバーの名前に置き換えます。

   * `<admin-login>` プレースホルダーを SQL Database の管理者ログインに置き換えます。

   * `<database-name>` プレースホルダーをデータベース名に置き換えます。

   メッセージが表示されたら、SQL Database 管理者ログインのパスワードを入力します。

   次のテキストのような出力が返されます。

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. `1>` プロンプトで、次のステートメントを入力します。

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. `GO` ステートメントを入力すると、前のステートメントが評価されます。

   このクエリにより、クラスター化インデックス付きの、**delays** という名前のテーブルが作成されます。

9. 次のクエリを使用して、テーブルが作成されたことを確認します。

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   出力は次のテキストのようになります。

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. Enter `exit` at the `1>`」と入力して、tsql ユーティリティを終了します。

## <a name="export-and-load-the-data"></a>データのエクスポートと読み込み

これまでのセクションで、変換済みデータを `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` という場所にコピーしました。 このセクションでは、Sqoop を使用して、`abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` のデータを、Azure SQL Database に作成したテーブルにエクスポートします。

1. 次のコマンドを使用して、Sqoop が SQL データベースを認識できることを確認します。

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   このコマンドにより、**delays** テーブルを作成したデータベースを含む、データベースの一覧が返されます。

2. 次のコマンドを使って、**hivesampletable** テーブルから **delays** テーブルにデータをエクスポートします。

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop は **delays** テーブルを含むデータベースに接続して、`/tutorials/flightdelays/output` ディレクトリから **delays** テーブルにデータをエクスポートします。

3. `sqoop` コマンドが完了したら、tsql ユーティリティを使用してデータベースに接続します。

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. 次のステートメントを使用して、データが **delays** テーブルにエクスポートされたことを確認します。

   ```sql
   SELECT * FROM delays
   GO
   ```

   テーブル内のデータの一覧が表示されます。 テーブルには、都市の名前と、その都市のフライトの平均遅延時間が含まれます。

5. 「`exit`」と入力して、tsql ユーティリティを終了します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで使用されるすべてのリソースは、既存のものです。 クリーンアップは必要ありません。

## <a name="next-steps"></a>次のステップ

HDInsight でのデータ操作の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)