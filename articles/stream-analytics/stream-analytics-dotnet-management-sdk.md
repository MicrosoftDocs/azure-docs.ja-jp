---
title: Azure Stream Analytics の管理用 .NET SDK
description: Stream Analytics 管理用 .NET SDK の使用 分析ジョブを設定および実行する方法について説明します。 プロジェクト、入力、出力、および変換を作成します。
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232887"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>管理用 .NET SDK:.NET 用 Azure Stream Analytics API を使用した分析ジョブの設定と実行
管理用 .NET SDK を使用する .NET 用 Stream Analytics API で、分析ジョブを設定して実行する方法について説明します。 プロジェクトの設定、入力と出力ソース、変換の作成、およびジョブの開始と停止を行います。 分析ジョブでは、BLOB ストレージまたはイベント ハブからデータをストリームできます。

[管理用 Stream Analytics API の管理リファレンス ドキュメント](/previous-versions/azure/dn889315(v=azure.100))を参照してください。

Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、フル マネージドのサービスです。 Stream Analytics により、ユーザーはデータ ストリームを分析するためにストリーミングのジョブを設定でき、ほぼリアルタイムで分析を実行できます。  

> [!NOTE]
> この記事では、Azure Stream Analytics 管理用 .NET SDK v2.x バージョンを使用してサンプル コードを更新しました。 レガシ (1.x) バージョンの SDK を使用するサンプル コードについては、「[管理用 .NET SDK v1.x: .NET 用 Azure Stream Analytics API を使用した分析ジョブの設定と実行]()」を参照してください。

## <a name="prerequisites"></a>前提条件
この記事の操作を始める前に、以下の要件を満たしておく必要があります。

* Visual Studio 2019 または 2015 のインストール。
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)のダウンロードとインストール。
* サブスクリプションに Azure リソース グループを作成する。 次の例に、サンプルの Azure PowerShell スクリプトを示します。 Azure PowerShell については、「 [Azure PowerShell のインストールおよび構成](/powershell/azure/)」を参照してください。  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* 接続する入力ソースと出力ターゲットをジョブ用に設定します。

## <a name="set-up-a-project"></a>プロジェクトの設定
.NET 用 Stream Analytics API を使用して分析ジョブを作成するには、まずプロジェクトを設定します。

1. Visual Studio C# .NET コンソール アプリケーションを作成します。
2. パッケージ マネージャー コンソールで、次のコマンドを実行して NuGet パッケージをインストールします。 1 つ目は、Azure Stream Analytics 管理用 .NET SDK です。 2 つ目は、Azure クライアント認証用です。

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. 次の **appSettings** セクションを App.config ファイルに追加します。
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    **SubscriptionId** および **ActiveDirectoryTenantId** の値を Azure サブスクリプションとテナント ID に置き換えます。 次の Azure PowerShell コマンドレットを実行すると、これらの値を取得できます。

   ```powershell
      Get-AzureAccount
   ```

4. .csproj ファイルに次の参照を追加します。

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. 次の **using** ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. 次の認証ヘルパー メソッドを追加します。

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics の管理クライアントの作成
**StreamAnalyticsManagementClient** オブジェクトを使用すると、入力、出力、変換などのジョブおよびジョブ コンポーネントを管理することができます。

**Main** メソッドの先頭に次のコードを追加します。

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

**resourceGroupName** 変数の値は、前の手順で作成または選択したリソース グループの名前と同じである必要があります。

ジョブの作成で資格情報の提示部分を自動化する場合は、「 [Azure リソース マネージャーでのサービス プリンシパルの認証](../active-directory/develop/howto-authenticate-service-principal-powershell.md)」をご覧ください。

この記事の以降のセクションでは、このコードが **Main** メソッドの先頭にあることを前提としています。

## <a name="create-a-stream-analytics-job"></a>Stream Analytics のジョブの作成
次のコードは、定義したリソース グループの下に Stream Analytics ジョブを作成します。 ジョブへの入力、出力、変換の追加は後で行います。

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Stream Analytics の入力ソースの作成
次のコードは、BLOB 入力ソースの種類と CSV シリアル化が指定された Stream Analytics の入力ソースを作成します。 イベント ハブ入力ソースを作成するには、**BlobStreamInputDataSource** の代わりに **EventHubStreamInputDataSource** を使用します。 同様に、入力ソースのシリアル化の種類もカスタマイズすることができます。

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

入力ソースは、BLOB ストレージのものであるか、イベント ハブのものであるかに関係なく、特定のジョブに関連付けられます。 1 つの入力ソースを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics の入力ソースのテスト
**TestConnection** メソッドは、Stream Analytics ジョブが入力ソースに接続できるかどうかと、入力ソースの種類別の他の側面についてテストします。 たとえば、前の手順で作成した BLOB 入力ソースの場合、このメソッドでは、ストレージのアカウント名とキーのペアを使用してストレージ アカウントに接続できるかどうか、および、指定されたコンテナーが存在しているかどうかが確認されます。

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
TestConnection 呼び出しの結果は、次の 2 つのプロパティを含む *ResourceTestResult* オブジェクトになります。

- *status*: ["TestNotAttempted", "TestSucceeded", "TestFailed"] のいずれかの文字列です。
- *error*: 次のプロパティを含む ErrorResponse 型です。
   - *code*: 文字列型の必須プロパティです。 値は、テスト中に受信した、標準の System.Net.HttpStatusCode です。
   - *message*: エラーを表す文字列型の必須プロパティです。 

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics の出力ターゲットの作成
出力ターゲットの作成は、Stream Analytics の入力ソースの作成と似ています。 入力ソースと同様、出力ターゲットも特定のジョブに関連付けられます。 1 つの出力ターゲットを複数のジョブで使用するには、メソッドを再度呼び出して別のジョブ名を指定する必要があります。

次のコードで、出力ターゲット (Azure SQL Database) を作成します。 出力ターゲットのデータとシリアル化の種類はカスタマイズできます。

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics の出力ターゲットのテスト
Stream Analytics の出力ターゲットにも、接続をテストするための **TestConnection** メソッドがあります。

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Stream Analytics の変換の作成
次のコードでは、"select * from Input" クエリで Stream Analytics の変換を作成し、Stream Analytics ジョブにストリーミング ユニットを 1 つ割り当てるように指定します。 ストリーミング ユニットの調整の詳細については、「 [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)」を参照してください。

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

入力や出力と同様に変換も、その下に作成された特定の Stream Analytics ジョブに関連付けられます。

## <a name="start-a-stream-analytics-job"></a>Stream Analytics ジョブの開始
Stream Analytics ジョブとその入力、出力、変換を作成したら、 **Start** メソッドを呼び出してジョブを開始できます。

次のサンプル コードは、カスタムの出力開始時刻が 2012 年 12 月 12 日 12 時 12 分 12 秒 (UTC) に設定された Stream Analytics ジョブを開始します。

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics ジョブの停止
**Stop** メソッドを呼び出すと、実行中の Stream Analytics ジョブを停止できます。

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics ジョブの削除
**Delete** メソッドを実行すると、ジョブと基になるサブリソース (ジョブの入力、出力、変換など) が削除されます。

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>サポートを受ける
詳細については、[Azure Stream Analytics に関する Microsoft Q&A 質問ページ](/answers/topics/azure-stream-analytics.html)を参照してください。

## <a name="next-steps"></a>次のステップ
分析ジョブを作成して実行するために .NET SDK を使用する方法の基本を学習できました。 詳細については、以下の記事をお読みください。

* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 管理用 .NET SDK](/previous-versions/azure/dn889315(v=azure.100))
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
