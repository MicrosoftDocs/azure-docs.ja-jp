---
title: Azure Storage を使用してクラウド内のアプリケーションのデータに対するアクセスをセキュリティで保護する | Microsoft Docs
description: SAS トークン、暗号化、および HTTPS を使用して、クラウド内にあるアプリケーションのデータをセキュリティで保護します。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 05/30/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: ea865fe246bcb414dd1ab3f83b7135989bc0adf3
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894906"
---
# <a name="secure-access-to-an-applications-data-in-the-cloud"></a>クラウド内のアプリケーションのデータへのアクセスをセキュリティで保護する

このチュートリアルは、シリーズの第 3 部です。 ストレージ アカウントへのアクセスをセキュリティで保護する方法について説明します。 

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * SAS トークンを使用してサムネイル画像にアクセスする
> * サーバー側暗号化を有効にする
> * HTTPS のみの転送を有効にする

[Azure Blob Storage](../common/storage-introduction.md#blob-storage) には、アプリケーションのファイルを格納する堅牢なサービスを提供しています。 このチュートリアルでは、[前のトピック][previous-tutorial]の続きとして、Web アプリケーションのストレージ アカウントに対するアクセスをセキュリティで保護する方法について説明します。 完了すると、画像は暗号化され、Web アプリは安全な SAS トークンを使用してサムネイル画像にアクセスできるようになります。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、前の Storage のチュートリアル「[Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する][previous-tutorial]」を完了している必要があります。 

## <a name="set-container-public-access"></a>コンテナーのパブリック アクセスを設定する

チュートリアル シリーズの今回は、サムネイルへのアクセスに SAS トークンを使用します。 この手順では、_thumbnails_ コンテナーのパブリック アクセスを `off` に設定します。

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbnails  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>サムネイルの SAS トークンを構成する

このチュートリアル シリーズの第 1 部では、Web アプリケーションでパブリック コンテナーの画像を表示しました。 シリーズの今回は、[Shared Access Signature (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) トークンを使用してサムネイル画像を取得します。 SAS トークンを使用すると、IP、プロトコル、間隔、または許可されている権限に基づいて、コンテナーまたは BLOB へのアクセスを制限することができます。

この例では、ソース コード リポジトリが `sasTokens` ブランチを使用します。このブランチには更新されたコード サンプルがあります。 [az webapp deployment source delete](/cli/azure/webapp/deployment/source) を使用して、既存の GitHub デプロイを削除します。 次に、[az webapp deployment source config](/cli/azure/webapp/deployment/source) コマンドを使用して、Web アプリへの GitHub デプロイを構成します。  

次のコマンドの `<web-app>` は、Web アプリの名前です。  

```azurecli-interactive 
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch sasTokens --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

リポジトリの `sasTokens` ブランチによって `StorageHelper.cs` ファイルが更新されます。 `GetThumbNailUrls` タスクは以下のコード例で置き換えられます。 更新されたタスクは、SAS トークンの開始時刻、有効期限、およびアクセス許可を指定するように [SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet) を設定し、サムネイルの URL を取得します。 Web アプリがデプロイされると、Web アプリは SAS トークンを使用し、URL を指定してサムネイルを取得します。 更新されたタスクは次の例のようになります。
    
```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create blob client
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the container
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ThumbnailContainer);

    BlobContinuationToken continuationToken = null;

    BlobResultSegment resultSegment = null;

    //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
    //When the continuation token is null, the last page has been returned and execution can exit the loop.
    do
    {
        //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
        //or by calling a different overload.
        resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);

        foreach (var blobItem in resultSegment.Results)
        {
            CloudBlockBlob blob = blobItem as CloudBlockBlob;
            //Set the expiry time and permissions for the blob.
            //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
            //The shared access signature will be valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

            sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);

            sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);

            sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

            //Generate the shared access signature on the blob, setting the constraints directly on the signature.
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(blob.Uri + sasBlobToken);

        }

        //Get the continuation token.
        continuationToken = resultSegment.ContinuationToken;
    }

    while (continuationToken != null);

    return await Task.FromResult(thumbnailUrls);
}
```

上のタスクでは、次のクラス、プロパティ、およびメソッドが使用されています。

|クラス  |Properties| メソッド  |
|---------|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)    |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)     | |[CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)        |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     | |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)     | |[SetPermissionsAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.setpermissionsasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_SetPermissionsAsync_Microsoft_WindowsAzure_Storage_Blob_BlobContainerPermissions_) <br> [ListBlobsSegmentedAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmentedasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmentedAsync_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_)       |
|[BlobContinuationToken](/dotnet/api/microsoft.windowsazure.storage.blob.blobcontinuationtoken?view=azure-dotnet)     |         |
|[BlobResultSegment](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment?view=azure-dotnet)    | [結果](/dotnet/api/microsoft.windowsazure.storage.blob.blobresultsegment.results?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobResultSegment_Results)         |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)    |         | [GetSharedAccessSignature](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_GetSharedAccessSignature_Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_)
|[SharedAccessBlobPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy?view=azure-dotnet)     | [SharedAccessStartTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessstarttime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessStartTime)<br>[SharedAccessExpiryTime](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.sharedaccessexpirytime?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_SharedAccessExpiryTime)<br>[アクセス許可](/dotnet/api/microsoft.windowsazure.storage.blob.sharedaccessblobpolicy.permissions?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_SharedAccessBlobPolicy_Permissions) |        |

## <a name="server-side-encryption"></a>サーバー側暗号化

[Azure Storage Service Encryption (SSE)](../common/storage-service-encryption.md) を使用すると、データを保護できます。 SSE は、暗号化、復号化、キー管理を処理して、保存データを暗号化します。 現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によってすべてのデータを暗号化します。

SSE は、すべてのパフォーマンス レベル (Standard および Premium)、すべてのデプロイ モデル (Azure Resource Manager とクラシック)、すべての Azure Storage サービス (BLOB、Queue、Table、File) のデータを自動的に暗号化します。 

## <a name="enable-https-only"></a>HTTPS のみを有効にする

ストレージ アカウントから送受信されるデータの要求をセキュリティで保護するには、要求を HTTPS のみに制限する方法があります。 [az storage account update](/cli/azure/storage/account) コマンドを使用して、ストレージ アカウントが必要なプロトコルを更新します。

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

`HTTP` プロトコルで `curl` を使用して接続をテストします。

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

これでセキュリティで保護された転送が必要になり、次のメッセージを受け取ります。

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>次の手順

シリーズの第 3 部では、ストレージ アカウントに対するアクセスをセキュリティで保護する方法について学びました。

> [!div class="checklist"]
> * SAS トークンを使用してサムネイル画像にアクセスする
> * サーバー側暗号化を有効にする
> * HTTPS のみの転送を有効にする

次はシリーズの第 4 部に進み、クラウド ストレージ アプリケーションの監視とトラブルシューティングの方法を学びます。

> [!div class="nextstepaction"]
> [クラウド アプリケーション ストレージの監視とトラブルシューティング](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
