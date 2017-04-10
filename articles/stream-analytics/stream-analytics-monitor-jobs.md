---
title: "Stream Analytics 上のジョブをプログラムで監視する | Microsoft Docs"
description: "REST API、Azure SDK、または Powershell を介して作成された Stream Analytics ジョブをプログラムで監視する方法の詳細について説明します。"
keywords: ".net モニター、ジョブ モニター、監視アプリ"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 2ec02cc9-4ca5-4a25-ae60-c44be9ad4835
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 37797743df0e2ce029b65f267a7cea5c8d793773
ms.lasthandoff: 03/06/2017


---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Stream Analytics ジョブ モニターをプログラムで作成する
 この記事では、Stream Analytics ジョブの監視を有効にする方法を示します。 REST API、Azure SDK、または Powershell を介して作成された Stream Analytics ジョブは、既定では監視は有効になっていません。  Azure ポータルで、ジョブの [監視] ページに移動し、[有効にする] ボタンをクリックして、手動で監視を有効にできます。または、この記事にある手順に従って、有効にするプロセスを自動化することもできます。 監視データは、Stream Analytics ジョブ用の Azure ポータルの [監視] タブに表示されます。

![ジョブ モニター、[ジョブ] タブ](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* Visual Studio 2017 または 2015。
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)のダウンロードとインストール。
* 監視を有効にする必要がある、既存の Stream Analytics ジョブ。

## <a name="setup-a-project"></a>プロジェクトのセットアップ
1. Visual Studio C# .Net コンソール アプリケーションを作成します。
2. パッケージ マネージャー コンソールで、次のコマンドを実行して NuGet パッケージをインストールします。 1 つ目は、Azure Stream Analytics 管理用 .NET SDK です。 2 つ目は、監視を有効にするために使用される Azure Monitor SDK です。 最後の&1; つは、認証で使用する Azure Active Directory クライアントです。
   
   ```
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. 次の appSettings セクションを App.config ファイルに追加します。
   
   ```
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   *SubscriptionId* および *ActiveDirectoryTenantId* の値を Azure サブスクリプションとテナント ID に置き換えます。 次の PowerShell コマンドレットを実行すると、これらの値を取得できます。
   
   ```
   Get-AzureAccount
   ```
4. 次の using ステートメントをプロジェクト内のソース ファイル (Program.cs) に追加します。
   
   ```
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. 認証ヘルパー メソッドを追加します。
   
     public static string GetAuthorizationHeader()
   
         {
             AuthenticationResult result = null;
             var thread = new Thread(() =>
             {
                 try
                 {
                     var context = new AuthenticationContext(
                         ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                         ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
   
                     result = context.AcquireToken(
                         resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                         clientId: ConfigurationManager.AppSettings["AsaClientId"],
                         redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                         promptBehavior: PromptBehavior.Always);
                 }
                 catch (Exception threadEx)
                 {
                     Console.WriteLine(threadEx.Message);
                 }
             });
   
             thread.SetApartmentState(ApartmentState.STA);
             thread.Name = "AcquireTokenThread";
             thread.Start();
             thread.Join();
   
             if (result != null)
             {
                 return result.AccessToken;
             }
   
             throw new InvalidOperationException("Failed to acquire token");
     }

## <a name="create-management-clients"></a>管理クライアントの作成
次のコードは、必要な変数と管理クライアントをセットアップします。

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>既存の Stream Analytics ジョブに対する監視の有効化
次のコードは、 **既存の** Stream Analytics ジョブに対して監視を有効にします。 コードの最初の部分では、Stream Analytics サービスに対して GET 要求を実行して、特定の Stream Analytics ジョブに関する情報を取得します。 コードの後半部分では、GET 要求で取得した "Id" プロパティをパラメーターとして Put メソッドが Insights サービスに送信され、Stream Analytics ジョブの監視を有効にします。

> [!WARNING]
> Azure Portal から、または次のコードを使用してプログラムにより、別の Stream Analytics ジョブの監視を有効にしている場合、 **前に監視を有効にしたときと同じストレージ アカウント名を指定することをお勧めします。**
> 
> ストレージ アカウントは、特定のジョブ自体ではなく、Stream Analytics ジョブを作成したリージョンに関連付けられます。
> 
> 同じリージョン内のすべての Stream Analytics ジョブ (その他のすべての Azure リソースを含む) で、このストレージ アカウントを共有して監視データを格納します。 別のストレージ アカウントを指定すると、他の Stream Analytics ジョブやその他の Azure リソースの監視に意図しない副作用が発生することがあります。
> 
> 次の ```“<YOUR STORAGE ACCOUNT NAME>”``` の置き換えに使用するストレージ アカウント名は、監視を有効にする Stream Analytics ジョブと同じサブスクリプション内にあるストレージ アカウントにする必要があります。
> 
> 

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
        PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
            Properties = new ServiceDiagnosticSettings()
            {
                StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
            }
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## <a name="get-support"></a>サポートを受ける
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


