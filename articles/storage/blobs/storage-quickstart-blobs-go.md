---
title: Azure クイック スタート - Go を使用してオブジェクト ストレージに BLOB を作成する | Microsoft Docs
description: このクイック スタートでは、ストレージ アカウントとコンテナーをオブジェクト (BLOB) ストレージ内に作成します。 その後、Go 用のストレージ クライアント ライブラリを使用して、Azure Storage への BLOB のアップロード、BLOB のダウンロード、およびコンテナー内の BLOB の一覧表示を行います。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: bf0e868e9ee746da1dfe1b03403d21f7edb3bd5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544651"
---
# <a name="quickstart-upload-download-and-list-blobs-using-go"></a>クイック スタート: Go を使用して BLOB をアップロード、ダウンロード、および一覧表示する

このクイックスタートでは、Go プログラミング言語を使って、Azure Blob Storage 内のコンテナーでブロック BLOB のアップロード、ダウンロード、一覧取得を行う方法を説明します。 

## <a name="prerequisites"></a>前提条件

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

次の追加の前提条件がインストールされていることを確認してください。
 
* [Go 1.8 以上](https://golang.org/dl/)
* [Azure Storage Blob SDK for Go](https://github.com/azure/azure-storage-blob-go/) (次のコマンドを使用)

    ```
    go get -u github.com/Azure/azure-storage-blob-go/azblob
    ``` 

    > [!NOTE]
    > SDK を使用するときに大文字と小文字の区別に関連するインポートの問題を回避するために、`Azure` の先頭文字は必ず大文字にしてください。 インポート ステートメントでも `Azure` の先頭文字を大文字にします。
    
## <a name="download-the-sample-application"></a>サンプル アプリケーションのダウンロード
このクイックスタートで使う[サンプル アプリケーション](https://github.com/Azure-Samples/storage-blobs-go-quickstart.git)は、基本的な Go アプリケーションです。  

アプリケーションのコピーを開発環境にダウンロードするには、[git](https://git-scm.com/) を使います。 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-go-quickstart 
```

このコマンドは、ローカルの git フォルダーにリポジトリを複製します。 BLOB ストレージ用の Go サンプルを開くために、storage-quickstart.go ファイルを探します。  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成
このソリューションでは、サンプルを実行するマシンに対してローカルな環境変数に、ストレージ アカウントの名前とキーが安全に格納される必要があります。 環境変数を作成するオペレーティング システムに応じて、以下のいずれかの例に従います。

# <a name="linux"></a>[Linux](#tab/linux)

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

# <a name="windows"></a>[Windows](#tab/windows)

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-sample"></a>サンプルを実行する
このサンプルでは、現在のフォルダーにテスト ファイルを作成してそれを BLOB ストレージにアップロードし、コンテナー内の BLOB の一覧を取得してから、そのファイルをバッファーにダウンロードします。 

サンプルを実行するには、次のコマンドを発行します。 

```go run storage-quickstart.go```

次の出力は、アプリケーションを実行するときに返される出力の例です。
  
```
Azure Blob storage quick start sample
Creating a container named quickstart-5568059279520899415
Creating a dummy file to test the upload and download
Uploading the file with blob name: 630910657703031215
Blob name: 630910657703031215
Downloaded the blob: hello world
this is a blob
Press the enter key to delete the sample files, example container, and exit the application.
```
キーを押して続行すると、サンプル プログラムによってストレージ コンテナーとファイルが削除されます。 

> [!TIP]
> [Azure Storage Explorer](https://storageexplorer.com) などのツールを使って、Blob Storage のファイルを表示することもできます。 Microsoft Azure Storage Explorer は無料のクロスプラットフォーム ツールであり、ストレージ アカウントの情報にアクセスできます。 
>

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

次に、サンプル コードを実行して、そのしくみを理解できるようにします。

### <a name="create-containerurl-and-bloburl-objects"></a>ContainerURL オブジェクトと BlobURL オブジェクトを作成する
最初に、BLOB ストレージにアクセスして管理するために使用される ContainerURL オブジェクトと BlobURL オブジェクトへの参照を作成します。 これらのオブジェクトは、REST API を発行する低レベルの API (Create、Upload、Download など) を提供します。

* [**SharedKeyCredential**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#SharedKeyCredential) 構造体を使用して資格情報を格納します。 

* 資格情報とオプションを使用して [**パイプライン**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#NewPipeline)を作成します。 パイプラインでは、再試行ポリシー、ログの記録、HTTP 応答ペイロードの逆シリアル化などを指定します。  

* コンテナーに対する操作 (Create) と BLOB に対する操作 (Upload および Download) を実行するために、新しい [**ContainerURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#ContainerURL) オブジェクトと新しい [**BlobURL**](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#BlobURL) オブジェクトをインスタンス化します。


ContainerURL を作成したら、BLOB をポイントする **BlobURL** オブジェクトをインスタンス化して、アップロード、ダウンロード、コピーなどの操作を実行できます。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前の詳細については、「[コンテナー、BLOB、メタデータの名前付けと参照](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

このセクションでは、新しいコンテナーを作成します。 コンテナーの名前は **quickstartblobs-[random string]** です。 

```go 
// From the Azure portal, get your storage account name and key and set environment variables.
accountName, accountKey := os.Getenv("AZURE_STORAGE_ACCOUNT"), os.Getenv("AZURE_STORAGE_ACCESS_KEY")
if len(accountName) == 0 || len(accountKey) == 0 {
    log.Fatal("Either the AZURE_STORAGE_ACCOUNT or AZURE_STORAGE_ACCESS_KEY environment variable is not set")
}

// Create a default request pipeline using your storage account name and account key.
credential, err := azblob.NewSharedKeyCredential(accountName, accountKey)
if err != nil {
    log.Fatal("Invalid credentials with error: " + err.Error())
}
p := azblob.NewPipeline(credential, azblob.PipelineOptions{})

// Create a random string for the quick start container
containerName := fmt.Sprintf("quickstart-%s", randomString())

// From the Azure portal, get your storage account blob service URL endpoint.
URL, _ := url.Parse(
    fmt.Sprintf("https://%s.blob.core.windows.net/%s", accountName, containerName))

// Create a ContainerURL object that wraps the container URL and a request
// pipeline to make requests.
containerURL := azblob.NewContainerURL(*URL, p)

// Create the container
fmt.Printf("Creating a container named %s\n", containerName)
ctx := context.Background() // This example uses a never-expiring context
_, err = containerURL.Create(ctx, azblob.Metadata{}, azblob.PublicAccessNone)
handleErrors(err)
```
### <a name="upload-blobs-to-the-container"></a>BLOB をコンテナーにアップロードする

Blob Storage は、ブロック BLOB、追加 BLOB、およびページ BLOB をサポートします。 最もよく使われるのはブロック BLOB であり、このクイックスタートでもそれを使います。  

ファイルを BLOB にアップロードするには、**os.Open** を使用してファイルを開きます。 Upload (PutBlob)、StageBlock/CommitBlockList (PutBlock/PutBlockList) のいずれかの REST API を使用して、指定したパスにファイルをアップロードできます。 

別の方法として、低レベルの REST API の上に構築された[高レベルの API](https://github.com/Azure/azure-storage-blob-go/blob/master/azblob/highlevel.go) が SDK に用意されています。 たとえば ***UploadFileToBlockBlob*** 関数では、スループットを最適化するために、StageBlock (PutBlock) 操作を使用してチャンクにしたファイルを同時にアップロードします。 ファイルが 256 MB 未満の場合、代わりに Upload (PutBlob) を使用して単一のトランザクションで転送を完了します。

次の例では、ファイルを **quickstartblobs-[randomstring]** という名前のコンテナーにアップロードします。

```go
// Create a file to test the upload and download.
fmt.Printf("Creating a dummy file to test the upload and download\n")
data := []byte("hello world this is a blob\n")
fileName := randomString()
err = ioutil.WriteFile(fileName, data, 0700)
handleErrors(err)

// Here's how to upload a blob.
blobURL := containerURL.NewBlockBlobURL(fileName)
file, err := os.Open(fileName)
handleErrors(err)

// You can use the low-level Upload (PutBlob) API to upload files. Low-level APIs are simple wrappers for the Azure Storage REST APIs.
// Note that Upload can upload up to 256MB data in one shot. Details: https://docs.microsoft.com/rest/api/storageservices/put-blob
// To upload more than 256MB, use StageBlock (PutBlock) and CommitBlockList (PutBlockList) functions. 
// Following is commented out intentionally because we will instead use UploadFileToBlockBlob API to upload the blob
// _, err = blobURL.Upload(ctx, file, azblob.BlobHTTPHeaders{ContentType: "text/plain"}, azblob.Metadata{}, azblob.BlobAccessConditions{})
// handleErrors(err)

// The high-level API UploadFileToBlockBlob function uploads blocks in parallel for optimal performance, and can handle large files as well.
// This function calls StageBlock/CommitBlockList for files larger 256 MBs, and calls Upload for any file smaller
fmt.Printf("Uploading the file with blob name: %s\n", fileName)
_, err = azblob.UploadFileToBlockBlob(ctx, file, blobURL, azblob.UploadToBlockBlobOptions{
    BlockSize:   4 * 1024 * 1024,
    Parallelism: 16})
handleErrors(err)
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

**ContainerURL** で **ListBlobs** メソッドを使用して、コンテナー内のファイルの一覧を取得します。 ListBlobs は、指定された **マーカー** から開始して単一セグメントの BLOB (最大 5,000) を返します。 空のマーカーを使用して最初から列挙を開始します。 BLOB 名は辞書式順序で返されます。 セグメントの取得後、それを処理し、前に返されたマーカーを渡す ListBlobs を再度呼び出します。  

```go
// List the container that we have created above
fmt.Println("Listing the blobs in the container:")
for marker := (azblob.Marker{}); marker.NotDone(); {
    // Get a result segment starting with the blob indicated by the current Marker.
    listBlob, err := containerURL.ListBlobsFlatSegment(ctx, marker, azblob.ListBlobsSegmentOptions{})
    handleErrors(err)

    // ListBlobs returns the start of the next segment; you MUST use this to get
    // the next segment (after processing the current result segment).
    marker = listBlob.NextMarker

    // Process the blobs returned in this result segment (if the segment is empty, the loop body won't execute)
    for _, blobInfo := range listBlob.Segment.BlobItems {
        fmt.Print(" Blob name: " + blobInfo.Name + "\n")
    }
}
```

### <a name="download-the-blob"></a>BLOB をダウンロードする

BlobURL に **Download** 低レベル関数を使用して、BLOB をダウンロードします。 これにより、**DownloadResponse** 構造体が返されます。 構造体に対して関数 **Body** を実行して、データを読み取るための **RetryReader** ストリームを取得します。 読み取り中に接続がエラーになると、接続を再確立して読み取りを続行するために、追加の要求が行われます。 MaxRetryRequests を 0 (既定) に設定して RetryReaderOption を指定すると、元の応答本文が返され、再試行は実行されません。 または、高レベルの API **DownloadBlobToBuffer** または **DownloadBlobToFile** を使用して、コードを簡素化します。

次のコードでは、**Download** 関数を使用して BLOB をダウンロードします。 BLOB の内容がバッファーに書き込まれ、コンソールに表示されます。

```go
// Here's how to download the blob
downloadResponse, err := blobURL.Download(ctx, 0, azblob.CountToEnd, azblob.BlobAccessConditions{}, false)

// NOTE: automatically retries are performed if the connection fails
bodyStream := downloadResponse.Body(azblob.RetryReaderOptions{MaxRetryRequests: 20})

// read the body into a buffer
downloadedData := bytes.Buffer{}
_, err = downloadedData.ReadFrom(bodyStream)
handleErrors(err)
```

### <a name="clean-up-resources"></a>リソースをクリーンアップする
このクイックスタートでアップロードした BLOB が不要になった場合は、**Delete** メソッドを使ってコンテナー全体を削除できます。 

```go
// Cleaning up the quick start by deleting the container and the file created locally
fmt.Printf("Press enter key to delete the sample files, example container, and exit the application.\n")
bufio.NewReader(os.Stdin).ReadBytes('\n')
fmt.Printf("Cleaning up.\n")
containerURL.Delete(ctx, azblob.ContainerAccessConditions{})
file.Close()
os.Remove(fileName)
```

## <a name="resources-for-developing-go-applications-with-blobs"></a>BLOB を使用する Go アプリケーションを開発するためのリソース

Blob Storage を使用する Go 開発については、以下の追加リソースを参照してください。

- GitHub で Azure Storage 用の [Go クライアント ライブラリ ソース コード](https://github.com/Azure/azure-storage-blob-go)を確認し、インストールします。
- Go クライアント ライブラリを使用して記述された [Blob Storage のサンプル](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob#pkg-examples)を確認します。

## <a name="next-steps"></a>次のステップ
 
このクイックスタートでは、Go を使ってローカル ディスクと Azure Blob Storage との間でファイルを転送する方法について学習しました。 Azure Storage Blob SDK の詳細については、[ソース コード](https://github.com/Azure/azure-storage-blob-go/)と [API リファレンス](https://godoc.org/github.com/Azure/azure-storage-blob-go/azblob)をご覧ください。