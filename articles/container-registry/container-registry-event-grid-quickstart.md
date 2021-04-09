---
title: クイックスタート - Event Grid にイベントを送信する
description: このクイック スタートでは、ご自身のコンテナー レジストリの Event Grid イベントを有効にして、コンテナー イメージの push イベントおよび delete イベントをサンプル アプリケーションに送信します。
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: b4038bb232c14278e838f2c34706dcc103def7e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919665"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>クイック スタート:プライベート コンテナー レジストリから Event Grid にイベントを送信する

Azure Event Grid は、パブリッシュ/サブスクライブ モデルを使用した画一的なイベント使用を提供する、完全に管理されたイベント ルーティング サービスです。 このクイック スタートでは、Azure CLI を使用してコンテナー レジストリを作成し、レジストリ イベントをサブスクライブして、イベントを受信するサンプル Web アプリケーションをデプロイします。 最後に、コンテナー イメージの `push` イベントおよび `delete` イベントをトリガーし、サンプル アプリケーションでイベント ペイロードを表示します。

この記事の手順を完了すると、ご自身のコンテナー レジストリから Event Grid に送信されたイベントが、サンプル Web アプリに表示されます。

![サンプル Web アプリケーションと 3 つの受信イベントがレンダリングされている Web ブラウザー][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事の Azure CLI コマンドは、**Bash** シェル用にフォーマットされています。 PowerShell、コマンド プロンプトなど、別のシェルを使用している場合は、必要に応じて、行連結文字または変数代入行を適切に調整する必要があります。 この記事では変数を使用して、必要なコマンド編集を最小限に抑えています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の [az group create][az-group-create] コマンドでは、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。 ご自身のリソース グループに別の名前を使用する場合は、`RESOURCE_GROUP_NAME` を別の値に設定してください。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

次に、以下のコマンドを使用して、コンテナー レジストリをリソース グループにデプロイします。 [az acr create][az-acr-create] コマンドを実行する前に、`ACR_NAME` を、ご自身のレジストリの名前に設定します。 名前は Azure 内で一意にする必要があります。また、5 から 50 文字の英数字に制限されています。

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

レジストリが作成されると、Azure CLI により次のような出力が返されます。

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```

## <a name="create-an-event-endpoint"></a>イベント エンドポイントの作成

このセクションでは、GitHub リポジトリにある Resource Manager テンプレートを使用して、構築済みサンプル Web アプリケーションを Azure App Service にデプロイします。 その後、レジストリの Event Grid イベントをサブスクライブし、このアプリを、イベントの送信先エンドポイントとして指定します。

サンプル アプリをデプロイするには、`SITE_NAME` をご自身の Web アプリの一意の名前に設定し、次のコマンドを実行します。 サイト名は Web アプリの完全修飾ドメイン名 (FQDN) の一部であるため、Azure 内で一意にする必要があります。 後のセクションで、Web ブラウザーを使用してアプリの FQDN に移動し、ご自身のレジストリのイベントを表示します。

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

デプロイが成功したら (数分をかかる場合があります)、ブラウザーを開いて、ご自身の Web アプリに移動し、アプリが実行中であることを確認します。

`http://<your-site-name>.azurewebsites.net`

サンプル アプリが、イベント メッセージなしでレンダリングされているはずです。

![イベントがないサンプル Web アプリが表示されている Web ブラウザー][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>レジストリ イベントのサブスクライブ

Event Grid では、"*トピック*" をサブスクライブすることで、どのイベントを追跡し、どこにイベントを送信するかをその Event Grid に伝えます。 次の [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] コマンドでは、作成したコンテナー レジストリをサブスクライブして、ご自身の Web アプリの URL を、イベントの送信先エンドポイントとして指定します。 前のセクションで設定した環境変数はここで再利用されます。したがって、編集は不要です。

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

サブスクリプションが完了すると、次のような出力が表示されます。

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>レジストリ イベントのトリガー

サンプル アプリが稼働し、Event Grid でご自身のレジストリをサブスクライブしたら、いつでもイベントを生成できます。 このセクションでは、ACR タスクを使用して、コンテナー イメージを作成し、レジストリにプッシュします。 ACR タスクは Azure Container Registry の一機能で、これによりクラウドでコンテナー イメージを作成できます。お使いのローカル コンピューターに Docker エンジンをインストールする必要はありません。

### <a name="build-and-push-image"></a>イメージの作成とプッシュ

次の Azure CLI コマンドを実行して、GitHub リポジトリのコンテンツからコンテナー イメージを作成します。 既定では、正常に作成されたイメージは、ACR タスクによりご自身のレジストリに自動的にプッシュされ、これにより `ImagePushed` イベントが生成されます。

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

ご自身のイメージが ACR タスクによって作成およびプッシュされている間は、次のような出力が表示されます。 簡潔にするため、次のサンプル出力は省略されています。

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

作成されたイメージがご自身のレジストリにあるかどうかを確認するには、次のコマンドを実行して、"myimage" リポジトリ内のタグを表示します。

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

作成したイメージの "v1" タグは、出力では次のように表示されます。

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>イメージの削除

次に、[az acr repository delete][az-acr-repository-delete] コマンドを使用してイメージを削除し、`ImageDeleted` イベントを生成します。

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

次のような出力が表示され、マニフェストおよび関連付けられているイメージを削除してもよいか確認を求められます。

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>レジストリ イベントの表示

これで、ご自身のレジストリにイメージがプッシュされ、その後、削除されました。 Event Grid ビューアー Web アプリに移動すると、`ImageDeleted` イベントと `ImagePushed` イベントの両方が表示されています。 「[レジストリ イベントのサブスクライブ](#subscribe-to-registry-events)」セクションでコマンドによって生成されたサブスクリプション検証イベントも表示されている場合があります。

次のスクリーンショットは 3 つのイベントが表示されたサンプル アプリを示しており、`ImageDeleted` イベントは展開され、その詳細が表示されています。

![ImagePushed イベントと ImageDeleted イベントを含むサンプル アプリが表示されている Web ブラウザー][sample-app-03]

お疲れさまでした。 `ImagePushed` イベントと `ImageDeleted` イベントが表示されている場合は、ご自身のレジストリによってイベントが Event Grid に送信され、そのイベントは、Event Grid によってご自身の Web アプリ エンドポイントに転送されています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートで作成したリソースの処理が完了したら、次の Azure CLI コマンドを使用してすべてのリソースを削除できます。 リソース グループを削除すると、そこに含まれているリソースはすべて、完全に削除されます。

**警告**: この操作は元に戻すことができません。 コマンドを実行する前に、グループ内のすべてのリソースが不要であることを必ず確認してください。

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid イベント スキーマ

Azure Container Registry イベント メッセージのスキーマ リファレンスについては、Event Grid のドキュメントをご覧ください。

[Container Registry 用の Azure Event Grid イベント スキーマ](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、コンテナー レジストリをデプロイし、ACR タスクでイメージを作成した後、それを削除しました。また、サンプル アプリケーションで Event Grid からご自身のレジストリのイベントを使用しました。 次は、ACR タスクのチュートリアルを進め、基本イメージの更新での自動作成など、クラウドでのコンテナー イメージ作成の詳細について確認します。

> [!div class="nextstepaction"]
> [ACR タスクを使用したクラウドでのコンテナー イメージの作成](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
