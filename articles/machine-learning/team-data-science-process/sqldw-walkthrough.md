---
title: Azure Synapse Analytics を使用してモデルを構築してデプロイする - Team Data Science Process
description: Azure Synapse Analytics で公開されているデータセットを使用して機械学習モデルを構築してデプロイします。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93316001"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Team Data Science Process の活用: Azure Synapse Analytics の使用
このチュートリアルでは、公開されている使用可能なデータセット ([NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) データセット) で Azure Synapse Analytics を使用して、機械学習モデルを構築し、デプロイする方法を説明します。 構築される二項分類モデルでは、乗車でチップが支払われるかどうかを予測します。  モデルには、多クラス分類 (チップがあるかどうかを問わない) と回帰 (支払われたチップ金額の分布) が含まれます。

この手順は、 [Team Data Science Process (TDSP)](./index.yml) ワークフローに従っています。 データ サイエンス環境のセットアップ方法、Azure Synapse Analytics にデータを読み込む方法、Azure Synapse Analytics または IPython Notebook を使用してデータを探索し、特徴をエンジニアリングする方法について説明します。 次に、Azure Machine Learning でのモデルのビルドとデプロイ方法について説明します。

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>NYC タクシー乗車データセット
NYC タクシー乗車データは、約 20 GB の圧縮された CSV ファイル (非圧縮では最大 48 GB) で構成されており、ファイルには 1 億 7300 万以上の個々の乗車と、各乗車に対して支払われた料金が記録されています。 各乗車レコードには、乗車と降車の場所と時間、匿名化されたタクシー運転手の (運転) 免許番号、メダリオン (タクシーの一意の ID) 番号が含まれています。 データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1. **trip_data.csv** ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。 いくつかのサンプル レコードを次に示します。

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. **trip_fare.csv** ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金と税、チップ、道路などの通行料、および合計支払金額など) が含まれます。 いくつかのサンプル レコードを次に示します。

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

trip\_data と trip\_fare の結合に使用される **一意のキー** は、

* medallion、
* hack\_license、
* pickup\_datetime です。

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>3 種類の予測タスクに対応する
3 種類のモデリング タスクを説明するために、*tip\_amount* に基づく 3 つの予測の問題を編成しました。

1. **二項分類**:乗車においてチップが支払われたかどうかを予測します。つまり、*tip\_amount* が $0 より大きい場合は肯定的な例で、*tip\_amount* が $0 の場合は否定的な例です。
2. **多クラス分類**:乗車で支払われたチップの範囲を予測します。 *tip\_amount* を次の 5 つの箱つまりクラスに分割します。

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **回帰タスク**:乗車で支払われたチップの金額を予測します。

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Azure データ サイエンス環境の高度な分析のためのセット アップ
Azure データ サイエンス環境をセット アップするには、以下の手順に従います。

**独自の Azure BLOB ストレージ アカウントを作成する**

* 独自の Azure BLOB ストレージをプロビジョニングするときに、**米国中南部** 内またはその場所にできるだけ近い Azure BLOB ストレージのジオロケーションを選択します。このストレージに NYC タクシー データが格納されています。 データは、AzCopy を使用してパブリック BLOB ストレージ コンテナーから独自のストレージ アカウント内のコンテナーにコピーされます。 Azure BLOB ストレージが米国中南部に近いほど、このタスク (ステップ 4) の完了が早くなります。
* 独自の Azure Storage アカウントを作成するには、「[Azure Storage アカウントの作成](../../storage/common/storage-account-create.md)」に示されている手順に従います。 以下のストレージ アカウントの資格情報の値は必ずメモしておいてください。これらはチュートリアルの後半で必要になります。

  * **ストレージ アカウント名**
  * **ストレージ アカウント キー**
  * **コンテナー名** (Azure BLOB ストレージ内のデータの格納先)

**Azure Synapse Analytics インスタンスをプロビジョニングします。**
[Azure portal での Azure Synapse Analytics の作成とクエリの実行](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)に関するドキュメントの説明に従って、Azure Synapse Analytics インスタンスをプロビジョニングします。 後の手順で使用される次の Azure Synapse Analytics の資格情報は必ずメモしておいてください。

* **サーバー名**: \<server Name>.database.windows.net
* **SQLDW (データベース) 名**
* **ユーザー名**
* **パスワード**

**Visual Studio と SQL Server Data Tools をインストールします。** 手順については、[Azure Synapse Analytics 用の Visual Studio 2019 の概要](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md)に関するページを参照してください。

**Visual Studio で Azure Synapse Analytics に接続します。** 手順については、[Azure Synapse Analytics 内で SQL Analytics に接続する方法](../../synapse-analytics/sql/connect-overview.md)に関する記事の手順 1 と 2 を参照してください。

> [!NOTE]
> Azure Synapse Analytics で作成したデータベースに対して (接続に関するトピックの手順 3 で示されているクエリではなく) 次の SQL クエリを実行して、**マスター キーを作成します**。
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Azure サブスクリプションで Azure Machine Learning ワークスペースを作成します。** 手順については、 [Azure Machine Learning のワークスペースの作成](../classic/create-workspace.md)に関するページをご覧ください。

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Azure Synapse Analytics にデータを読み込む
Windows PowerShell コマンド コンソールを開きます。 以下の PowerShell コマンドを実行して、サンプルの SQL スクリプト ファイルを *-DestDir* パラメーターで指定したローカル ディレクトリにダウンロードします。このファイルは GitHub で共有されています。 *-DestDir* パラメーターの値は任意のローカル ディレクトリに変更できます。 *-DestDir* が存在しない場合は、PowerShell スクリプトによって作成されます。

> [!NOTE]
> **DestDir** ディレクトリに対する作成または書き込みに管理者特権が必要な場合は、以下の PowerShell スクリプトを実行するときに *管理者として実行* しなければならない場合があります。
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

正しく実行されると、現在の作業ディレクトリが *- DestDir* に変わります。 画面は次のようになります。

![現在の作業ディレクトリの変化][19]

*-DestDir* で、以下の PowerShell スクリプトを管理者モードで実行します。

```azurepowershell
./SQLDW_Data_Import.ps1
```

PowerShell スクリプトを初めて実行するときに、Azure Synapse Analytics と Azure BLOB ストレージ アカウントの情報の入力を求められます。 この PowerShell スクリプトを初めて完了した場合、入力した資格情報は現在の作業ディレクトリ内の構成ファイル SQLDW.conf に書き込まれます。 この PowerShell スクリプト ファイルの今後の実行では、この構成ファイルから必要なすべてのパラメーターを読み取ることができます。 いくつかのパラメーターを変更する必要がある場合は、構成ファイルを削除し、要求されたパラメーター値を入力してプロンプト画面でパラメーターを入力するか、 *-DestDir* ディレクトリの SQLDW.conf ファイルを編集してパラメーター値を変更できます。

> [!NOTE]
> スキーマ名が Azure Synapse Analytics に既に存在するものと競合しないように、SQLDW.conf ファイルから直接パラメーターを読み取るときに、実行ごとに既定のスキーマ名として 3 桁の乱数が SQLDW.conf ファイルのスキーマ名に追加されます。 PowerShell スクリプトから、スキーマ名の入力を求められることがあります。任意の名前を指定できます。
>
>

この **PowerShell スクリプト** ファイルで、次のタスクが完了します。

* AzCopy がまだインストールされていない場合は、**AzCopy をダウンロードしてインストールします**

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **プライベート BLOB ストレージ アカウントにデータをコピーします**

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* 以下のコマンドでは、プライベート BLOB ストレージ アカウントから、 **(LoadDataToSQLDW.sql を実行することで) Polybase を使用して Azure Synapse Analytics にデータを読み込みます**。

  * スキーマの作成

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * データベース スコープの資格情報の作成

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Azure Storage BLOB の外部データ ソースの作成

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * csv ファイルの外部ファイル形式を作成します。 データは圧縮されず、フィールドはパイプ文字で区切られます。

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Azure Blob ストレージに NYC タクシー データセットの外部料金および乗車のテーブルを作成します。

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Azure Blob ストレージの外部テーブルのデータを Azure Synapse Analytics に読み込む

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - サンプル データ テーブル (NYCTaxi_Sample) を作成し、乗車および料金テーブルで SQL クエリを選択してデータをテーブルに挿入します (このチュートリアルのいくつかの手順では、このサンプル テーブルを使用する必要があります)。

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

ストレージ アカウントの地理的な場所によって、読み込み時間は異なります。

> [!NOTE]
> プライベート BLOB ストレージ アカウントの地理的位置に応じて、パブリック BLOB からプライベート ストレージ アカウントへのデータのコピー プロセスには約 15 分 (またはそれ以上) かかる場合があります。また、ストレージ アカウントから Azure Synapse Analytics へのデータの読み込みプロセスには 20 分以上かかる場合があります。
>
>

コピー元のファイルとコピー先のファイルが重複する場合は、どのように対処するかを決定する必要があります。

> [!NOTE]
> パブリック BLOB ストレージからプライベート BLOB ストレージ アカウントにコピーする .csv ファイルが既にプライベート BLOB ストレージ アカウントに存在する場合、AzCopy によってファイルを上書きするかどうかが尋ねられます。 上書きしない場合は、確認を求めるメッセージが表示されたときに「 **n** 」と入力します。 **すべて** 上書きする場合は、確認を求めるメッセージが表示されたときに「**a**」と入力します。 「 **y** 」と入力して、.csv ファイルを個別に上書きすることもできます。
>
>

![AzCopy からの出力][21]

独自のデータを使用することができます。 実際のアプリケーションのオンプレミス マシンにデータがある場合でも、AzCopy を使用してオンプレミス データをプライベート Azure Blob Storage にアップロードできます。 アップロードするには、PowerShell スクリプト ファイルの AzCopy コマンドで、**Source** の場所 (`$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`) を、データが格納されているローカル ディレクトリに変更します。

> [!TIP]
> 実際のアプリケーションのプライベート Azure BLOB ストレージ内にデータが既にある場合は、PowerShell スクリプトでの AzCopy ステップをスキップして、直接データを Azure Synapse Analytics にアップロードできます。 この場合、データの形式に合わせてスクリプトをさらに編集する必要があります。
>
>

また、この PowerShell スクリプトは、データ探索のサンプル ファイルである SQLDW_Explorations.sql、SQLDW_Explorations.ipynb、および SQLDW_Explorations_Scripts.py に Azure Synapse Analytics の情報を取り込み、PowerShell スクリプトが完了したらすぐにこれら 3 つのファイルを試せるようにします。

正しく実行されると、画面は次のようになります。

![成功したスクリプト実行の出力][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Azure Synapse Analytics でのデータの探索と特徴エンジニアリング
このセクションでは、 **Visual Studio Data Tools** を使用して直接 Azure Synapse Analytics に対して SQL クエリを実行し、データの探索と特徴の生成を行います。 このセクションで使用されるすべての SQL クエリは、*SQLDW_Explorations.sql* という名前のサンプル スクリプトにあります。 このファイルは、PowerShell スクリプトによってローカル ディレクトリに既にダウンロードされています。 [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql)から取得することもできます。 ただし、GitHub のファイルには Azure Synapse Analytics の情報は含まれていません。

Visual Studio で、Azure Synapse Analytics ログイン名とパスワードを使用して Azure Synapse Analytics に接続し、**SQL オブジェクト エクスプローラー** を開いて、データベースとテーブルがインポートされていることを確認します。 *SQLDW_Explorations.sql* ファイルを取得します。

> [!NOTE]
> Parallel Data Warehouse (PDW) クエリ エディターを開くには、**SQL オブジェクト エクスプローラー** で PDW を選択して **[新しいクエリ]** コマンドを使用します。 標準の SQL クエリ エディターは PDW でサポートされていません。
>
>

このセクションで実行されるデータの探索と特徴の生成タスクの種類を以下に示します。

* さまざまな期間で、いくつかのフィールドのデータの分布を探索する。
* [経度] フィールドと [緯度] フィールドのデータの品質を調査する。
* **tip\_amount** に基づいて、二項分類および多クラス分類のラベルを生成する。
* 特徴を生成するとともに、乗車距離を計算または比較する。
* 2 つのテーブルを結合して、モデルのビルドに使用するランダム サンプルを抽出する。

### <a name="data-import-verification"></a>データのインポートの確認
以前、Polybase の並行一括インポートを使用してデータを入力したテーブルの行と列の数を簡単に確認するには、次のクエリを実行します。

-- テーブル スキャンなしでテーブル <nyctaxi_trip> の行数を報告する

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

-- テーブル <nyctaxi_trip> の列数を報告する

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**出力:** 173、179、759 の行と 14 の列が取得されます。

### <a name="exploration-trip-distribution-by-medallion"></a>探索:medallion (タクシー番号) ごとの乗車回数の分布
このクエリ例では、指定した期間内で乗車回数が 100 を超えた medallion (タクシー番号) を識別します。 **pickup\_datetime** のパーティション スキームによって条件が設定されるため、クエリはパーティション分割されたテーブルへのアクセスからメリットを得られます。 データセット全体に対するクエリの実行でも、パーティション テーブルまたはインデックス スキャンを活用できます。

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**出力:** クエリによって、13,369 のメダリオン (タクシー) と 2013 年に完了した乗車回数を示す行で構成されるテーブルが返されます。 最後の列には、完了した乗車回数が含まれます。

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>探索:medallion および hack_license ごとの乗車回数の分布
この例では、指定した期間内で乗車回数が 100 を超えた medallion (タクシー番号) と hack_license 番号 (運転) を識別します。

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**出力:** クエリによって、2013 年に 100 回を超える乗車を完了した 13,369 の車両/運転手 ID を示す 13,369 の行で構成されるテーブルが返されます。 最後の列には、完了した乗車回数が含まれます。

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>データ品質の評価:経度と緯度が正しくないレコードを確認する
この例では、いずれかの経度または緯度のフィールドに無効な値 (ラジアン角は -90 ～ 90 の間である必要があります)、または座標 (0, 0) のいずれかが含まれているかどうかを調査します。

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**出力:** クエリによって、経度フィールドまたは緯度フィールドが無効である 837,467 件の乗車が返されます。

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>探索:チップが支払われた乗車と支払われなかった乗車の分布
この例では、特定の期間中 (または、1 年を対象とする場合は全データセットで) チップが支払われた乗車と支払われなかった乗車の数を確認します。 この分布は、後で二項分類のモデリングで使用する二項ラベルの分布を反映しています。

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**出力:** クエリによって、次のような 2013 年のチップの頻度が返されます。チップが支払われたのが 90,447,622 件、支払われなかったのが 82,264,709 件。

### <a name="exploration-tip-classrange-distribution"></a>探索:チップのクラス/範囲の分布
この例では、特定の期間中に (または、1 年間をカバーする場合はデータセット全体で) チップの範囲の分布を計算します。 これは、後で多クラス分類のモデリングに使用されるラベル クラスの分布です。

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**出力:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>探索:乗車距離の計算と比較
この例では、pickup (乗車) と drop-off (降車) の経度と緯度を、SQL geography ポイントに変換し、SQL geography ポイントの差を使用して乗車距離を計算し、比較するためにランダムな結果のサンプルを返します。 この例では、前述したデータ品質評価のクエリを使用して、結果を有効な座標のみに限定します。

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>SQL 関数を使用する特徴エンジニア リング
特徴エンジニアリングでは、SQL 関数を使用する方が効率的な場合があります。 このチュートリアルでは、乗車場所と降車場所の直線距離を計算する SQL 関数が定義されています。 **Visual Studio Data Tools** で次の SQL スクリプトを実行できます。

距離関数を定義する SQL スクリプトを以下に示します。

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

この関数を呼び出して SQL クエリで特徴を生成する場合の例を以下に示します。

-- 機能を作成する関数を呼び出すサンプル クエリ

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**出力:** このクエリは、乗車場所と降車場所の緯度と経度、およびマイル単位の直線距離を示す (2,803,538 行で構成される) テーブルを生成します。 最初の 3 つの行の結果を次に示します。

| (行番号) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40.731804 |-74.001083 |40.736622 |-73.988953 |.7169601222 |
| 2 |40.715794 |-74,010635 |40.725338 |-74.00399 |.7448343721 |
| 3 |40.761456 |-73.999886 |40.766544 |-73.988228 |0.7037227967 |

### <a name="prepare-data-for-model-building"></a>モデルのビルド用にデータを準備する
次のクエリはテーブル **nyctaxi\_trip** と **nyctaxi\_fare** を結合して、二項分類ラベル **[tipped]** 、多クラス分類ラベル **[tip\_class]** を生成し、結合データセット全体からサンプルを抽出します。 サンプリングは、降車時間に基づいて乗車のサブセットを取得することで行われます。  その後、このクエリをコピーして [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) の[データのインポート][import-data] モジュールに直接貼り付け、Azure の SQL データベース インスタンスから直接データを取り込めます。 このクエリは、座標が正しくないレコード (0, 0) を除外します。

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

Azure Machine Learning に進む準備ができれば、次のいずれかを実行できます。

1. データを抽出してサンプリングする最終的な SQL クエリを保存し、このクエリをコピーして直接 Azure Machine Learning のデータのインポート[import-data] モジュールに貼り付けます。または、
2. 構築するモデルに使用する予定のサンプリングおよびエンジニアリング済みのデータを新しい Azure Synapse Analytics テーブルに保持し、Azure Machine Learning の[データのインポート][import-data] モジュールでその新しいテーブルを使用します。 このタスクは、前の手順の PowerShell スクリプトで既に行われています。 データのインポート モジュールでは、このテーブルから直接読み取ることできます。

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>IPython Notebook でのデータの探索と特徴エンジニアリング
このセクションでは、以前作成した Azure Synapse Analytics に対して Python と SQL の両方のクエリを使用して、データの探索と特徴の生成を行います。 **SQLDW_Explorations.ipynb** という名前のサンプルの IPython Notebook および **SQLDW_Explorations_Scripts.py** という Python スクリプト ファイルは、ローカル ディレクトリにダウンロードされています。 これらは [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)からも入手できます。 これら 2 つのファイルは、Python スクリプトでは同一です。 IPython Notebook サーバーがない場合は、Python スクリプト ファイルが提供されます。 これら 2 つのサンプルの Python ファイルは、 **Python 2.7** で設計されています。

ローカル マシンにダウンロードされたサンプルの IPython Notebook と Python スクリプト ファイルで必要な Azure Synapse Analytics の情報は、PowerShell スクリプトによって既に取り込まれています。 これらは変更しなくても実行できます。

Azure Machine Learning ワークスペースを既に設定している場合は、サンプルの IPython Notebook を AzureML IPython Notebook サービスに直接アップロードして、実行を開始できます。 AzureML IPython Notebook サービスにアップロードする手順を次に示します。

1. Azure Machine Learning ワークスペースにログインし、Web ページの上部にある **[Studio]** をクリックしてから、左側にある **[NOTEBOOKS]** をクリックします。

    ![[Studio]、[NOTEBOOKS] の順にクリックする][22]
2. Web ページの左下隅にある **[新規]** をクリックし、 **[Python 2]** を選択します。 次に、Notebook に名前を指定し、チェック マークをクリックして新しい空白の IPython Notebook を作成します。

    ![[新規] をクリックして [Python 2] を選択する][23]
3. 新しい IPython Notebook の左上隅にある "**Jupyter**" のシンボルをクリックします。

    ![Jupyter Notebook のシンボルをクリックする][24]
4. サンプルの IPython Notebook をドラッグして AzureML IPython Notebook サービスの **ツリー** ページにドロップしてから、 **[アップロード]** をクリックします。 これで、サンプルの IPython Notebook が AzureML IPython Notebook サービスにアップロードされます。

    ![[アップロード] をクリックします][25]

サンプルの IPython Notebook または Python スクリプト ファイルを実行するには、以下の Python パッケージが必要です。 AzureML IPython Notebook サービスを使用している場合、これらのパッケージはプレインストールされています。

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

大量のデータを使用して Azure Machine Learning に高度な分析ソリューションを構築する場合は、以下の順で行うことをお勧めします。

* メモリ内のデータ フレームに、データの小さなサンプルを読み込みます。
* サンプリングされたデータを使用して、視覚化と探索を行います。
* サンプリングされたデータを使用して、特徴エンジニアリングの実験を行います。
* 大規模なデータの探索、データの操作、および特徴エンジニアリングの場合は、Python を使用して Azure Synapse Analytics に対して直接 SQL クエリを実行します。
* Azure Machine Learning のモデルの構築に適したサンプルのサイズを決定します。

いくつかのデータの探索、データの視覚化、および特徴エンジニアリングの例を次に示します。 その他のデータの探索については、サンプルの IPython Notebook とサンプルの Python スクリプト ファイルを参照してください。

### <a name="initialize-database-credentials"></a>データベースの資格情報を初期化する
次の変数で、データベース接続の設定を初期化します。

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>データベース接続を作成する
データベースへの接続を作成する接続文字列を以下に示します。

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>テーブル <nyctaxi_trip> の行数と列数を報告する

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* 行数の合計 = 173179759
* 列数の合計 = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>テーブル <nyctaxi_fare> の行数と列数を報告する

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* 行数の合計 = 173179759
* 列数の合計 = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Azure Synapse Analytics データベースから小さなデータ サンプルを読み取る

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

サンプル テーブルの読み取り時間は 14.096495 秒です。
取得した行数と列数 = (1000, 21)。

### <a name="descriptive-statistics"></a>説明的な統計情報
これで、サンプリングされたデータを探索する準備ができました。 最初に **trip\_distance** (または指定するその他のフィールド) のいくつかの説明的な統計情報を確認します。

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>視覚化:ボックス プロットの例
次に、変位置を視覚化するために、乗車距離のボックス プロットを確認します。

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![ボックス プロットの出力][1]

### <a name="visualization-distribution-plot-example"></a>視覚化:配布プロットの例
サンプリングされた乗車距離に関する配布を可視化するプロットとヒストグラム。

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![配布プロットの出力][2]

### <a name="visualization-bar-and-line-plots"></a>視覚化:棒と線のプロット
この例では、乗車距離を 5 つの箱にビン分割し、ビン分割の結果を視覚化します。

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

上記の箱の分布を、次のように棒または線でプロットできます。

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![棒のプロットの出力][3]

and

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![線のプロットの出力][4]

### <a name="visualization-scatterplot-examples"></a>視覚化:散布図の例
**trip\_time\_in\_secs** と **trip\_distance** 間の散布図を表示し、相関関係があるか判断できます。

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![時間と距離の間の関係の散布図の出力][6]

同様に、**rate\_code** と **trip\_distance** のリレーションシップを確認できます。

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![コードと距離の間の関係の散布図の出力][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>IPython Notebook での SQL クエリを使用したサンプリングされたデータのデータ探索
このセクションでは、上記で作成した新しいテーブルに保持されているサンプリングされたデータを使用して、データの分布を探索します。 元のテーブルを使用して同様の探索を実行できます。

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>探索:サンプリングされたテーブルの行数と列数を報告する

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>探索:チップが支払われた乗車と支払われなかった乗車の分布

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>探索:チップのクラスの分布

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>探索:クラスごとのチップの分布をプロットする

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![プロット #26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>探索:1 日ごとの乗車の分布

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>探索:medallion (タクシー番号) ごとの乗車回数の分布

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>探索:medallion および hack_license ごとの乗車回数の分布

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>探索:乗車時間の分布

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>探索:乗車距離の分布

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>探索:支払いの種類の分布

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>特徴化されたテーブルの最終形式を検証する

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Azure Machine Learning でモデルを作成する
これで、[Azure Machine Learning](https://studio.azureml.net) でのモデルの作成とモデルのデプロイに進む準備が整いました。 データは、以前特定したどの予測の問題でも使用できる状態になりました。予測の問題とは、

1. **二項分類**:乗車に対してチップが支払われたかどうかを予測します。
2. **多クラス分類**:あらかじめ定義したクラスに従って、支払われたチップの範囲を予測します。
3. **回帰タスク**:乗車で支払われたチップの金額を予測します。

モデリングの演習を開始するには、**Azure Machine Learning (クラシック)** ワークスペースにログインします。 Machine Learning ワークスペースをまだ作成していない場合は、[Azure Machine Learning Studio (クラシック) ワークスペースの作成](../classic/create-workspace.md)に関するページを参照してください。

1. Azure Machine Learning の使用を開始するには、「[Machine Learning Studio (クラシック) とは](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)」を参照してください。
2. [Azure Machine Learning Studio (クラシック)](https://studio.azureml.net) にログインします。
3. Machine Learning Studio (クラシック) のホーム ページには、豊富な情報、ビデオ、チュートリアル、モジュール リファレンスへのリンク、その他のリソースが用意されています。 Azure Machine Learning の詳細については、[Azure Machine Learning ドキュメント センター](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

一般的なトレーニング実験は以下のステップで構成されています。

1. **+NEW** 実験を作成する
2. Azure Machine Learning Studio (クラシック) へのデータの取得。
3. 必要に応じたデータの事前処理、変換、および操作。
4. 必要に応じた特徴の生成。
5. トレーニング/検証/テスト データ セットへのデータの分割 (またはそれぞれに個別のデータセットを用意する)。
6. 解決する学習問題によって変わる、1 つ以上の機械学習アルゴリズムの選択。 たとえば、二項分類、多クラス分類、回帰です。
7. トレーニング データセットを使用した、1 つ以上のモデルのトレーニング。
8. トレーニング済みのモデルを使用した、検証データセットのスコアリング。
9. 学習問題の関連メトリックを計算するためのモデルの評価。
10. モデルの調整およびデプロイに最適なモデルの選択。

この演習では、既に Azure Synapse Analytics でデータの探索とエンジニアリングを実行し、Azure Machine Learning Studio (クラシック) に取り込むサンプルのサイズを決定しました。 1 つ以上の予測モデルを作成する手順を以下に示します。

1. **[データの入力と出力]** セクションで利用できる [データのインポート][import-data] モジュールを使用して、Azure Machine Learning Studio (クラシック) にデータを取り込みます。 詳細については、[データのインポート][import-data] モジュールのリファレンスのページをご覧ください。

    ![Azure ML データのインポート][17]
2. **[プロパティ]** パネルで、**Azure SQL Database** を **データ ソース** として選択します。
3. データベースの DNS 名を **[データベース サーバー名]** フィールドに入力します。 形式: `tcp:<your_virtual_machine_DNS_name>,1433`
4. **データベース名** を対応するフィールドに入力します。
5. <*SQL ユーザー名*> を **[Server user account name (サーバーのユーザー アカウント名)]** に、<*パスワード*> を **[Server user account password (サーバーのユーザー アカウントのパスワード)]** に入力します。
7. **データベース クエリ** テキスト編集領域で、必要なデータベース フィールド (ラベルなどの計算フィールドなど) を抽出するクエリを貼り付けてから、データを希望するサンプルのサイズにダウンサンプリングします。

Azure Synapse Analytics データベースから直接データを読み取る、二項分類の実験の例を次の図に示します (テーブル名の nyctaxi_trip と nyctaxi_fare は、必ずチュートリアルで使用したスキーマ名とテーブル名に置き換えてください)。 同様の実験を、多クラス分類と回帰問題で構築することができます。

![Azure ML トレーニング][10]

> [!IMPORTANT]
> 前のセクションに記載されたモデリング データの抽出とサンプリングのクエリの例では、 **3 つのモデリングの演習用のラベルはすべてクエリに含まれています**。 各モデリングの演習における重要な (必須の) 手順は、他の 2 つの問題用の不要なラベルと、その他のすべての **ターゲット リーク** を **除外する** ことです。 たとえば、二項分類を使用する場合は、ラベル **tipped** を使用し、フィールド **[tip\_class]** 、 **[tip\_amount]** 、 **[total\_amount]** は除外します。 使用しないものは支払われたチップを意味しているため、ターゲットのリークになります。
>
> 不要な列またはターゲット リークを除外するには、[データセット内の列の選択][select-columns]モジュールまたは[メタデータの編集][edit-metadata]を使用できます。 詳細については、[データセット内の列の選択][select-columns]と[メタデータの編集][edit-metadata]のリファレンス ページを参照してください。
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Azure Machine Learning にモデルをデプロイする
モデルの準備ができたら、実験から直接 Web サービスとして簡単にデプロイできます。 Azure ML Web サービスのデプロイの詳細については、「 [Azure Machine Learning Web サービスをデプロイする](../classic/deploy-a-machine-learning-web-service.md)」を参照してください。

新しい Web サービスをデプロイするには以下のことを実行する必要があります。

1. スコア付け実験の作成。
2. Web サービスのデプロイ。

**終了した** トレーニング実験からスコア付け実験を作成するには、下部の操作バーにある **[スコア付け実験の作成]** をクリックしてください。

![Azure スコアリング][18]

Azure Machine Learning は、トレーニング実験のコンポーネントに基づいて、スコア付け実験の作成を試みます。 特に、以下のことを実行します。

1. トレーニング済みのモデルを保存し、モデルのトレーニング モジュールを削除する。
2. 予想される入力データ スキーマを表す論理 **入力ポート** を特定する。
3. 予想される Web サービスの出力スキーマを表す論理 **出力ポート** を特定する。

スコア付け実験が作成されたら、結果を確認して、必要に応じて調整します。 一般的な調整は、入力データセットまたはクエリをラベル フィールドを除くものに置き換えることです。これらのラベル フィールドは、サービスの呼び出し時にスキーマにマップされないからです。 入力データセットまたはクエリのサイズを、入力スキーマを示すのに十分な 2、3 個のレコードまで削減することをお勧めします。 出力ポートでは、一般的に、すべての入力フィールドを除外し、[データセット内の列の選択][select-columns]モジュールを使用して、**スコアリングしたラベル** と **スコアリングした確率** のみを出力に含めます。

サンプルのスコア付け実験を次の図に示します。 デプロイできる状態になったら、下部の操作バーにある **[Web サービスの発行]** ボタンをクリックします。

![Azure ML 発行][11]

## <a name="summary"></a>まとめ
このチュートリアルで行ったことをまとめると、Azure のデータ サイエンス環境を作成し、データの取得からモデルのトレーニング、Azure Machine Learning Web サービスのデプロイメントまでを通して、Team Data Science Process を活用して大規模な公開されているデータセットを操作したことになります。

### <a name="license-information"></a>ライセンス情報
このサンプルのチュートリアルとそれに付随するスクリプトおよび IPython notebooks は、MIT ライセンスの下で Microsoft と共有されています。 詳細については、GitHub のサンプル コードのディレクトリにある LICENSE.txt ファイルを確認してください。

## <a name="references"></a>References
- [Andrés Monroy NYC Taxi Trips のダウンロード ページ](https://www.andresmh.com/nyctaxitrips/)
- [NYC のタクシー乗車データを FOIL する (Chris Whong)](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi and Limousine Commission の調査および統計](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data