---
title: チュートリアル:Azure Databricks Delta テーブルを更新する Data Lake キャプチャ パターンを実装する | Microsoft Docs
description: このチュートリアルでは、Event Grid サブスクリプション、Azure 関数、および Azure Databricks ジョブを使用して、Azure DataLake Storage Gen2 に格納されているテーブルにデータの行を挿入する方法について説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516398"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>チュートリアル:Databricks Delta テーブルを更新する Data Lake キャプチャ パターンを実装する

このチュートリアルでは、階層型名前空間を持つストレージ アカウント内でイベントを処理する方法について説明します。

販売注文を記述したコンマ区切り値 (csv) ファイルをアップロードすることで、ユーザーが Databricks Delta テーブルにデータを設定できるようにするための小規模なソリューションを構築します。 このソリューションを構築するには、Event Grid サブスクリプション、Azure 関数、および Azure Databricks 内の[ジョブ](/azure/databricks/jobs)を 1 つに接続します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure 関数を呼び出す Event Grid サブスクリプションを追加します。
> * イベントから通知を受信し、Azure Databricks 内でジョブを実行する Azure 関数を作成します。
> * ストレージ アカウントに配置されている Databricks Delta テーブルに顧客注文を挿入する Databricks ジョブを作成します。

このソリューションの構築は、Azure Databricks ワークスペースから始めて、逆の順序で行います。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* 階層型名前空間 (Azure Data Lake Storage Gen2) を持つストレージ アカウントを作成します。 このチュートリアルでは、`contosoorders` という名前のストレージ アカウントを使用します。 ユーザー アカウントに[ストレージ BLOB データ共同作成者ロール](../common/storage-auth-aad-rbac-portal.md)が割り当てられていることを確認します。

   「[Azure Data Lake Storage Gen2 で使用するストレージ アカウントを作成する](create-data-lake-storage-account.md)」をご覧ください。

* サービス プリンシパルを作成する。 UnitTesting.Conditions.ExportTestConditionAttribute について詳しくは、「[リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」のガイダンスに従って、サービス プリンシパルを作成します。

  この記事の手順を実行する際に、いくつかの特定の作業を行う必要があります。

  :heavy_check_mark:記事の「[アプリケーションをロールに割り当てる](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)」セクションの手順を実行するときに、必ず **ストレージ BLOB データ共同作成者** ロールをサービス プリンシパルに割り当ててください。

  > [!IMPORTANT]
  > Data Lake Storage Gen2 ストレージ アカウントの範囲内のロールを割り当てるようにしてください。 親リソース グループまたはサブスクリプションにロールを割り当てることはできますが、それらのロール割り当てがストレージ アカウントに伝達されるまで、アクセス許可関連のエラーが発生します。

  :heavy_check_mark:記事の「[サインインするための値を取得する](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)」セクションの手順を行うときは、テナント ID、アプリ ID、およびパスワードの値をテキスト ファイルに貼り付けてください。 これらの値は、後で必要になります。

## <a name="create-a-sales-order"></a>販売注文を作成する

まず、販売注文を記述した csv ファイルを作成し、そのファイルをストレージ アカウントにアップロードします。 後で、このファイルのデータを使用して、Databricks Delta テーブルの最初の行に値を設定します。

1. Azure ストレージ エクスプローラーを開きます。 次に、ストレージ アカウントに移動し、 **[BLOB コンテナー]** セクションで、**data** という名前の新しいコンテナーを作成します。

   ![data フォルダー](./media/data-lake-storage-events/data-container.png "data フォルダー")

   Storage Explorer の使用方法の詳細については、「[Azure Storage Explorer を使用して Azure Data Lake Storage Gen2 アカウントのデータを管理する](data-lake-storage-explorer.md)」を参照してください。

2. **data** コンテナーで、**input** という名前のフォルダーを作成します。

3. 次のテキストをテキスト エディターに貼り付けます。

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. このファイルをローカル コンピューターに保存し、**data.csv** という名前を付けます。

5. Storage Explorer で、このファイルを **input** フォルダーにアップロードします。  

## <a name="create-a-job-in-azure-databricks"></a>Azure Databricks でジョブを作成する

このセクションでは、以下のタスクを実行します。

* Azure Databricks ワークスペースを作成する。
* Notebook を作成します。
* Databricks Delta テーブルを作成し、データを設定します。
* Databricks Delta テーブルに行を挿入するコードを追加します。
* ジョブを作成します。

### <a name="create-an-azure-databricks-workspace"></a>Azure Databricks ワークスペースを作成する

このセクションでは、Azure Portal を使って Azure Databricks ワークスペースを作成します。

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure portal での Databricks](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Azure portal での Databricks")

2. **[Azure Databricks サービス]** で値を指定して、Databricks ワークスペースを作成します。

    ![Azure Databricks ワークスペースを作成します](./media/data-lake-storage-events/new-databricks-service.png "Azure Databricks ワークスペースを作成する")

    ワークスペースの作成には数分かかります。 操作の状態を監視するには、上部の進行状況バーを確認します。

### <a name="create-a-spark-cluster-in-databricks"></a>Databricks に Spark クラスターを作成する

1. [Azure portal](https://portal.azure.com) で、作成した Azure Databricks ワークスペースに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

2. Azure Databricks ポータルにリダイレクトされます。 ポータルで **[新規]**  >  **[クラスター]** を選択します。

    ![Azure での Databricks](./media/data-lake-storage-events/databricks-on-azure.png "Azure での Databricks")

3. **[New cluster]\(新しいクラスター\)** ページで、クラスターを作成するための値を指定します。

    ![Azure で Databricks Spark クラスターを作成する](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Azure で Databricks Spark クラスターを作成する")

    以下を除くすべての値は、既定値のままにします。

    * クラスターの名前を入力します。
    * **[Terminate after 120 minutes of inactivity]** \(アクティビティが 120 分ない場合は終了する\) チェック ボックスをオンにします。 クラスターが使われていない場合にクラスターを終了するまでの時間 (分単位) を指定します。

4. **[クラスターの作成]** を選択します。 クラスターが実行されたら、ノートブックをクラスターにアタッチして、Spark ジョブを実行できます。

クラスターの作成について詳しくは、[Azure Databricks での Spark クラスターの作成に関するページ](/azure/databricks/clusters/create)をご覧ください。

### <a name="create-a-notebook"></a>ノートブックを作成する

1. 左側のウィンドウで、 **[ワークスペース]** を選択します。 **[ワークスペース]** ドロップダウンで、 **[作成]**  >  **[ノートブック]** の順に選択します。

    ![Databricks でノートブックを作成する](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Databricks でノートブックを作成する")

2. **[ノートブックの作成]** ダイアログ ボックスでノートブックの名前を入力します。 言語として **[Python]** を選んで、前に作成した Spark クラスターを選びます。

    ![[Create Notebook]\(ノートブックの作成\) ダイアログ ボックスと、言語として Python を選択する場所を示すスクリーンショット。](./media/data-lake-storage-events/new-databricks-notebook.png "Databricks でノートブックを作成する")

    **［作成］** を選択します

### <a name="create-and-populate-a-databricks-delta-table"></a>Databricks Delta テーブルを作成してデータを設定する

1. 作成したノートブックで、次のコード ブロックをコピーして最初のセルに貼り付けます。ただし、このコードはまだ実行しないでください。  

   このコード ブロックの `appId`、`password`、および `tenant` のプレースホルダー値を、このチュートリアルの前提条件を満たすための作業で収集した値に置き換えます。

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    このコードによって、**source_file** という名前のウィジェットが作成されます。 後で、このコードを呼び出し、そのウィジェットにファイル パスを渡す Azure 関数を作成します。  また、このコードは、ストレージ アカウントを使用してサービス プリンシパルを認証し、他のセルで使用する変数をいくつか作成します。

    > [!NOTE]
    > 運用設定では、認証キーを Azure Databricks に格納することを検討してください。 次に、認証キーではなくルック アップ キーをコード ブロックに追加します。 <br><br>たとえば、`spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")` というコード行を使用する代わりに、`spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))` というコード行を使用します。 <br><br>このチュートリアルの完了後、Azure Databricks Web サイトの記事「[Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)」で、このアプローチの例を参照してください。

2. **Shift + Enter** キーを押して、このブロック内のコードを実行します。

3. 次のコード ブロックをコピーして別のセルに貼り付け、**Shift + Enter** キーを押して、このブロックのコードを実行します。

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   このコードは、ストレージ アカウントに Databricks Delta テーブルを作成し、前にアップロードした csv ファイルからいくつかの初期データを読み込みます。

4. このコード ブロックが正常に実行された後、このコード ブロックをノートブックから削除します。

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Databricks Delta テーブルに行を挿入するコードを追加する

1. 次のコード ブロックをコピーして、別のセルに貼り付けます。ただし、このセルは実行しないでください。

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   このコードは、csv ファイルのデータを使用して、一時テーブル ビューにデータを挿入します。 その csv ファイルへのパスは、前の手順で作成した入力ウィジェットから取得されます。

2. 次のコードを追加して、一時テーブル ビューの内容を Databricks Delta テーブルとマージします。

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>ジョブを作成する

先ほど作成したノートブックを実行するジョブを作成します。 後で、イベントが発生したときにこのジョブを実行する Azure 関数を作成します。

1. **[ジョブ]** をクリックします。

2. **[ジョブ]** ページで **[ジョブの作成]** をクリックします。

3. ジョブの名前を指定し、`upsert-order-data` ワークブックを選択します。

   ![ジョブを作成する](./media/data-lake-storage-events/create-spark-job.png "ジョブの作成")

## <a name="create-an-azure-function"></a>Azure Function の作成

ジョブを実行する Azure 関数を作成します。

1. Databricks ワークスペースの上隅で、人のアイコンを選択し、 **[ユーザー設定]** を選択します。

   ![アカウントを管理する](./media/data-lake-storage-events/generate-token.png "ユーザー設定")

2. **[新しいトークンの生成]** をクリックし、 **[生成]** をクリックします。

   トークンは安全な場所にコピーしてください。 Azure 関数では、Databricks に認証してジョブを実行するためにこのトークンが必要です。
  
3. Azure portal の左上隅にある **[リソースの作成]** を選択し、 **[コンピューティング] > [Function App]** の順に選択します。

   ![Azure 関数を作成する](./media/data-lake-storage-events/function-app-create-flow.png "Azure 関数を作成する")

4. Function App の **[作成]** ページで、必ずランタイム スタックに **.NET Core** を選択し、Application Insights インスタンスを構成してください。

   ![関数アプリを構成する](./media/data-lake-storage-events/new-function-app.png "Function App を構成する")

5. Function App の **[概要]** ページで、 **[構成]** をクリックします。

   ![[Configured features]\(構成済みの機能\) の下の [構成] オプションが強調表示されているスクリーンショット。](./media/data-lake-storage-events/configure-function-app.png "Function App を構成する")

6. **[アプリケーションの設定]** ページで、 **[新しいアプリケーション設定]** を選択して各設定を追加します。

   ![構成設定を追加する](./media/data-lake-storage-events/add-application-setting.png "構成設定を追加する")

   以下の設定を追加します。

   |設定名 | Value |
   |----|----|
   |**DBX_INSTANCE**| Databricks ワークスペースのリージョン。 例: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| 前に生成した個人用アクセス トークン。 |
   |**DBX_JOB_ID**|実行中のジョブの識別子。 ここでは、この値を `1` にします。|
7. 関数アプリの概要ページで、 **[新しい関数]** をクリックします。

   ![新しい関数](./media/data-lake-storage-events/new-function.png "新しい関数")

8. **[Azure Event Grid Trigger]\(Azure Event Grid トリガー\)** を選択します。

   **Microsoft.Azure.WebJobs.Extensions.EventGrid** 拡張機能のインストールを求めるメッセージが表示されたら、インストールします。 インストールが必要な場合、関数を作成するには、 **[Azure Event Grid Trigger]\(Azure Event Grid トリガー\)** を再度選択する必要があります。

   **[新しい関数]** ウィンドウが表示されます。

9. **[新しい関数]** ウィンドウで、関数に **UpsertOrder** という名前を付け、 **[作成]** をクリックします。

10. コード ファイルの内容を次のコードに置き換え、 **[保存]** をクリックします。

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   このコードは、発生したストレージ イベントに関する情報を解析し、イベントをトリガーしたファイルの URL を含む要求メッセージを作成します。 この関数は、前に作成した **source_file** ウィジェットに、メッセージの一部として値を渡します。 関数コードは、Databricks ジョブにメッセージを送信し、前に取得したトークンを認証として使用します。

## <a name="create-an-event-grid-subscription"></a>Event Grid のサブスクリプションを作成する

このセクションでは、ファイルがストレージ アカウントにアップロードされたときに Azure 関数を呼び出す Event Grid サブスクリプションを作成します。

1. 関数コードのページで、 **[Event Grid サブスクリプションの追加]** をクリックします。

   ![[Add Event Grid subscription]\(Event Grid サブスクリプションの追加\) ボタンが強調表示されているスクリーンショット。](./media/data-lake-storage-events/new-event-subscription.png "新しいイベント サブスクリプションを追加する")

2. **[イベント サブスクリプションの作成]** ページで、サブスクリプションに名前を指定し、ページ内のフィールドを使用してストレージ アカウントを選択します。

   ![新しいイベント サブスクリプションを追加する](./media/data-lake-storage-events/new-event-subscription-2.png "新しいイベント サブスクリプションを追加する")

3. **[イベントの種類のフィルター]** ボックスの一覧で、 **[Blob Created]\(BLOB の作成\)** および **[Blob Deleted]\(BLOB の削除\)** イベントを選択し、 **[作成]** をクリックします。

## <a name="test-the-event-grid-subscription"></a>Event Grid サブスクリプションをテストする

1. `customer-order.csv` という名前のファイルを作成し、そのファイルに次の情報を貼り付けて、ローカル コンピューターに保存します。

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Storage Explorer で、このファイルを自分のストレージ アカウントの **input** フォルダーにアップロードします。

   ファイルをアップロードすると、**Microsoft.Storage.BlobCreated** イベントが発生します。 Event Grid は、そのイベントのすべてのサブスクライバーに通知を発信します。 ここでは、Azure 関数が唯一のサブスクライバーです。 Azure 関数は、イベント パラメーターを解析して、発生したイベントを特定します。 その後、ファイルの URL を Databricks ジョブに渡します。 Databricks ジョブはファイルを読み取り、ストレージ アカウントにある Databricks Delta テーブルに行を追加します。

3. ジョブが成功したかどうかを確認するには、Databricks ワークスペースを開き、 **[ジョブ]** をクリックして、ジョブを開きます。

4. ジョブのページを開くには、ジョブを選択します。

   ![Spark ジョブ](./media/data-lake-storage-events/spark-job.png "Spark ジョブ")

   ジョブが完了すると、完了状態が表示されます。

   ![正常完了したジョブ](./media/data-lake-storage-events/spark-job-completed.png "正常完了したジョブ")

5. 新しいワークブック セルで、セル内のこのクエリを実行して、更新された Delta テーブルを表示します。

   ```
   %sql select * from customer_data
   ```

   返されるテーブルには、最新のレコードが表示されます。

   ![テーブルに最新のレコードが表示される](./media/data-lake-storage-events/final_query.png "テーブルに最新のレコードが表示される")

6. このレコードを更新するには、`customer-order-update.csv` という名前のファイルを作成し、そのファイルに次の情報を貼り付けて、ローカル コンピューターに保存します。

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   この CSV ファイルは、前出のファイルとほぼ同じものですが、発注数量が `228` から `22` に変更されています。

7. Storage Explorer で、このファイルを自分のストレージ アカウントの **input** フォルダーにアップロードします。

8. `select` クエリを再実行して、更新された Delta テーブルを表示します。

   ```
   %sql select * from customer_data
   ```

   返されるテーブルには、更新後のレコードが表示されます。

   ![テーブルに更新後のレコードが表示される](./media/data-lake-storage-events/final_query-2.png "テーブルに更新後のレコードが表示される")

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。 これを行うには、ストレージ アカウントのリソース グループを選択し、 **[削除]** を選択してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Blob Storage のイベント処理](storage-blob-event-overview.md)