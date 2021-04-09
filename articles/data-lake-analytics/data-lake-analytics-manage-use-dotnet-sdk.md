---
title: Azure .NET SDK を使用して Azure Data Lake Analytics を管理する
description: この記事では、Azure .NET SDK を使用して、Data Lake Analytics のジョブ、データ ソース、ユーザーを管理するアプリを作成する方法について説明します。
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/18/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a9ece3fe54ca8596f610439f8a0dd228ae1ef907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220909"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Azure Data Lake Analytics .NET アプリを管理する

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

この記事では、Azure .NET SDK を使用して記述されたアプリを使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、ジョブを管理する方法について説明します。 

## <a name="prerequisites"></a>前提条件

* **Visual Studio 2015、Visual Studio 2013 Update 4、または Visual Studio 2012 (Visual C++ インストール済み)** 。
* **Microsoft Azure SDK for .NET バージョン 2.5 以上**。  [Web Platform Installer を使用してインストールします](https://www.microsoft.com/web/downloads/platform.aspx)。
* **必要な NuGet パッケージ**

### <a name="install-nuget-packages"></a>NuGet パッケージのインストール

|Package|Version|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-preview|

これらのパッケージは、NuGet コマンド ラインで次のコマンドを使ってインストールできます。

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>共通の変数

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>認証

Azure Data Lake Analytics にログオンするいくつかのオプションがあります。 次のスニペットは、ポップアップの対話ユーザー認証での認証の例です。

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

**GetCreds_User_Popup** のソース コードおよび認証の他のオプションのコードについては、[Data Lake Analytics .NET の認証オプション](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options)に関する記事をご覧ください


## <a name="create-the-client-management-objects"></a>クライアント管理オブジェクトを作成する

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>アカウントの管理

### <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する

Azure リソース グループをまだ作成していない場合は、Data Lake Analytics コンポーネントを作成する際に必要になります。 認証資格情報、サブスクリプション ID、場所が必要です。 次のコードは、リソース グループの作成方法を示しています。

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

詳細については、「Azure リソース グループと Data Lake Analytics」を参照してください。

### <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する

ADLA アカウントには ADLS アカウントが必要です。 使うものがまだない場合は、次のコードで作成できます。

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する

次のコードは ADLS アカウントを作成します

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Data Lake Store アカウントを一覧表示する

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントを一覧表示します。

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>アカウントが存在するかどうか確認する

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>アカウントに関する情報を取得する

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>アカウントの削除

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントを取得する

すべての Data Lake Analytics アカウントに、既定の Data Lake Store アカウントが必要です。 次のコードを使用して、Analytics アカウントの既定の Store アカウントを決定使用します。

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>データ ソースを管理する

Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage アカウント](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Azure Storage アカウントにリンクする

Azure Storage アカウントへのリンクを作成することができます。

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Azure Storage データ ソースを一覧表示する

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store データ ソースを一覧表示する

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>フォルダーとファイルのアップロードとダウンロード

Data Lake Store ファイル システムのクライアント管理オブジェクトを使用して、Azure からローカル コンピューターに次のメソッドを使用してファイルやフォルダーをアップロードおよびダウンロードできます。

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

これらのメソッドの最初のパラメーターは Data Lake Store のアカウント名で、次にソース パスと対象のパスのパラメーターが続きます。

次の例では、Data Lake Store 内のフォルダーをダウンロードする方法を示します。

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store アカウントにファイルを作成する

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Azure ストレージ アカウント パスを確認する

次のコードは、Data Lake Analytics アカウント (analyticsAccountName) 内に Azure ストレージ アカウント (storageAccntName) が存在するかどうかと、その Azure ストレージ アカウント内にコンテナー (containerName) が存在するかどうかを確認します。

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>カタログとジョブを管理する

DataLakeAnalyticsCatalogManagementClient オブジェクトは、各 Azure Data Lake Analytics アカウントに提供された SQL Database を管理するための方法を提供します。 DataLakeAnalyticsJobManagementClient は、U-SQL スクリプトを使用してデータベースで実行されるジョブを送信および管理する方法を提供します。

### <a name="list-databases-and-schemas"></a>データベースとスキーマを一覧表示する

一覧表示できる対象の中で、最も一般的なものはデータベースとスキーマです。 次のコードは、データベースのコレクションを取得し、各データベースのスキーマを列挙します。

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>テーブルの列を一覧表示する

次のコードでは、Data Lake Analytics カタログ管理クライアントを使用してデータベースにアクセスして、指定したテーブルの列を一覧表示する方法を示します。

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>U-SQL ジョブの送信

次のコードは、Data Lake Analytics のジョブ管理クライアントを使用してジョブを送信する方法を示しています。

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>失敗したジョブを一覧表示する

次のコードは、失敗したジョブに関する情報を一覧表示します。

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>パイプラインを一覧表示する

次のコードは、アカウントに送信されたジョブの各パイプラインに関する情報を一覧表示します。

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>反復を一覧表示する

次のコードは、アカウントに送信されたジョブの各反復に関する情報を一覧表示します。

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>一般的なグラフ シナリオ

### <a name="look-up-user-in-the-aad-directory"></a>AAD ディレクトリでユーザーを検索する

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>AAD ディレクトリでユーザーの ObjectId を取得する

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>コンピューティング ポリシーを管理する

DataLakeAnalyticsAccountManagementClient オブジェクトでは、Data Lake Analytics アカウントのコンピューティング ポリシーを管理するためのメソッドが提供されています。

### <a name="list-compute-policies"></a>コンピューティング ポリシーを一覧表示する

次のコードは、Data Lake Analytics アカウントのコンピューティング ポリシーの一覧を取得します。

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>新しいコンピューティング ポリシーを作成する

次のコードは、Data Lake Analytics アカウントの新しいコンピューティング ポリシーを作成し、指定したユーザーが使用できる最大 AU を 50 に、最小ジョブ優先順位を 250 に設定します。

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>次のステップ

* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
* [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)