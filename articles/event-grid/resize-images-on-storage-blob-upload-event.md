---
title: Azure Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する | Microsoft Docs
description: Azure Event Grid は、Azure Storage での BLOB アップロードをトリガーできます。 これを使って、Azure Storage にアップロードされたイメージ ファイルを、サイズ変更や他の改善のために Azure Functions などの他のサービスに送信することができます。
services: event-grid, functions
author: spelluru
manager: jpconnoc
editor: ''
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/29/2019
ms.author: spelluru
ms.custom: mvc
ms.openlocfilehash: c09e2cd812dd34976218ff71036734466943e8cd
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "69623868"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>チュートリアル: Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する

[Azure Event Grid](overview.md) は、クラウドのイベント処理サービスです。 Event Grid を使うと、Azure サービスまたはサード パーティのリソースによって生成されるイベントのサブスクリプションを作成できます。  

このチュートリアルは、ストレージ チュートリアル シリーズの第 2 部です。 [前のストレージ チュートリアル][previous-tutorial]に、Azure Event Grid と Azure Functions を使うサーバーレスの自動サムネイル生成機能を追加します。 Event Grid により、[Azure Functions](../azure-functions/functions-overview.md) は [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) のイベントに応答して、アップロードされたイメージのサムネイルを生成できます。 Blob Storage の作成イベントに対して、イベント サブスクリプションが作成されます。 特定の Blob Storage コンテナーに BLOB が追加されると、関数エンドポイントが呼び出されます。 Event Grid から関数バインドに渡されたデータが、BLOB へのアクセスとサムネイル イメージの生成に使われます。

既存のイメージ アップロード アプリにサイズ変更機能を追加するには、Azure CLI と Azure Portal を使います。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルを完了するには、以下が必要です。

前の Blob ストレージのチュートリアル「[Azure Storage を使用してクラウドに画像データをアップロードする][previous-tutorial]」を完了している必要があります。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

以前サブスクリプションに Event Grid リソース プロバイダーを登録していない場合は、それが登録されるようにします。

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.14 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

Cloud Shell を使用していない場合は、先に `az login` でサインインする必要があります。

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Azure Functions には、一般的なストレージ アカウントが必要です。 前のチュートリアルで作成した BLOB ストレージ アカウントに加え、[az storage account create](/cli/azure/storage/account) コマンドを使って、リソース グループ内に一般的なストレージ アカウントを別に作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 

1. 前のチュートリアルで作成したリソース グループの名前を保持する変数を設定します。 

    ```azurecli-interactive
    resourceGroupName=myResourceGroup
    ```
2. Azure 関数が必要とする新しいストレージ アカウントの名前の変数を設定します。 

    ```azurecli-interactive
    functionstorage=<name of the storage account to be used by the function>
    ```
3. Azure 関数用のストレージ アカウントを作成します。 

    ```azurecli-interactive
    az storage account create --name $functionstorage --location southeastasia \
    --resource-group $resourceGroupName --sku Standard_LRS --kind storage
    ```

## <a name="create-a-function-app"></a>Function App を作成する  

関数の実行をホストするには Function App が必要です。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App の作成には、[az functionapp create](/cli/azure/functionapp) コマンドを使用します。 

以下のコマンドには、実際に使用する一意の関数アプリ名を使用してください。 この関数アプリ名は、関数アプリの既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 

1. 作成する関数アプリの名前を指定します。 

    ```azurecli-interactive
    functionapp=<name of the function app>
    ```
2. Azure 関数を作成します。 

    ```azurecli-interactive
    az functionapp create --name $functionapp --storage-account $functionstorage \
    --resource-group $resourceGroupName --consumption-plan-location southeastasia
    ```

ここで、[前のチュートリアル][previous-tutorial]で作成した BLOB ストレージ アカウントに接続するように関数アプリを構成する必要があります。

## <a name="configure-the-function-app"></a>Function App を構成する

関数には、BLOB ストレージ アカウントの資格情報が必要となります。[az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) コマンドを使用して、関数アプリのアプリケーション設定にこの資格情報を追加します。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString \
THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

```azurecli-interactive
blobStorageAccount=<name of the Blob storage account you created in the previous tutorial>

blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
-n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
--name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
--settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` 設定によって、関数アプリは Azure Functions ランタイムのバージョン 2.x で動作するようになります。

この Function App に関数コード プロジェクトをデプロイできるようになります。

## <a name="deploy-the-function-code"></a>関数コードをデプロイする 

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

C# のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/function-image-upload-resize) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) コマンドを使って、このコード プロジェクトを関数アプリにデプロイします。 

```azurecli-interactive
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName --branch master --manual-integration --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

Node.js のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node
```

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

Node.js のサイズ変更関数のサンプルは、[GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10) で入手できます。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。

```azurecli-interactive
az functionapp deployment source config --name $functionapp \
--resource-group $resourceGroupName --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```
---

イメージのサイズ変更関数は、Event Grid サービスからその関数に送信される HTTP 要求によってトリガーされます。 Event Grid には、イベントのサブスクリプションを作成して関数の URL でこれらの通知を取得することを指示します。 このチュートリアルでは、BLOB によって作成されたイベントをサブスクライブします。

Event Grid の通知から関数に渡されるデータには、BLOB の URL が含まれます。 その URL は、Blob Storage からアップロードされたイメージを取得するために入力バインドに渡されます。 関数はサムネイル イメージを生成し、結果のストリームを Blob Storage 内の別のコンテナーに書き込みます。 

このプロジェクトでは、トリガーの種類として `EventGridTrigger` が使用されています。 汎用の HTTP トリガーよりも Event Grid トリガーを使用することをお勧めします。 Event Grid では、Event Grid 関数トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](security-authentication.md)を実装する必要があります。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

この関数の詳細については、[function.json ファイルと run.csx ファイル](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions)を参照してください。

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

この関数の詳細については、[function.json ファイルと index.js ファイル](https://github.com/Azure-Samples/storage-blob-resize-function-node/tree/master/Thumbnail)を参照してください。

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

この関数の詳細については、[function.json ファイルと index.js ファイル](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)を参照してください。

---

関数プロジェクトのコードは、パブリック サンプル リポジトリから直接デプロイされます。 Azure Functions のデプロイ オプションについて詳しくは、「[Azure Functions の継続的なデプロイ](../azure-functions/functions-continuous-deployment.md)」をご覧ください。

## <a name="create-an-event-subscription"></a>イベント サブスクリプションの作成

イベント サブスクリプションは、どのプロバイダー生成イベントを特定のエンドポイントに送信するかを示します。 この場合、エンドポイントは関数によって公開されます。 Azure Portal で関数に通知を送信するイベント サブスクリプションを作成するには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) の左側のメニューで **[すべてのサービス]** を選択し、 **[Function App]** を選択します。 

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 目的の関数アプリを展開して、**Thumbnail** 関数を選択し、 **[Event Grid サブスクリプションの追加]** を選択します。

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 次の表で指定されているようにイベント サブスクリプションを設定します。
    
    ![Azure Portal で関数からイベント サブスクリプションを作成する](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | Setting      | 推奨値  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | imageresizersub | 新しいイベント サブスクリプションを示す名前。 | 
    | **トピックの種類** |  ストレージ アカウント | ストレージ アカウント イベント プロバイダーを選びます。 | 
    | **サブスクリプション** | お使いの Azure サブスクリプション | 既定では、現在の Azure サブスクリプションが選択されています。   |
    | **リソース グループ** | myResourceGroup | **[既存のものを使用]** を選び、このチュートリアルで使っているリソース グループを選びます。  |
    | **リソース** |  お使いの BLOB ストレージ アカウント |  作成した Blob Storage アカウントを選びます。 |
    | **イベントの種類** | Blob created (作成された BLOB) | **[Blob created]\(作成された BLOB\)** 以外のすべての種類をオフにします。 `Microsoft.Storage.BlobCreated` のイベントの種類のみが関数に渡されます。| 
    | **サブスクライバーの種類** |  自動生成 |  Web hook として事前定義されています。 |
    | **サブスクライバー エンドポイント** | 自動生成 | 自動的に生成されるエンドポイントの URL を使います。 | 
4. **[フィルター]** タブに切り替えて、次の手順を実行します。     
    1. **[サブジェクト フィルタリングを有効にする]** オプションを選択します。
    2. **[次で始まるサブジェクト]** には、「 **/blobServices/default/containers/images/blobs/** 」と入力します。

        ![イベント サブスクリプションのフィルターを指定する](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png) 
2. **[作成]** を選択して、イベント サブスクリプションを追加します。 これにより、BLOB が `images` コンテナーに追加されたときに `Thumbnail` 関数をトリガーするイベント サブスクリプションが作成されます。 この関数により、イメージはサイズが変更されて、`thumbnails` コンテナーに追加されます。

バックエンド サービスの構成が済んだので、サンプル Web アプリでイメージ サイズ変更の機能をテストします。 

## <a name="test-the-sample-app"></a>サンプル アプリをテストする

Web アプリでイメージのサイズ変更をテストするには、公開したアプリの URL を参照します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。

# <a name="nettabdotnet"></a>[\.NET](#tab/dotnet)

**[Upload photos]** 領域をクリックし、ファイルを選んでアップロードします。 この領域に写真をドラッグしてもかまいません。 

アップロードされたイメージが消えた後、アップロードされたイメージのコピーが **[Generated thumbnails]** 領域に表示されることを確認します。 この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v2-sdktabnodejs"></a>[Node.js V2 SDK](#tab/nodejs)

**[Choose File]** をクリックしてファイルを選び、 **[Upload Image]** をクリックします。 アップロードが成功すると、ブラウザーが成功ページに移動します。 リンクをクリックすると、ホーム ページに戻ります。 **[Generated thumbnails]** 領域には、アップロードされた画像のコピーが表示されます。 画像が表示されない場合は、ページを再度読み込んでみてください。この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

# <a name="nodejs-v10-sdktabnodejsv10"></a>[Node.js V10 SDK](#tab/nodejsv10)

**[Choose File]** をクリックしてファイルを選び、 **[Upload Image]** をクリックします。 アップロードが成功すると、ブラウザーが成功ページに移動します。 リンクをクリックすると、ホーム ページに戻ります。 **[Generated thumbnails]** 領域には、アップロードされた画像のコピーが表示されます。 画像が表示されない場合は、ページを再度読み込んでみてください。この画像は、関数によってサイズが変更され、*thumbnails* コンテナーに追加された後、Web クライアントによってダウンロードされたものです。

![ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

ストレージ チュートリアル シリーズの第 3 部に進み、ストレージ アカウントへのアクセスをセキュリティで保護する方法について学習します。

> [!div class="nextstepaction"]
> [クラウド内のアプリケーションのデータへのアクセスをセキュリティで保護する](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid について詳しくは、「[Azure Event Grid の概要](overview.md)」をご覧ください。 
+ Azure Functions を利用するもう 1 つのチュートリアルを試すには、「[Azure Logic Apps と統合される関数を作成する](../azure-functions/functions-twitter-email.md)」をご覧ください。 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
