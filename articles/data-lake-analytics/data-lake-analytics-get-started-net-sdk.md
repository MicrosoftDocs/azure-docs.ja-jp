---
title: ".NET SDK で Azure Data Lake Analytics の使用を開始する | Microsoft Docs"
description: ".NET SDK を使用して、Data Lake Analytics アカウントを作成し、Data Lake Analytics ジョブを作成してから、U-SQL で記述されたジョブを送信する方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 1dfcbc3d-235d-4074-bc2a-e96def8298b6
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/26/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 8e092e30c9c4186e4687efeacf9ea1f6b4bf431c
ms.openlocfilehash: f617d997bc34d39f7635a87c4e5c88b1ebdc0ff8


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-net-sdk"></a>チュートリアル: .NET SDK で Azure Data Lake Analytics の使用を開始する
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Azure .NET SDK を使用して、 [U-SQL](data-lake-analytics-u-sql-get-started.md) で記述されたジョブを Data Lake Analytics に送信する方法について説明します。 Data Lake Analytics の詳細については、「 [Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

このチュートリアルでは、U-SQL ジョブを送信する C# コンソール アプリケーションを開発します。このジョブでは、タブ区切り値 (TSV) ファイルが読み取られ、それがコンマ区切り値 (CSV) ファイルに変換されます。 サポートされている他のツールを使用した同じチュートリアルを読み進めるには、この記事の上部にあるタブをクリックします。

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Visual Studio 2015、Visual Studio 2013 update 4、または Visual Studio 2012 (Visual C++ インストール済み)**。
* **Microsoft Azure SDK for .NET バージョン 2.5 以上**。  [Web Platform Installer を使用してインストールします](http://www.microsoft.com/web/downloads/platform.aspx)。
* **Azure Data Lake Analytics アカウント**。 「 [Manage Data Lake Analytics using Azure .NET SDK (Azure .NET SDK を使用した Data Lake Analytics の管理)](data-lake-analytics-manage-use-dotnet-sdk.md)」を参照してください。

## <a name="create-console-application"></a>コンソール アプリケーションの作成
このチュートリアルでは、いくつかの検索ログを処理します。  検索ログは、Data Lake Store または Azure Blob Storage に格納できます。 

サンプルの検索ログは、パブリック Azure BLOB コンテナーにあります。 アプリケーションで、ファイルをワークステーションにダウンロードしてから、Data Lake Analytics アカウントの既定の Data Lake Store アカウントにそのファイルをアップロードします。

**U-SQL スクリプトを作成するには**

Data Lake Analtyics ジョブは U-SQL 言語で記述されます。 U-SQL の詳細については、[U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関する記事および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。

次の U-SQL スクリプトを使用して **SampleUSQLScript.txt** ファイルを作成し、**C:\temp\** パスにファイルを配置します。  このパスは、次の手順で作成する .NET アプリケーションでハードコーディングされます。  

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();

この U-SQL スクリプトでは、**Extractors.Tsv()** を使用してソース データ ファイルを読み取ってから、**Outputters.Csv()** を使用して csv ファイルを作成します。 

C# プログラムでは、**/Samples/Data/SearchLog.tsv** ファイルと **/Output/** フォルダーを準備する必要があります。    

既定の Data Lake アカウントに格納されたファイルの相対パスを使用する方が簡単です。 絶対パスを使用することもできます。  たとえば、次のように入力します。 

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

リンクされたストレージ アカウント内のファイルへのアクセスには、絶対パスを使用する必要があります。  リンクされた Azure Storage アカウントに格納されているファイルの構文は次のとおりです。

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

> [!NOTE]
> 現時点で、Azure Data Lake Service の既知の問題があります。  サンプル アプリケーションが中断されたり、エラーが発生した場合は、スクリプトによって作成された Data Lake Store と Data Lake Analytics のアカウントを手動で削除することが必要になる場合があります。  Azure Portal に慣れていない場合は、「[Azure ポータルを使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」から始めることをお勧めします。       
> 
> 

**アプリケーションを作成するには**

1. Visual Studio を開きます。
2. C# コンソール アプリケーションを作成します。
3. NuGet パッケージ管理コンソールを開き、次のコマンドを実行します。
   
        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage
4. Program.cs では、次のコードを貼り付けます。
   
        using System;
        using System.IO;
        using System.Collections.Generic;
        using System.Threading;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
        using Microsoft.Azure.Management.DataLake.Analytics;
        using Microsoft.Azure.Management.DataLake.Analytics.Models;
        using Microsoft.WindowsAzure.Storage.Blob;
   
        namespace SdkSample
        {
          class Program
          {
            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
   
            private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
            private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";
   
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
   
            private static void Main(string[] args)
            {
                string localFolderPath = @"c:\temp\";
   
                // Connect to Azure
                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);
   
                SetupClients(creds, SUBSCRIPTIONID);
   
                // Transfer the source file from a public Azure Blob container to Data Lake Store.
                CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
                blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
                UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
                WaitForNewline("Source data file prepared.", "Submitting a job.");

                // Submit the job
                Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
                WaitForNewline("Job submitted.", "Waiting for job completion.");

                // Wait for job completion
                WaitForJob(jobId);
                WaitForNewline("Job completed.", "Downloading job output.");

                // Download job output
                DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");

                  WaitForNewline("Job output downloaded. You can now exit.");
            }

            public static ServiceClientCredentials AuthenticateAzure(
                string domainName,
                string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
            {
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
                _adlaClient.SubscriptionId = subscriptionId;

                _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
            }

            public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
            {
                var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
                var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
                var uploader = new DataLakeStoreUploader(parameters, frontend);
                uploader.Execute();
            }

            public static void DownloadFile(string srcPath, string destPath)
            {
                var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
                var fileStream = new FileStream(destPath, FileMode.Create);

                stream.CopyTo(fileStream);
                fileStream.Close();
                stream.Close();
            }

            // Helper function to show status and wait for user input
            public static void WaitForNewline(string reason, string nextAction = "")
            {
                Console.WriteLine(reason + "\r\nPress ENTER to continue...");

                Console.ReadLine();

                if (!String.IsNullOrWhiteSpace(nextAction))
                    Console.WriteLine(nextAction);
            }

            // List all Data Lake Analytics accounts within the subscription
            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
                for (int i = 0; i < accounts.Count; i++)
                {
                    Console.WriteLine(accounts[i].Name);
                }

                return accounts;
            }

            public static Guid SubmitJobByPath(string scriptPath, string jobName)
            {
                var script = File.ReadAllText(scriptPath);

                var jobId = Guid.NewGuid();
                var properties = new USqlJobProperties(script);
                var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
                var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

                return jobId;
            }

            public static JobResult WaitForJob(Guid jobId)
            {
                var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                while (jobInfo.State != JobState.Ended)
                {
                    jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
                }
                return jobInfo.Result.Value;
            }
          }
        }

5. **F5** キーを押してアプリケーションを実行します。 出力は次のようになります。
   
    ![Azure Data Lake Analytics job U-SQL .NET SDK output](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)
6. 出力ファイルをチェックします。  既定のパスとファイル名は、c:\Temp\SearchLog-from-Data-Lake.csv です。

## <a name="see-also"></a>関連項目
* 他のツールを使用する同じチュートリアルを表示するには、ページの上部にあるタブ セレクターをクリックします。
* より複雑なクエリを表示する場合は、「 [チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する](data-lake-analytics-analyze-weblogs.md)」をご覧ください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「[チュートリアル: Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」および「[U-SQL Language Reference (U-SQL 言語リファレンス)](http://go.microsoft.com/fwlink/?LinkId=691348)」をご覧ください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。




<!--HONumber=Nov16_HO3-->


