---
title: Azure SignalR Service のイベントを Event Grid に送信する方法
description: このガイドでは、SignalR Service で Event Grid イベントを有効にし、サンプル アプリケーションにクライアント接続の接続/接続解除イベントを送信する方法について説明します。
services: signalr
author: chenyl
ms.service: azure-signalr
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: chenyl
ms.openlocfilehash: 52e4194acd6a3abfed3fabadb892b0de76025b7e
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296857"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>イベントを Azure SignalR Service から Event Grid に送信する方法

Azure Event Grid は、pub-sub モデルを使用した画一的なイベント使用を提供する、完全に管理されたイベント ルーティング サービスです。 このガイドでは、Azure CLI を使用して Azure SignalR Service を作成し、接続イベントをサブスクライブして、イベントを受信するサンプル Web アプリケーションをデプロイします。 最後に、接続および接続解除し、サンプル アプリケーションでイベントのペイロードを確認します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-account] を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

この記事の Azure CLI コマンドは、**Bash** シェル用にフォーマットされています。 PowerShell、コマンド プロンプトなど、別のシェルを使用している場合は、必要に応じて、行連結文字または変数代入行を適切に調整する必要があります。 この記事では変数を使用して、必要なコマンド編集を最小限に抑えています。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の [az group create][az-group-create] コマンドでは、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。 ご自身のリソース グループに別の名前を使用する場合は、`RESOURCE_GROUP_NAME` を別の値に設定してください。

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>SignalR Service の作成

次に、以下のコマンドを使用して、Azure SignalR Service をリソース グループにデプロイします。
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

SignalR Service が作成されると、Azure CLI により次のような出力が返されます。

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>イベント エンドポイントの作成

このセクションでは、GitHub リポジトリにある Resource Manager テンプレートを使用して、構築済みサンプル Web アプリケーションを Azure App Service にデプロイします。 その後、レジストリの Event Grid イベントをサブスクライブし、このアプリを、イベントの送信先エンドポイントとして指定します。

サンプル アプリをデプロイするには、`SITE_NAME` をご自身の Web アプリの一意の名前に設定し、次のコマンドを実行します。 サイト名は Web アプリの完全修飾ドメイン名 (FQDN) の一部であるため、Azure 内で一意にする必要があります。 後のセクションで、Web ブラウザーを使用してアプリの FQDN に移動し、ご自身のレジストリのイベントを表示します。

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

デプロイが成功したら (数分をかかる場合があります)、ブラウザーを開いて、ご自身の Web アプリに移動し、アプリが実行中であることを確認します。

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>レジストリ イベントのサブスクライブ

Event Grid では、"*トピック*" をサブスクライブすることで、どのイベントを追跡し、どこにイベントを送信するかをその Event Grid に伝えます。 次の [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] コマンドでは、作成した Azure SignalR Service をサブスクライブして、ご自身の Web アプリの URL を、イベントの送信先エンドポイントとして指定します。 前のセクションで設定した環境変数はここで再利用されます。したがって、編集は不要です。

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

サブスクリプションが完了すると、次のような出力が表示されます。

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>レジストリ イベントのトリガー

サービス モードを `Serverless Mode` に切り替え、SignalR Service へのクライアント接続をセットアップします。 [サーバーレス サンプル](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless)を参考にすることができます。

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a seperate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>レジストリ イベントの表示

クライアントを SignalR Service に接続できました。 Event Grid ビューアー Web アプリに移動すると、`ClientConnectionConnected` イベントが表示されています。 クライアントを終了すると、`ClientConnectionDisconnected` イベントも表示されます。

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
