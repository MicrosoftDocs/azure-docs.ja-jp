---
title: "Azure クイックスタート - .NET を使用して Azure Blob Storage との間でオブジェクトを転送する | Microsoft Docs"
description: ".NET を使って Azure Blob Storage との間で双方向にオブジェクトを転送する方法を説明します"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: robinsh
ms.openlocfilehash: 97bacc2c1285fe4a467a54f224bb9fabbd851fee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-objects-tofrom-azure-blob-storage-using-net"></a>.NET を使用して Azure Blob Storage との間でオブジェクトを転送する

このクイックスタートでは、C#.NET を使って、Windows 上の Azure Blob Storage 内のブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、以下が必要です。

* 次のワークロードを指定して [Visual Studio 2017](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) をインストールします。
    - **Azure 開発**

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-storage-account-using-the-azure-portal"></a>Azure Portal を使用してストレージ アカウントを作成する

最初に、このクイックスタートで使う新しい汎用的なストレージ アカウントを作成します。 

1. [Azure Portal](https://portal.azure.com) にアクセスし、Azure アカウントを使ってログインします。 
2. ハブ メニューで、**[新規]** > **[ストレージ]** > **[ストレージ アカウント - Blob、File、Table、Queue]** の順に選びます。 
3. ストレージ アカウントの名前を入力します。 名前の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 また、一意である必要があります。
4. [`Deployment model`] を **[リソース マネージャー]** に設定します。
5. [`Account kind`] を **[汎用]** に設定します。
6. [`Performance`] を **[Standard]** に設定します。 
7. [`Replication`] を **[ローカル冗長ストレージ (LRS)]** に設定します。
8. [`Storage service encryption`] を **[無効]** に設定します。
9. [`Secure transfer required`] を **[無効]** に設定します。
10. サブスクリプションを選択します。 
11. [`resource group`] では、新しいリソース グループを作成して一意名を指定します。 
12. [`Location`] でストレージ アカウントに使う場所を選びます。
13. **[ダッシュボードにピン留めする]** をオンにし、**[作成]** をクリックしてストレージ アカウントを作成します。 

ストレージ アカウントが作成されて、ダッシュボードにピン留めされます。 そのストレージ アカウントをクリックして開きます。 [設定] で **[アクセス キー]** をクリックします。 キーを選び、[接続文字列] をクリップボードにコピーして、後で使うためにメモ帳に貼り付けます。

## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード

このクイックスタートで使うサンプル アプリケーションは、基本的なコンソール アプリケーションです。 

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-dotnet-quickstart.git
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 Visual Studio ソリューションを開くには、storage-blobs-dotnet-quickstart フォルダーを開き、storage-blobs-dotnet-quickstart.sln をダブルクリックします。 

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 Visual Studio のソリューション エクスプローラーから `app.config` ファイルを開きます。 StorageConnectionString エントリを探します。 **value** で、接続文字列の値全体を、Azure Portal からメモ帳に保存した値に置き換えます。 終了すると次のようになります。

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;
    AccountName=youraccountname;
    AccountKey=7NGE5jasdfdRzASDFNOMEx1u20W/thisisjustC/anexampleZK/Rt5pz2xNRrDckyv8EjB9P1WGF==" />
  </appSettings>
</configuration>
```

## <a name="run-the-sample"></a>サンプルの実行

このサンプルは、[マイ ドキュメント] にテスト ファイルを作成し、それを Blob Storage にアップロードし、コンテナー内の BLOB の一覧を取得してから、古いファイルと新しいファイルを比較できるように新しい名前でファイルをダウンロードします。 

F5 キーを押してサンプルを実行します。 コンソール ウィンドウに次のような出力が表示されます。 

```
Temp file = C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Uploading to Blob storage as blob 'QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt'
List blobs in container.
https://mystorage.blob.core.windows.net/quickstartblobs/QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8.txt
Downloading blob to C:\Users\azureuser\Documents\QuickStart_cbd5f95c-6ab8-4cbf-b8d2-a58e85d7a8e8_DOWNLOADED.txt
```

いずれかのキーを押して続行すると、ストレージ コンテナーとファイルが削除されます。 続行する前に、[マイ ドキュメント] で 2 つのファイルを確認します。ファイルを開き、同じであることを確認します。 コンソール ウィンドウから BLOB の URL をコピーしてブラウザーに貼り付け、Blob Storage のファイルの内容を表示します。

[Azure Storage Explorer](http://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 

ファイルを確認した後、任意のキーを押してデモを終了し、テスト ファイルを削除します。 サンプルの機能がわかったら、Program.cs ファイルを開いてコードを確認します。 

## <a name="get-references-to-the-storage-objects"></a>ストレージ オブジェクトへの参照を取得する

最初に、Blob Storage にアクセスして管理するために使うオブジェクトへの参照を作成します。 これらのオブジェクトは、他のオブジェクトを基にして作成されます。各オブジェクトは、一覧で次にあるオブジェクトによって使われます。

* ストレージ アカウントを指す **CloudStorageAccount** オブジェクトをインスタンス化します。 

* ストレージ アカウントの Blob service を指す **CloudBlobClient** オブジェクトをインスタンス化します。 

* アクセスしているコンテナーを表す **CloudBlobContainer** オブジェクトをインスタンス化します。 コンテナーは、コンピューターでフォルダーを使ってファイルを整理するのと同じように、BLOB を整理するために使われます。

**CloudBlobContainer** を作成した後は、関心がある特定の BLOB を指す **CloudBlockBlob** オブジェクトをインスタンス化して、アップロード、ダウンロード、コピーなどの操作を実行できます。

このセクションでは、オブジェクトをインスタンス化し、新しいコンテナーを作成した後、BLOB をパブリックにして URL のみでアクセスできるように、コンテナーに対するアクセス許可を設定します。 コンテナーの名前は **quickstartblobs** です。 

この例では、サンプルを実行するたびに新しいコンテナーを作成したいので、CreateIfNotExists を使います。 アプリケーション全体で同じコンテナーを使う運用環境では、CreateIfNotExists を 1 回だけ呼び出すか、または前もってコンテナーを作成しておきコードで作成する必要がないようにするのが、よい方法です。

```csharp
// Create a CloudStorageAccount instance pointing to your storage account.
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the CloudBlobClient that is used to call the Blob Service for that storage account.
CloudBlobClient cloudBlobClient = storageAccount.CreateCloudBlobClient();

// Create a container called 'quickstartblobs'. 
CloudBlobContainer cloudBlobContainer = 
    cloudBlobClient.GetContainerReference("quickstartblobs");
await cloudBlobContainer.CreateIfNotExistsAsync();

// Set the permissions so the blobs are public. 
BlobContainerPermissions permissions = new BlobContainerPermissions();
permissions.PublicAccess = BlobContainerPublicAccessType.Blob;
await cloudBlobContainer.SetPermissionsAsync(permissions);
```

## <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage では、ブロック BLOB、追加 BLOB、およびページ BLOB がサポートされています。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。 

ファイルを BLOB にアップロードするには、ターゲット コンテナーの BLOB に対する参照を取得します。 BLOB の参照を取得した後は、[CloudBlockBlob.UploadFromFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromfileasync) を使ってそれにデータをアップロードできます。 この操作により、BLOB がまだ存在しない場合は作成され、既に存在する場合は上書きされます。

サンプル コードは、アップロードとダウンロードに使うローカル ファイルを作成し、アップロードするファイルを **fileAndPath** として、BLOB の名前を **localFileName** に格納します。 次の例では、ファイルを **quickstartblobs** という名前のコンテナーにアップロードします。

```csharp
// Create a file in MyDocuments to test the upload and download.
string localPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
string localFileName = "QuickStart_" + Guid.NewGuid().ToString() + ".txt";
string fileAndPath = Path.Combine(localPath, localFileName);
File.WriteAllText(fileAndPath, "Hello, World!");

//Upload the file.
CloudBlockBlob blockBlob = container.GetBlockBlobReference(localFileName);
await blockBlob.UploadFromFileAsync(fileAndPath);
```

Blob Storage では複数のアップロード方法を使うことができます。 たとえば、メモリ ストリームがある場合、UploadFromFileAsync ではなく UploadFromStreamAsync メソッドを使うことができます。 

ブロック BLOB の最大サイズは 4.7 TB であり、Excel スプレッドシートから大きなビデオ ファイルまで何にでも使うことができます。 ページ BLOB は、主に、IaaS VM のバックアップ用の VHD ファイルに使われます。 追加 BLOB は、ファイルに書き込んでから情報を追加する場合など、ログ記録に使われます。 BLOB ストレージに格納されているほとんどのオブジェクトはブロック BLOB です。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

コンテナー内のファイルの一覧を取得するには、[CloudBlobContainer.ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync) を使います。 次のコードは、BLOB の一覧を取得し、ループ処理して、見つかった BLOB の URI を表示します。 コマンド ウィンドウから URI をコピーしてブラウザーに貼り付けることで、ファイルを表示できます。

コンテナー内の BLOB が 5,000 個以下の場合、ListBlobsSegmentedAsync を 1 回呼び出すと、すべての BLOB 名が取得されます。 コンテナー内の BLOB が 5000 個より多い場合は、すべての BLOB 名が取得されるまで、5,000 個単位で一覧が取得されます。 この API を初めて呼び出すと、最初の 5,000 個の BLOB 名と継続トークンが返されます。 2 回目以降の呼び出しでは、このトークンを渡して、次の BLOB 名のセットを取得します。これを、継続トークンが null になるまで続けます。null は、すべての BLOB 名が取得されたことを示します。 

```csharp
// List the blobs in the container.
Console.WriteLine("List blobs in container.");
BlobContinuationToken blobContinuationToken = null;
do
{
    var results = await cloudBlobContainer.ListBlobsSegmentedAsync(null, blobContinuationToken);
    foreach(IListBlobItem item in results.Results)
    {
        Console.WriteLine(item.Uri);
    }
} while (blobContinuationToken != null);
```

## <a name="download-blobs"></a>BLOB をダウンロードする

ローカル ディスクに BLOB をダウンロードするには、[CloudBlob.DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync) を使います。

次のコードは、前のセクションでアップロードした BLOB をダウンロードし、ローカル ディスクで両方のファイルを見ることができるように、BLOB 名に "_DOWNLOADED" というサフィックスを追加します。 

```csharp
// Download blob. In most cases, you would have to retrieve the reference
//   to cloudBlockBlob here. However, we created that reference earlier, and 
//   haven't changed the blob we're interested in, so we can reuse it. 
// First, add a _DOWNLOADED before the .txt so you can see both files in MyDocuments.
string fileAndPath2 = fileAndPath.Replace(".txt", "_DOWNLOADED.txt");
Console.WriteLine("Downloading blob to {0}", fileAndPath2);
await cloudBlockBlob.DownloadToFileAsync(fileAndPath2, FileMode.Create);
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このクイックスタートでアップロードした BLOB が不要になった場合は、[CloudBlobContainer.DeleteAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteasync) を使ってコンテナー全体を削除できます。 また、不要になった場合は、作成されたファイルも削除します。

```csharp
await cloudBlobContainer.DeleteAsync();
File.Delete(fileAndPath);
File.Delete(fileAndPath2);    
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、.NET を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Blob Storage の操作についてさらに学習するには、Blob Storage の操作方法に関するトピックに進んでください。

> [!div class="nextstepaction"]
> [Blob Storage の操作方法](storage-dotnet-how-to-use-blobs.md)

Storage Explorer と BLOB について詳しくは、「[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」をご覧ください。
