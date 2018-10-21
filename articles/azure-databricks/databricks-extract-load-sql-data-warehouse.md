---
title: 'チュートリアル: Azure Databricks を使用して ETL 操作を実行する'
description: Data Lake Store から Azure Databricks にデータを抽出し、変換して、Azure SQL Data Warehouse に読み込む方法を説明します。
services: azure-databricks
author: nitinme
ms.author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.workload: Active
ms.date: 07/26/2018
ms.openlocfilehash: c416937f98f6bcb49f86fce18213ca4ed349c513
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902061"
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>チュートリアル: Azure Databricks を使用したデータの抽出、変換、読み込み

このチュートリアルでは、Azure Databricks を使用して ETL (データの抽出、変換、読み込み) 操作を実行します。 Azure Data Lake Store から Azure Databricks にデータを抽出し、Azure Databricks でデータの変換を実行した後、その変換済みのデータを Azure SQL Data Warehouse に読み込みます。 

このチュートリアルの手順では、Azure Databricks 用の SQL Data Warehouse コネクタを使って Azure Databricks にデータを転送します。 その後、このコネクタによって、Azure Databricks クラスターと Azure SQL Data Warehouse の間で転送されるデータの一時記憶域として Azure Blob Storage が使用されます。

次の図に、アプリケーション フローを示します。

![Data Lake Store を使用する Azure Databricks と SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Data Lake Store を使用する Azure Databricks と SQL Data Warehouse")

このチュートリアルに含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Databricks ワークスペースを作成する
> * Azure Databricks で Spark クラスターを作成する
> * Azure Data Lake Store アカウントを作成する
> * Azure Data Lake Store にデータをアップロードする
> * Azure Databricks でノートブックを作成する
> * Data Lake Store からデータを抽出する
> * Azure Databricks でデータを変換する
> * Azure SQL Data Warehouse へのデータの読み込み

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の要件を満たしてください。
- Azure SQL Data Warehouse を作成し、サーバー レベルのファイアウォール規則を作成して、サーバー管理者としてサーバーに接続します。[Azure SQL Data Warehouse の作成に関するクイック スタート](../sql-data-warehouse/create-data-warehouse-portal.md)の手順に従ってください。
- Azure SQL Data Warehouse に使用するデータベース マスター キーを作成します。 「[データベース マスター キーの作成](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key)」の手順に従ってください。
- Azure Blob Storage アカウントを作成し、そこにコンテナーを作成します。 また、ストレージ アカウントにアクセスするためのアクセス キーを取得します。 [Azure BLOB ストレージ アカウントの作成に関するクイック スタート](../storage/blobs/storage-quickstart-blobs-portal.md)の手順に従ってください。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする

[Azure Portal](https://portal.azure.com/) にログインします。

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、Azure Portal を使って Azure Databricks ワークスペースを作成します。 

1. Azure Portal で、**[リソースの作成]** > **[データ + 分析]** > **[Azure Databricks]** の順に選択します。

    ![Azure Portal の Databricks](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Azure Portal の Databricks")

3. **[Azure Databricks サービス]** で値を指定して、Databricks ワークスペースを作成します。

    ![Azure Databricks ワークスペースを作成する](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Azure Databricks ワークスペースを作成する")

    次の値を指定します。 
     
    |プロパティ  |[説明]  |
    |---------|---------|
    |**[ワークスペース名]**     | Databricks ワークスペースの名前を指定します        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**[リソース グループ]**     | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../azure-resource-manager/resource-group-overview.md)に関するページをご覧ください。 |
    |**場所**     | **[米国東部 2]** を選択します。 使用可能な他のリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」をご覧ください。        |
    |**価格レベル**     |  **Standard** と **Premium** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

    **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、**[作成]** を選択します。

4. アカウントの作成には数分かかります。 アカウント作成時に、ポータルの右側に **[Submitting deployment for Azure Databricks]\(Azure Databricks のデプロイを送信しています\)** タイルが表示されます。 このタイルを表示するために、ダッシュボードを右へスクロールしなければならない場合があります。 スクリーンの上部に進行状況バーも表示されます。 いずれかの領域で進行状況を確認できます。

    ![Databricks のデプロイのタイル](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Databricks のデプロイのタイル")

## <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

1. Azure Portal で、作成した Databricks ワークスペースに移動して、**[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[クラスター]** を選択します。

    ![Azure の Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Azure の Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    以下の値を除き、他のすべての値は既定値のままにします。

    * クラスターの名前を入力します。
    * この記事では、**4.0** ランタイムを使用してクラスターを作成します。 
    * **[Terminate after \_\_ minutes of inactivity]\(アクティビティが \_\_ 分ない場合は終了する\)** チェック ボックスをオンにします。 クラスターが使われていない場合にクラスターを終了するまでの時間 (分単位) を指定します。
    
    **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

## <a name="create-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントを作成する

このセクションでは、Azure Data Lake Store アカウントを作成し、そこに Azure Active Directory のサービス プリンシパルを関連付けます。 このチュートリアルで後ほどこのサービス プリンシパルを使い、Azure Databricks から Azure Data Lake Store にアクセスします。 

1. [Azure Portal](https://portal.azure.com) で、**[リソースの作成]** > **[ストレージ]** > **[Data Lake Store]** を選択します。
3. **[新しい Data Lake Store]** ブレードで、次のスクリーンショットに示されているように値を指定します。
   
    ![新しい Azure Data Lake Store アカウントの作成](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "新しい Azure Data Lake アカウントの作成")

    次の値を指定します。 
     
    |プロパティ  |[説明]  |
    |---------|---------|
    |**名前**     | Data Lake Store アカウントの一意の名前を入力します。        |
    |**サブスクリプション**     | ドロップダウンから Azure サブスクリプションを選択します。        |
    |**[リソース グループ]**     | このチュートリアルでは、Azure Databricks ワークスペースの作成時に使用したものと同じリソース グループを選択します。  |
    |**場所**     | **[米国東部 2]** を選択します。  |
    |**価格パッケージ**     |  **[従量課金制]** を選択します。 |
    | **暗号化設定** | 既定の設定のままにします。 |

    **[ダッシュボードにピン留めする]** チェック ボックスをオンにして、**[作成]** を選択します。

次に、Azure Active Directory のサービス プリンシパルを作成し、作成済みの Data Lake Store アカウントに関連付けます。

### <a name="create-an-azure-active-directory-service-principal"></a>Azure Active Directory のサービス プリンシパルを作成する
   
1. [Azure Portal](https://portal.azure.com) で、**[すべてのサービス]** を選択し、**Azure Active Directory** を探します。

2. **[アプリの登録]** を選択します。

   ![[アプリの登録] を選択する](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. **[新しいアプリケーションの登録]** を選択します。

   ![アプリを追加する](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. アプリケーションの名前と URL を指定します。 作成するアプリケーションの種類として、**[Web アプリ/API]** を選択します。 サインオン URL を指定し、**[作成]** を選択します。

   ![アプリケーションの名前指定](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Azure Databricks から Data Lake Store アカウントにアクセスするには、作成した Azure Active Directory のサービス プリンシパルに関して、次の値が必要となります。
- アプリケーション ID
- 認証キー
- テナント ID

以降のセクションでは、先ほど作成した Azure Active Directory のサービス プリンシパルについて、これらの値を取得します。

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>サービス プリンシパルのアプリケーション ID と認証キーを取得する

プログラムによってログインするときは、アプリケーションの ID と認証キーが必要です。 これらの値を取得するには、次の手順に従います。

1. Azure Active Directory の **[アプリの登録]** で、アプリケーションを選択します。

   ![アプリケーションを選択する](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. **アプリケーション ID** をコピーし、アプリケーション コードに保存します。 一部の[サンプル アプリケーション](#log-in-as-the-application)では、この値はクライアント ID と呼ばれます。

   ![クライアント ID](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. 認証キーを生成するには、**[設定]** を選択します。

   ![設定を選択](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. 認証キーを生成するには、**[キー]** を選択します。

   ![[キー] を選択する](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. キーの説明を入力し、キーの期間を指定します。 操作が完了したら、**[保存]** をクリックします。

   ![キーを保存する](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   キーを保存すると、キーの値が表示されます。 キーは後で取得できないため、この値をコピーしておきます。 キー値は、アプリケーションとしてログインする際にアプリケーション ID と共に入力します。 アプリケーションが取得できる場所にキー値を保存します。

   ![保存されたキー](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>テナント ID を取得する

プログラムによってログインするときは、認証要求と共にテナント ID を渡す必要があります。

1. **[Azure Active Directory]** を選択します。

   ![[Azure Active Directory] を選択する](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. テナント ID を取得するには、Azure AD テナントの **[プロパティ]** を選択します。

   ![Azure AD のプロパティを選択する](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. **ディレクトリ ID** をコピーします。 この値がテナント ID です。

   ![テナント ID](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

## <a name="upload-data-to-data-lake-store"></a>Data Lake Store にデータをアップロードする

このセクションでは、サンプル データ ファイルを Data Lake Store にアップロードします。 このファイルは、後から Azure Databricks でいくつかの変換処理を実行する際に使用します。 このチュートリアルで使用するサンプル データ (**small_radio_json.json**) は、こちらの [GitHub リポジトリ](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json)で入手できます。

1. [Azure Portal](https://portal.azure.com) で、作成した Data Lake Store アカウントを選択します。

2. **[概要]** タブの **[データ エクスプローラー]** をクリックします。

    ![データ エクスプローラーを開く](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "データ エクスプローラーを開く")

3. データ エクスプローラー内の **[アップロード]** をクリックします。

    ![[アップロード] オプション](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "[アップロード] オプション")

4. **[ファイルのアップロード]** で、サンプル データ ファイルの場所を参照し、**[選択されたファイルを追加します]** を選択します。

    ![[アップロード] オプション](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "[アップロード] オプション")

5. このチュートリアルでは、Data Lake Store のルートにデータ ファイルをアップロードしました。 したがって、この時点でファイルには `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json` でアクセスすることができます。

## <a name="associate-service-principal-with-azure-data-lake-store"></a>Azure Data Lake Store にサービス プリンシパルを関連付ける

このセクションでは、作成した Azure Active Directory のサービス プリンシパルに Azure Data Lake Store アカウントのデータを関連付けます。 これにより、Azure Databricks から Data Lake Store アカウントにアクセスできるようになります。 この記事のシナリオでは、Data Lake Store のデータを読み込んで、SQL Data Warehouse のテーブルに設定します。 [Data Lake Store のアクセス制御の概要](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)に関するページによれば、Data Lake Store のファイルに対して読み取りアクセスを行うには、以下が必要です。

- フォルダー構造内のファイルに至るまでのすべてのフォルダーに対する**実行**アクセス許可。
- ファイル自体に対する**読み取り**アクセス許可。

これらのアクセス許可を付与するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、作成した Data Lake Store アカウントを選択し、**[データ エクスプローラー]** を選択します。

    ![データ エクスプローラーを起動する](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-data-explorer.png "データ エクスプローラーを起動する")

2. このシナリオでは、サンプル データ ファイルがフォルダー構造のルートにあるため、フォルダー ルートだけに**実行**アクセス許可を割り当てる必要があります。 そうするには、データ エクスプローラーのルートで **[アクセス]** を選択します。

    ![フォルダーの ACL を追加する](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-1.png "フォルダーの ACL を追加する")

3. **[アクセス]** で **[追加]** を選択します。

    ![フォルダーの ACL を追加する](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-2.png "フォルダーの ACL を追加する")

4. **[アクセス許可の割り当て]** で **[ユーザーまたはグループの選択]** をクリックし、前に作成した Azure Active Directory のサービス プリンシパルを検索します。

    ![Data Lake Store アクセスの追加](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Data Lake Store アクセスの追加")

    割り当てる AAD サービス プリンシパルを選択し、**[選択]** をクリックします。

5. **[アクセス許可の割り当て]** で、**[アクセス許可の選択]** > **[実行]** の順にクリックします。 他の既定値はそのままにして、**[アクセス許可の選択]** の下の **[OK]** を選択し、**[アクセス許可の割り当て]** の下の [OK] を選択します。

    ![Data Lake Store アクセスの追加](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-4.png "Data Lake Store アクセスの追加")

6. データ エクスプローラーに戻り、読み取りアクセス許可を割り当てるファイルをクリックします。 **[ファイルのプレビュー]** の下の **[アクセス]** を選択します。

    ![Data Lake Store アクセスの追加](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-1.png "Data Lake Store アクセスの追加")

7. **[アクセス]** で **[追加]** を選択します。 **[アクセス許可の割り当て]** で **[ユーザーまたはグループの選択]** をクリックし、前に作成した Azure Active Directory のサービス プリンシパルを検索します。

    ![Data Lake Store アクセスの追加](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-folder-3.png "Data Lake Store アクセスの追加")

    割り当てる AAD サービス プリンシパルを選択し、**[選択]** をクリックします。

8. **[アクセス許可の割り当て]** で、**[アクセス許可の選択]**  > **[読み取り]** の順にクリックします。 **[アクセス許可の選択]** の下の **[OK]** を選択し、**[アクセス許可の割り当て]** の下の [OK] を選択します。

    ![Data Lake Store アクセスの追加](./media/databricks-extract-load-sql-data-warehouse/add-adls-access-file-2.png "Data Lake Store アクセスの追加")

    サービス プリンシパルに、Azure Data Lake Store からサンプル データ ファイルを読み取るための十分なアクセス許可が付与されました。

## <a name="extract-data-from-data-lake-store"></a>Data Lake Store からデータを抽出する

このセクションでは、Azure Databricks ワークスペースにノートブックを作成し、Data Lake Store から Azure Databricks にデータを抽出するコード スニペットを実行します。

1. [Azure Portal](https://portal.azure.com) で、作成した Azure Databricks ワークスペースに移動して、**[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. 左側のウィンドウで、**[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンで、**[作成]** > **[ノートブック]** の順に選択します。

    ![Databricks でノートブックを作成する](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[ノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。 言語として **[Scala]** を選んで、前に作成した Spark クラスターを選びます。

    ![Databricks でノートブックを作成する](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Databricks でノートブックを作成する")

    **[作成]** を選択します。

3. 空のコード セルに次のスニペットを追加します。プレースホルダーの値は、先ほど保存した Azure Active Directory のサービス プリンシパルの値に置き換えてください。

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    **Shift + Enter** キーを押して、コード セルを実行します。

4. これで、Data Lake Store 内のサンプル json ファイルをデータフレームとして Azure Databricks に読み込むことができます。 新しいコード セルに次のスニペットを貼り付け、プレースホルダーの値を置き換えて、**Shift + Enter** キーを押します。

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. 次のコード スニペットを実行して、データ フレームの内容を表示します。

        df.show()

    次のスニペットのような出力が表示されます。

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Azure Data Lake Store から Azure Databricks にデータが抽出されました。

## <a name="transform-data-in-azure-databricks"></a>Azure Databricks でデータを変換する

未加工のサンプル データ **small_radio_json.json** は、ラジオ局のリスナーを収集したものであり、さまざまな列を含んでいます。 このセクションでは、このデータを変換して、データセットから特定の列だけを取得します。 

1. まず、あらかじめ作成しておいたデータフレームから、*firstName*、*lastName*、*gender*、*location*、*level* の各列だけを取得します。

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
        specificColumnsDf.show()

    次のスニペットに示されているような出力が得られます。

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

2.  さらにこのデータを変換し、**level** 列の名前を **subscription_type** に変更することができます。

        val renamedColumnsDf = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDf.show()

    次のスニペットに示されているような出力が得られます。

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

## <a name="load-data-into-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse へのデータの読み込み

このセクションでは、変換したデータを Azure SQL Data Warehouse にアップロードします。 Azure Databricks 用の Azure SQL Data Warehouse コネクタを使用すると、データフレームを SQL Data Warehouse のテーブルとして直接アップロードすることができます。

前述のように、SQL Data Warehouse コネクタは、Azure Databricks と Azure SQL Data Warehouse との間でデータをアップロードするための一時記憶域の場所として Azure Blob Storage を使用します。 それにはまず、そのストレージ アカウントに接続するための構成を指定します。 このアカウントは、この記事の前提条件としてあらかじめ作成しておく必要があります。

1. Azure Databricks から Azure Storage アカウントにアクセスするための構成を指定します。

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Azure Databricks と Azure SQL Data Warehouse との間でデータを移動するときに使用する一時フォルダーを指定します。

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. 次のスニペットを実行して、Azure Blob Storage のアクセス キーを構成に格納します。 これにより、アクセス キーをプレーンテキストのままノートブックに保持せずに済みます。

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Azure SQL Data Warehouse インスタンスに接続するための値を指定します。 SQL Data Warehouse は、前提条件の 1 つとして、あらかじめ作成しておく必要があります。

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. 次のスニペットを実行して、変換済みのデータフレーム (**renamedColumnsDf**) をテーブルとして SQL Data Warehouse に読み込みます。 このスニペットは、SQL データベースに **SampleTable** というテーブルを作成します。 Azure SQL DW にはマスター キーが必要であることに注意してください。  SQL Server Management Studio で "CREATE MASTER KEY;" コマンドを実行すると、マスター キーを作成できます。

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDf.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. SQL データベースに接続し、**SampleTable** が見えることを確認します。

    ![サンプル テーブルの確認](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "サンプル テーブルの確認")

7. 選択クエリを実行して、テーブルの内容を確認します。 **renamedColumnsDf** データフレームと同じデータが存在しているはずです。

    ![サンプル テーブルの内容の確認](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "サンプル テーブルの内容の確認")

## <a name="clean-up-resources"></a>リソースのクリーンアップ

チュートリアルの実行が完了したら、クラスターを終了できます。 そのためには、Azure Databricks ワークスペースの左側のウィンドウで、**[クラスター]** を選択します。 終了するクラスターで、**[アクション]** 列の下にある省略記号をポイントし、**[終了]** アイコンを選択します。

![Databricks クラスターを停止する](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Databricks クラスターを停止する")

クラスター作成時に **[Terminate after __ minutes of inactivity]\(アクティビティが __ 分ない場合は終了する\)** チェック ボックスをオンにしていた場合、手動で終了しなくともクラスターは自動で停止します。 このような場合、クラスターは、一定の時間だけ非アクティブな状態が続くと自動的に停止します。

## <a name="next-steps"></a>次の手順 
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Databricks ワークスペースを作成する
> * Azure Databricks で Spark クラスターを作成する
> * Azure Data Lake Store アカウントを作成する
> * Azure Data Lake Store にデータをアップロードする
> * Azure Databricks でノートブックを作成する
> * Data Lake Store からデータを抽出する
> * Azure Databricks でデータを変換する
> * Azure SQL Data Warehouse へのデータの読み込み

次のチュートリアルに進み、Azure Event Hubs を使ってリアルタイム データを Azure Databricks にストリーミングする方法について見てみましょう。

> [!div class="nextstepaction"]
>[Event Hubs を使用してデータを Azure Databricks にストリーム配信する](databricks-stream-from-eventhubs.md)
