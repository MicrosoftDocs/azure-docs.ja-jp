---
title: "Azure Storage を使用してクラウド内のアプリケーションのデータに対するアクセスをセキュリティで保護する | Microsoft Docs"
description: "SAS トークン、暗号化、および HTTPS を使用して、クラウド内にあるアプリケーションのデータをセキュリティで保護します。"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/19/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 11b141617bea5962c45e1b91cf6629c2edd26c3d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
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

このチュートリアルを完了するには、前の Storage のチュートリアル「[Automate resizing uploaded images using Event Grid][previous-tutorial]」(Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する) を完了している必要があります。 

## <a name="set-container-public-access"></a>コンテナーのパブリック アクセスを設定する

チュートリアル シリーズの今回は、サムネイルへのアクセスに SAS トークンを使用します。 この手順では、_thumbs_ コンテナーのパブリック アクセスを `off` に設定します。

```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \ --account-name $blobStorageAccount \ --account-key $blobStorageAccountKey \ --name thumbs  \
--public-access off
``` 

## <a name="configure-sas-tokens-for-thumbnails"></a>サムネイルの SAS トークンを構成する

このチュートリアル シリーズの第 1 部では、Web アプリケーションでパブリック コンテナーの画像を表示しました。 シリーズの今回は、[Secure Access Signature (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md#what-is-a-shared-access-signature) トークンを使用してサムネイル画像を取得します。 SAS トークンを使用すると、IP、プロトコル、間隔、または許可されている権限に基づいて、コンテナーまたは BLOB へのアクセスを制限することができます。

この例では、ソース コード リポジトリが `sasTokens` ブランチを使用します。このブランチには更新されたコード サンプルがあります。 [az webapp deployment source delete](/cli/azure/webapp/deployments/source#delete) を使用して、既存の GitHub デプロイを削除します。 次に、[az webapp deployment source config](/cli/azure/webapp/deployment/source#config) コマンドを使用して、Web アプリへの GitHub デプロイを構成します。  

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

    // Set the permission of the container to public
    await container.SetPermissionsAsync(new BlobContainerPermissions { PublicAccess = BlobContainerPublicAccessType.Blob });

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

|クラス  |プロパティ| メソッド  |
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

次のサンプルでは、BLOB の暗号化を有効にしています。 暗号化を有効にする前に作成した既存の BLOB は暗号化されません。 BLOB に対する要求の `x-ms-server-encrypted` ヘッダーには、BLOB の暗号化状態が表示されます。

```azurecli-interactive
az storage account update --encryption-services blob --name <storage-account-name> --resource-group myResourceGroup
```

暗号化が有効になった Web アプリケーションに新しいイメージをアップロードします。

`curl` とスイッチ `-I` を使用して、ヘッダーのみを取得し、`<storage-account-name>`、`<container>`、および `<blob-name>` に実際の値に置き換えます。  

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<blob-name>?$sasToken -I
```

応答の `x-ms-server-encrypted` ヘッダーには `true` が表示されます。 このヘッダーを見ると、データが SSE で暗号化されていることがわかります。

```
HTTP/1.1 200 OK
Content-Length: 209489
Content-Type: image/png
Last-Modified: Mon, 11 Sep 2017 19:27:42 GMT
Accept-Ranges: bytes
ETag: "0x8D4F94B2BE76D45"
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 57047db3-001e-0050-3e34-2ba769000000
x-ms-version: 2017-04-17
x-ms-lease-status: unlocked
x-ms-lease-state: available
x-ms-blob-type: BlockBlob
x-ms-server-encrypted: true
Date: Mon, 11 Sep 2017 19:27:46 GMT
```

## <a name="enable-https-only"></a>HTTPS のみを有効にする

ストレージ アカウントから送受信されるデータの要求をセキュリティで保護するには、要求を HTTPS のみに制限する方法があります。 [az storage account update](/cli/azure/storage/account#update) コマンドを使用して、ストレージ アカウントが必要なプロトコルを更新します。

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

## <a name="next-steps"></a>次のステップ

シリーズの第 3 部では、ストレージ アカウントに対するアクセスをセキュリティで保護する方法について学びました。

> [!div class="checklist"]
> * SAS トークンを使用してサムネイル画像にアクセスする
> * サーバー側暗号化を有効にする
> * HTTPS のみの転送を有効にする

次はシリーズの第 4 部に進み、クラウド ストレージ アプリケーションの監視とトラブルシューティングの方法を学びます。

> [!div class="nextstepaction"]
> [クラウド アプリケーション ストレージの監視とトラブルシューティング](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json