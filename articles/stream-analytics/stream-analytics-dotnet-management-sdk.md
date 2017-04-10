---
title: "Stream Analytics の管理用 .NET SDK | Microsoft Docs"
description: "Stream Analytics 管理用 .NET SDK の使用 分析ジョブの設定と実行方法について: プロジェクト、入力、出力、および変換を作成します。"
keywords: ".net SDK、analytics API"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5e93de87-0c6f-4f4b-be98-08d63f832897
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: b2729dfdb6bf85e6d254b08669eeb259f4ab59d5
ms.lasthandoff: 03/06/2017


---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>管理用 .NET SDK: .NET 用 Azure Stream Analytics API を使用した分析ジョブの設定と実行
管理用 .NET SDK を使用する .NET 用 Stream Analytics API で、分析ジョブを設定して実行する方法について説明します。 プロジェクトの設定、入力と出力ソース、変換の作成、およびジョブの開始と停止を行います。 分析ジョブでは、BLOB ストレージまたはイベント ハブからデータをストリームできます。

[管理用 Stream Analytics API の管理リファレンス ドキュメント](https://msdn.microsoft.com/library/azure/dn889315.aspx)を参照してください。

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、十分に管理されたサービスです。 Stream Analytics により、ユーザーはデータ ストリームを分析するためにストリーミングのジョブを設定でき、ほぼリアルタイムで分析を実行できます。  

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* Visual Studio 2017 または 2015 のインストール。
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)のダウンロードとインストール。
* サブスクリプションに Azure リソース グループを作成する。 次に、サンプルの Azure PowerShell スクリプトを示します。 Azure PowerShell については、「 [Azure PowerShell のインストールおよび構成](/powershell/azureps-cmdlets-docs)」を参照してください。  

        # Log in to your Azure account
        Add-AzureAccount

        # Select the Azure subscription you want to use to create the resource group
        Select-AzureSubscription -SubscriptionName <subscription name>

            # If Stream Analytics has not been registered to the subscription, remove the remark symbol (#) to run the Register-AzureRMProvider cmdlet to register the provider namespace
            #Register-AzureRMProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'

        # Create an Azure resource group
        New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>


* 使用する入力ソースと出力ターゲットを設定します。 詳しい手順については、サンプル入力を設定する場合は「[入力を追加する](stream-analytics-add-inputs.md)」、サンプル出力を設定する場合は「[出力を追加する](stream-analytics-add-outputs.md)」を参照してください。

## <a name="set-up-a-project"></a>プロジェクトの設定
.NET 用 Stream Analytics API を使用して分析ジョブを作成するには、まずプロジェクトを設定します。

1. Visual Studio C# .NET コンソール アプリケーションを作成します。
2. パッケージ マネージャー コンソールで、次のコマンドを実行して NuGet パッケージをインストールします。 1 つ目は、Azure Stream Analytics 管理用 .NET SDK です。 2 つ目は、認証で使用する Azure Active Directory クライアントです。
   
        Install-Package Microsoft.Azure.Management.StreamAnalytics
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
3. 次の **appSettings** セクションを App.config ファイルに追加します。
   
        <appSettings>
          <!--CSM Prod related values-->
          <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
          <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
          <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
          <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
          <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
          <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
          <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
        </appSettings>

    **SubscriptionId** および **ActiveDirectoryTenantId** の値を Azure サブスクリプションとテナント ID に置き換えます。 次の Azure PowerShell コマンドレットを実行すると、これらの値を取得できます。

        Get-AzureAccount

1. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。
   
        using System;
        using System.Configuration;
        using System.Threading.Tasks;
        
        using Microsoft.Azure;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
2. 次の認証ヘルパー メソッドを追加します。

   ```   
   private static async Task<string> GetAuthorizationHeader()
   {
       var context = new AuthenticationContext(
           ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
           ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

        AuthenticationResult result = await context.AcquireTokenASync(
           resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
           clientId: ConfigurationManager.AppSettings["AsaClientId"],
           redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
           promptBehavior: PromptBehavior.Always);

        if (result != null)
            return result.AccessToken;

       throw new InvalidOperationException("Failed to acquire token");
   }
   ```  

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics の管理クライアントの作成
**StreamAnalyticsManagementClient** オブジェクトを使用すると、入力、出力、変換などのジョブおよびジョブ コンポーネントを管理することができます。

**Main** メソッドの先頭に次のコードを追加します。

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
    string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
    string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
    string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
        ConfigurationManager.AppSettings["SubscriptionId"],
        GetAuthorizationHeader().Result);

    // Create Stream Analytics management client
    StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

**resourceGroupName** 変数の値は、前の手順で作成または選択したリソース グループの名前と同じである必要があります。

ジョブの作成で資格情報の提示部分を自動化する場合は、「 [Azure リソース マネージャーでのサービス プリンシパルの認証](../azure-resource-manager/resource-group-authenticate-service-principal.md)」をご覧ください。

この記事の以降のセクションでは、このコードが **Main** メソッドの先頭にあることを前提としています。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成
次のコードは、定義したリソース グループの下に Stream Analytics ジョブを作成します。 ジョブへの入力、出力、変換の追加は後で行います。

    // Create a Stream Analytics job
    JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
    {
        Job = new Job()
        {
            Name = streamAnalyticsJobName,
            Location = "<LOCATION>",
            Properties = new JobProperties()
            {
                EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
                Sku = new Sku()
                {
                    Name = "Standard"
                }
            }
        }
    };

    JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## <a name="create-a-stream-analytics-input-source"></a>Stream Analytics の入力ソースの作成
次のコードは、BLOB 入力ソースの種類と CSV シリアル化が指定された Stream Analytics の入力ソースを作成します。 イベント ハブ入力ソースを作成するには、**BlobStreamInputDataSource** の代わりに **EventHubStreamInputDataSource** を使用します。 同様に、入力ソースのシリアル化の種類もカスタマイズすることができます。

    // Create a Stream Analytics input source
    InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
    {
        Input = new Input()
        {
            Name = streamAnalyticsInputName,
            Properties = new StreamInputProperties()
            {
                Serialization = new CsvSerialization
                {
                    Properties = new CsvSerializationProperties
                    {
                        Encoding = "UTF8",
                        FieldDelimiter = ","
                    }
                },
                DataSource = new BlobStreamInputDataSource
                {
                    Properties = new BlobStreamInputDataSourceProperties
                    {
                        StorageAccounts = new StorageAccount[]
                        {
                            new StorageAccount()
                            {
                                AccountName = "<YOUR STORAGE ACCOUNT NAME>",
                                AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
                            }
                        },
                        Container = "samples",
                        PathPattern = ""
                    }
                }
            }
        }
    };

    InputCreateOrUpdateResponse inputCreateResponse =
        client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

入力ソースは、BLOB ストレージのものであるか、イベント ハブのものであるかに関係なく、特定のジョブに関連付けられます。 1 つの入力ソースを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics の入力ソースのテスト
**TestConnection** メソッドは、Stream Analytics ジョブが入力ソースに接続できるかどうかと、入力ソースの種類別の他の側面についてテストします。 たとえば、前の手順で作成した BLOB 入力ソースの場合、このメソッドでは、ストレージのアカウント名とキーのペアを使用してストレージ アカウントに接続できるかどうか、および、指定されたコンテナーが存在しているかどうかが確認されます。

    // Test input source connection
    DataSourceTestConnectionResponse inputTestResponse =
        client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics の出力ターゲットの作成
出力ターゲットの作成は、Stream Analytics の入力ソースの作成とよく似ています。 入力ソースと同様、出力ターゲットも特定のジョブに関連付けられます。 1 つの出力ターゲットを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。

次のコードで出力ターゲット (Azure SQL データベース) を作成します。 出力ターゲットのデータとシリアル化の種類はカスタマイズできます。

    // Create a Stream Analytics output target
    OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
    {
        Output = new Output()
        {
            Name = streamAnalyticsOutputName,
            Properties = new OutputProperties()
            {
                DataSource = new SqlAzureOutputDataSource()
                {
                    Properties = new SqlAzureOutputDataSourceProperties()
                    {
                        Server = "<YOUR DATABASE SERVER NAME>",
                        Database = "<YOUR DATABASE NAME>",
                        User = "<YOUR DATABASE LOGIN>",
                        Password = "<YOUR DATABASE LOGIN PASSWORD>",
                        Table = "<YOUR DATABASE TABLE NAME>"
                    }
                }
            }
        }
    };

    OutputCreateOrUpdateResponse outputCreateResponse =
        client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics の出力ターゲットのテスト
Stream Analytics の出力ターゲットにも、接続をテストするための **TestConnection** メソッドがあります。

    // Test output target connection
    DataSourceTestConnectionResponse outputTestResponse =
        client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## <a name="create-a-stream-analytics-transformation"></a>Stream Analytics の変換の作成
次のコードでは、"select * from Input" クエリで Stream Analytics の変換を作成し、Stream Analytics ジョブにストリーミング ユニットを&1; つ割り当てるように指定します。 ストリーミング ユニットの調整の詳細については、「 [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」を参照してください。

    // Create a Stream Analytics transformation
    TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
    {
        Transformation = new Transformation()
        {
            Name = streamAnalyticsTransformationName,
            Properties = new TransformationProperties()
            {
                StreamingUnits = 1,
                Query = "select * from Input"
            }
        }
    };

    var transformationCreateResp =
        client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

入力や出力と同様に変換も、その下に作成された特定の Stream Analytics ジョブに関連付けられます。

## <a name="start-a-stream-analytics-job"></a>Stream Analytics ジョブの開始
Stream Analytics ジョブとその入力、出力、変換を作成したら、 **Start** メソッドを呼び出してジョブを開始できます。

次のサンプル コードは、カスタムの出力開始時刻が 2012 年 12 月 12 日 12 時 12 分 12 秒 (UTC) に設定された Stream Analytics ジョブを開始します。

    // Start a Stream Analytics job
    JobStartParameters jobStartParameters = new JobStartParameters
    {
        OutputStartMode = OutputStartMode.CustomTime,
        OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
    };

    LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics ジョブの停止
**Stop** メソッドを呼び出すと、実行中の Stream Analytics ジョブを停止できます。

    // Stop a Stream Analytics job
    LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics ジョブの削除
**Delete** メソッドを実行すると、ジョブと基になるサブリソース (ジョブの入力、出力、変換など) が削除されます。

    // Delete a Stream Analytics job
    LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);

## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
分析ジョブを作成して実行するために .NET SDK を使用する方法の基本を学習できました。 詳細については、次の記事を参照してください。

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 管理用 .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

