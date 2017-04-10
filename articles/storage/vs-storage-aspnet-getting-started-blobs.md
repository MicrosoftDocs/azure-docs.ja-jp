---
title: "Azure Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET) | Microsoft Docs"
description: "Visual Studio 接続済みサービスを使用してストレージ アカウントに接続した後、Visual Studio の ASP.NET プロジェクトで Azure Blob Storage の使用を開始する方法について説明します。"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: b3497055-bef8-4c95-8567-181556b50d95
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/21/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 5840ec74f6af2e373d9ebb34b0f6e13094c33f19
ms.openlocfilehash: 003ca36c80274c4cad430ac67cf8cf3551d4137a


---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Azure Blob Storage と Visual Studio 接続済みサービスの概要 (ASP.NET)
[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要

Azure Blob Storage は、非構造化データをクラウド内にオブジェクト/BLOB として格納するサービスです。 Blob Storage は、ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを格納できます。 Blob Storage は、オブジェクト ストレージとも呼ばれます。

このチュートリアルでは、Azure Blob Storage を使用していくつかの一般的なシナリオの ASP.NET コードを記述する方法を示します。 シナリオには、BLOB コンテナーの作成や、BLOB のアップロード、一覧表示、ダウンロード、および削除が含まれます。

##<a name="prerequisites"></a>前提条件

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Azure Storage アカウント](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC コントローラーを作成する 

1. **ソリューション エクスプローラー**で**コントローラー**を右クリックし、コンテキスト メニューで **[追加]、[コントローラー]** の順に選択します。

    ![ASP.NET MVC アプリケーションへのコントローラーの追加](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. **[スキャフォールディングの追加]** ダイアログ ボックスで **[MVC 5 コントローラー - 空]** を選択し、**[追加]** を選択します。

    ![MVC コントローラーの種類を指定する](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. **[コントローラーの追加]** ダイアログで、コントローラーに *BlobsController* という名前を付けて、**[追加]** を選択します。

    ![MVC コントローラー指定](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. 次の *using* ディレクティブを `BlobsController.cs` ファイルに追加します。

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="create-a-blob-container"></a>BLOB コンテナーを作成する

BLOB コンテナーとは、BLOB とフォルダーの入れ子になった階層です。 次の手順では、BLOB コンテナーを作成する方法を説明します。

> [!NOTE]
> 
> このセクションのコードは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。 

1. `BlobsController.cs` ファイルを開きます。

1. **ActionResult** を返す **CreateBlobContainer** というメソッドを追加します。

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **CreateBlobContainer** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウント情報を取得できます。 (*&lt;storage-account-name>* はアクセスする Azure ストレージ アカウントの名前に変更します)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. BLOB サービス クライアントを表す **CloudBlobClient** オブジェクトを取得します。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. 目的の BLOB コンテナー名への参照を表す **CloudBlobContainer** オブジェクトを取得します。 **CloudBlobClient.GetContainerReference** メソッドでは、Blob Storage ストレージに対する要求は行われません。 BLOB コンテナーが存在するかどうかにかかわらず、参照が返されます。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. まだない場合は、**CloudBlobContainer.CreateIfNotExists** メソッドを呼び出して、コンテナーを作成します。 **CloudBlobContainer.CreateIfNotExists** メソッドでは、コンテナーが存在せず、正常に作成された場合は **true** が返されます。 それ以外の場合は、**false** が返されます。    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. **ViewBag** を BLOB コンテナーの名前に変更します。

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、**[BLOB]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加] ** ダイアログで、ビューの名前として **CreateBlobContainer** と入力し、**[追加]** を選択します。

1. `CreateBlobContainer.cshtml` を開き、次のコード スニペットのように変更します。

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. アプリケーションを実行して **[BLOB コンテナーの作成]** を選択し、次のスクリーン ショットと同様の結果が表示されることを確認します。
  
    ![BLOB コンテナーを作成する](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    前述したように、**CloudBlobContainer.CreateIfNotExists** メソッドは、コンテナーが存在しないため作成された場合にのみ **true** を返します。 そのため、コンテナーが存在するときにアプリを実行した場合、メソッドは **false** を返します。 アプリを複数回実行するには、アプリを再実行する前にコンテナーを削除する必要があります。 コンテナーを削除するには、**CloudBlobContainer.Delete** メソッドを使用します。 [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) または [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用してコンテナーを削除することもできます。  

## <a name="upload-a-blob-into-a-blob-container"></a>BLOB コンテナーに BLOB をアップロードする

[BLOB コンテナーを作成したら](#create-a-blob-container)、そのコンテナーにファイルをアップロードできます。 このセクションでは、ローカル ファイルを BLOB コンテナーにアップロードする手順を説明します。 この手順では、*test-blob-container* という名前の BLOB コンテナーを作成していることを前提とします。 

> [!NOTE]
> 
> このセクションのコードは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。 

1. `BlobsController.cs` ファイルを開きます。

1. **EmptyResult** を返す **UploadBlob** というメソッドを追加します。

    ```csharp
    public EmptyResult UploadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. **UploadBlob** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウントの情報を取得します (*&lt;storage-account-name>* をアクセス対象の Azure ストレージ アカウントの名前に変更してください)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. BLOB サービス クライアントを表す **CloudBlobClient** オブジェクトを取得します。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. BLOB コンテナー名への参照を表す **CloudBlobContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. 前述のように、Azure Storage ではさまざまな種類の BLOB がサポートされています。 ページ BLOB への参照を取得するには、**CloudBlobContainer.GetPageBlobReference** メソッドを呼び出します。 ブロック BLOB への参照を取得するには、**CloudBlobContainer.GetBlockBlobReference** メソッドを呼び出します。 通常は、ブロック BLOB を使用することをお勧めします。 (<blob-name>* を、アップロードされたときに BLOB に付ける名前に変更します)。

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. BLOB の参照を取得したら、BLOB 参照オブジェクトの **UploadFromStream** メソッドを呼び出すことによって、データの任意のストリームを BLOB にアップロードできます。 **UploadFromStream** メソッドにより、BLOB が存在しない場合は作成され、存在する場合は上書きされます。 (*&lt;file-to-upload>* を、アップロードするファイルへの完全修飾パスに変更します)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(<file-to-upload>))
    {
        blob.UploadFromStream(fileStream);
    }
    ```

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、**[BLOB]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. アプリケーションを実行し、**[BLOB のアップロード]** を選択します。  
  
「[BLOB コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-blob-container)」セクションで、BLOB コンテナー内の BLOB を一覧表示する方法について説明します。    

## <a name="list-the-blobs-in-a-blob-container"></a>BLOB コンテナー内の BLOB を一覧表示する

このセクションでは、BLOB コンテナー内の BLOB を一覧表示する方法について説明します。 サンプル コードでは、セクション「[BLOB コンテナーを作成する](#create-a-blob-container)」で作成された *test-blob-container* を参照します。

> [!NOTE]
> 
> このセクションのコードは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。 

1. `BlobsController.cs` ファイルを開きます。

1. **ActionResult** を返す **ListBlobs** というメソッドを追加します。

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ListBlobs** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウントの情報を取得します (*&lt;storage-account-name>* をアクセス対象の Azure ストレージ アカウントの名前に変更してください)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. BLOB サービス クライアントを表す **CloudBlobClient** オブジェクトを取得します。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. BLOB コンテナー名への参照を表す **CloudBlobContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. BLOB コンテナー内の BLOB を一覧表示するには、**CloudBlobContainer.ListBlobs** メソッドを使います。 **CloudBlobContainer.ListBlobs** メソッドは、**CloudBlockBlob**、**CloudPageBlob**、または **CloudBlobDirectory** オブジェクトにキャストする **IListBlobItem** オブジェクトを返します。 次のコード スニペットでは、BLOB コンテナー内のすべての BLOB を列挙します。 各 BLOB がその型に基づいて適切なオブジェクトにキャストされ、その名前 (または、**CloudBlobDirectory** の場合は URI) が一覧に追加されます。

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs(null, false))
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    BLOB コンテナーには BLOB だけでなく、ディレクトリを含めることもできます。 次のような階層の *test-blob-container* いう BLOB コンテナーがあるとします。

        foo.png
        dir1/bar.png
        dir2/baz.png

    前のコード例を使用すると、**BLOB** 文字列リストには、次のような値が含まれます。

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    ご覧のように、リストには最上位のエンティティのみが含まれ、入れ子になったエンティティ (*bar.png* や *baz.png*) は含まれていません。 BLOB コンテナー内のすべてのエンティティを一覧表示するには、**CloudBlobContainer.ListBlobs** メソッドを呼び出して、**useFlatBlobListing** パラメーターに **true** を渡す必要があります。    

    ```csharp
    ...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    ...
    ```

    **useFlatBlobListing** パラメーターを **true** に設定すると、BLOB コンテナー内のすべてのエンティティのフラットな一覧が返され、次の結果が返されます。

        foo.png
        dir1/bar.png
        dir2/baz.png

1. **ソリューション エクスプローラー**で **[ビュー]** フォルダーを展開し、**[BLOB]** を右クリックし、コンテキスト メニューで **[追加]、[ビュー]** の順に選択します。

1. **[ビューの追加] ** ダイアログで、ビューの名前として **ListBlobs** と入力し、**[追加]** を選択します。

1. `ListBlobs.cshtml` を開き、次のコード スニペットのように変更します。

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. アプリケーションを実行して **[List blobs (BLOB の一覧表示)]** を選択し、次のスクリーン ショットと同様の結果が表示されることを確認します。
  
    ![BLOB の一覧](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>BLOB をダウンロードする

このセクションでは、BLOB をダウンロードし、ローカル ストレージに保存するか、または内容を文字列に読み込む方法を説明します。 サンプル コードでは、セクション「[BLOB コンテナーを作成する](#create-a-blob-container)」で作成された *test-blob-container* を参照します。

1. `BlobsController.cs` ファイルを開きます。

1. **ActionResult** を返す **DownloadBlob** というメソッドを追加します。

    ```csharp
    public EmptyResult DownloadBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```
 
1. **DownloadBlob** メソッド内で、ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウントの情報を取得します (*&lt;storage-account-name>* をアクセス対象の Azure ストレージ アカウントの名前に変更してください)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. BLOB サービス クライアントを表す **CloudBlobClient** オブジェクトを取得します。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. BLOB コンテナー名への参照を表す **CloudBlobContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. **CloudBlobContainer.GetBlockBlobReference** または **CloudBlobContainer.GetPageBlobReference** メソッドを呼び出して、BLOB 参照オブジェクトを取得します。 (*&lt;blob-name>* をダウンロードする BLOB の名前に変更します)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
    ```

1. BLOB をダウンロードするには、BLOB の種類に基づいて **CloudBlockBlob.DownloadToStream** または **CloudPageBlob.DownloadToStream** メソッドを使用します。 次のコード スニペットは、**CloudBlockBlob.DownloadToStream** メソッドを使用して、ローカル ファイルに保存されるストリーム オブジェクトに BLOB の内容を転送します (*&lt;local-file-name>* を BLOB をダウンロードする場所を表す完全修飾ファイル名に変更します)。 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. アプリケーションを実行し、**[BLOB のダウンロード]** を選択して、BLOB をダウンロードします。 **CloudBlobContainer.GetBlockBlobReference** メソッド呼び出しで指定された BLOB が、**File.OpenWrite** メソッド呼び出しで指定された場所にダウンロードされます。 

## <a name="delete-blobs"></a>BLOB を削除する

次の手順では、BLOB を削除する方法を説明します。

> [!NOTE]
> 
> このセクションのコードは、「[開発環境を設定する](#set-up-the-development-environment)」の手順を完了していることを前提にしています。 

1. `BlobsController.cs` ファイルを開きます。

1. **ActionResult** を返す **DeleteBlob** というメソッドを追加します。

    ```csharp
    public EmptyResult DeleteBlob()
    {
        // The code in this section goes here.

        return new EmptyResult();
    }
    ```

1. ストレージ アカウント情報を表す **CloudStorageAccount** オブジェクトを取得します。 次のコードを使用して、Azure サービス構成からストレージ接続文字列とストレージ アカウントの情報を取得します (*&lt;storage-account-name>* をアクセス対象の Azure ストレージ アカウントの名前に変更してください)。
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. BLOB サービス クライアントを表す **CloudBlobClient** オブジェクトを取得します。
   
    ```csharp
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    ```

1. BLOB コンテナー名への参照を表す **CloudBlobContainer** オブジェクトを取得します。 
   
    ```csharp
    CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
    ```

1. **CloudBlobContainer.GetBlockBlobReference** または **CloudBlobContainer.GetPageBlobReference** メソッドを呼び出して、BLOB 参照オブジェクトを取得します。 (*&lt;blob-name>* を、削除する BLOB の名前に変更します)

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference(<blob-name>);
        ```

1. To delete a blob, use the **Delete** method.

    ```csharp
    blob.Delete();
    ```

1. **ソリューション エクスプローラー**で、**[ビュー]、[共有]** フォルダーを順に展開し、`_Layout.cshtml` を開きます。

1. 最後の **Html.ActionLink** の後に、次の **Html.ActionLink** を追加します。

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. アプリケーションを実行し、**[BLOB の削除]** を選択して、**CloudBlobContainer.GetBlockBlobReference** メソッド呼び出しで指定された BLOB を削除します。 

## <a name="next-steps"></a>次のステップ
Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。

  * [テーブル ストレージと Visual Studio 接続済みサービスの概要 (ASP.NET)](./vs-storage-aspnet-getting-started-tables.md)
  * [Azure キュー ストレージと Visual Studio 接続済みサービスの概要](./vs-storage-aspnet-getting-started-queues.md)


<!--HONumber=Jan17_HO1-->


