---
title: "サンプル: Azure Application Insights からエクスポートされたデータの解析 | Microsoft Docs"
description: "連続エクスポート機能を使用して、Application Insights でテレメトリの独自の分析をコーディングします。 データを SQL に保存します。"
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 3ffb62b6-3fe9-455d-a260-b2a0201b5ecd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: fedd078402bbd220bce9b71cd035508d46f92f82


---
# <a name="code-sample-parse-data-exported-from-application-insights"></a>コード サンプル: Application Insights からエクスポートされたデータの解析
この記事では、[連続エクスポート][export]を使用して [Azure Application Insights][start] からエクスポートされたデータを処理するコードを記述する方法を示します。 連続エクスポートは JSON 形式でテレメトリを Azure Storage に移動するため、コードを記述し、JSON オブジェクトを解析してデータベース テーブルに行を作成します。

例として、テレメトリ データを Application Insights から SQL データベースに移動するコードを記述します。

開始する前に、次のことに注意してください。

* エクスポートされたデータをデータベースに転送するより効率的な方法は [Stream Analytics の使用](app-insights-code-sample-export-sql-stream-analytics.md)ですが、ここでの目的はエクスポートされたデータを処理するコードを示すことです。 エクスポートされたテレメトリで他の処理を行うように、このコード サンプルを適合させることもできます。
* この例では、Azure worker ロールでコードを実行することにより、データを Azure データベースに移動します。 ただし、オンプレミス サーバーで実行されるようにこのコードを調整して、データをオンプレミス SQL サーバーにプルすることもできます。
* エクスポートなしに Application Insights で[テレメトリに直接アクセスするコードを記述](http://dev.applicationinsights.io/)できます。

Application Insights による Web アプリケーションの監視をまだ開始していない場合は、[今すぐ行います][start]。



## <a name="create-storage-in-azure"></a>Azure でのストレージの作成
Application Insights のデータは、常に JSON 形式で Azure ストレージ アカウントにエクスポートされます。 コードでは、このストレージからデータを読み取ります。

1. [Azure Portal][portal] で、サブスクリプションの "クラシック" ストレージ アカウントを作成します。
   
    ![Azure ポータルで、[新規]、[データ]、[Storage] の順に選択します](./media/app-insights-code-sample-export-telemetry-sql-database/040-store.png)
2. コンテナーを作成する
   
    ![新しいストレージで、[コンテナー] を選択し、[コンテナー] タイルをクリックし、[追加] を選択します](./media/app-insights-code-sample-export-telemetry-sql-database/050-container.png)

## <a name="start-continuous-export-to-azure-storage"></a>Azure ストレージへの連続エクスポートの開始
1. Azure ポータルで、アプリケーション用に作成した Application Insights リソースを参照します。
   
    ![[参照]、[Application Insights]、アプリケーションの順に選択します](./media/app-insights-code-sample-export-telemetry-sql-database/060-browse.png)
2. 連続エクスポートを作成します。
   
    ![[設定]、[連続エクスポート]、[追加] の順に選択します](./media/app-insights-code-sample-export-telemetry-sql-database/070-export.png)

    以前に作成したストレージ アカウントを選択します。

    ![エクスポート先の設定](./media/app-insights-code-sample-export-telemetry-sql-database/080-add.png)

    表示するイベントの種類を設定します。

    ![イベントの種類の選択](./media/app-insights-code-sample-export-telemetry-sql-database/085-types.png)

1. データを蓄積します。 しばらく待機し、ユーザーにアプリケーションを使用してもらいます。 テレメトリが開始し、統計グラフが[メトリックス エクスプローラー](app-insights-metrics-explorer.md)に表示され、個々のイベントが[診断検索](app-insights-diagnostic-search.md)に表示されます。 
   
    また、データはストレージにもエクスポートされます。 
2. エクスポートされたデータを検査します。 Visual Studio で、**[表示]、[Cloud Explorer]** の順に選びます。[Azure]、[Storage] の順に開きます  (このメニュー オプションがない場合は、Azure SDK をインストールする必要があります。[新しいプロジェクト] ダイアログを開き、[Visual c#]、[クラウド]、[Microsoft Azure SDK for .NET の取得] の順に開きます)。
   
    ![Visual Studio で次の順に開きます。[サーバー ブラウザー]、[Azure]、[Storage]](./media/app-insights-code-sample-export-telemetry-sql-database/087-explorer.png)
   
    パス名の共通部分を書き留めます。共通部分はアプリケーションの名前とインストルメンテーション キーから派生します。 

イベントが JSON 形式で BLOB ファイルに書き込まれます。 各ファイルに&1; つ以上のイベントが含まれる場合があります。 このため、イベント データを読み取って必要なフィールドをフィルター処理します。 データの処理に関して行えることはありますが、今日の計画は、データを SQL データベースに移動するコードを記述することです。 それにより、興味深い多くのクエリを実行しやすくなります。

## <a name="create-an-azure-sql-database"></a>Azure SQL Database の作成
この例では、データベースにデータをプッシュするコードを記述します。

再び、[Azure Portal][portal] 内のサブスクリプションから、データを書き込むデータベース (および、まだ存在しない場合は新しいサーバー) を作成します。

![[新規]、[データ]、[SQL]](./media/app-insights-code-sample-export-telemetry-sql-database/090-sql.png)

データベース サーバーに Azure サービスがアクセス可能であることをご確認ください。

![[参照]、[サーバー]、[使用するサーバー]、[設定]、[ファイアウォール]、[Azure へのアクセスの許可]](./media/app-insights-code-sample-export-telemetry-sql-database/100-sqlaccess.png)

## <a name="create-a-worker-role"></a>worker ロールを作成する
エクスポートされた BLOB で JSON を解析する [コード](https://sesitai.codeplex.com/) を記述し、データベースにレコードを作成することができるようになりました。 エクスポート ストアとデータベースはどちらも Azure にあるため、コードを Azure worker ロールで実行します。

このコードでは、JSON に存在するすべてのプロパティを自動的に抽出します。 各プロパティの詳細については、「 [Application Insights エクスポート データ モデル](app-insights-export-data-model.md)」をご覧ください。

#### <a name="create-worker-role-project"></a>worker ロール プロジェクトの作成
Visual Studio で、worker ロールの新しいプロジェクトを作成します。

![[新しいプロジェクト]、[Visual C#]、[クラウド]、[Azure Cloud Service]](./media/app-insights-code-sample-export-telemetry-sql-database/110-cloud.png)

![[新しいクラウド サービス] ダイアログで、[Visual C#]、[worker ロール] の順に選択します](./media/app-insights-code-sample-export-telemetry-sql-database/120-worker.png)

#### <a name="connect-to-the-storage-account"></a>ストレージ アカウントへの接続
Azure で、ストレージ アカウントから接続文字列を取得します。

![[ストレージ アカウント] で、[キー] を選択し、プライマリ接続文字列をコピーします](./media/app-insights-code-sample-export-telemetry-sql-database/055-get-connection.png)

Visual Studio で、ストレージ アカウントの接続文字列で worker ロール設定を構成します。

![ソリューション エクスプローラーの [Cloud Service] プロジェクトの下の [ロール] を展開し、worker ロールを開きます。 [設定] タブを開き、[設定の追加] を選択し、name=StorageConnectionString、type= 接続文字列と設定し、クリックして値を設定します。 これを手動で設定し、接続文字列を貼り付けます。](./media/app-insights-code-sample-export-telemetry-sql-database/130-connection-string.png)

#### <a name="packages"></a>パッケージ
ソリューション エクスプローラーで worker ロール プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
それらのパッケージを検索してインストールします。 

* EntityFramework 6.1.2 以降 - これを使用して、BLOB の JSON の内容に基づいて DB テーブル スキーマをその場で生成します。
* JsonFx - JSON を C# クラスのプロパティにフラット化するためにこれを使用します。

このツールを使用して、単一の JSON ドキュメントから C# クラスを生成します。 これにはわずかな変更が必要になります。 urlData_port) 

* [JSON C# クラス ジェネレーター](http://jsonclassgenerator.codeplex.com/)

## <a name="code"></a>コード
このコードを `WorkerRole.cs`に入れることができます。

#### <a name="imports"></a>インポートする
    using Microsoft.WindowsAzure.Storage;

    using Microsoft.WindowsAzure.Storage.Blob;

#### <a name="retrieve-the-storage-connection-string"></a>ストレージ接続文字列を取得する
    private static string GetConnectionString()
    {
      return Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
    }

#### <a name="run-the-worker-at-regular-intervals"></a>一定の間隔で worker を実行する
既存の実行メソッドを置換し、必要に応じて間隔を選択します。 これは少なくとも&1; 時間とします。エクスポート機能は&1; つの JSON オブジェクトを&1; 時間以内で完了するからです。

    public override void Run()
    {
      Trace.TraceInformation("WorkerRole1 is running");

      while (true)
      {
        Trace.WriteLine("Sleeping", "Information");

        Thread.Sleep(86400000); //86400000=24 hours //1 hour=3600000

        Trace.WriteLine("Awake", "Information");

        ImportBlobtoDB();
      }
    }

#### <a name="insert-each-json-object-as-a-table-row"></a>各 JSON オブジェクトをテーブル行として挿入する
    public void ImportBlobtoDB()
    {
      try
      {
        CloudStorageAccount account = CloudStorageAccount.Parse(GetConnectionString());

        var blobClient = account.CreateCloudBlobClient();
        var container = blobClient.GetContainerReference(FilterContainer);

        foreach (CloudBlobDirectory directory in container.ListBlobs())//Parent directory
        {
          foreach (CloudBlobDirectory subDirectory in directory.ListBlobs())//PageViewPerformance
             {
            foreach (CloudBlobDirectory dir in subDirectory.ListBlobs())//2015-01-31
            {
              foreach (CloudBlobDirectory subdir in dir.ListBlobs())//22
              {
                foreach (IListBlobItem item in subdir.ListBlobs())//3IAwm6u3-0.blob
                {
                  itemname = item.Uri.ToString();
                  ParseEachBlob(container, item);
                  AuditBlob(container, directory, subDirectory, dir, subdir, item);
                } //item loop
              } //subdir loop
            } //dir loop
          } //subDirectory loop
        } //directory loop
      }
      catch (Exception ex)
      {
        //handle exception
      }
    }

#### <a name="parse-each-blob"></a>各 BLOB を解析する
    private void ParseEachBlob(CloudBlobContainer container, IListBlobItem item)
    {
      try
      {
        var blob = container.GetBlockBlobReference(item.Parent.Prefix + item.Uri.Segments.Last());

        string json;

        using (var memoryStream = new MemoryStream())
        {
          blob.DownloadToStream(memoryStream);
          json = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());

          IEnumerable<string> entities = json.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));

          recCount = entities.Count();
          failureCount = 0; //resetting failure count

          foreach (var entity in entities)
          {
            var reader = new JsonFx.Json.JsonReader();
            dynamic output = reader.Read(entity);

            Dictionary<string, object> dict = new Dictionary<string, object>();

            GenerateDictionary((System.Dynamic.ExpandoObject)output, dict, "");

            switch (FilterType)
            {
              case "PageViewPerformance":

              if (dict.ContainsKey("clientPerformance"))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["clientPerformance"])[0], dict, "");
                }

              if (dict.ContainsKey("context_custom_dimensions"))
              {
                if (dict["context_custom_dimensions"].GetType() == typeof(System.Dynamic.ExpandoObject[]))
                {
                  GenerateDictionary(((System.Dynamic.ExpandoObject[])dict["context_custom_dimensions"])[0], dict, "");
                }
              }

            PageViewPerformance objPageViewPerformance = (PageViewPerformance)GetObject(dict);

            try
            {
              using (var db = new TelemetryContext())
              {
                db.PageViewPerformanceContext.Add(objPageViewPerformance);
                db.SaveChanges();
              }
            }
            catch (Exception ex)
            {
              failureCount++;
            }
            break;

            default:
            break;
          }
        }
      }
    }
    catch (Exception ex)
    {
      //handle exception 
    }
    }

#### <a name="prepare-a-dictionary-for-each-json-document"></a>各 JSON ドキュメント用にディクショナリを準備する
    private void GenerateDictionary(System.Dynamic.ExpandoObject output, Dictionary<string, object> dict, string parent)
        {
            try
            {
                foreach (var v in output)
                {
                    string key = parent + v.Key;
                    object o = v.Value;

                    if (o.GetType() == typeof(System.Dynamic.ExpandoObject))
                    {
                        GenerateDictionary((System.Dynamic.ExpandoObject)o, dict, key + "_");
                    }
                    else
                    {
                        if (!dict.ContainsKey(key))
                        {
                            dict.Add(key, o);
                        }
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }
        }

#### <a name="cast-the-json-document-into-c-class-telemetry-object-properties"></a>JSON ドキュメントを C# クラスのテレメトリ オブジェクト プロパティにキャストする
     public object GetObject(IDictionary<string, object> d)
        {
            PropertyInfo[] props = null;
            object res = null;

            try
            {
                switch (FilterType)
                {
                    case "PageViewPerformance":

                        props = typeof(PageViewPerformance).GetProperties();
                        res = Activator.CreateInstance<PageViewPerformance>();
                        break;

                    default:
                        break;
                }

                for (int i = 0; i < props.Length; i++)
                {
                    if (props[i].CanWrite && d.ContainsKey(props[i].Name))
                    {
                        props[i].SetValue(res, d[props[i].Name], null);
                    }
                }
            }
            catch (Exception ex)
            {
              //handle exception 
            }

            return res;
        }

#### <a name="pageviewperformance-class-file-generated-out-of-json-document"></a>JSON ドキュメントから生成された PageViewPerformance クラス ファイル
    public class PageViewPerformance
    {
        [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
        public Guid Id { get; set; }

        public string url { get; set; }

        public int urlData_port { get; set; }

        public string urlData_protocol { get; set; }

        public string urlData_host { get; set; }

        public string urlData_base { get; set; }

        public string urlData_hashTag { get; set; }

        public double total_value { get; set; }

        public double networkConnection_value { get; set; }

        public double sendRequest_value { get; set; }

        public double receiveRequest_value { get; set; }

        public double clientProcess_value { get; set; }

        public string name { get; set; }

        public string internal_data_id { get; set; }

        public string internal_data_documentVersion { get; set; }

        public DateTime? context_data_eventTime { get; set; }

        public string context_device_id { get; set; }

        public string context_device_type { get; set; }

        public string context_device_os { get; set; }

        public string context_device_osVersion { get; set; }

        public string context_device_locale { get; set; }

        public string context_device_userAgent { get; set; }

        public string context_device_browser { get; set; }

        public string context_device_browserVersion { get; set; }

        public string context_device_screenResolution_value { get; set; }

        public string context_user_anonId { get; set; }

        public string context_user_anonAcquisitionDate { get; set; }

        public string context_user_authAcquisitionDate { get; set; }

        public string context_user_accountAcquisitionDate { get; set; }

        public string context_session_id { get; set; }

        public bool context_session_isFirst { get; set; }

        public string context_operation_id { get; set; }

        public double context_location_point_lat { get; set; }

        public double context_location_point_lon { get; set; }

        public string context_location_clientip { get; set; }

        public string context_location_continent { get; set; }

        public string context_location_country { get; set; }

        public string context_location_province { get; set; }

        public string context_location_city { get; set; }
    }


#### <a name="dbcontext-for-sql-interaction-by-entity-framework"></a>Entity Framework による SQL 操作用の DBcontext
    public class TelemetryContext : DbContext
    {
        public DbSet<PageViewPerformance> PageViewPerformanceContext { get; set; }
        public TelemetryContext()
            : base("name=TelemetryContext")
        {
        }
    }

`TelemetryContext` という名前の DB 接続文字列を `app.config` に追加します。

## <a name="schema-information-only"></a>スキーマ (情報のみ)
これは、PageView で生成されるテーブルのスキーマです。

> [!NOTE]
> このスクリプトを実行する必要はありません。 JSON の属性によって、テーブルの列が決まります。
> 
> 

    CREATE TABLE [dbo].[PageViewPerformances](
    [Id] [uniqueidentifier] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlData_port] [int] NOT NULL,
    [urlData_protocol] [nvarchar](max) NULL,
    [urlData_host] [nvarchar](max) NULL,
    [urlData_base] [nvarchar](max) NULL,
    [urlData_hashTag] [nvarchar](max) NULL,
    [total_value] [float] NOT NULL,
    [networkConnection_value] [float] NOT NULL,
    [sendRequest_value] [float] NOT NULL,
    [receiveRequest_value] [float] NOT NULL,
    [clientProcess_value] [float] NOT NULL,
    [name] [nvarchar](max) NULL,
    [User] [nvarchar](max) NULL,
    [internal_data_id] [nvarchar](max) NULL,
    [internal_data_documentVersion] [nvarchar](max) NULL,
    [context_data_eventTime] [datetime] NULL,
    [context_device_id] [nvarchar](max) NULL,
    [context_device_type] [nvarchar](max) NULL,
    [context_device_os] [nvarchar](max) NULL,
    [context_device_osVersion] [nvarchar](max) NULL,
    [context_device_locale] [nvarchar](max) NULL,
    [context_device_userAgent] [nvarchar](max) NULL,
    [context_device_browser] [nvarchar](max) NULL,
    [context_device_browserVersion] [nvarchar](max) NULL,
    [context_device_screenResolution_value] [nvarchar](max) NULL,
    [context_user_anonId] [nvarchar](max) NULL,
    [context_user_anonAcquisitionDate] [nvarchar](max) NULL,
    [context_user_authAcquisitionDate] [nvarchar](max) NULL,
    [context_user_accountAcquisitionDate] [nvarchar](max) NULL,
    [context_session_id] [nvarchar](max) NULL,
    [context_session_isFirst] [bit] NOT NULL,
    [context_operation_id] [nvarchar](max) NULL,
    [context_location_point_lat] [float] NOT NULL,
    [context_location_point_lon] [float] NOT NULL,
    [context_location_clientip] [nvarchar](max) NULL,
    [context_location_continent] [nvarchar](max) NULL,
    [context_location_country] [nvarchar](max) NULL,
    [context_location_province] [nvarchar](max) NULL,
    [context_location_city] [nvarchar](max) NULL,
    CONSTRAINT [PK_dbo.PageViewPerformances] PRIMARY KEY CLUSTERED 
    (
     [Id] ASC
    )WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]

    GO

    ALTER TABLE [dbo].[PageViewPerformances] ADD  DEFAULT (newsequentialid()) FOR [Id]
    GO


この例の動作を確認するには、完全な作業コードを[ダウンロード](https://sesitai.codeplex.com/)し、`app.config` の設定を変更して、worker ロールを Azure に発行します。

## <a name="next-steps"></a>次のステップ

* [テレメトリに直接アクセスするコードを記述する](http://dev.applicationinsights.io/)
* [worker ロールを使用して SQL にエクスポートする](app-insights-code-sample-export-telemetry-sql-database.md)
* [Application Insights での連続エクスポート](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)
* [データのエクスポート モデル](app-insights-export-data-model.md)
* [その他のサンプルとチュートリアル](app-insights-code-samples.md)


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md





<!--HONumber=Jan17_HO4-->


