---
title: "Azure Storage を使用してクラウドに画像データをアップロードする | Microsoft Docs"
description: "Web アプリで Azure Blob Storage を使用してアプリケーション データを格納します"
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
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: b0b043f0e30eec7a9456ef15b28b9c89fccf33df
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="upload-image-data-in-the-cloud-with-azure-storage"></a>Azure Storage を使用してクラウドに画像データをアップロードする

このチュートリアルは、シリーズの第 1 部です。 このチュートリアルでは、Azure Storage Client Library を使用してストレージ アカウントに画像をアップロードする Web アプリケーションのデプロイ方法を示します。 このチュートリアルでは、Web アプリの保存と Azure Storage からの画像の表示を実習します。

![images コンテナーの表示](media/storage-upload-process-images/figure2.png)

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントを作成する
> * コンテナーを作成し、アクセス許可を設定する
> * アクセス キーを取得する
> * アプリケーション設定を構成する
> * Web アプリを Azure にデプロイする
> * Web アプリと対話する

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-resource-group"></a>リソース グループを作成する 

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。
 
次の例では、`myResourceGroup` という名前のリソース グループを作成します。   
 
```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus 
``` 

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成
 
サンプルでは、Azure Storage アカウント内の BLOB コンテナーに画像をアップロードします。 ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を用意します。 [az storage account create](/cli/azure/storage/account#create) コマンドを使用して作成したリソース グループ内にストレージ アカウントを作成します。 

> [!IMPORTANT] 
> このチュートリアルの第 2 部では、BLOB ストレージのイベント サブスクリプションを使用します。 イベント サブスクリプションは、現時点では米国中西部と米国西部の BLOB ストレージ アカウントのみをサポートしています。 この制約のため、サンプル アプリが画像とサムネイルを格納するために使用する BLOB ストレージ アカウントを作成する必要があります。   

次のコマンドの `<blob_storage_account>` プレースホルダーを、BLOB ストレージ アカウントのグローバルな一意の名前に置き換えます。  

```azurecli-interactive 
az storage account create --name <blob_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind blobstorage --access-tier hot 
``` 
 
## <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーの作成 
 
アプリでは、BLOB ストレージ アカウント内の 2 つのコンテナーを使用します。 コンテナーはフォルダーに似ており、BLOB の格納に使用します。 "_images_" コンテナーは、アプリが高解像度のイメージをアップロードする場所です。 このシリーズの後半で、Azure 関数アプリで、サイズ変更した画像を "_thumbs_" コンテナーにアップロードします。 

[az storage account keys list](/cli/azure/storage/account/keys#list) コマンドを使用して、ストレージ アカウント キーを取得します。 次に、[az storage container create](/cli/azure/storage/container#create) コマンドでこのキーを使用して、2 つのコンテナーを作成します。  
 
ここでは、`<blob_storage_account>` は、作成した BLOB ストレージ アカウントの名前です。 "_images_" コンテナーのパブリック アクセスは `off` に、_thumbs_ コンテナーのパブリック アクセスは `container` に設定されます。 `container` パブリック アクセス設定は、Web ページにアクセスしたユーザーに対してサムネイルを表示できるようにします。
 
```azurecli-interactive 
blobStorageAccount=<blob_storage_account>

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $blobStorageAccount --query [0].value --output tsv) 

az storage container create -n images --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access off 

az storage container create -n thumbs --account-name $blobStorageAccount \
--account-key $blobStorageAccountKey --public-access container

echo "Make a note of your blob storage account key..." 
echo $blobStorageAccountKey 
``` 

BLOB ストレージ アカウント名とキーをメモしておきます。 サンプル アプリでは、これらの設定を使用して、画像をアップロードするストレージ アカウントに接続します。 

## <a name="create-an-app-service-plan"></a>App Service プランを作成する 

[App Service プラン](../../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)は、アプリのホストとなる Web サーバー ファームの場所、サイズ、機能を規定します。 

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

次の例では、**Free** 価格レベルの `myAppServicePlan` という名前の App Service プランを作成します。 

```azurecli-interactive 
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE 
``` 

## <a name="create-a-web-app"></a>Web アプリを作成する 

Web アプリは、GitHub サンプル リポジトリからデプロイされるサンプル アプリ コード用のホスティング容量を用意します。 [az webapp create](/cli/azure/webapp#create) コマンドを使って、`myAppServicePlan`App Service プランに [Web アプリ](../../app-service-web/app-service-web-overview.md)を作成します。  
 
次のコマンドの `<web_app>` を一意の名前に置き換えます (有効な文字は、`a-z`、`0-9`、および `-` です)。 `<web_app>` が一意でない場合は、"_指定された名前 `<web_app>` の Web サイトは既に存在します_" というエラー メッセージが表示されます。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。  

```azurecli-interactive 
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan 
``` 

## <a name="deploy-the-sample-app-from-the-github-repository"></a>GitHub リポジトリからサンプル アプリをデプロイする 

App Service は、コンテンツを Web アプリにデプロイするさまざまな方法をサポートしています。 このチュートリアルでは、公開されている GitHub サンプル リポジトリ ([https://github.com/Azure-Samples/storage-blob-upload-from-webapp](https://github.com/Azure-Samples/storage-blob-upload-from-webapp)) から Web アプリをデプロイします。 [az webapp deployment source config](/cli/azure/webapp/deployment/source#config) コマンドを使用して、Web アプリへの GitHub のデプロイを構成します。 `<web_app>` を、前の手順で作成した Web アプリの名前に置き換えます。

サンプル プロジェクトには、画像を受信してストレージ アカウントに保存し、サムネイル コンテナーの画像を表示する [ASP.NET MVC](https://www.asp.net/mvc) アプリが含まれています。 この Web アプリは、Azure Storage Client Library の [Microsoft.WindowsAzure.Storage](/dotnet/api/microsoft.windowsazure.storage?view=azure-dotnet)、[Microsoft.WindowsAzure.Storage.Blob](/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet)、および [Microsoft.WindowsAzure.Storage.Auth](/dotnet/api/microsoft.windowsazure.storage.auth?view=azure-dotnet) 名前空間を使用して Azure Storage と対話します。 

```azurecli-interactive 
az webapp deployment source config --name <web_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
``` 

## <a name="configure-web-app-settings"></a>Web アプリの設定を構成する 

サンプル Web アプリでは、[Azure Storage Client Library](/dotnet/api/overview/azure/storage?view=azure-dotnet) を使用して、画像をアップロードするために使用するアクセス トークンを要求します。 ストレージ SDK によって使用されるストレージ アカウントの資格情報が、Web アプリのアプリケーション設定に設定されます。 [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set) コマンドを使用して、デプロイされたアプリにアプリケーション設定を追加します。 

次のコマンドでは、`<blob_storage_account>` は BLOB ストレージ アカウントの名前であり、`<blob_storage_key>` は関連付けられているキーです。 `<web_app>` を、前の手順で作成した Web アプリの名前に置き換えます。     

```azurecli-interactive 
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<blob_storage_account> \
AzureStorageConfig__ImageContainer=images  \
AzureStorageConfig__ThumbnailContainer=thumbs \
AzureStorageConfig__AccountKey=<blob_storage_key>  
``` 

Web アプリをデプロイして構成したら、アプリで画像のアップロード機能をテストできます。   

## <a name="upload-an-image"></a>イメージをアップロードする 

Web アプリをテストするには、発行したアプリの URL に移動します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。 **[写真のアップロード]** 領域を選択し、ファイルを選択してアップロードするか、ファイルを領域にドラッグ アンド ドロップします。 正常にアップロードされると、画像は表示されなくなります。

![ImageResizer アプリ](media/storage-upload-process-images/figure1.png)

サンプル コードでは、`Storagehelper.cs` ファイル内の `UploadFiletoStorage` タスクの [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) メソッドを使用して、ストレージ アカウント内の `images` コンテナーに画像をアップロードします。 次のコード サンプルに、`UploadFiletoStorage` タスクが含まれています。 

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName, AzureStorageConfig _storageConfig)
{
    // Create storagecredentials object by reading the values from the configuration (appsettings.json)
    StorageCredentials storageCredentials = new StorageCredentials(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create cloudstorage account by passing the storagecredentials
    CloudStorageAccount storageAccount = new CloudStorageAccount(storageCredentials, true);

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get reference to the blob container by passing the name by reading the value from the configuration (appsettings.json)
    CloudBlobContainer container = blobClient.GetContainerReference(_storageConfig.ImageContainer);

    // Get the reference to the block blob from the container
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

    // Upload the file
    await blockBlob.UploadFromStreamAsync(fileStream);

    return await Task.FromResult(true);
}
```

上のタスクでは、次のクラスとメソッドが使用されています。

|クラス  |メソッド  |
|---------|---------|
|[StorageCredentials](/dotnet/api/microsoft.windowsazure.storage.auth.storagecredentials?view=azure-dotnet)     |         |
|[CloudStorageAccount](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount?view=azure-dotnet)    |  [CreateCloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount.createcloudblobclient?view=azure-dotnet#Microsoft_WindowsAzure_Storage_CloudStorageAccount_CreateCloudBlobClient)       |
|[CloudBlobClient](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient?view=azure-dotnet)     |[GetContainerReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobclient.getcontainerreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobClient_GetContainerReference_System_String_)         |
|[CloudBlobContainer](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer?view=azure-dotnet)    | [GetBlockBlobReference](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.getblockblobreference?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_GetBlockBlobReference_System_String_)        |
|[CloudBlockBlob](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob?view=azure-dotnet)     | [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet)        |

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>ストレージ アカウント内に画像が表示されることを確認する

https://portal.azure.com にサインインします。左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[概要]** で、**[images]** コンテナーを選択します。

コンテナー内に画像が表示されることを確認します。

![images コンテナーの表示](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>サムネイルの表示をテストする

サムネイルの表示をテストするには、thumbnail コンテナーに画像をアップロードして、アプリケーションが thumbnail コンテナーを読み取ることができることを確認します。

https://portal.azure.com にサインインします。左側のメニューから **[ストレージ アカウント]** を選択し、ストレージ アカウントの名前を選択します。 **[Blob Service]** で **[コンテナー]** を選択し、**thumbs** コンテナーを選択します。 **[アップロード]** を選択して **[BLOB のアップロード]** ウィンドウを開きます。

ファイル ピッカーを使用してファイルを選択し、**[アップロード]** を選択します。

アプリに戻って、**thumbs** コンテナーにアップロードした画像が表示されていることを確認します。

![images コンテナーの表示](media/storage-upload-process-images/figure2.png)

Azure ポータルの **thumbs** コンテナーで、アップロードした画像を選択し、**[削除]** を選択して画像を削除します。 このシリーズの第 2 部でサムネイル画像の作成を自動化するため、このテスト画像は必要ありません。

CDN を有効にして、Azure ストレージ アカウントのコンテンツをキャッシュできます。 CDN を Azure ストレージ アカウントで有効にする方法はこのチュートリアルでは説明していません。「[Azure ストレージ アカウントと Azure CDN との統合](../../cdn/cdn-create-a-storage-account-with-cdn.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、次の操作を行って、ストレージと対話する Web アプリを構成する方法について説明しました。

> [!div class="checklist"]
> * ストレージ アカウントを作成する
> * コンテナーを作成し、アクセス許可を設定する
> * アクセス キーを取得する
> * アプリケーション設定を構成する
> * Web アプリを Azure にデプロイする
> * Web アプリと対話する

シリーズの第 2 部に進んで、イベント グリッドを使用して画像のサイズを変更する Azure 関数をトリガーする方法を確認してください。  

> [!div class="nextstepaction"]
> [イベント グリッドを使用して画像のサイズを変更する Azure 関数をトリガーする](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

