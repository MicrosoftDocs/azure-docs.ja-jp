---
title: Azure Storage を使用してクラウドに画像データをアップロードする | Microsoft Docs
description: Web アプリで Azure Blob Storage を使用して、ストレージ アカウントにアプリ データを格納します。 このチュートリアルでは、Azure Storage に画像を格納したりその画像を表示したりする Web アプリを作成します。
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b50aadfa16ed95bacb5247187c15489a1b017d39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676576"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>チュートリアル:Azure Storage を使用してクラウドに画像データをアップロードする

このチュートリアルは、シリーズの第 1 部です。 このチュートリアルでは、Web アプリをデプロイする方法について説明します。 この Web アプリは、Azure Blob Storage クライアント ライブラリを使用してストレージ アカウントに画像をアップロードします。 終了すると、Azure Storage に画像を格納して表示する Web アプリが完成します。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![.NET の画像サイズ変更アプリ](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![JavaScript の画像サイズ変更アプリ](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]

> * ストレージ アカウントの作成
> * コンテナーを作成し、アクセス許可を設定する
> * アクセス キーを取得する
> * Web アプリを Azure にデプロイする
> * アプリケーションの設定の構成
> * Web アプリと対話する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。 始める前に、[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.4 以降を実行してください。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。  

次の例では、`myResourceGroup` という名前のリソース グループを作成します。

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

サンプルでは、Azure ストレージ アカウント内の BLOB コンテナーに画像をアップロードします。 ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。 [az storage account create](/cli/azure/storage/account) コマンドを使用して作成したリソース グループ内にストレージ アカウントを作成します。

> [!IMPORTANT]
> このチュートリアルの第 2 部では、BLOB ストレージで Azure Event Grid を使用します。 Event Grid がサポートされている Azure リージョンにストレージ アカウントを作成してください。 サポートされているリージョンの一覧は、[リージョン別の Azure 製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)に関するページをご覧ください。

次のコマンドの `<blob_storage_account>` プレースホルダーを、BLOB ストレージ アカウントのグローバルな一意の名前に置き換えます。

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーを作成する

アプリでは、BLOB ストレージ アカウント内の 2 つのコンテナーを使用します。 コンテナーはフォルダーに似ており、BLOB を格納します。 "*images*" コンテナーは、アプリが高解像度のイメージをアップロードする場所です。 このシリーズの後半で、Azure 関数アプリで、サイズ変更した画像を *thumbnails* コンテナーにアップロードします。

[az storage account keys list](/cli/azure/storage/account/keys) コマンドを使用して、ストレージ アカウント キーを取得します。 次に、このキーを使用して、[az storage container create](/cli/azure/storage/container) コマンドで 2 つのコンテナーを作成します。

*images* コンテナーのパブリック アクセスは、`off` に設定されています。 *thumbnails* コンテナーのパブリック アクセスは、`container` に設定されています。 `container` パブリック アクセスに設定すると、Web ページにアクセスしたユーザーはサムネイルを表示できます。

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

BLOB ストレージ アカウント名とキーをメモしておきます。 サンプル アプリでは、これらの設定を使用して、画像をアップロードするストレージ アカウントに接続します。 

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

[App Service プラン](../../app-service/overview-hosting-plans.md)は、アプリのホストとなる Web サーバー ファームの場所、サイズ、機能を規定します。

[az appservice plan create](/cli/azure/appservice/plan) コマンドで、App Service プランを作成します。

次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Web アプリを作成する

Web アプリでは、GitHub サンプル リポジトリからデプロイされるサンプル アプリ コード用のホスト領域を提供します。 [az webapp create](/cli/azure/webapp) コマンドを使って、`myAppServicePlan`App Service プランに [Web アプリ](../../app-service/overview.md)を作成します。  

次のコマンドで、`<web_app>` を一意の名前に置き換えます。 有効な文字は、`a-z`、`0-9`、および `-` です。 `<web_app>` が一意でない場合は、"*指定された名前 `<web_app>` の Web サイトは既に存在します*" というエラー メッセージが表示されます。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>GitHub リポジトリからサンプル アプリをデプロイする

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

App Service は、コンテンツを Web アプリにデプロイするさまざまな方法をサポートしています。 このチュートリアルでは、[パブリック GitHub サンプル リポジトリ](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)から Web アプリをデプロイします。 [az webapp deployment source config](/cli/azure/webapp/deployment/source) コマンドを使用して、Web アプリへの GitHub のデプロイを構成します。

サンプル プロジェクトには、[ASP.NET MVC](https://www.asp.net/mvc) アプリが含まれています。 そのアプリは、画像を受け取り、ストレージ アカウントに保存して、サムネイル コンテナーから画像を表示します。 Web アプリは、[Azure.Storage](/dotnet/api/azure.storage)、[Azure.Storage.Blobs](/dotnet/api/azure.storage.blobs)、および [Azure.Storage.Blobs.Models](/dotnet/api/azure.storage.blobs.models) 名前空間を使用して、Azure Storage サービスと対話します。

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

App Service は、コンテンツを Web アプリにデプロイするさまざまな方法をサポートしています。 このチュートリアルでは、[パブリック GitHub サンプル リポジトリ](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs)から Web アプリをデプロイします。 [az webapp deployment source config](/cli/azure/webapp/deployment/source) コマンドを使用して、Web アプリへの GitHub のデプロイを構成します。

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>Web アプリの設定を構成する

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

このサンプル Web アプリでは、[.NET 用 Azure Storage API](/dotnet/api/overview/azure/storage) シリーズを使用して、画像をアップロードします。 ストレージ アカウントの資格情報は、Web アプリのアプリ設定で設定されています。 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) コマンドを使用して、デプロイされるアプリにアプリ設定を追加します。

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

このサンプル Web アプリでは、[JavaScript 用の Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage)を使用して画像をアップロードします。 ストレージ アカウントの資格情報は、Web アプリのアプリ設定で設定されています。 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings) コマンドを使用して、デプロイされるアプリにアプリ設定を追加します。

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

Web アプリのデプロイと構成が済むと、アプリで画像のアップロード機能をテストできます。

## <a name="upload-an-image"></a>イメージをアップロードする

Web アプリをテストするには、発行したアプリの URL に移動します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

**[写真のアップロード]** 領域を選択し、ファイルを指定してアップロードするか、ファイルを領域にドラッグします。 正常にアップロードされると、画像は表示されなくなります。 **[Generated Thumbnails]** セクションは、後でこのチュートリアルの中でテストするまで空のままになります。

![.NET で写真をアップロードする](media/storage-upload-process-images/figure1.png)

サンプル コードでは、*Storagehelper.cs* ファイル内の `UploadFileToStorage` タスクの [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) メソッドを使用して、ストレージ アカウント内の *images* コンテナーに画像をアップロードします。 次のコード サンプルに、`UploadFileToStorage` タスクが含まれています。

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

上のタスクでは、次のクラスとメソッドが使用されています。

| クラス | Method |
|-------|--------|
| [Uri](/dotnet/api/system.uri) | [Uri コンストラクター](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (String, String) コンストラクター](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

**[Choose File]** を選択してファイルを選び、 **[Upload Image]** をクリックします。 **[Generated Thumbnails]** セクションは、後でこのチュートリアルの中でテストするまで空のままになります。

![Node.js で写真をアップロードする](media/storage-upload-process-images/upload-app-nodejs.png)

このサンプル コードでは、`post` ルートが画像を BLOB コンテナーにアップロードする処理を担当します。 このルートは、次のモジュールを使用してアップロードを処理します。

- [multer](https://github.com/expressjs/multer) には、ルート ハンドラーのアップロード戦略が実装されています。
- [into-stream](https://github.com/sindresorhus/into-stream) では、[uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-) の要求に応じてバッファーがストリームに変換されます。

ファイルがルートに送信されると、ファイルが BLOB コンテナーにアップロードされるまで、ファイルの内容はメモリに残ります。

> [!IMPORTANT]
> 大きいファイルをメモリに読み込むと、Web アプリのパフォーマンスが低下する可能性があります。 ユーザーが大きなファイルを投稿することが想定される場合は、Web サーバー ファイル システム上にファイルをステージングしてから、BLOB ストレージへのアップロードをスケジュールする処理の検討をお勧めします。 ファイルが BLOB ストレージに格納されたら、サーバー ファイル システムからそのファイルを削除できます。

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>ストレージ アカウント内に画像が表示されることを確認する

[Azure portal](https://portal.azure.com) にサインインします。 左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[コンテナー]** を選択し、 **[images]\(イメージ\)** コンテナーを選択します。

コンテナー内に画像が表示されることを確認します。

![Azure portal の画像コンテナーの表示](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>サムネイルの表示をテストする

サムネイルの表示をテストするには、**thumbnails** コンテナーに画像をアップロードして、アプリが **thumbnails** コンテナーを読み取れることを確認します。

[Azure portal](https://portal.azure.com) にサインインします。 左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[コンテナー]** を選択し、 **[サムネイル]** コンテナーを選択します。 **[アップロード]** を選択して **[BLOB のアップロード]** ウィンドウを開きます。

ファイル ピッカーでファイルを選択し、 **[アップロード]** を選択します。

アプリに戻って、**thumbnails** コンテナーにアップロードした画像が表示されていることを確認します。

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

![新しい画像が表示された .NET 画像サイズ変更アプリ](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

![新しい画像が表示された Node.js 画像サイズ変更アプリ](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

シリーズの第 2 部では、サムネイル画像の作成を自動化して、この画像が必要ないようにします。 **thumbnails** コンテナーで、アップロードした画像を選択し、 **[削除]** を選択して画像を削除します。

Content Delivery Network (CDN) を有効にして、Azure ストレージ アカウントからのコンテンツをキャッシュすることができます。 詳しくは、「[Azure ストレージ アカウントと Azure CDN との統合](../../cdn/cdn-create-a-storage-account-with-cdn.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、ストレージと対話するように Web アプリを構成する方法について学習しました。

シリーズの第 2 部に進み、Event Grid を使用して画像のサイズを変更する Azure 関数をトリガーする方法を学習してください。

> [!div class="nextstepaction"]
> [イベント グリッドを使用して画像のサイズを変更する Azure 関数をトリガーする](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
