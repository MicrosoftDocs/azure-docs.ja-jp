---
title: "Azure .NET SDK を使用して Azure Data Lake Analytics を管理する | Microsoft Docs"
description: "Data Lake Analytics のジョブ、データ ソース、ユーザーの管理方法について説明します。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Azure .NET SDK を使用して Azure Data Lake Analytics を管理する
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Azure .NET SDK を使用して、Azure Data Lake Analytics のアカウント、データ ソース、ユーザー、およびジョブを管理する方法について説明します。 他のツールを使用する管理のトピックを表示する場合は、上のタブ セレクターをクリックします。

## <a name="prerequisites"></a>前提条件

* **Visual Studio 2015、Visual Studio 2013 Update 4、または Visual Studio 2012 (Visual C++ インストール済み)**。
* **Microsoft Azure SDK for .NET バージョン 2.5 以上**。  [Web Platform Installer を使用してインストールします](http://www.microsoft.com/web/downloads/platform.aspx)。
* **必要な NuGet パッケージ**

### <a name="install-nuget-packages"></a>NuGet パッケージのインストール
   
   1. Visual Studio のソリューション エクスプローラーでプロジェクト名を右クリックし、**[NuGet パッケージの管理]** をクリックします。
   2. **[Nuget パッケージ マネージャー]** タブで、**[パッケージ ソース]** が **nuget.org** に設定されており、**[プレリリースを含める]** チェック ボックスがオンになっていることを確認します。

   3. 以下の NuGet パッケージを検索してインストールします。

    - Microsoft.Rest.ClientRuntime.Azure.Authentication - このチュートリアルでは V2.2.12 を使用します
    - Microsoft.Azure.Management.DataLake.Analytics - このチュートリアルでは V2.1.0 プレビューを使用します
    - Microsoft.Azure.Management.DataLake.Store - このチュートリアルでは V2.1.0 プレビューを使用します

   4. **NuGet パッケージ マネージャー**を閉じます。

## <a name="client-management-objects"></a>クライアント管理オブジェクト
Azure Data Lake Analytics と Azure Data Lake Store API には、プログラミングの大部分を行うことができるクライアント管理オブジェクトのセットが含まれます。 これらのオブジェクトは、次の&2; つの名前空間に属します。
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

次の表にクライアント管理オブジェクトと変数を示します。変数は、この記事でのコード例に使用します。

| クライアント管理オブジェクト                  | コード変数        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Data Lake Store 管理クライアント オブジェクト:
* DataLakeStoreAccountManagementClient - Data Lake Store アカウントの作成および管理に使用します。
* DataLakeFileSystemAccountManagementClient - フォルダーとファイルの作成、ファイルのアップロード、ファイルの一覧表示、ACL および資格情報へのアクセス、Azure Storage Blob へのリンクの追加などのファイル システムのタスクに使用します。

Data Lake から Azure Storage へのリンクを作成できますが、そのコンテンツにアクセスすることはできません。 そのためには、Azure Storage SDK API を使用する必要があります。 ただし、Azure Storage Blob で U-SQL スクリプトを実行することができます。

### <a name="data-lake-analytics-management-client-objects"></a>Data Lake Analytics 管理クライアント オブジェクト:
* DataLakeAnalyticsAccountManagementClient - Data Lake Analytics アカウントの作成および管理に使用します。
* DataLakeAnalyticsCatalogManagementClient - Data Lake Analytics のカタログ項目の参照に使用します。
* DataLakeAnalyticsJobManagementClient - Data Lake Analytics のジョブを送信および管理します。

### <a name="example"></a>例

この記事で次に説明する、推奨される認証方法で取得した資格情報 (creds) を使用して、クライアント管理オブジェクトを初期化します。 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>Azure Data Lake Analytics の認証と接続
Azure Data Lake Analytics にログオンするいくつかのオプションがあります。

### <a name="interactive-with-provided-credentials"></a>提供された資格情報で対話的に認証
次のスニペットは、ユーザーが提供する資格情報 (ユーザー名とパスワード、または暗証番号 (PIN) など) による、最も簡単な認証を示します。

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

Azure Active Directory テナント内で独自のアプリケーションとサービス プリンシパルを作成し、ここで使用するサンプル ID ではなく、そのアプリケーションのクライアント ID を使用することをお勧めします。

### <a name="non-interactive-with-a-client-secret"></a>クライアント シークレットで非対話的に認証
次のスニペットは、アプリケーション/サービス プリンシパルのクライアント シークレット/キーを使用して、アプリケーションを非対話的に認証するために使用できます。 この認証オプションを、既存の [Azure AD "Web アプリ" アプリケーションと](../azure-resource-manager/resource-group-create-service-principal-portal.md)共に使用します。

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>サービス プリンシパルで非対話的に認証
3 つ目のオプションとして、次のスニペットは、アプリケーション/サービス プリンシパルの証明書を使用して、アプリケーションを非対話的に認証するためにも使用できます。 この認証オプションを、既存の [Azure AD "Web アプリ" アプリケーションと](../azure-resource-manager/resource-group-create-service-principal-portal.md)共に使用します。

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>アカウントの作成
Data Lake Analytics ジョブを実行するには、Data Lake Analytics アカウントが必要です。 また、Data Lake Analytics アカウントには、少なくとも&1; つの Data Lake Store アカウントが必要です。 詳細については、「[Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。

### <a name="create-an-azure-resource-group"></a>Azure リソース グループを作成する
Azure リソース グループをまだ作成していない場合は、Data Lake Analytics コンポーネントを作成する際に必要になります。 認証資格情報、サブスクリプション ID、および場所が必要です。 次のコードは、リソース グループの作成方法を示しています。

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

詳細については、「[Azure リソース グループと Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics)」を参照してください。


### <a name="create-a-data-lake-store-account"></a>Data Lake Store アカウントを作成する
次のコードは、Data Lake Store アカウントの作成方法を示しています。 Create メソッドを使用する前に、場所を指定することでパラメーターを定義する必要があります。

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントを作成する
次のコードは、非同期メソッドを使用して Data Lake Analytics アカウントを作成する方法を示しています。 CreateAsync メソッドは、Data Lake Store アカウントのコレクションをそのパラメーターの&1; つとして受け取ります。 このコレクションには、DataLakeStoreAccountInfo オブジェクトのインスタンスを入力する必要があります。 この例では、これらの DataLakeStoreAccountInfo オブジェクトはヘルパー メソッド (AdlaFromAdlsStoreAccounts) から取得されます。

Data Lake Analytics アカウントには、必要な分析を実行する Data Lake Store アカウントのみを含めます。 これらの Data Lake Store アカウントのいずれかが、既定の Data Lake Store アカウントである必要があります。

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>アカウントの管理

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Data Lake Store と Data Lake Analytics のアカウントを一覧表示する
次のコードは、サブスクリプション内の Data Lake Store アカウントを一覧表示します。 リスト操作では、オブジェクトのすべてのプロパティが提供されるわけではないため、場合によっては、オブジェクトで Get 操作を実行する必要があります。

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>アカウントを取得する
次のコードは、DataLakeAnalyticsAccountManagementClient を使用して Data Lake Analytics アカウントを取得します。 最初にアカウントが存在するかどうかが確認されます。

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

同様に、同じ方法で DataLakeStoreAccountManagementClient (adlsClient) を使用して Data Lake Store アカウントを取得することができます。

### <a name="delete-an-account"></a>アカウントの削除
次のコードは、Data Lake Analytics アカウントが存在する場合にそのアカウントを削除します。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

DataLakeStoreAccountManagementClient を使用して同じ方法で Data Lake Store アカウントを削除することもできます。

### <a name="get-the-default-data-lake-store-account"></a>既定の Data Lake Store アカウントを取得する
すべての Data Lake Analytics アカウントに、既定の Data Lake Store アカウントが必要です。 次のコードを使用して、Analytics アカウントの既定の Store アカウントを決定使用します。

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>データ ソースを管理する
Data Lake Analytics では現在、以下のデータ ソースがサポートされています。

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage アカウント](../storage/storage-introduction.md)

Analytics アカウントを作成する際には、既定の Data Lake Store アカウントとして Azure Data Lake Store アカウントを指定する必要があります。 既定の Data Lake Store アカウントは、ジョブ メタデータとジョブ監査ログの格納に使用されます。 Analytics アカウントを作成したら、さらに Data Lake Store と Azure Storage (BLOB) アカウントへのリンクを追加することができます。

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Data Lake Analytics アカウントから Azure Storage アカウントへのリンク
Azure Storage アカウントへのリンクを作成することができます。

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store データ ソースを一覧表示する
次のコードは、Data Lake Store アカウントと、指定した Data Lake Analytics アカウントに使用する Azure Storage アカウントを一覧表示します。

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>フォルダーとファイルのアップロードとダウンロード
Data Lake Store ファイル システムのクライアント管理オブジェクトを使用して、Azure からローカル コンピューターに次のメソッドを使用してファイルやフォルダーをアップロードおよびダウンロードできます。

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

これらのメソッドの最初のパラメーターは Data Lake Store のアカウント名で、次にソース パスと対象のパスのパラメーターが続きます。

次の例では、Data Lake Store 内のフォルダーをダウンロードする方法を示します。


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store アカウントにファイルを作成する
.NET Framework の IO 操作を使用して、Data Lake Store 内のファイルのコンテンツを作成できます。 次のコードは、100 個のランダムなバイト配列の最初の 4 つの値を .csv ファイルに書き込みます。

    MemoryStream azMem = new MemoryStream();
    StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

    for (int i = 0; i < 100; i++)
    {
        byte[] gA = Guid.NewGuid().ToByteArray();
        string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
        sw.WriteLine(dataLine);
    }
    sw.Flush();
    azMem.Position = 0;

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Azure Storage アカウントの BLOB コンテナーを一覧表示する
次のコードは、指定した Azure Storage アカウントのコンテナーを一覧表示します。

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Azure ストレージ アカウント パスを確認する
次のコードは、Data Lake Analytics アカウント (analyticsAccountName) 内に Azure ストレージ アカウント (storageAccntName) が存在するかどうかと、その Azure ストレージ アカウント内にコンテナー (containerName) が存在するかどうかを確認します。

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>カタログとジョブを管理する
DataLakeAnalyticsCatalogManagementClient オブジェクトは、各 Azure Data Lake Store に提供された SQL Database を管理するための方法を提供します。 DataLakeAnalyticsJobManagementClient は、U-SQL スクリプトを使用してデータベースで実行されるジョブを送信および管理する方法を提供します。

### <a name="list-databases-and-schemas"></a>データベースとスキーマを一覧表示する
一覧表示できる対象の中で、最も一般的なものはデータベースとスキーマです。 次のコードは、データベースのコレクションを取得し、各データベースのスキーマを列挙します。

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
        }
    }

既定の master データベースで実行すると、この例は次のように出力されます。

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>テーブルの列を一覧表示する
次のコードでは、Data Lake Analytics カタログ管理クライアントを使用してデータベースにアクセスして、指定したテーブルの列を一覧表示する方法を示します。

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
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
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>失敗したジョブを一覧表示する
次のコードは、失敗したジョブに関する情報を一覧表示します。

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>関連項目
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure Portal で Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
* [Azure  Portal を使用して Azure Data Lake Analytics を管理する](data-lake-analytics-manage-use-portal.md)
* [Azure  Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

