---
title: アプリケーション データへのアクセスをセキュリティで保護する
titleSuffix: Azure Storage
description: SAS トークン、暗号化、および HTTPS を使用して、クラウド内にあるアプリケーションのデータをセキュリティで保護します。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: f7c5dbaf30965fdd5f438f0351cfa2cd60e05b70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92746552"
---
# <a name="secure-access-to-application-data"></a>アプリケーション データへのアクセスをセキュリティで保護する

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

チュートリアル シリーズの今回は、サムネイルへのアクセスに SAS トークンを使用します。 この手順では、*thumbnails* コンテナーのパブリック アクセスを `off` に設定します。

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>サムネイルの SAS トークンを構成する

このチュートリアル シリーズの第 1 部では、Web アプリケーションでパブリック コンテナーの画像を表示しました。 シリーズの今回は、Shared Access Signature (SAS) トークンを使用してサムネイル画像を取得します。 SAS トークンを使用すると、IP、プロトコル、間隔、または許可されている権限に基づいて、コンテナーまたは BLOB へのアクセスを制限することができます。 SAS の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。

この例では、ソース コード リポジトリが `sasTokens` ブランチを使用します。このブランチには更新されたコード サンプルがあります。 [az webapp deployment source delete](/cli/azure/webapp/deployment/source) を使用して、既存の GitHub デプロイを削除します。 次に、[az webapp deployment source config](/cli/azure/webapp/deployment/source) コマンドを使用して、Web アプリへの GitHub デプロイを構成します。

次のコマンドの `<web-app>` は、Web アプリの名前です。

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

リポジトリの `sasTokens` ブランチによって `StorageHelper.cs` ファイルが更新されます。 `GetThumbNailUrls` タスクは以下のコード例で置き換えられます。 更新されたタスクは、SAS トークンの開始時刻、有効期限、およびアクセス許可を指定するために [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) を使用してサムネイルの URL を取得します。 Web アプリがデプロイされると、Web アプリは SAS トークンを使用し、URL を指定してサムネイルを取得します。 更新されたタスクは次の例のようになります。

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

上のタスクでは、次のクラス、プロパティ、およびメソッドが使用されています。

| クラス | Properties | メソッド |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [名前](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [クエリ](/dotnet/api/system.uribuilder.query) |  |
|[一覧](/dotnet/api/system.collections.generic.list-1) | | [追加](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure Storage の暗号化

[Azure Storage 暗号化](../common/storage-service-encryption.md)は、保存データを暗号化し、暗号化と解読を処理することで、ユーザーがデータを保護するのを支援します。 現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によってすべてのデータを暗号化します。

Microsoft に暗号化キーの管理を任せることも、Azure Key Vault または Key Vault Managed Hardware Security Model (HSM) (プレビュー) に格納されたカスタマー マネージド キーによって独自のキーを使用することもできます。 詳細については、「[Azure Storage 暗号化のカスタマー マネージド キー](../common/customer-managed-keys-overview.md)」を参照してください。

Azure Storage 暗号化は、すべてのパフォーマンス レベル (Standard および Premium)、すべてのデプロイ モデル (Azure Resource Manager とクラシック)、すべての Azure Storage サービス (Blob、Queue、Table、File) のデータを自動的に暗号化します。

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
