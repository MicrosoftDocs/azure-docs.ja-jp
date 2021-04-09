---
title: SQL Server VM でモデルを構築し、デプロイする - Team Data Science Process
description: Azure VM で SQL Server と公開されているデータセットを使用して機械学習モデルを構築してデプロイします。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96007411"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Team Data Science Process の活用: SQL Sever の使用
このチュートリアルでは、SQL Server と公開されているデータセット ([NYC タクシー乗車](https://www.andresmh.com/nyctaxitrips/)データセット) を使って、機械学習モデルを構築してデプロイするプロセスを説明します。 ここで使用する手順は、標準的なデータ サイエンス ワークフローを踏襲しています。つまり、データの取り込みと調査、特徴エンジニアリングによる学習の円滑化を経てモデルを構築し、デプロイします。

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC タクシー乗車データセットの説明
NYC タクシー乗車データは、約 20 GB の圧縮された CSV ファイル (非圧縮では最大 48 GB) です。1 億 7300 万以上の個々の乗車と、各乗車に支払われた料金で構成されています。 各乗車レコードには、乗車と降車の場所と時間、匿名化されたタクシー (運転手の) 免許番号、および営業許可番号 (タクシーの一意の ID) が含まれています。 データには 2013 年のすべての乗車が含まれ、データは月ごとに次の 2 つのデータセットに用意されています。

1. 「trip_data」の CSV ファイルには、乗車の詳細 (乗客数、乗車地点、降車地点、乗車時間、乗車距離など) が含まれています。 いくつかのサンプル レコードを次に示します。
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. 「trip_fare」の CSV ファイルには、各乗車に対して支払われた料金の詳細 (支払いの種類、料金、追加料金と税、チップ、道路などの通行料、および合計支払金額など) が含まれます。 いくつかのサンプル レコードを次に示します。
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

trip\_data と trip\_fare を結合するための一意のキーは medallion、hack\_licence、pickup\_datetime の各フィールドで構成されています。

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>予測タスクの例
*tip\_amount* に基づく 3 つの予測問題について説明します。つまり、

* 二項分類:乗車においてチップが支払われたかどうかを予測します。つまり、*tip\_amount* が $0 より大きい場合は肯定的な例で、*tip\_amount* が $0 の場合は否定的な例です。
* 多クラス分類:乗車で支払われたチップの範囲を予測します。 *tip\_amount* を次の 5 つの箱つまりクラスに分割します。

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* 回帰タスク:乗車で支払われたチップの金額を予測します。  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Azure のデータ サイエンス環境の高度な分析のためのセット アップ
「 [環境の計画](plan-your-environment.md) 」ガイドからわかるように、Azure で NYC タクシー乗車データセットを操作するいくつかの方法があります。

* Azure BLOB でデータを操作し、Azure Machine Learning でモデリングする
* データを SQL Server データベースに読み込み、Azure Machine Learning でモデリングする

このチュートリアルでは、SQL Server へのデータの並行一括インポート、データの探索、特徴エンジニアリング、および SQL Server Management Studio と IPython Notebook を使用したダウンサンプリングについて説明します。 [サンプルのスクリプト](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)と [IPython Notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) は GitHub で共有されています。 Azure BLOB のデータを操作するサンプルの IPython Notebook は同じ場所で利用できます。

Azure のデータ サイエンス環境をセット アップするには、

1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md)
2. [Azure Machine Learning ワークスペースの作成](../classic/create-workspace.md)
3. [データ サイエンス仮想マシンをプロビジョニングする](../data-science-virtual-machine/overview.md)。この仮想マシンにより、SQL Server と IPython Notebook サーバーが用意されます。
   
   > [!NOTE]
   > サンプル スクリプトと IPython Notebook は、セットアップ プロセス中、データ サイエンス仮想マシンにダウンロードされます。 仮想マシンのインストール後スクリプトが完了すると、サンプルは仮想マシンのドキュメント ライブラリに入ります。  
   > 
   > * サンプルのスクリプト: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * サンプルの IPython Notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   where `<user_name>` は VM の Windows ログイン名です。 ここでのサンプル フォルダーは **Sample Scripts** と **Sample IPython Notebooks** です。
   > 
   > 

データセットのサイズ、データ ソースの場所、選択された Azure の対象環境に基づくと、このシナリオは「[シナリオ \#5: ローカル ファイルの大規模データセット (Azure VM の SQL Server を対象)](plan-sample-scenarios.md#largelocaltodb)」と類似しています。

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>公開されているソースからデータを取得する
公開されている場所から [NYC タクシー乗車](https://www.andresmh.com/nyctaxitrips/)データセットを取得するには、「[Azure Blob Storage との間でデータを移動する](move-azure-blob.md)」で説明するいずれかの方法を使用して、データを新しい仮想マシンにコピーします。

AzCopy を使用してデータをコピーするには

1. 仮想マシン (VM) にログインする
2. VM のデータ ディスクで新しいディレクトリを作成します (注記: VM にデータ ディスクとして付属している一時ディスクは使用しないでください)。
3. コマンド プロンプト ウィンドウで、次の Azcopy コマンド ラインを実行します。<path_to_data_folder> を、(2) で作成したデータ フォルダーに置き換えてください。

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    AzCopy が完了すると、合計 24 のZIP CSV ファイル (trip\_data に 12 個、trip\_fare に 12 個) がデータ フォルダーにあるはずです。
4. ダウンロードしたファイルを解凍します。 圧縮されていないファイルが存在するフォルダーに注意してください。 このフォルダーは <path\_to\_data\_files\> です。

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>SQL Server データベースにデータを一括インポートする
*Partitioned テーブルと Views* を使用すると、大量のデータを SQL データベースおよび後続のクエリに読み込んだり転送したりする際のパフォーマンスを向上させることができます。 このセクションでは、「 [SQL パーティション テーブルを使用したデータの並行一括インポート](parallel-load-sql-partitioned-tables.md) 」で説明する手順に従ってデータベースを新規作成し、並行してデータをパーティション分割されたテーブルに読み込みます。

1. VM にログオンした状態で、 **SQL Server Management Studio** を起動します。
2. Windows 認証を使用して接続します。
   
    ![SSMS 接続][12]
3. まだ SQL Server の認証モードの変更と SQL ログイン ユーザーの新規作成を行っていない場合は、**Sample Scripts** フォルダーにある **change\_auth.sql** という名前のファイルを開きます。 既定のユーザー名とパスワードを変更します。 ツールバーにある **[実行]** をクリックすると、スクリプトが実行されます。
   
    ![スクリプトの実行][13]
4. SQL Server の既定のデータベースとログ フォルダーを確認または変更し、新規作成したデータベースが確実にデータ ディスクに格納されるようにします。 データ ウェアハウス用に最適化された SQL Server VM イメージは、データとログ ディスクが事前構成されています。 VM にデータ ディスクが含まれていなかったため、VM のセットアップ プロセス中に新しい仮想ハードディスクを追加した場合は、既定のフォルダーを次のように変更します。
   
   * 左側のパネルで SQL Server 名を右クリックしてから、 **[プロパティ]** をクリックします。
     
       ![SQL Server プロパティ][14]
   * 左側の **[ページの選択]** リストから、 **[データベース設定]** を選択します。
   * **[データベースの既定の場所]** が選択した **データ ディスク** の場所になっているか確認し、なっていなければ変更します。 この場所は、既定の設定で作成した場合に新しいデータベースが存在する場所です。
     
       ![SQL Database 既定値][15]  
5. 新しいデータベースとファイルグループのセットを作成してパーティション分割されたテーブルを保持するには、サンプルのスクリプト **create\_db\_default.sql** を開きます。 スクリプトは、既定のデータの場所に **TaxiNYC** という名前の新しいデータベースと 12 のファイルグループを作成します。 各ファイルグループは、1 か月分の trip\_data と trip\_fare data を保持します。 必要な場合は、データベース名を変更します。 スクリプトを実行するには、 **[実行]** をクリックします。
6. 次に、2 つのパーティション テーブルを作成します。1 つは trip\_data 用に、もう 1 つは trip\_fare 用です。 サンプルのスクリプト **create\_partitioned\_table.sql** を開きます。このスクリプトは、
   
   * 月単位でデータを分割するパーティション関数を作成します。
   * 各月のデータを別のファイルグループにマップするパーティション スキームを作成します。
   * パーティション スキームにマッピングされたパーティション テーブルを 2 つ作成します。**nyctaxi\_trip** は trip\_data を保持し、**nyctaxi\_fare** は trip\_fare を保持します。
     
     **[実行]** をクリックしてスクリプトを実行し、パーティション分割されたテーブルを作成します。
7. **Sample Scripts** フォルダーには、SQL Server テーブルへのデータの並行一括インポートのデモを行うために用意された 2 つのサンプル PowerShell スクリプトがあります。
   
   * **bcp\_parallel\_generic.ps1** は、テーブルにデータを並行一括インポートする汎用スクリプトです。 このスクリプトを変更し、スクリプト内のコメント行に示されているとおりに入力変数とターゲット変数を設定します。
   * **bcp\_parallel\_nyctaxi.ps1** は汎用スクリプトの構成済みのバージョンであり、NYC タクシー乗車データの両方のテーブルを読み込むために使用できます。  
8. スクリプト名 **bcp\_parallel\_nyctaxi.ps1** を右クリックしてから、 **[編集]** をクリックして PowerShell で開きます。 事前設定された変数を確認し、選択したデータベース名、入力データ フォルダー、対象のログ フォルダー、およびサンプルのフォーマット ファイル **nyctaxi_trip.xml** と **nyctaxi\_fare.xml** (**Sample Scripts** フォルダーにあります) へのパスに合わせて変更します。
   
    ![データの一括インポート][16]
   
    また、認証モードを選択することもできます。既定は Windows 認証です。 実行するには、ツールバーの緑色の矢印をクリックします。 スクリプトは、24 の一括インポート操作を並行して開始します。パーティション分割されたテーブルごとに 12 の操作を開始します。 上記でセットされた SQL Server の既定のデータ フォルダーを開いて、データ インポートの進行状況を監視できます。
9. PowerShell スクリプトは、開始時刻と終了時刻を報告します。 一括インポートがすべて完了すると、終了時刻が報告されます。 対象のログ フォルダーをチェックして、一括インポートが成功したこと、つまり、対象のログ フォルダーにエラー報告がされていないことを確認します。
10. これで、データベースは、探索、特徴エンジニア リング、および必要に応じてその他の操作を行える状態になりました。 テーブルは **[pickup\_datetime]** フィールドに従ってパーティション分割されるので、**WHERE** 句に条件 **pickup\_datetime** が含まれるクエリは、パーティション スキームからメリットを得られます。
11. **SQL Server Management Studio** で、提供されたサンプルのスクリプト **sample\_queries.sql** を探索します。 サンプル クエリのいずれかを実行するには、クエリ行を強調表示してから、ツールバーの **[実行]** をクリックします。
12. NYC タクシー乗車データは、2 つの個別のテーブルに読み込まれます。 結合操作を向上させるため、テーブルのインデックスを作成することを強くお勧めします。 サンプルのスクリプト **create\_partitioned\_index.sql** は、パーティション分割されたインデックスを複合結合キー (**medallion、hack\_license、pickup\_datetime**) に作成します。

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>SQL Server でのデータの探索と特徴エンジニアリング
このセクションでは、以前作成した SQL Server データベースを使用して、 **SQL Server Management Studio** で直接 SQL クエリを実行することで、データの探索および特徴の生成を行います。 **sample\_queries.sql** という名前のサンプルのスクリプトが、**Sample Scripts** フォルダーに用意されています。 データベース名が既定の名前 (**TaxiNYC**) と異なる場合は、スクリプトのデータベース名を変更します。

この演習では、以下のことを実行します。

* Windows 認証を使用するか、SQL 認証で SQL ログイン名およびパスワードを使用して、 **SQL Server Management Studio** に接続します。
* さまざまな期間で、いくつかのフィールドのデータの分布を探索する。
* [経度] フィールドと [緯度] フィールドのデータの品質を調査する。
* **tip\_amount** に基づいて、二項分類および多クラス分類のラベルを生成する。
* 特徴を生成するとともに、乗車距離を計算または比較する。
* 2 つのテーブルを結合して、モデルのビルドに使用するランダム サンプルを抽出する。

Azure Machine Learning に進む準備ができれば、次のいずれかを実行できます。  

1. データを抽出してサンプリングする最終的な SQL クエリを保存し、このクエリをコピーして直接 Azure Machine Learning の[データのインポート][import-data] モジュールに貼り付けます。または、
2. モデル作成に使用する予定のサンプリング データとエンジニア リング データを新しいデータベース テーブルに保持し、Azure Machine Learning の[データのインポート][import-data] モジュールでこの新しいテーブルを使用します。

このセクションでは、最終的なクエリを保存してから、データの抽出とサンプリングを実行します。 2 番目の方法は、「 [IPython Notebook でのデータの探索と特徴エンジニアリング](#ipnb) 」セクションで説明しています。

以前、並行一括インポートを使用してデータを入力したテーブルの行と列の数を簡単に確認するには、次を実行します。

- テーブル スキャンなしでテーブル nyctaxi_trip の行数を報告する: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- テーブル nyctaxi_trip の列数を報告する: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>探索:medallion (タクシー番号) ごとの乗車回数の分布
この例では、指定した期間内で乗車回数が 100 を超える medallion (タクシー番号) を識別します。 **pickup\_datetime** のパーティション スキームによって条件が設定されるため、クエリはパーティション分割されたテーブルへのアクセスからメリットを得られます。 データセット全体に対するクエリの実行でも、パーティション テーブルまたはインデックス スキャンを活用できます。

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>探索:medallion および hack_license ごとの乗車回数の分布

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>データ品質の評価: 経度と緯度が正しくないレコードを確認する
この例では、いずれかの経度または緯度のフィールドに無効な値 (ラジアン角は -90 ～ 90 の間である必要があります)、または座標 (0, 0) のいずれかが含まれているかどうかを調査します。

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>探索:チップが支払われた乗車と支払われなかった乗車の分布
この例では、特定の期間中 (または、1 年間をカバーする場合はデータセット全体で) チップが支払われた乗車と支払われなかった乗車の数を確認します。 この分布は、後で二項分類のモデリングで使用する二項ラベルの分布を反映しています。

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>探索:チップのクラス/範囲の分布
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
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>探索:乗車距離の計算と比較
この例では、pickup (乗車) と drop-off (降車) の経度と緯度を、SQL geography ポイントに変換し、SQL geography ポイントの差を使用して乗車距離を計算し、比較するためにランダムな結果のサンプルを返します。 この例では、前述したデータ品質評価のクエリを使用して、結果を有効な座標のみに限定します。

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>SQL クエリでの特徴エンジニアリング
ラベルの生成と geography 変換探索クエリは、カウントする部分を削除してラベルや特徴を生成することにも使用できます。 その他の特徴エンジニアリングの SQL の例は、「 [IPython Notebook でのデータの探索と特徴エンジニアリング](#ipnb) 」セクションにあります。 SQL Server データベースのインスタンスで直接実行する SQL クエリを使用して、データセット全体または大規模なサブセットで特徴生成クエリを実行するとより効率的です。 クエリは **SQL Server Management Studio**、IPython Notebook、データベースにローカルやリモートにアクセスできるいずれかの開発ツールまたは環境で実行します。

#### <a name="preparing-data-for-model-building"></a>モデル作成用にデータを準備する
次のクエリはテーブル **nyctaxi\_trip** と **nyctaxi\_fare** を結合して、二項分類ラベル **[tipped]** 、多クラス分類ラベル **[tip\_class]** を生成し、結合データセット全体から 1% のランダム サンプルを抽出します。 このクエリをコピーして [Azure Machine Learning Studio](https://studio.azureml.net) の[データのインポート][import-data] モジュールに直接貼り付け、Azure の SQL Server データベース インスタンスから直接データ インジェストを行うことができます。 このクエリは、座標が正しくないレコード (0, 0) を除外します。

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>IPython Notebook でのデータの探索と特徴エンジニアリング
このセクションでは、以前作成した SQL Server データベースに対して Python クエリと SQL クエリの両方を実行し、データの探索と特徴の生成を行います。 **machine-Learning-data-science-process-sql-story.ipynb** という名前のサンプルの IPython Notebooks が、**Sample IPython Notebooks** フォルダーに用意されています。 このノートブックは [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)からも入手できます。

ビッグ データを使用する場合は、次の推奨される手順に従ってください。

* メモリ内のデータ フレームに、データの小さなサンプルを読み込みます。
* サンプリングされたデータを使用して、視覚化と探索を行います。
* サンプリングされたデータを使用して、特徴エンジニアリングの実験を行います。
* 大規模なデータの探索、データの操作、および特徴エンジニアリングの場合は、Python を使用して Azure VM の SQL Server データベースに対して直接 SQL クエリを実行します。
* Azure Machine Learning のモデルの構築に使用するサンプルのサイズを決定します。

Azure Machine Learning に進む準備ができたら、次のいずれかを実行します。  

1. データを抽出してサンプリングする最終的な SQL クエリを保存し、そのクエリをコピーして、直接 Azure Machine Learning の[データのインポート][import-data] モジュールに貼り付けます。 この方法は、「 [Azure Machine Learning でのモデルの作成](#mlmodel) 」セクションで説明しています。    
2. モデル作成に使用する予定のサンプリング データとエンジニア リング データを新しいデータベース テーブルに保持し、[データのインポート][import-data] モジュールでこの新しいテーブルを使用します。

いくつかのデータの探索、データの視覚化、および特徴エンジニアリングの例を次に示します。 その他の例については、 **Sample IPython Notebooks** フォルダーにあるサンプルの SQL IPython Notebook を参照してください。

#### <a name="initialize-database-credentials"></a>データベースの資格情報を初期化する
次の変数で、データベース接続の設定を初期化します。

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>データベース接続を作成する

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>テーブル nyctaxi_trip の行数と列数を報告する

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* 行数の合計 = 173179759  
* 列数の合計 = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>SQL Server データベースから小規模なサンプルのデータを読み込む

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

サンプルのテーブルの読み取り時間は 6.492000 秒です。  
取得した行数と列数 = (84952, 21)

#### <a name="descriptive-statistics"></a>説明的な統計情報
サンプリングされたデータを探索する準備ができました。 最初に **trip\_distance** (またはその他のフィールド) の説明的な統計を確認します。

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>視覚化:ボックス プロットの例
次に、変位置を視覚化するために、乗車距離のボックス プロットを確認します

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![プロット #1][1]

#### <a name="visualization-distribution-plot-example"></a>視覚化:配布プロットの例

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![プロット #2][2]

#### <a name="visualization-bar-and-line-plots"></a>視覚化:棒と線のプロット
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

![プロット #3][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![プロット #4][4]

#### <a name="visualization-scatterplot-example"></a>視覚化:散布図の例
**trip\_time\_in\_secs** と **trip\_distance** 間の散布図を表示し、相関関係があるか判断できます。

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![プロット #6][6]

同様に、**rate\_code** と **trip\_distance** のリレーションシップを確認できます。

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![プロット #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>SQL でのデータのサブサンプリング
[Azure Machine Learning Studio](https://studio.azureml.net) でモデル作成用のデータを準備する際、**SQL クエリをデータのインポート モジュールで直接使用する** か、エンジニアリングとサンプリングが行われたデータを新しいテーブルで保持するかを決定できます。新しいテーブルは、簡単な **SELECT * FROM <your\_new\_table\_name>** によって [データのインポート][import-data] モジュールで使用できます。

このセクションでは、サンプリング データとエンジニアリング データを保持するためにテーブルを新規作成します。 モデルを構築するための直接的な SQL クエリの例は、「 [SQL Server でのデータの探索と特徴エンジニアリング](#dbexplore) 」セクションに記載されています。

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>サンプルのテーブルを作成し、結合されたテーブルの 1% を入力します。 存在する場合は最初にテーブルを削除します。
このセクションでは、テーブルの **nyctaxi\_trip** と **nyctaxi\_fare** を結合し、1% のランダム サンプルを抽出して、**nyctaxi\_one\_percent** という名前の新しいテーブルにサンプリングされたデータを保持します。

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>IPython Notebook での SQL クエリを使用したデータの探索
このセクションでは、上記で作成した新しいテーブルに保持されている 1% のサンプリングされたデータを使用して、データの分布を探索します。 元のテーブルを使用して、オプションで探索のサンプルを制限する **TABLESAMPLE** を使用し、または **pickup\_datetime** パーティションを使用して結果を指定した期間に限定して同様の探索を行うこともできます。これは、「[SQL サーバーでのデータの探索と特徴エンジニアリング](#dbexplore)」セクションで説明しています。

#### <a name="exploration-daily-distribution-of-trips"></a>探索:1 日ごとの乗車の分布

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>探索:medallion (タクシー番号) ごとの乗車回数の分布

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>IPython Notebook での SQL クエリを使用した特徴の生成
このセクションでは、SQL クエリを使って、前のセクションで作成した 1% のサンプルのテーブルを操作して、新しいラベルと特徴を生成します。

#### <a name="label-generation-generate-class-labels"></a>ラベルの生成: クラスのラベルを生成する
次の例では、モデリング用に 2 セットのラベルを生成します。

1. 二項ラベル **tipped** (チップが支払われるかどうか予測する)
2. 多クラス ラベル **tip\_class** (チップの範囲を予測する)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>特徴エンジニアリング: カテゴリ列の特徴をカウントする
この例では、各カテゴリをデータの出現回数に置き換えて、カテゴリ フィールドを数値フィールドに変換します。

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>特徴エンジニアリング: 数値型列のビン分割特徴
この例では、連続する数値フィールドを事前設定されたカテゴリの範囲に変換します。つまり、数値フィールドをカテゴリのフィールドに変換します。

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>特徴エンジニアリング: 10 進数の緯度と経度から抽出する場所特徴
この例では、"緯度" フィールドや "経度" フィールドの 10 進数表記を、国または地域、都市、町、ブロックなどの異なる粒度に細分化します。新しい geo フィールドは実際の場所にマップされていません。 Geocode の場所のマッピングの詳細については、「[Bing マップの REST サービス](/bingmaps/rest-services/locations/find-a-location-by-point)」を参照してください。

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>特徴化されたテーブルの最終形式を検証する

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

これで、[Azure Machine Learning](https://studio.azureml.net) でのモデルの作成とモデルのデプロイに進む準備が整いました。 データは、以前特定したどの予測の問題でも使用できる状態になりました。予測の問題とは、

1. 二項分類:乗車に対してチップが支払われたかどうかを予測します。
2. 多クラス分類:あらかじめ定義したクラスに従って、支払われたチップの範囲を予測します。
3. 回帰タスク:乗車で支払われたチップの金額を予測します。  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Azure Machine Learning でのモデルの作成
モデリングの演習を開始するには、Azure Machine Learning ワークスペースにログインします。 Machine Learning ワークスペースをまだ作成していない場合は、 [Azure Machine Learning ワークスペースの作成](../classic/create-workspace.md)に関する記事をご覧ください。

1. Azure Machine Learning の使用を開始するには、「 [Azure Machine Learning Studio とは](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. [Azure Machine Learning Studio](https://studio.azureml.net)にログインします。
3. Studio のホーム ページには、豊富な情報、ビデオ、チュートリアル、モジュール リファレンスへのリンク、およびその他のリソースが用意されています。 Azure Machine Learning の詳細については、 [Azure Machine Learning ドキュメント センター](https://azure.microsoft.com/documentation/services/machine-learning/)をご覧ください。

一般的なトレーニング実験は以下のステップで構成されています。

1. **+NEW** 実験を作成する
2. Azure Machine Learning へのデータの取得。
3. 必要に応じたデータの事前処理、変換、および操作。
4. 必要に応じた特徴の生成。
5. トレーニング/検証/テスト データ セットへのデータの分割 (またはそれぞれに個別のデータセットを用意する)。
6. 解決する学習問題によって変わる、1 つ以上の機械学習アルゴリズムの選択。 たとえば、二項分類、多クラス分類、回帰です。
7. トレーニング データセットを使用した、1 つ以上のモデルのトレーニング。
8. トレーニング済みのモデルを使用した、検証データセットのスコアリング。
9. 学習問題の関連メトリックを計算するためのモデルの評価。
10. モデルの調整およびデプロイに最適なモデルの選択。

この演習では、SQL Server でデータの探索とエンジニアリングを既に実行し、Azure Machine Learning に取り込むサンプルのサイズを決定しました。 決定した 1 つ以上の予測モデルを作成するには、

1. **[データの入力と出力]** セクションにある [[データのインポート]][import-data] モジュール を使用して、Azure Machine Learning にデータを取り込みます。 詳細については、[データのインポート][import-data] モジュールのリファレンス ページを参照してください。
   
    ![Azure Machine Learning の [データのインポート]][17]
2. **[プロパティ]** パネルで、**Azure SQL Database** を **データ ソース** として選択します。
3. データベースの DNS 名を **[データベース サーバー名]** フィールドに入力します。 形式: `tcp:<your_virtual_machine_DNS_name>,1433`
4. **データベース名** を対応するフィールドに入力します。
5. <**SQL ユーザー名**> を **[Server user account name (サーバーのユーザー アカウント名)]** に、<**パスワード**> を **[Server user account password (サーバーのユーザー アカウントのパスワード)]** に入力します。
7. **データベース クエリ** テキスト編集領域で、必要なデータベース フィールド (ラベルなどの計算フィールドなど) を抽出するクエリを貼り付けてから、データを希望するサンプルのサイズにダウンサンプリングします。

SQL Server データベースから直接データを読み取る、二項分類の実験の例を次の図に示します。 同様の実験を、多クラス分類と回帰問題で構築することができます。

![Azure Machine Learning のトレーニング][10]

> [!IMPORTANT]
> 前のセクションに記載されたモデリング データの抽出とサンプリングのクエリの例では、 **3 つのモデリングの演習用のラベルはすべてクエリに含まれています**。 各モデリングの演習における重要な (必須の) 手順は、他の 2 つの問題用の不要なラベルと、その他のすべての **ターゲット リーク** を **除外する** ことです。 たとえば、二項分類を使用する場合は、ラベル **tipped** を使用し、フィールド **[tip\_class]** 、 **[tip\_amount]** 、 **[total\_amount]** は除外します。 使用しないものは支払われたチップを意味しているため、ターゲットのリークになります。
> 
> 不要な列またはターゲット リークを除外するには、[データセット内の列の選択][select-columns]モジュールまたは[メタデータの編集][edit-metadata]を使用できます。 詳細については、[データセット内の列の選択][select-columns]と[メタデータの編集][edit-metadata]のリファレンス ページを参照してください。
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Azure Machine Learning にモデルを配置する
モデルの準備ができたら、実験から直接 Web サービスとして簡単にデプロイできます。 Azure Machine Learning Web サービスのデプロイの詳細については、「 [Azure Machine Learning Web サービスをデプロイする](../classic/deploy-a-machine-learning-web-service.md)」をご覧ください。

新しい Web サービスをデプロイするには以下のことを実行する必要があります。

1. スコア付け実験の作成。
2. Web サービスのデプロイ。

**終了した** トレーニング実験からスコア付け実験を作成するには、下部の操作バーにある **[スコア付け実験の作成]** をクリックしてください。

![Azure スコアリング][18]

Azure Machine Learning は、トレーニング実験のコンポーネントに基づいて、スコア付け実験の作成を試みます。 特に、以下のことを実行します。

1. トレーニング済みのモデルを保存し、モデルのトレーニング モジュールを削除する。
2. 予想される入力データ スキーマを表す論理 **入力ポート** を特定する。
3. 予想される Web サービスの出力スキーマを表す論理 **出力ポート** を特定する。

スコア付け実験が作成されたら、それを確認し、必要に応じて調整します。 一般的な調整は、入力データセットやクエリをラベル フィールドを除くものに置き換えることです。これらのラベルは、サービスの呼び出し時にスキーマで使用できないためです。 入力データセットまたはクエリのサイズを、入力スキーマを示すのに十分な 2、3 個のレコードまで削減することをお勧めします。 出力ポートでは、一般的に、すべての入力フィールドを除外し、[データセット内の列の選択][select-columns]モジュールを使用して、**スコアリングしたラベル** と **スコアリングした確率** のみを出力に含めます。

サンプルのスコア付け実験を次の図に示します。 デプロイできる状態になったら、下部の操作バーにある **[Web サービスの発行]** ボタンをクリックします。

![Azure Machine Learning の発行][11]

要点をまとめると、このチュートリアルでは、Azure のデータ サイエンス環境を作成し、データの取得からモデルのトレーニング、Azure Machine Learning Web サービスのデプロイまでを通して、大規模な公開されているデータセットを操作しました。

### <a name="license-information"></a>ライセンス情報
このサンプルのチュートリアルとそれに付随するスクリプトおよび IPython notebooks は、MIT ライセンスの下で Microsoft と共有されています。 詳細については、GitHub のサンプル コードのディレクトリにある LICENSE.txt ファイルを確認してください。

### <a name="references"></a>References
•    [Andrés Monroy NYC タクシー乗車データ ダウンロード ページ](https://www.andresmh.com/nyctaxitrips/)  
•    [NYC のタクシー乗車データを FOIL する (Chris Whong)](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [ニューヨーク市タクシー&リムジン委員会調査および統計](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data