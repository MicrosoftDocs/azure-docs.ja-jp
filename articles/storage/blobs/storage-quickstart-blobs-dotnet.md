---
title: クイック スタート:.NET を使用してオブジェクト ストレージ内に BLOB を作成する - Azure Storage
description: このクイック スタートでは、.NET 用 Azure Storage クライアント ライブラリを使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 11/14/2018
ms.author: tamram
ms.openlocfilehash: 50bb13ecaa9e6076f00749d54b492a1e6663a93e
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287113"
---
# <a name="quickstart-use-net-to-create-a-blob-in-object-storage"></a>クイック スタート:.NET を使用してオブジェクト ストレージ内に BLOB を作成する

このクイック スタートでは、.NET 用 Azure Storage クライアント ライブラリを使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次に、ご使用のオペレーティング システム用の .NET Core 2.0 をダウンロードしてインストールします。 Windows を実行している場合は、Visual Studio をインストールして、.NET Framework を使用してもかまいません。 オペレーティング システムで使用するエディターをインストールすることもできます。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

- [.NET Core for Windows](https://www.microsoft.com/net/download/windows) または [.NET Framework](https://www.microsoft.com/net/download/windows) (Visual Studio for Windows に付属) をインストールする
- [Visual Studio for Windows](https://www.visualstudio.com/) をインストールする。 .NET Core を使用する場合、Visual Studio のインストールは任意です。  

.NET Core と .NET Framework の選択については、[サーバー アプリ用 .NET Core と .NET Framework の選択](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server)に関するページを参照してください。

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

- [Linux 用 .NET Core](https://www.microsoft.com/net/download/linux) をインストールする
- オプションで、[Visual Studio Code](https://www.visualstudio.com/) および [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)をインストールする

# <a name="macostabmacos"></a>[macOS](#tab/macos)

- [macOS 用 .NET Core](https://www.microsoft.com/net/download/macos) をインストールする。
- オプションで、[Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/) をインストールする

---

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使うサンプル アプリケーションは、基本的なコンソール アプリケーションです。 [GitHub](https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart) でサンプル アプリケーションを調べることができます。

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Visual Studio ソリューションを開くには、*storage-blobs-dotnet-quickstart* フォルダーを開き、*storage-blobs-dotnet-quickstart.sln* をダブルクリックします。 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションを実行するには、ストレージ アカウントの接続文字列を指定する必要があります。 サンプル アプリケーションは、環境変数から接続文字列を読み取り、それを使って、Azure Storage に対する要求を承認します。

接続文字列をコピーした後、アプリケーションを実行しているローカル マシンの新しい環境変数にそれを書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

実行中のプログラムのうち、環境変数の読み取りを必要とするプログラム (コンソール ウィンドウを含む) については、環境変数を追加した後で再起動が必要となる場合があります。 たとえば、Visual Studio をエディターとして使用している場合、サンプルを実行する前に Visual Studio を再起動する必要があります。 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export storageconnectionstring=<yourconnectionstring>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

# <a name="macostabmacos"></a>[macOS](#tab/macos)

次のように .bash_profile を編集し、環境変数を追加します。

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。

---

## <a name="run-the-sample"></a>サンプルを実行する

このサンプルは、ローカルの **[マイ ドキュメント]** フォルダーにテスト ファイルを作成し、BLOB ストレージにアップロードします。 次に、コンテナー内の BLOB を一覧表示し、ファイルを新しい名前でダウンロードして、古いファイルと新しいファイルを比較できるようにします。 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Visual Studio をエディターとして使用している場合は、**F5** キーを押して実行することができます。 

それ以外の場合は、お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

# <a name="macostabmacos"></a>[macOS](#tab/macos)

お使いのアプリケーションのディレクトリに移動し、`dotnet run` コマンドを使ってアプリケーションを実行します。

```
dotnet run
```

---

サンプル アプリケーションの出力は次の例のようになります。

```
Azure Blob storage - .NET Quickstart sample

Created container 'quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f'

Temp file = C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt
Uploading to Blob storage as blob 'QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt'

Listing blobs in container.
https://storagesamples.blob.core.windows.net/quickstartblobs33c90d2a-eabd-4236-958b-5cc5949e731f/QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db.txt

Downloading blob to C:\Users\myusername\Documents\QuickStart_c5e7f24f-a7f8-4926-a9da-9697c748f4db_DOWNLOADED.txt

Press any key to delete the sample files and example container.
```

**Enter** キーを押すと、ストレージ コンテナーとファイルが削除されます。 削除する前に、**[マイ ドキュメント]** フォルダーで 2 つのファイルをチェックします。 それらを開いて、同じであるかどうかを確認します。 コンソール ウィンドウから BLOB の URL をコピーしてブラウザーに貼り付け、BLOB の内容を表示します。

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、Program.cs ファイルを開いてコードを確認します。 

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを調べて、そのしくみを理解できるようにします。

### <a name="try-parsing-the-connection-string"></a>接続文字列を解析してみる

サンプルで最初に行われるのは、ストレージ アカウントを指す [CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) オブジェクトの作成のために解析できる接続文字列が環境変数に含まれているかどうかを確認することです。 接続文字列が有効であることを確認するには、[TryParse](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.tryparse) メソッドを使用します。 **TryParse** が成功すると、*storageAccount* 変数が初期化され、**true** が返されます。

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable on the machine running the application called storageconnectionstring.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell or application needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
CloudStorageAccount storageAccount;
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with operations against Blob storage here.
    ...
}
else
{
    // Otherwise, let the user know that they need to define the environment variable.
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
    Console.WriteLine("Press any key to exit the sample application.");
    Console.ReadLine();
}
```

### <a name="create-the-container-and-set-permissions"></a>コンテナーを作成し、アクセス許可を設定する

次に、このサンプルは、コンテナーを作成し、コンテナー内のすべての BLOB がパブリックになるようにアクセス許可を設定します。 BLOB がパブリックの場合は、すべてのクライアントが匿名でアクセスすることができます。

コンテナーを作成するには、まず、ストレージ アカウント内の BLOB ストレージを指す [CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient) オブジェクトのインスタンスを作成します。 次に、[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer) オブジェクトのインスタンスを作成し、コンテナーを作成します。 

この場合、サンプルはコンテナーを作成するために [CreateAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createasync) メソッドを呼び出します。 コンテナー名には、一意になるように、GUID 値が追加されます。 運用環境では、[CreateIfNotExistsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.createifnotexistsasync) メソッドの使用が望ましいことがよくあります。コンテナーがまだ存在していない場合にだけ作成することで、名前の競合を避けられるようにするためです。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。


```csharp
// Create the CloudBlobClient that represents the Blob storage endpoint for the storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs' and append a GUID value to it to make the name unique. 
CloudBlobContainer cloudBlobContainer = cloudBlobClient.GetContainerReference("quickstartblobs" + Guid.NewGuid().ToString());
await cloudBlobContainer.CreateAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
};
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

次に、このサンプルは、ローカル ファイルをブロック BLOB にアップロードします。 このコード例では、前のセクションで作成したコンテナーで [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference) メソッドを呼び出して、**CloudBlockBlob** オブジェクトへの参照を取得します。 次に、[UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) メソッドを呼び出して、選択されたファイルを BLOB にアップロードします。 このメソッドは、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。 

```csharp
// Create a file in your local MyDocuments folder to upload to a blob.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
sourceFile = Path.Combine(localPath, localFileName);
// Write text to the file.
File.WriteAllText(sourceFile, "Hello, World!");

Console.WriteLine("Temp file = {0}", sourceFile);
Console.WriteLine("Uploading to Blob storage as blob '{0}'", localFileName);

// Get a reference to the blob address, then upload the file to the blob.
// Use the value of localFileName for the blob name.
CloudBlockBlob cloudBlockBlob = cloudBlobContainer.GetBlockBlobReference(localFileName);
await cloudBlockBlob.UploadFromFileAsync(sourceFile);
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

このサンプルは、[ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) メソッドを使用して、コンテナー内の BLOB を一覧表示します。 このサンプルの場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

1 回の呼び出しで返す BLOB が多すぎる場合 (既定では 5,000 を超える場合)、**ListBlobsSegmentedAsync** メソッドは結果セット全体のうちの一部のセグメントと継続トークンを返します。 BLOB の次のセグメントを取得するには、前の呼び出しから返された継続トークンを指定します。この操作を、継続トークンが null になるまで繰り返します。 null の継続トークンは、すべての BLOB を取得し終わったことを示します。 サンプル コードは、ベスト プラクティスのために継続トークンを使用する方法を示しています。

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    // Get the value of the continuation token returned by the listing call.
    blobContinuationToken = results.ContinuationToken;
    foreach (IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null); // Loop while the continuation token is not null. 

```

### <a name="download-blobs"></a>BLOB をダウンロードする

このサンプルは、次に [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) メソッドを使用して、以前に作成した BLOB をローカル ファイル システムにダウンロードします。 サンプル コードは、ローカル ファイル システムで両方のファイルを見ることができるように、BLOB の名前に "_DOWNLOADED" というサフィックスを追加します。

```csharp
// Download the blob to a local file, using the reference created earlier. 
// Append the string "_DOWNLOADED" before the .txt extension so that you can see both files in MyDocuments.
destinationFile = sourceFile.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", destinationFile);
await cloudBlockBlob.DownloadToFileAsync(destinationFile, FileMode.Create);  
```

### <a name="clean-up-resources"></a>リソースのクリーンアップ

このサンプルは、[CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) を使用してコンテナー全体を削除することによって、作成したリソースをクリーンアップします。 また、必要に応じてローカル ファイルを削除することもできます。

```csharp
Console.WriteLine("Press the 'Enter' key to delete the sample files, example container, and exit the application.");
Console.ReadLine();
// Clean up resources. This includes the container and the two temp files.
Console.WriteLine("Deleting the container");
if (cloudBlobContainer != null)
{
    await cloudBlobContainer.DeleteIfExistsAsync();
}
Console.WriteLine("Deleting the source, and downloaded files");
File.Delete(sourceFile);
File.Delete(destinationFile);
```

## <a name="resources-for-developing-net-applications-with-blobs"></a>BLOB を使用する .NET アプリケーションを開発するためのリソース

BLOB ストレージを使用する .NET 開発については、以下の追加リソースを参照してください。

### <a name="binaries-and-source-code"></a>バイナリとソース コード

- Azure Storage 用の [.NET クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)の最新バージョンの NuGet パッケージをダウンロードします。 
- GitHub で [.NET クライアント ライブラリのソース コード](https://github.com/Azure/azure-storage-net)を確認します。

### <a name="client-library-reference-and-samples"></a>クライアント ライブラリ リファレンスとサンプル

- .NET クライアント ライブラリの詳細については、[.NET API リファレンス](https://docs.microsoft.com/dotnet/api/overview/azure/storage)を参照してください。
- .NET クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=blob)を確認します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、.NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明しました。 

BLOB ストレージに画像をアップロードする Web アプリの作成方法を学習するには、「[Azure Storage を使用してクラウドに画像データをアップロードする](storage-upload-process-images.md)」に進んでください。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](storage-dotnet-how-to-use-blobs.md)

- .NET Core の詳細については、「[Get started with .NET in 10 minutes (10 分で .NET を使い始める)](https://www.microsoft.com/net/learn/get-started/)」を参照してください。
- Windows 用 Visual Studio からデプロイできるサンプル アプリケーションを調べるには、「[.NET Photo Gallery Web Application Sample with Azure Blob Storage (Azure Blob Storage を使用した .NET フォト ギャラリー Web アプリケーション サンプル)](https://azure.microsoft.com/resources/samples/storage-blobs-dotnet-webapp/)」を参照してください。
 
