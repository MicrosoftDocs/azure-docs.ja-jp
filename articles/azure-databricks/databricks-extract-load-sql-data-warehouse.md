---
title: 'チュートリアル: Azure Databricks を使用して ETL 操作を実行する'
description: Data Lake Storage Gen2 から Azure Databricks にデータを抽出し、変換して、Azure SQL Data Warehouse に読み込む方法を説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 4e28da9ab9502e2dac4fc08452a46841c4e50b66
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466808"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う

このチュートリアルでは、Azure Databricks を使用して ETL (データの抽出、変換、読み込み) 操作を実行します。 Azure Data Lake Storage Gen2 から Azure Databricks にデータを抽出し、Azure Databricks でそのデータに対する変換を実行した後、変換されたデータを Azure SQL Data Warehouse に読み込みます。

このチュートリアルの手順では、Azure Databricks 用の SQL Data Warehouse コネクタを使って Azure Databricks にデータを転送します。 その後、このコネクタによって、Azure Databricks クラスターと Azure SQL Data Warehouse の間で転送されるデータの一時記憶域として Azure Blob Storage が使用されます。

次の図に、アプリケーション フローを示します。

![Data Lake Store を使用する Azure Databricks と SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Data Lake Store を使用する Azure Databricks と SQL Data Warehouse")

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * Azure Databricks サービスを作成する。
> * Azure Databricks で Spark クラスターを作成する。
> * Data Lake Storage Gen2 アカウントでファイル システムを作成する。
> * サンプル データを Azure Data Lake Storage Gen2 アカウントにアップロードする。
> * サービス プリンシパルを作成する。
> * Azure Data Lake Storage Gen2 アカウントからデータを抽出する。
> * Azure Databricks でデータを変換する。
> * Azure SQL Data Warehouse にデータを読み込む。

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

> [!Note]
> **Azure 無料試用版サブスクリプション**を使用してこのチュートリアルを実行することはできません。
> 無料アカウントをお持ちの場合は、お使いのプロファイルにアクセスし、サブスクリプションを **[従量課金制]** に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。 次に、リージョン内の vCPU について[使用制限を削除し](https://docs.microsoft.com/azure/billing/billing-spending-limit#remove-the-spending-limit-in-account-center)、[クォータの増加を依頼](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)します。 Azure Databricks ワークスペースを作成するときに、 **[Trial (Premium - 14-Days Free DBUs)]\(試用版 (Premium - 14 日間の無料 DBU)\)** の価格レベルを選択し、ワークスペースから 14 日間無料の Premium Azure Databricks DBU にアクセスできるようにします。
     
## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、以下のタスクを完了します。

* Azure SQL データ ウェアハウスを作成し、サーバーレベルのファイアウォール規則を作成して、サーバー管理者としてサーバーに接続します。「[クイック スタート:Azure Portal で Azure SQL データ ウェアハウスを作成し、クエリを実行する](../sql-data-warehouse/create-data-warehouse-portal.md)」を参照してください。

* Azure SQL データ ウェアハウスに使用するデータベース マスター キーを作成します。 「[データベース マスター キーの作成](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)」を参照してください。

* Azure Blob Storage アカウントを作成し、そこにコンテナーを作成します。 また、ストレージ アカウントにアクセスするためのアクセス キーを取得します。 「[クイック スタート:Azure portal を使用して BLOB をアップロード、ダウンロード、および一覧表示する](../storage/blobs/storage-quickstart-blobs-portal.md)」を参照してください。

* Azure Data Lake Storage Gen2 ストレージ アカウントを作成します。 「[クイック スタート:Azure Data Lake Storage Gen2 ストレージ アカウントを作成する](../storage/blobs/data-lake-storage-quickstart-create-account.md)」を参照してください。

* サービス プリンシパルを作成する。 「[方法:リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)」のガイダンスに従って、サービス プリンシパルを作成します。

   この記事の手順を実行する際に、いくつかの特定の作業を行う必要があります。

   * 記事の「[アプリケーションをロールに割り当てる](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role)」セクションの手順を行うときは、必ず、Data Lake Storage Gen2 アカウントのスコープ内で **ストレージ BLOB データ共同作成者**ロールをサービス プリンシパルに割り当ててください。 親リソース グループまたはサブスクリプションにロールを割り当てる場合、それらのロール割り当てがストレージ アカウントに伝達されるまで、アクセス許可関連のエラーが発生します。

      アクセス制御リスト (ACL) を使用して特定のファイルまたはディレクトリにサービス プリンシパルを関連付ける場合は、「[Azure Data Lake Storage Gen2 のアクセス制御](../storage/blobs/data-lake-storage-access-control.md)」を参照してください。

   * 記事の「[サインインするための値を取得する](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)」セクションの手順を行うときは、テナント ID、アプリ ID、およびパスワードの値をテキスト ファイルに貼り付けてください。 これらはすぐに必要になります。

* [Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="gather-the-information-that-you-need"></a>必要な情報を収集する

このチュートリアルの前提条件を満たしていることを確認します。

   開始する前に、以下の情報が必要です。

   :heavy_check_mark:Azure SQL Data Warehouse のデータベース名、データベース サーバー名、ユーザー名、およびパスワード。

   :heavy_check_mark:BLOB ストレージ アカウントのアクセス キー。

   :heavy_check_mark:Data Lake Storage Gen2 ストレージ アカウントの名前。

   :heavy_check_mark:サブスクリプションのテナント ID。

   :heavy_check_mark:Azure Active Directory (Azure AD) に登録したアプリのアプリケーション ID。

   :heavy_check_mark:Azure AD に登録したアプリの認証キー。

## <a name="create-an-azure-databricks-service"></a>Azure Databricks サービスを作成する

このセクションでは、Azure portal を使用して Azure Databricks サービスを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Azure Portal の Databricks")

2. **[Azure Databricks サービス]** で次の値を指定して、Databricks サービスを作成します。

    |プロパティ  |説明  |
    |---------|---------|
    |**ワークスペース名**     | Databricks ワークスペースの名前を指定します。        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**リソース グループ**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**Location**     | **[米国西部 2]** を選択します。  使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。      |
    |**価格レベル**     |  **[Standard]** を選択します。     |

3. アカウントの作成には数分かかります。 操作の状態を監視するには、上部の進行状況バーを確認します。

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、 **[作成]** を選択します。

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Azure Databricks で Spark クラスターを作成する

1. Azure portal で、作成した Databricks サービスに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** を選択します。

    ![Azure の Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

4. 次のフィールドに値を入力し、他のフィールドの既定値はそのまま使用します。

    * クラスターの名前を入力します。

    * **[Terminate after \_\_ minutes of inactivity]\(アクティビティが \_\_ 分ない場合は終了する\)** チェック ボックスを必ずオンにします。 クラスターが使われていない場合は、クラスターを終了するまでの時間 (分単位) を指定します。

    * **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 アカウントでファイル システムを作成する

このセクションでは、Azure Databricks ワークスペースにノートブックを作成し、ストレージ アカウントを構成するコード スニペットを実行します

1. [Azure portal](https://portal.azure.com) で、作成した Azure Databricks サービスに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. 左側の **[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンで、 **[作成]**  >  **[ノートブック]** の順に選択します。

    ![Databricks でノートブックを作成する](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Databricks でノートブックを作成する")

3. **[ノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。 言語として **[Scala]** を選んで、前に作成した Spark クラスターを選びます。

    ![Databricks でノートブックの詳細情報を入力する](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Databricks でノートブックの詳細情報を入力する")

4. **作成** を選択します。

5. 次のコード ブロックでは、Spark セッションでアクセスされる ADLS Gen 2 アカウント用の既定のサービス プリンシパル資格情報を設定します。 2 つ目のコード ブロックでは、特定の ADLS Gen 2 アカウントの資格情報を指定する設定にアカウント名を追加します。  いずれかのコード ブロックをコピーして、Azure Databricks ノートブックの最初のセルに貼り付けます。

   **セッションの構成**

   ```scala
   val appID = "<appID>"
   val password = "<password>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

   **アカウントの構成**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val password = "<password>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + password + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. このコード ブロックでは、`<app-id>`、`<password>`、`<tenant-id>`、および `<storage-account-name>` のプレースホルダー値を、このチュートリアルの前提条件の実行中に収集した値で置き換えます。 `<file-system-name>` プレースホルダーの値を、ファイル システムに付けたい名前に置き換えます。

   * `<app-id>` および `<password>` は、サービス プリンシパルの作成の一環として Active Directory に登録したアプリのものです。

   * `<tenant-id>` は、自分のサブスクリプションのものです。

   * `<storage-account-name>` は、Azure Data Lake Storage Gen2 ストレージ アカウントの名前です。

7. **Shift + Enter** キーを押して、このブロック内のコードを実行します。

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 アカウントにサンプル データを取り込む

このセクションで始める前に、次の前提条件を満たす必要があります。

ノートブックのセルに次のコードを入力します。

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

セル内で **Shift + Enter** キーを押して、コードを実行します。

次に、その下の新しいセルに次のコードを入力します。ブラケットで囲まれている値は、前に使用したのと同じ値に置き換えてください。

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccount + ".dfs.core.windows.net/")

セル内で **Shift + Enter** キーを押して、コードを実行します。

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Azure Data Lake Storage Gen2 アカウントからデータを抽出する

1. これで、サンプル json ファイルをデータフレームとして Azure Databricks に読み込むことができます。 新しいセルに次のコードを貼り付けます。 角かっこで囲まれているプレースホルダーは、実際の値に置き換えてください。

   ```scala
   val df = spark.read.json("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json")
   ```
2. **Shift + Enter** キーを押して、このブロック内のコードを実行します。

3. 次のコードを実行して、データ フレームの内容を表示します。

    ```scala
    df.show()
    ```
   次のスニペットのような出力が表示されます。

   ```bash
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Azure Data Lake Storage Gen2 から Azure Databricks にデータが抽出されました。

## <a name="transform-data-in-azure-databricks"></a>Azure Databricks でデータを変換する

未加工のサンプル データ ファイル **small_radio_json.json** は、ラジオ局のリスナー情報を収集したものであり、さまざまな列を含んでいます。 このセクションでは、このデータを変換して、データセットから特定の列だけを取得します。

1. まず、作成したデータフレームから、**firstName**、**lastName**、**gender**、**location**、**level** の各列だけを取得します。

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   次のスニペットに示されているような出力が得られます。

   ```bash
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. さらにこのデータを変換し、**level** 列の名前を **subscription_type** に変更することができます。

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   次のスニペットに示されているような出力が得られます。

   ```bash
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータの読み込み

このセクションでは、変換したデータを Azure SQL Data Warehouse にアップロードします。 Azure Databricks 用の Azure SQL Data Warehouse コネクタを使用して、データフレームを SQL データ ウェアハウスのテーブルとして直接アップロードします。

前述のように、SQL Data Warehouse コネクタによって、Azure Blob Storage が一時ストレージとして使用され、Azure Databricks と Azure SQL Data Warehouse との間でデータがアップロードされます。 それにはまず、そのストレージ アカウントに接続するための構成を指定します。 このアカウントは、この記事の前提条件としてあらかじめ作成しておく必要があります。

1. Azure Databricks から Azure Storage アカウントにアクセスするための構成を指定します。

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Azure Databricks と Azure SQL Data Warehouse の間でデータを移動するときに使用する一時フォルダーを指定します。

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. 次のスニペットを実行して、Azure Blob Storage のアクセス キーを構成に格納します。 このアクションにより、アクセス キーをプレーンテキストのままノートブックに保持せずに済みます。

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Azure SQL Data Warehouse インスタンスに接続するための値を指定します。 SQL データ ウェアハウスは、前提条件としてあらかじめ作成しておく必要があります。 **dwServer** の完全修飾サーバー名を使用します。 たとえば、「 `<servername>.database.windows.net` 」のように入力します。

   ```scala
   //SQL Data Warehouse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. 次のスニペットを実行して、変換済みのデータフレーム (**renamedColumnsDf**) をテーブルとして SQL データ ウェアハウスに読み込みます。 このスニペットは、SQL データベースに **SampleTable** というテーブルを作成します。

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > このサンプルでは `forward_spark_azure_storage_credentials` フラグを使用します。これにより、SQL Data Warehouse は、アクセス キーを使用して BLOB ストレージからのデータにアクセスします。 これは、サポートされている唯一の認証方法です。
   >
   > Azure Blob Storage が仮想ネットワークを選択するように制限されている場合、SQL Data Warehouse には[アクセス キーではなく、マネージド サービス ID](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) が必要です。 これにより、"This request is not authorized to perform this operation (この要求には、この操作を実行する権限がありません)" というエラーが発生します。

6. SQL データベースに接続し、**SampleTable** という名前のデータベースが表示されることを確認します。

   ![サンプル テーブルを確認する](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "サンプル テーブルを確認する")

7. 選択クエリを実行して、テーブルの内容を確認します。 このテーブルには、**renamedColumnsDf** データフレームと同じデータが存在しているはずです。

    ![サンプル テーブルの内容を確認する](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "サンプル テーブルの内容を確認する")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルが完了したら、クラスターを終了できます。 Azure Databricks ワークスペースで、左側の **[クラスター]** を選択します。 クラスターが終了するように、 **[アクション]** の下にある省略記号 (...) をポイントし、**終了**アイコンを選択します。

![Databricks クラスターを停止する](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスター作成時に **[Terminate after \_\_ minutes of inactivity]\(アクティビティが \_\_ 分ない場合は終了する\)** チェック ボックスをオンにした場合は、手動で終了しなくても、クラスターは自動的に停止します。 このような場合、クラスターは、非アクティブな状態が一定の時間続くと自動的に停止します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure Databricks サービスを作成する
> * Azure Databricks で Spark クラスターを作成する
> * Azure Databricks でノートブックを作成する
> * Data Lake Storage Gen2 アカウントからデータを抽出する
> * Azure Databricks でデータを変換する
> * Azure SQL Data Warehouse へのデータの読み込み

次のチュートリアルに進み、Azure Event Hubs を使ってリアルタイム データを Azure Databricks にストリーミングする方法について見てみましょう。

> [!div class="nextstepaction"]
>[Event Hubs を使用してデータを Azure Databricks にストリーム配信する](databricks-stream-from-eventhubs.md)
