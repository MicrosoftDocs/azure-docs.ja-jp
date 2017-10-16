---
title: "Azure Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する | Microsoft Docs"
description: "Azure Event Grid は、Azure Storage での BLOB アップロードをトリガーできます。 これを使って、Azure Storage にアップロードされたイメージ ファイルを、サイズ変更や他の改善のために Azure Functions などの他のサービスに送信することができます。"
services: event-grid
author: ggailey777
manager: cfowler
editor: 
ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 358015d6cfd9961508b209f628b2d648a75e3c2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="automate-resizing-uploaded-images-using-event-grid"></a>Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する

[Azure Event Grid](overview.md) は、クラウドのイベント処理サービスです。 Event Grid を使うと、Azure サービスまたはサード パーティのリソースによって生成されるイベントのサブスクリプションを作成できます。  

このチュートリアルは、ストレージ チュートリアル シリーズの第 2 部です。 [前のストレージ チュートリアル][previous-tutorial]に、Azure Event Grid と Azure Functions を使うサーバーレスの自動サムネイル生成機能を追加します。 Event Grid により、[Azure Functions](..\azure-functions\functions-overview.md) は [Azure Blob Storage](..\storage\blobs\storage-blobs-introduction.md) のイベントに応答して、アップロードされたイメージのサムネイルを生成できます。 Blob Storage の作成イベントに対して、イベント サブスクリプションが作成されます。 特定の Blob Storage コンテナーに BLOB が追加されると、関数エンドポイントが呼び出されます。 Event Grid から関数バインドに渡されたデータが、BLOB へのアクセスとサムネイル イメージの生成に使われます。 

既存のイメージ アップロード アプリにサイズ変更機能を追加するには、Azure CLI と Azure Portal を使います。

[!INCLUDE [storage-events-note.md](../../includes/storage-events-note.md)]

![Edge ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ 前の Blob Storage チュートリアル「[Upload image data in the cloud with Azure Storage][previous-tutorial]」(Azure Storage でクラウドにイメージ データをアップロードする) を完了している必要があります。 
+ Blob Storage イベント機能へのアクセスを申請して許可されている必要があります。 トピックの他の手順に進む前に、[Blob Storage イベントへのアクセスを要求](#request-storage-access)します。  

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使う場合、このトピックでは、Azure CLI バージョン 2.0.14 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

Cloud Shell を使用していない場合は、先に `az login` でサインインする必要があります。

## <a name="enable-blob-storage-events"></a>Blob Storage イベントを有効にする

この時点で、Blob Storage イベント機能へのアクセスを要求する必要があります。  

### <a name="request-storage-access"></a>Blob Storage イベントへのアクセスを要求する

`az feature register` コマンドでアクセスを要求します。

> [!IMPORTANT]  
> Blob Storage イベント プレビューの参加者は、参加を要求した順序で受け付けられます。 この機能へのアクセスを許可されるまでに、1 - 2 営業日かかる可能性があります。 

```azurecli-interactive
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```

### <a name="check-access-status"></a>承認状態を確認する

Blob Storage イベントへのアクセスが許可されたことを通知するメールを Microsoft から受け取ります。 アクセス要求の状態は、`az feature show` コマンドでいつでも確認できます。

```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid --query properties.state
```
Blob Storage イベント機能へのアクセスが許可されると、このコマンドは `"Registered"` という値を返すようになります。 
 
登録が済むと、このチュートリアルを続行できます。

## <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

Azure Functions には、一般的なストレージ アカウントが必要です。 [az storage account create](/cli/azure/storage/account#create) コマンドを使って、リソース グループ内に一般的なストレージ アカウントを別に作成します。

ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 

次のコマンドで、`<general_storage_account>` プレースホルダーを一般的なストレージ アカウントのグローバルで一意な名前に置き換えます。 

```azurecli-interactive
az storage account create --name <general_storage_account> \
--location westcentralus --resource-group myResourceGroup \
--sku Standard_LRS --kind storage
```

## <a name="create-a-function-app"></a>Function App を作成する  

関数の実行をホストするには Function App が必要です。 Function App は、関数コードのサーバーレス実行の環境を提供します。 Function App の作成には、[az functionapp create](/cli/azure/functionapp#create) コマンドを使用します。 

次のコマンドで、`<function_app>` プレースホルダーを独自の一意の Function App 名に置き換えます。 `<function_app>` は、Function App の既定の DNS ドメインとして使用されます。そのため、名前は Azure のすべてのアプリ間で一意である必要があります。 この場合、`<general_storage_account>` は作成した一般的なストレージ アカウントの名前です。  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <general_storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westcentralus
```

次に、Blob Storage に接続するように Function App を構成する必要があります。 

## <a name="configure-the-function-app"></a>Function App を構成する

関数が Blob Storage アカウントに接続するには接続文字列が必要です。 この場合、`<blob_storage_account>` は前のチュートリアルで作成した Blob Storage アカウントの名前です。 [az storage account show-connection-string](/cli/azure/storage/account#show-connection-string) コマンドで接続文字列を取得します。 サムネイル イメージのコンテナー名も `thumbs` に設定する必要があります。 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set) コマンドで、これらのアプリケーション設定を Function App に追加します。

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <blob_storage_account> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <function_app> \
--resource-group myResourceGroup \
--settings myblobstorage_STORAGE=$storageConnectionString \
myContainerName=thumbs
```

この Function App に関数コード プロジェクトをデプロイできるようになります。

## <a name="deploy-the-function-code"></a>関数コードをデプロイする 

イメージのサイズ変更を実行する C# 関数は、この[サンプル GitHub リポジトリ](https://github.com/Azure-Samples/function-image-upload-resize)にあります。 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) コマンドを使って、この Functions コード プロジェクトを関数アプリにデプロイします。 

次のコマンドで、`<function_app>` は前のスクリプトで作成したものと同じ関数アプリです。

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
--resource-group myResourceGroup --branch master --manual-integration \
--repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

イメージ サイズ変更関数は、BLOB で作成されるイベントのイベント サブスクリプションによってトリガーされます。 トリガーに渡されるデータには BLOB の URL が含まれ、この URL はアップロードされたイメージを Blob Storage から取得するために入力バインドに渡されます。 関数はサムネイル イメージを生成し、結果のストリームを Blob Storage 内の別のコンテナーに書き込みます。 この関数について詳しくは、[サンプル リポジトリの readme ファイル](https://github.com/Azure-Samples/function-image-upload-resize/blob/master/README.md)をご覧ください。
 
関数プロジェクトのコードは、パブリック サンプル リポジトリから直接デプロイされます。 Azure Functions のデプロイ オプションについて詳しくは、「[Azure Functions の継続的なデプロイ](../azure-functions/functions-continuous-deployment.md)」をご覧ください。

## <a name="create-your-event-subscription"></a>イベント サブスクリプションを作成する

イベント サブスクリプションは、どのプロバイダー生成イベントを特定のエンドポイントに送信するかを示します。 この場合、エンドポイントは関数によって公開されます。 Azure Portal で関数からイベント サブスクリプションを作成するには、次の手順のようにします。 

1. [Azure Portal](https://portal.azure.com) で、左下にある矢印をクリックしてすべてのサービスを表示し、**[フィルター]** フィールドに「`functions`」と入力して、**[Function App]** を選びます。 

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/portal-find-functions.png)

2. 目的の関数アプリを展開して、**imageresizerfunc** 関数を選び、**[Event Grid サブスクリプションの追加]** を選びます。

    ![Azure Portal で Function App を参照する](./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png)

3. 次の表で指定されているようにイベント サブスクリプションを設定します。

    ![Azure Portal で関数からイベント サブスクリプションを作成する](./media/resize-images-on-storage-blob-upload-event/event-subscription-create-flow.png)

    | 設定      | 推奨値  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | imageresizersub | 新しいイベント サブスクリプションを示す名前。 | 
    | **トピックの種類** |  ストレージ アカウント | ストレージ アカウント イベント プロバイダーを選びます。 | 
    | **サブスクリプション** | 該当するサブスクリプション | 既定で、現在のサブスクリプションが選ばれています。   |
    | **[リソース グループ]** | myResourceGroup | **[既存のものを使用]** を選び、このトピックで使っているリソース グループを選びます。  |
    | **インスタンス** |  `<blob_storage_account>` |  作成した Blob Storage アカウントを選びます。 |
    | **イベントの種類** | Blob created (作成された BLOB) | **[Blob created]\(作成された BLOB\)** 以外のすべての種類をオフにします。 `Microsoft.Storage.BlobCreated` のイベントの種類のみが関数に渡されます。| 
    | **サブスクライバー エンドポイント** | 自動生成 | 自動的に生成されるエンドポイントの URL を使います。 | 
    | **プレフィックス フィルター** | /blobServices/default/containers/images/blobs/ | **images** コンテナーのストレージ イベントだけになるようにフィルター処理します。| 

4. **[作成]** をクリックしてイベント サブスクリプションを追加します。 BLOB が **images** コンテナーに追加されると **imageresizerfunc** をトリガーするイベント サブスクリプションが作成されます。 サイズを変更されたイメージは、**thumbs** コンテナーに追加されます。

バックエンド サービスの構成が済んだので、サンプル Web アプリでイメージ サイズ変更の機能をテストします。 

## <a name="test-the-sample-app"></a>サンプル アプリをテストする

Web アプリでイメージのサイズ変更をテストするには、公開したアプリの URL を参照します。 Web アプリの既定の URL は、`https://<web_app>.azurewebsites.net` です。

**[Upload photos]** 領域をクリックし、ファイルを選んでアップロードします。 この領域に写真をドラッグしてもかまいません。 

アップロードされたイメージが消えた後、アップロードされたイメージのコピーが **[Generated thumbnails]** 領域に表示されることを確認します。 このイメージは、関数によってサイズを変更され、thumbs コンテナーに追加された後、Web クライアントによってダウンロードされたものです。 

![Edge ブラウザーでの発行された Web アプリ](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 一般 Azure ストレージ アカウントを作成します
> * Azure Functions を使ってサーバーレス コードをデプロイします
> * Event Grid で Blob Storage のイベント サブスクリプションを作成します

ストレージ チュートリアル シリーズの第 3 部に進み、ストレージ アカウントへのアクセスをセキュリティで保護する方法について学習します。

> [!div class="nextstepaction"]
> [クラウド内のアプリケーションのデータへのアクセスをセキュリティで保護する](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)


+ Event Grid について詳しくは、「[Azure Event Grid の概要](overview.md)」をご覧ください。 
+ Azure Functions を利用するもう 1 つのチュートリアルを試すには、「[Azure Logic Apps と統合される関数を作成する](..\azure-functions\functions-twitter-email.md)」をご覧ください。 

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md