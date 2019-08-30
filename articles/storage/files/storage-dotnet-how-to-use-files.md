---
title: .NET での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する.NET アプリケーションとサービスを開発する方法を説明します。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/22/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b79086298983e807cbfe0f4413d1fde54969cc6c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986370"
---
# <a name="develop-for-azure-files-with-net"></a>.NET を使用して Azure Files 用に開発する

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

このチュートリアルでは、ファイル データの格納に [Azure Files](storage-files-introduction.md) を使うアプリケーションを開発するための .NET の基本的な使い方を示します。 このチュートリアルでは、単純なコンソール アプリケーションを作成し、.NET と Azure Files で次のような基本的な操作を実行します。

* ファイルの内容を取得する
* ファイル共有のクォータ (最大サイズ) を設定する
* 共有で定義されている共有アクセス ポリシーを使用するファイルの Shared Access Signature (SAS キー) を作成する
* ファイルを、同じストレージ アカウント内の別のファイルにコピーする
* ファイルを、同じストレージ アカウント内の BLOB にコピーする
* トラブルシューティングに Azure Storage メトリックを使用します。

Azure Files の詳細については、「[Azure Files の概要](storage-files-introduction.md)」を参照してください。

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>.NET API について

Azure Files は、クライアント アプリケーションに対して、2 つの幅広いアプローチを提供します。それは、サーバー メッセージ ブロック (SMB) と REST です。 .NET 内では、これらのアプローチは `System.IO` API と `WindowsAzure.Storage` API によって抽象化されています。

API | いつ使用するか | メモ
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | アプリケーションが次のような場合。 <ul><li>SMB 経由でファイルの読み取り/書き込みをする必要がある</li><li>ポート 445 経由で Azure Files アカウントにアクセスするデバイスで実行している</li><li>ファイル共有のどの管理設定も管理する必要がない</li></ul> | SMB 経由の Azure Files によるファイル I/O のコーディングは、通常、ネットワーク ファイル共有またはローカル ストレージ デバイスでの I/O のコーディングと同じです。 ファイル I/O など、.NET のさまざまな機能の概要については、[このチュートリアル](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)を参照してください。
[Microsoft.Azure.Storage.File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | アプリケーションが次のような場合。 <ul><li>ファイアウォールや ISP の制約のため、ポート 445 の SMB 経由で Azure Files にアクセスできない</li><li>ファイル共有のクォータを設定したり共有アクセス署名を作成したりする管理機能を必要としている</li></ul> | この記事では、SMB の代わりに REST とファイル共有の管理を使用するファイル I/O に `Microsoft.Azure.Storage.File` を使用する方法について説明します。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>コンソール アプリケーションの作成とアセンブリの取得
Visual Studio で、新しい Windows コンソール アプリケーションを作成します。 次の手順では、Visual Studio 2017 でコンソール アプリケーションを作成する方法を説明しますが、この手順は Visual Studio の他のバージョンでも同様です。

1. **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
2. **[インストール済み]**  >  **[テンプレート]**  >  **[Visual C#]**  >  **[Windows クラシック デスクトップ]** の順に選択します。
3. **[コンソール アプリ (.NET Framework)]** を選択します。
4. **[名前]** フィールドに、アプリケーションの名前を入力します。
5. **[OK]** を選択します。

このチュートリアルのすべてのコード例は、コンソール アプリケーションの `Program.cs` ファイルの `Main()` メソッドに追加できます。

Azure クラウド サービス、Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなど、どの種類の .NET アプリケーションでも Azure Storage クライアント ライブラリを使用できます。 このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet を使用した必要なパッケージのインストール
このチュートリアルを完了するには、次のパッケージをプロジェクトで参照する必要があります。

* [.NET 用 Microsoft Azure Storage 共通ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/):このパッケージを使用すると、ストレージ アカウント内の共通リソースにプログラムでアクセスできます。
* [.NET 用 Microsoft Azure Storage Blob ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/):このパッケージを使用すると、ストレージ アカウント内の BLOB リソースにプログラムでアクセスできます。
* [.NET 用の Microsoft Azure Storage ファイル ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/):このパッケージを使用すると、ストレージ アカウント内のファイル リソースにプログラムでアクセスできます。
* [.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/):このパッケージには、アプリケーションの実行場所に関係なく、構成ファイルの接続文字列を解析するためのクラスが用意されています。

NuGet を使って両方のパッケージを取得できます。 次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。
2. "WindowsAzure.Storage" をオンラインで検索し、 **[インストール]** をクリックしてストレージ クライアント ライブラリとその依存関係をインストールします。
3. "WindowsAzure.ConfigurationManager" をオンラインで検索し、 **[インストール]** をクリックして Azure Configuration Manager をインストールします。

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>ストレージ アカウントの資格情報を app.config ファイルに保存
次に、プロジェクトの app.config ファイルに資格情報を保存します。 次の例のようになるように app.config ファイルを編集します。ここで、`myaccount` は実際のストレージ アカウント名に置き換え、`mykey` は実際のストレージ アカウント キーに置き換えてください。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> 最新バージョンの Azure ストレージ エミュレーターでは、Azure Files はサポートされません。 Azure Files を操作するには、接続文字列がクラウド内の Azure ストレージ アカウントを対象とする必要があります。

## <a name="add-using-directives"></a>using ディレクティブを追加する
ソリューション エクスプローラーで `Program.cs` ファイルを開き、次の using ディレクティブをファイルの先頭に追加します。

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>プログラムによるファイル共有へのアクセス
次に、前に示したコードの後で、`Main()` メソッドに次のコードを追加し、接続文字列を取得します。 このコードは、前の手順で作成したファイルへの参照を取得し、その内容をコンソール ウィンドウに出力します。

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

コンソール アプリケーションを実行して、出力結果を確認します。

## <a name="set-the-maximum-size-for-a-file-share"></a>ファイル共有の最大サイズの設定
Azure Storage クライアント ライブラリのバージョン 5.x 以降では、GB 単位でファイル共有のクォータ (最大サイズ) を設定できます。 また、共有に現在格納されているデータの量も確認できます。

共有のクォータを設定することにより、共有に格納するファイルの合計サイズを制限できます。 共有上のファイルの合計サイズが共有に設定されたクォータを超過すると、クライアントは既存ファイルのサイズを増やせなくなったり、新しいファイルを作成できなくなったりします。ただし、これらのファイルが空である場合は除きます。

次の例では、共有の現在の使用状況を確認する方法と、共有のクォータを設定する方法を示します。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>ファイルまたはファイル共有の Shared Access Signature の生成
Azure Storage クライアント ライブラリのバージョン 5.x 以降、ファイル共有または個々のファイルの Shared Access Signature (SAS) を生成できます。 また、ファイル共有に共有アクセス ポリシーを作成して、Shared Access Signature を管理することもできます。 共有アクセス ポリシーを作成することをお勧めします。これにより、侵害されそうな場合に SAS を取り消すことができます。

次の例では、共有上に共有アクセス ポリシーを作成し、そのポリシーを使用して共有上のファイルの SAS に制約を指定します。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Shared Access Signature の作成方法と使用方法の詳細については、「[Shared Access Signatures (SAS) の使用](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

## <a name="copy-files"></a>ファイルのコピー
Azure Storage クライアント ライブラリのバージョン 5.x 以降では、ファイルを別のファイルにコピーしたり、ファイルを BLOB にコピーしたり、BLOB をファイルにコピーしたりすることができます。 次のセクションでは、プログラムを使用してこれらのコピー操作を実行する方法を示します。

また、AzCopy を使用してあるファイルを別のファイルにコピーしたり、BLOB をファイルにコピーしたり、その反対の操作をしたりすることもできます。 「 [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)」を参照してください。

> [!NOTE]
> BLOB をファイルにコピーしたり、ファイルを BLOB にコピーしたりする場合は、同じストレージ アカウント内でコピーする場合でも、Shared Access Signature (SAS) を使用してソース オブジェクトへのアクセスを承認する必要があります。
> 
> 

**別のファイルへのファイルのコピー** 次の例では、同じ共有内の別のファイルに、ファイルをコピーします。 このコピー操作では同じストレージ アカウント内のファイルがコピーされるため、共有キー認証を使用してコピーを実行できます。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

**BLOB へのファイルのコピー** 次の例では、ファイルを作成し、同じストレージ アカウント内の BLOB にそれをコピーします。 この例では、ソース ファイルの SAS を作成します。サービスはこれを使用してコピー操作中にソース ファイルへのアクセスを承認します。

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

同じ方法で、ファイルに BLOB をコピーできます。 ソース オブジェクトが BLOB である場合、SAS を作成して、コピー操作中にその BLOB へのアクセスを承認します。

## <a name="share-snapshots"></a>共有スナップショット
Azure Storage クライアント ライブラリのバージョン 8.5 以降では、共有スナップショットを作成できます。 また、共有スナップショットを一覧表示または参照したり、削除したりすることもできます。 共有スナップショットは読み取り専用であるため、共有スナップショットに対する書き込み操作は許可されていません。

**共有スナップショットを作成する**

次の例では、ファイル共有スナップショットを作成します。

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```
**共有スナップショットを一覧表示する**

次の例では、共有上の共有スナップショットを一覧表示します。

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

**共有スナップショット内のファイルとディレクトリを参照する**

次の例では、共有スナップショット内のファイルとディレクトリを参照します。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

**共有と共有スナップショットを一覧表示し、共有スナップショットからファイル共有またはファイルを復元する** 

ファイル共有のスナップショットを取得すると、将来的に個々のファイルまたはファイル共有全体を復旧できます。 

ファイル共有の共有スナップショットを照会することで、ファイル共有スナップショットからファイルを復元できます。 その後、特定の共有スナップショットに属しているファイルを取得し、そのバージョンを使用して、直接読み取りと比較を行うか復元することができます。

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();

       var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

           
CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();

       var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
       SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
       sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
       sasConstraints.Permissions = SharedAccessFilePermissions.Read;
       //Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));

```


**共有スナップショットを削除する**

次の例では、ファイル共有スナップショットを削除します。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshooting-azure-files-using-metrics"></a>メトリックを使用した Azure Files のトラブルシューティング
Azure ストレージ分析で Azure Files のメトリックがサポートされるようになりました。 メトリック データを使用すると、要求のトレースや問題の診断ができます。

[Azure portal](https://portal.azure.com) から Azure Files のメトリックを有効にすることができます。 REST API を使用して Set File Service Properties 操作を呼び出すか、ストレージ クライアント ライブラリのアナログの 1 つを使用して、プログラムでメトリックを有効にすることも可能です。

次のコード例では、.NET 用ストレージ クライアント ライブラリを使用して、Azure Files のメトリックを有効にする方法を示します。

まず、`Program.cs` ファイルで、先ほど追加したディレクティブに加え、次の `using` ディレクティブを追加します。

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Azure BLOB、Azure テーブル、Azure キューは `Microsoft.Azure.Storage.Shared.Protocol` 名前空間の共有 `ServiceProperties` 型を使用しますが、Azure Files はその独自の型である、`Microsoft.Azure.Storage.File.Protocol` 名前空間の `FileServiceProperties` 型を使用することに注意してください。 ただし、両方の名前空間は、次のコードのコンパイルのため、自身のコードから参照される必要があります。

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

エンド ツー エンドのトラブルシューティング ガイダンスについては、[Azure File のトラブルシューティングに関する記事](storage-troubleshoot-windows-file-connection-problems.md)も参照してください。

## <a name="next-steps"></a>次の手順
Azure Files の詳細については、次のリンクをご覧ください。

### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ
* [Azure Files: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux で Azure Files を使用する方法](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage 用のツールのサポート
* [Microsoft Azure Storage で AzCopy を使用する方法](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Azure Storage での Azure CLI の使用](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Azure Files に関する問題のトラブルシューティング](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>リファレンス
* [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [File サービスの REST API リファレンス](https://msdn.microsoft.com/library/azure/dn167006.aspx)

### <a name="blog-posts"></a>ブログ記事
* [Azure Files が一般公開されました](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Azure Files の内部](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure File サービスの概要](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Microsoft Azure Files への接続の維持](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
