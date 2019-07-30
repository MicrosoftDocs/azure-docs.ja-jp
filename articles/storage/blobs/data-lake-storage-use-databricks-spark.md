---
title: チュートリアル:Spark を使用して Azure Databricks で Azure Data Lake Storage Gen2 のデータにアクセスする | Microsoft Docs
description: このチュートリアルでは、Azure Databricks クラスター上で Spark クエリを実行して、Azure Data Lake Storage Gen2 ストレージ アカウント内のデータにアクセスする方法を説明します。
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: a745ade33e5d9c493fed187bbd9a4309e1a2d0ff
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360105"
---
# <a name="tutorial-access-data-lake-storage-gen2-data-with-azure-databricks-using-spark"></a>チュートリアル:Spark を使用して Azure Databricks で Data Lake Storage Gen2 のデータにアクセスする

このチュートリアルでは、Azure Data Lake Storage Gen2 対応の Azure ストレージ アカウント内の格納データに Azure Databricks クラスターを接続する方法を説明します。 この接続を使用することで、必要なデータに関するクエリや分析をクラスターからネイティブに実行することができます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Databricks クラスターを作成する
> * 非構造化データをストレージ アカウントに取り込む
> * Blob Storage 内のデータに対して分析を実行する

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

* Azure Data Lake Storage Gen2 アカウントを作成します。

  「[Azure Data Lake Storage Gen2 アカウントを作成する](data-lake-storage-quickstart-create-account.md)」を参照してください。

* ユーザー アカウントに[ストレージ BLOB データ共同作成者ロール](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)が割り当てられていることを確認します。

* AzCopy v10 をインストールします。 [AzCopy v10 を使用したデータ転送](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

* サービス プリンシパルを作成する。 「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)」のガイダンスに従って、サービス プリンシパルを作成します。

  この記事の手順を実行する際に、いくつかの特定の作業を行う必要があります。

  :heavy_check_mark:記事の「[アプリケーションをロールに割り当てる](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)」セクションの手順を実行するときに、必ず**ストレージ BLOB データ共同作成者**ロールをサービス プリンシパルに割り当ててください。

  > [!IMPORTANT]
  > Data Lake Storage Gen2 ストレージ アカウントの範囲内のロールを割り当てるようにしてください。 親リソース グループまたはサブスクリプションにロールを割り当てることはできますが、それらのロール割り当てがストレージ アカウントに伝達されるまで、アクセス許可関連のエラーが発生します。

  :heavy_check_mark:記事の「[サインインするための値を取得する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)」セクションの手順を行うときは、テナント ID、アプリ ID、およびパスワードの値をテキスト ファイルに貼り付けてください。 これらはすぐに必要になります。

### <a name="download-the-flight-data"></a>フライト データのダウンロード

このチュートリアルでは、運輸統計局からのフライト データを使用して ETL 操作を実行する方法を示します。 チュートリアルを完了するには、このデータをダウンロードする必要があります。

1. [米国運輸省研究・革新技術庁/運輸統計局](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)のページに移動します。

2. **[Prezipped file]\(事前に圧縮されたファイル\)** チェックボックスをオンにして、すべてのデータ フィールドを選択します。

3. **[ダウンロード]** ボタンを選択して、ご使用のコンピューターに結果を保存します。 

4. ZIP ファイルの内容を解凍し、ファイル名とファイル パスをメモします。 この情報は後の手順で必要になります。

## <a name="create-an-azure-databricks-service"></a>Azure Databricks サービスを作成する

このセクションでは、Azure portal を使用して Azure Databricks サービスを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/data-lake-storage-use-databricks-spark/azure-databricks-on-portal.png "Azure Portal の Databricks")

2. **[Azure Databricks サービス]** で次の値を指定して、Databricks サービスを作成します。

    |プロパティ  |説明  |
    |---------|---------|
    |**ワークスペース名**     | Databricks ワークスペースの名前を指定します。  |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**Location**     | **[米国西部 2]** を選択します。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。       |
    |**価格レベル**     |  **[Standard]** を選択します。     |

    ![Azure Databricks ワークスペースを作成する](./media/data-lake-storage-use-databricks-spark/create-databricks-workspace.png "Azure Databricks サービスを作成する")

3. アカウントの作成には数分かかります。 操作の状態を監視するには、上部の進行状況バーを確認します。

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、 **[作成]** を選択します。

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Azure Databricks で Spark クラスターを作成する

1. Azure portal で、作成した Databricks サービスに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** を選択します。

    ![Azure の Databricks](./media/data-lake-storage-use-databricks-spark/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/data-lake-storage-use-databricks-spark/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

4. 次のフィールドに値を入力し、他のフィールドの既定値はそのまま使用します。

    * クラスターの名前を入力します。

    * この記事では、**5.1** ランタイムを使用してクラスターを作成します。

    * **[Terminate after \_\_ minutes of inactivity]\(アクティビティが \_\_ 分ない場合は終了する\)** チェック ボックスを必ずオンにします。 クラスターが使われていない場合は、クラスターを終了するまでの時間 (分単位) を指定します。

    * **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

## <a name="ingest-data"></a>データの取り込み

### <a name="copy-source-data-into-the-storage-account"></a>ソース データをストレージ アカウントにコピーする

AzCopy を使用して *.csv* ファイルから Data Lake Storage Gen2 アカウントにデータをコピーします。

1. コマンド プロンプト ウィンドウを開き、次のコマンドを入力してストレージ アカウントにログインします。

   ```bash
   azcopy login
   ```

   コマンド プロンプト ウィンドウに表示される指示に従って、ユーザー アカウントを認証します。

2. *.csv* アカウントからデータをコピーするには、次のコマンドを入力します。

   ```bash
   azcopy cp "<csv-folder-path>" https://<storage-account-name>.dfs.core.windows.net/<file-system-name>/folder1/On_Time.csv
   ```

   * プレースホルダー `<csv-folder-path>` の値は、 *.csv* ファイルへのパスに置き換えます。

   * `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

   * `<file-system-name>` プレースホルダーを、ファイル システムに付ける任意の名前に置き換えます。

## <a name="create-a-file-system-and-mount-it"></a>ファイル システムを作成してマウントする

このセクションでは、ストレージ アカウントにファイル システムとフォルダーを作成します。

1. [Azure portal](https://portal.azure.com) で、作成した Azure Databricks サービスに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. 左側の **[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンで、 **[作成]**  >  **[ノートブック]** の順に選択します。

    ![Databricks でノートブックを作成する](./media/data-lake-storage-use-databricks-spark/databricks-create-notebook.png "Databricks でノートブックを作成する")

3. **[ノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。 言語として **[Python]** を選んで、前に作成した Spark クラスターを選びます。

4. **作成** を選択します。

5. 次のコード ブロックをコピーして最初のセルに貼り付けます。ただし、このコードはまだ実行しないでください。

    ```Python
    configs = {"fs.azure.account.auth.type": "OAuth",
           "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
           "fs.azure.account.oauth2.client.id": "<appId>",
           "fs.azure.account.oauth2.client.secret": "<password>",
           "fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<tenant>/oauth2/token",
           "fs.azure.createRemoteFileSystemDuringInitialization": "true"}

    dbutils.fs.mount(
    source = "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/folder1",
    mount_point = "/mnt/flightdata",
    extra_configs = configs)
    ```

18. このコード ブロックでは、`appId`、`password`、`tenant`、および `storage-account-name` のプレースホルダー値を、このチュートリアルの前提条件の実行中に収集した値で置き換えます。 `file-system-name` プレースホルダーの値は、前の手順で ADLS ファイル システムに付けた名前に置き換えます。

これらの値で、説明したプレース ホルダーを置き換えます。

   * `appId` および `password` は、サービス プリンシパルの作成の一環として Active Directory に登録したアプリのものです。

   * `tenant-id` は、自分のサブスクリプションのものです。

   * `storage-account-name` は、Azure Data Lake Storage Gen2 ストレージ アカウントの名前です。

   * `file-system-name` プレースホルダーを、ファイル システムに付ける任意の名前に置き換えます。

   > [!NOTE]
   > 運用設定では、パスワードを Azure Databricks に格納することを検討してください。 次に、パスワードではなくルック アップ キーをコード ブロックに追加します。 このクイック スタートの完了後、Azure Databricks Web サイトの記事「[Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)」で、このアプローチの例を参照してください。

19. **Shift + Enter** キーを押して、このブロック内のコードを実行します。

   このノートブックは開いたままにしておいてください。後でコマンドを追加します。

### <a name="use-databricks-notebook-to-convert-csv-to-parquet"></a>Databricks Notebook を使用して CSV を Parquet に変換する

前もって作成しておいたノートブックに新しいセルを追加し、そこに次のコードを貼り付けます。 

```python
# Use the previously established DBFS mount point to read the data.
# create a data frame to read data.

flightDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/*.csv")

# read the airline csv file and write the output to parquet format for easy query.
flightDF.write.mode("append").parquet("/mnt/flightdata/parquet/flights")
print("Done")
```

## <a name="explore-data"></a>データを調査する

AzCopy 経由でアップロードされた CSV ファイルの一覧を取得するために、次のコードを新しいセルに貼り付けます。

```python
import os.path
import IPython
from pyspark.sql import SQLContext
display(dbutils.fs.ls("/mnt/flightdata"))
```

新しいファイルを作成して *parquet/flights* フォルダー内のファイルの一覧を作成するには、次のスクリプトを実行します。

```python
dbutils.fs.put("/mnt/flightdata/1.txt", "Hello, World!", True)
dbutils.fs.ls("/mnt/flightdata/parquet/flights")
```

上記のコード サンプルでは、Data Lake Storage Gen2 対応のストレージ アカウントに格納されたデータを使って HDFS の階層的な性質を調査しました。

## <a name="query-the-data"></a>データを照会する

これで、ストレージ アカウントにアップロードしたデータの照会を開始できます。 次のコード ブロックをそれぞれ **[Cmd 1]** に入力し、**Cmd を押しながら Enter** キーを押して Python スクリプトを実行します。

データ ソースのデータフレームを作成するには、次のスクリプトを実行します。

* プレースホルダー `<csv-folder-path>` の値は、 *.csv* ファイルへのパスに置き換えます。

```python
# Copy this into a Cmd cell in your notebook.
acDF = spark.read.format('csv').options(
    header='true', inferschema='true').load("/mnt/flightdata/On_Time.csv")
acDF.write.parquet('/mnt/flightdata/parquet/airlinecodes')

# read the existing parquet file for the flights database that was created earlier
flightDF = spark.read.format('parquet').options(
    header='true', inferschema='true').load("/mnt/flightdata/parquet/flights")

# print the schema of the dataframes
acDF.printSchema()
flightDF.printSchema()

# print the flight database size
print("Number of flights in the database: ", flightDF.count())

# show the first 20 rows (20 is the default)
# to show the first n rows, run: df.show(n)
acDF.show(100, False)
flightDF.show(20, False)

# Display to run visualizations
# preferably run this in a separate cmd cell
display(flightDF)
```

いくつかの基本的な分析クエリをデータに対して実行するために、次のスクリプトを入力します。

```python
# Run each of these queries, preferably in a separate cmd cell for separate analysis
# create a temporary sql view for querying flight information
FlightTable = spark.read.parquet('/mnt/flightdata/parquet/flights')
FlightTable.createOrReplaceTempView('FlightTable')

# create a temporary sql view for querying airline code information
AirlineCodes = spark.read.parquet('/mnt/flightdata/parquet/airlinecodes')
AirlineCodes.createOrReplaceTempView('AirlineCodes')

# using spark sql, query the parquet file to return total flights in January and February 2016
out1 = spark.sql("SELECT * FROM FlightTable WHERE Month=1 and Year= 2016")
NumJan2016Flights = out1.count()
out2 = spark.sql("SELECT * FROM FlightTable WHERE Month=2 and Year= 2016")
NumFeb2016Flights = out2.count()
print("Jan 2016: ", NumJan2016Flights, " Feb 2016: ", NumFeb2016Flights)
Total = NumJan2016Flights+NumFeb2016Flights
print("Total flights combined: ", Total)

# List out all the airports in Texas
out = spark.sql(
    "SELECT distinct(OriginCityName) FROM FlightTable where OriginStateName = 'Texas'")
print('Airports in Texas: ', out.show(100))

# find all airlines that fly from Texas
out1 = spark.sql(
    "SELECT distinct(Reporting_Airline) FROM FlightTable WHERE OriginStateName='Texas'")
print('Airlines that fly to/from Texas: ', out1.show(100, False))
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。 これを行うには、ストレージ アカウントのリソース グループを選択し、 **[削除]** を選択してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"] 
> [Azure HDInsight の Apache Hive を使用してデータの抽出、変換、読み込みを行う](data-lake-storage-tutorial-extract-transform-load-hive.md)
