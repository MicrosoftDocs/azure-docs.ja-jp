---
title: 'クイックスタート: Azure Blob Storage ライブラリ v12 - Xamarin'
description: このクイックスタートでは、Xamarin で Azure Blob Storage クライアント ライブラリ バージョン 12 を使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、モバイル デバイスに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: dab938a526aa89f6fe5a014e10869bd8da8b475d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98802362"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>クイックスタート: Azure Blob Storage クライアント ライブラリ v12 と Xamarin

Xamarin で Azure Blob Storage クライアント ライブラリ v12 を使用してみましょう。 Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。

Xamarin で Azure Blob Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

* コンテナーを作成する
* Azure Storage へ BLOB をアップロードする
* コンテナー内のすべての BLOB を一覧表示する
* お使いのデバイスに BLOB をダウンロードする
* コンテナーを削除する

参照リンク:

* [API リファレンス ドキュメント](/dotnet/api/azure.storage.blobs)
* [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [サンプル](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
* Azure Storage アカウント - [ストレージ アカウントの作成](../common/storage-account-create.md)
* [Mobile Development for .NET ワークロード](/xamarin/get-started/installation/?pivots=windows)がインストールされた Visual Studio または [Visual Studio for Mac](/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>設定
    
このセクションでは、Xamarin で Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。
    
### <a name="create-the-project"></a>プロジェクトを作成する

1. Visual Studio を開き、空白フォームのアプリを作成します。
1. これに次のように名前を付けます: BlobQuickstartV12

### <a name="install-the-package"></a>パッケージをインストールする

1. [ソリューション エクスプローラー] ウィンドウでソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択します。
1. **Azure.Storage.Blobs** を検索し、最新の安定バージョンを、ソリューション内のすべてのプロジェクトにインストールします。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

**BlobQuickstartV12** ディレクトリで次の操作を行います。

1. エディターで *MainPage.xaml* ファイルを開きます
1. `<ContentPage></ContentPage>` 要素間のすべてを削除し、以下で置き換えます。

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>オブジェクト モデル

Azure Blob Storage は、大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。 Blob Storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のコンテナー
* コンテナー内の BLOB

次の図に、これらのリソースの関係を示します。

![Blob Storage のアーキテクチャ図](./media/storage-blobs-introduction/blob1.png)

これらのリソースとやり取りするには、以下の .NET クラスを使用します。

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient):`BlobServiceClient` クラスを使用して、Azure Storage リソースと BLOB コンテナーを操作できます。
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient):`BlobContainerClient` クラスを使用して、Azure Storage コンテナーとその BLOB を操作できます。
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient):`BlobClient` クラスを使用して、Azure Storage BLOB を操作できます。
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo):`BlobDownloadInfo` クラスは、BLOB のダウンロードによって返されるプロパティとコンテンツを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、Xamarin.Forms アプリで .NET 用 Azure Blob Storage クライアント ライブラリを使用して以下のタスクを実行する方法を示します。

* [クラス レベルの変数を作成する](#create-class-level-variables)
* [コンテナーの作成](#create-a-container)
* [コンテナーに BLOB をアップロードする](#upload-blobs-to-a-container)
* [コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-container)
* [BLOB をダウンロードする](#download-blobs)
* [コンテナーの削除](#delete-a-container)

### <a name="create-class-level-variables"></a>クラス レベルの変数を作成する

以下のコードは、いくつかのクラス レベルの変数を宣言しています。 これらは、このサンプルの残りの部分で Azure Blob Storage と通信する必要があります。

これらを、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成したストレージ アカウントの接続文字列に追加します。

次のコードを *MainPage.xaml.cs* ファイル内のクラス レベルの変数として追加します。

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>コンテナーを作成する

新しいコンテナーの名前を決定します。 次のコードでは、確実に一意になるように、コンテナー名に GUID 値を追加します。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) クラスのインスタンスを作成します。 次に、[CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) メソッドを呼び出して、ストレージ アカウントにコンテナーを作成します。

次のコードを *MainPage.xaml.cs* ファイルに追加します。

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>コンテナーに BLOB をアップロードする

次のコード スニペット:

1. テキストの `MemoryStream` を作成します。
1. [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) クラスの [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) 関数を呼び出し、ファイル名とテキストの `MemoryStream` の両方でそれを渡してテキストを BLOB にアップロードします。 このメソッドは、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。

次のコードを *MainPage.xaml.cs* ファイルに追加します。

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) メソッドを呼び出して、コンテナー内の BLOB を一覧表示します。 この場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

次のコードを *MainPage.xaml.cs* ファイルに追加します。

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) メソッドを呼び出して、以前に作成した BLOB をダウンロードします。 このサンプル コードでは、BLOB の `Stream` 表現をまず `MemoryStream` にコピーし、次に `StreamReader` にコピーして、テキストを表示できるようにしています。

次のコードを *MainPage.xaml.cs* ファイルに追加します。

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>コンテナーを削除する

次のコードでは、[DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) を使用してコンテナー全体を削除することで、アプリによって作成されたリソースをクリーンアップします。

アプリは、BLOB とコンテナーを削除する前に、まず確認を求めます。 このとき、リソースが削除される前に、正しく作成されたことを確認できます。

次のコードを *MainPage.xaml.cs* ファイルに追加します。

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>コードの実行

アプリが起動されると、表示されるように、コンテナーが最初に作成されます。 ユーザーは、BLOB のアップロード、一覧表示、ダウンロード、およびコンテナーの削除を行うために、ボタンをクリックする必要があります。

Windows 上でアプリを実行するには、F5 キーを押します。 Mac 上でアプリを実行するには、Cmd + Enter キーを押します。

アプリは、各操作の後に画面に書き込みます。 アプリの出力は、次の例のようになります。

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

クリーンアップ プロセスを開始する前に、画面上の BLOB の内容の出力がアップロードされた値と一致していることを確認します。

値を確認したら、プロンプトを確認してコンテナーを削除し、デモを終了します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Xamarin で Azure Blob Storage クライアント ライブラリ v12 を使用して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明しました。

BLOB ストレージのサンプル アプリを確認するには、以下に進んでください。

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 の Xamarin サンプル](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* チュートリアル、サンプル、クイック スタート、およびその他のドキュメントについては、[モバイル開発者のための Azure](/azure/mobile-apps) に関するページを参照してください。
* Xamarin の詳細については、「[Xamarin の概要](/xamarin/get-started/)」を参照してください。
