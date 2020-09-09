---
title: .NET での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する.NET アプリケーションとサービスを開発する方法を説明します。
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-csharp
ms.openlocfilehash: c5c51bb052948c58d4849c556d0756240d6968ca
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011533"
---
# <a name="develop-for-azure-files-with-net"></a>.NET を使用して Azure Files 用に開発する

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

このチュートリアルでは、ファイル データの格納に [Azure Files](storage-files-introduction.md) を使うアプリケーションを開発するための .NET の基本的な使い方を示します。 このチュートリアルでは、シンプルなコンソール アプリケーションを作成し、.NET と Azure Files で次のような基本的な操作を行います。

* ファイルの内容を取得する
* ファイル共有の最大サイズ (*クォータ*) を設定する
* 共有で定義されている保存されたアクセス ポリシーを使用するファイルの Shared Access Signature (SAS キー) を作成する
* ファイルを、同じストレージ アカウント内の別のファイルにコピーする
* ファイルを、同じストレージ アカウント内の BLOB にコピーする
* トラブルシューティングに Azure Storage メトリックを使用する

Azure Files の詳細については、「[Azure Files とは](storage-files-introduction.md)」を参照してください。

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>.NET API について

Azure Files は、クライアント アプリケーションに対して、サーバー メッセージ ブロック (SMB) と REST という 2 つの幅広いアプローチを提供します。 .NET 内では、これらのアプローチは `System.IO` および `WindowsAzure.Storage` API によって抽象化されています。

API | 使用する場合 | メモ
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | アプリケーションが次のような場合。 <ul><li>SMB を使用してファイルの読み取り/書き込みをする必要がある</li><li>ポート 445 経由で Azure Files アカウントにアクセスするデバイスで実行している</li><li>ファイル共有のどの管理設定も管理する必要がない</li></ul> | SMB 経由の Azure Files によるファイル I/O の実装は、通常、ネットワーク ファイル共有またはローカル ストレージ デバイスでの I/O と同じです。 ファイル I/O など、.NET のさまざまな機能の概要については、「[コンソール アプリケーション](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter)」のチュートリアルを参照してください。
[Microsoft.Azure.Storage.File](/dotnet/api/overview/azure/storage?view=azure-dotnet#version-11x) | アプリケーションが次のような場合。 <ul><li>ファイアウォールや ISP の制約のため、ポート 445 で SMB を使用して Azure Files にアクセスできない</li><li>ファイル共有のクォータを設定したり共有アクセス署名を作成したりする管理機能を必要としている</li></ul> | この記事では、SMB の代わりに REST を使用するファイル I/O のため、およびファイル共有の管理のために、`Microsoft.Azure.Storage.File` を使用する方法について説明します。

## <a name="create-the-console-application-and-obtain-the-assembly"></a>コンソール アプリケーションの作成とアセンブリの取得

Visual Studio で、新しい Windows コンソール アプリケーションを作成します。 次の手順では、Visual Studio 2019 でコンソール アプリケーションを作成する方法を説明します。 この手順は Visual Studio の他のバージョンでも同様です。

1. Visual Studio を起動し、 **[新しいプロジェクトの作成]** を選択します。
1. **[新しいプロジェクトの作成]** で、C# の **[コンソール アプリ (.NET Framework)]** を選択してから、 **[次へ]** を選択します。
1. **[新しいプロジェクトの構成]** で、アプリの名前を入力し、 **[作成]** を選択します。

このチュートリアルのすべてのコード例は、コンソール アプリケーションの `Program.cs` ファイルの `Main()` メソッドに追加できます。

Azure Storage クライアント ライブラリは、すべての種類の .NET アプリケーションで使用できます。 Azure クラウド サービスまたは Web アプリ、デスクトップ アプリケーション、モバイル アプリケーションなどの種類があります。 このガイドでは、わかりやすくするためにコンソール アプリケーションを使用します。

## <a name="use-nuget-to-install-the-required-packages"></a>NuGet を使用した必要なパッケージのインストール

このチュートリアルを完了するには、プロジェクトの次のパッケージを参照してください。

* [.NET 用 Microsoft Azure Storage 共通ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  このパッケージを使用すると、ストレージ アカウント内の共通リソースにプログラムでアクセスできます。
* [.NET 用 Microsoft Azure Storage Blob ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  このパッケージを使用すると、ストレージ アカウント内の BLOB リソースにプログラムでアクセスできます。
* [.NET 用 Microsoft Azure Storage ファイル ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  このパッケージを使用すると、ストレージ アカウント内のファイル リソースにプログラムでアクセスできます。
* [.NET 用 Microsoft Azure Configuration Manager ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  このパッケージには、アプリケーションの実行場所に関係なく、構成ファイル内の接続文字列を解析するためのクラスが用意されています。

NuGet を使って両方のパッケージを取得できます。 次の手順に従います。

1. **ソリューション エクスプローラー**でプロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。
1. **NuGet パッケージ マネージャー**で、 **[参照]** を選択します。 次に、**Microsoft.Azure.Storage.Blob** を検索して選択し、 **[インストール]** を選択します。

   この手順により、パッケージとその依存関係がインストールされます。
1. それらのパッケージを検索してインストールします。

   * **Microsoft.Azure.Storage.Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft.Azure.ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>ストレージ アカウントの資格情報を App.config ファイルに保存

次に、プロジェクトの `App.config` ファイルに資格情報を保存します。 **ソリューション エクスプローラー**で `App.config` をダブルクリックし、次の例のようにファイルを編集します。 `myaccount` を実際のストレージ アカウント名に置き換え、`mykey` を実際のストレージ アカウント キーに置き換えます。

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
> 最新バージョンの Azure Storage エミュレーターと Azurite オープンソース エミュレーターは、Azure Files をサポートしていません。 Azure Files を操作するには、接続文字列がクラウド内の Azure ストレージ アカウントを対象とする必要があります。

## <a name="add-using-directives"></a>using ディレクティブを追加する

**ソリューション エクスプローラー**で `Program.cs` ファイルを開き、次の using ディレクティブをファイルの先頭に追加します。

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>プログラムによるファイル共有へのアクセス

次に、前に示したコードの後にある `Main()` メソッドに次のコードを追加し、接続文字列を取得します。 このコードでは、前の手順で作成したファイルへの参照を取得し、その内容を出力します。

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

Azure Storage クライアント ライブラリのバージョン 5.x 以降では、ファイル共有のクォータ (最大サイズ) を設定できます。 また、共有に現在格納されているデータの量も確認できます。

共有のクォータを設定することにより、共有に格納するファイルの合計サイズを制限します。 共有上のファイルの合計サイズが、共有に設定されているクォータを超えると、クライアントは既存のファイルのサイズを増やすことができません。 それらのファイルが空でない限り、クライアントは新しいファイルを作成できません。

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

Azure Storage クライアント ライブラリのバージョン 5.x 以降、ファイル共有または個々のファイルの Shared Access Signature (SAS) を生成できます。 また、保存されているアクセス ポリシーをファイル共有に作成して、Shared Access Signature を管理することもできます。 保存されているアクセス ポリシーを使用すると、SAS が危害を受けた場合に SAS を失効させることができるため、保存されているアクセス ポリシーを作成することをお勧めします。

次の例では、共有に対して保存されているアクセス ポリシーを作成します。 この例では、そのポリシーを使用して、共有内のファイルの SAS に制約を指定しています。

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

    // Create a new stored access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the stored access policy to the share's policies. Note that each policy must have a unique name.
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

Shared Access Signature の作成方法と使用方法の詳細については、「[Shared Access Signature の機能](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works)」を参照してください。

## <a name="copy-files"></a>ファイルのコピー

Azure Storage クライアント ライブラリのバージョン 5.x 以降では、ファイルを別のファイルにコピーしたり、ファイルを BLOB にコピーしたり、BLOB をファイルにコピーしたりすることができます。 次のセクションでは、プログラムを使用してこれらのコピー操作を行う方法を示します。

また、AzCopy を使用してあるファイルを別のファイルにコピーしたり、BLOB をファイルにコピーしたり、その反対の操作をしたりすることもできます。 [AzCopy の作業開始](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)に関するページを参照してください。

> [!NOTE]
> BLOB をファイルにコピーしたり、ファイルを BLOB にコピーしたりする場合は、同じストレージ アカウント内でコピーする場合でも、Shared Access Signature (SAS) を使用してソース オブジェクトへのアクセスを承認する必要があります。
>

### <a name="copy-a-file-to-another-file"></a>別のファイルへのファイルのコピー

次の例では、同じ共有内の別のファイルに、ファイルをコピーします。 このコピー操作では同じストレージ アカウント内のファイル間でコピーされるため、共有キー認証を使用してコピーを行うことができます。

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

### <a name="copy-a-file-to-a-blob"></a>BLOB へのファイルのコピー

次の例では、ファイルを作成し、同じストレージ アカウント内の BLOB にそれをコピーします。 この例では、ソース ファイルの SAS を作成します。サービスはこれを使用してコピー操作中にソース ファイルへのアクセスを承認します。

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

### <a name="create-share-snapshots"></a>共有スナップショットを作成する

次の例では、ファイル共有スナップショットを作成します。

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>共有スナップショットの一覧表示

次の例では、共有上の共有スナップショットを一覧表示します。

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>共有スナップショット内のファイルとディレクトリを参照する

次の例では、共有スナップショット内のファイルとディレクトリを参照します。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>共有と共有スナップショットを一覧表示し、共有スナップショットからファイル共有またはファイルを復元する

ファイル共有のスナップショットを取得すると、将来的に個々のファイルまたはファイル共有全体を復旧できます。

ファイル共有の共有スナップショットを照会することで、ファイル共有スナップショットからファイルを復元できます。 その後、特定の共有スナップショットに属するファイルを取得できます。 そのバージョンを使用して、直接読み取って比較するか、復元します。

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

### <a name="delete-share-snapshots"></a>共有スナップショットを削除する

次の例では、ファイル共有スナップショットを削除します。

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## <a name="troubleshoot-azure-files-by-using-metrics"></a>メトリックを使用した Azure Files のトラブルシューティング<a name="troubleshooting-azure-files-using-metrics"></a>

Azure ストレージ分析で Azure Files のメトリックがサポートされるようになりました。 メトリック データを使用すると、要求のトレースや問題の診断ができます。

[Azure portal](https://portal.azure.com) から Azure Files のメトリックを有効にすることができます。 REST API またはストレージ クライアント ライブラリの類似のものをどれか使用して、Set File Service Properties 操作を呼び出すことによって、プログラムでメトリックを有効にすることも可能です。

次のコード例では、.NET 用ストレージ クライアント ライブラリを使用して、Azure Files のメトリックを有効にする方法を示します。

まず、`Program.cs` ファイルで、先ほど追加したディレクティブと共に、次の `using` ディレクティブを追加します。

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Azure BLOB、Azure テーブル、Azure キューでは `Microsoft.Azure.Storage.Shared.Protocol` 名前空間の共有 `ServiceProperties` 型が使用されますが、Azure Files ではその独自の型である、`Microsoft.Azure.Storage.File.Protocol` 名前空間の `FileServiceProperties` 型が使用されます。 ただし、次のコードをコンパイルするためには、ご自身のコードから両方の名前空間を参照する必要があります。

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

問題が発生した場合は、「[Windows での Azure Files に関する問題のトラブルシューティング](storage-troubleshoot-windows-file-connection-problems.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Files の詳細については、次のリソースを参照してください。

### <a name="conceptual-articles-and-videos"></a>概念に関する記事とビデオ

* [Azure Files: Windows および Linux 用の円滑なクラウド SMB ファイル システム](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Linux で Azure Files を使用する](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>File Storage 用のツールのサポート

* [AzCopy を使ってみる](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Windows での Azure Files に関する問題のトラブルシューティング](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>リファレンス

* [.NET 用 Azure Storage API](/dotnet/api/overview/azure/storage)
* [ファイル サービスの REST API](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>ブログ記事

* [Azure File Storage の一般提供を開始](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Inside Azure File Storage (Azure File Storage の内部)](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Microsoft Azure Files サービスの概要](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Microsoft Azure Files への接続の維持](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
