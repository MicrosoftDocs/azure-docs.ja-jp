---
title: チュートリアル:カスタム イベントをハイブリッド接続に送信する - Event Grid
description: チュートリアル:Azure Event Grid と Azure CLI を使用して、トピックを発行したり、そのイベントをサブスクライブしたりします。 ハイブリッド接続はエンドポイントに使用されます｡
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: c709d77827551860cc917c3c84c5a849d9fdc512
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566828"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>チュートリアル:Azure CLI および Azure Event Grid を利用した Azure Relay Hybrid Connections へのカスタム イベントの転送

Azure Event Grid は、クラウドのイベント処理サービスです。 Azure Relay Hybrid Connections はサポートされているイベント ハンドラーの 1 つです｡ パブリックなエンドポイントがないアプリケーションからのイベントを処理する必要がある場合は､イベント ハンドラーとしてハイブリッド接続を利用します｡ そうしたアプリケーションは､コーポレート エンタープライズ ネットワークに存在することがあります｡ この記事では、Azure CLI からカスタム トピックを作成してカスタム トピックにサブスクライブし、イベントをトリガーして結果を表示します。 イベントはハイブリッド接続に送信します｡

## <a name="prerequisites"></a>前提条件

- この記事では､ハイブリッド接続およびリスナー アプリケーションがすでに作成されていることを前提としています｡ ハイブリッド接続を開始するには､[Get started with Relay Hybrid Connections - .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) または [Get started with Relay Hybrid Connections - Node](../azure-relay/relay-hybrid-connections-node-get-started.md) を参照してください｡

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- この記事では、Azure CLI のバージョン 2.0.56 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Event Grid のトピックは Azure リソースであり、Azure リソース グループに配置する必要があります。 リソース グループは、Azure リソースをまとめてデプロイして管理するための論理上のコレクションです。

[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。 

次の例では、*gridResourceGroup* という名前のリソース グループを *westus2* の場所に作成します。

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>カスタム トピックの作成

Event Grid のトピックは、イベントの送信先となるユーザー定義のエンドポイントになります。 次の例では、リソース グループにカスタム トピックを作成します。 `<topic_name>` は、カスタム トピックの一意の名前に置き換えてください。 イベント グリッド トピック名は、DNS エントリによって表されるため、一意である必要があります。

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>カスタム トピックへのサブスクライブ

どのイベントを追跡するかは、イベント グリッド トピックにサブスクライブすることによって Event Grid に伝えます。次の例では､作成したカスタム トピックにサブスクライブして､エンドポイントに対するハイブリッド接続のリソース ID を渡しています｡ ハイブリッド接続の ID は以下の形式です｡

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

次のスクリプトは､リレー名前空間のリソース ID を取得します｡ ハイブリッド接続の ID を作成し､イベント グリッドのトピックにサブスクライブします｡ このスクリプトは、エンドポイントのタイプを `hybridconnection` に設定し､そのエンドポイントにハイブリッド接続 ID を使用します。

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

サブスクリプションに[有効期限の日付](concepts.md#event-subscription-expiration)が設定されていることに注意してください。

## <a name="create-application-to-process-events"></a>イベントを処理するアプリケーションを作成する

ハイブリッド接続からイベントを取得できるアプリケーションが必要です。 [C# 用の Microsoft Azure Event Grid Hybrid Connection Consumer サンプル](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination)は、その操作を実行します。 前提条件の手順は既に完了しています。

1. Visual Studio 2019 以降を使用していることを確認してください。

1. ローカル コンピューターにリポジトリを複製します。

1. Visual Studio に HybridConnectionConsumer プロジェクトを読み込みます。

1. Program.cs で、`<relayConnectionString>` と `<hybridConnectionName>` を、作成した Relay 接続文字列およびハイブリッド接続名に置き換えます。

1. Visual Studio からアプリケーションをコンパイルして実行します。

## <a name="send-an-event-to-your-topic"></a>トピックへのイベントの送信

イベントをトリガーして、Event Grid がメッセージをエンドポイントに配信するようすを見てみましょう。 この記事では、Azure CLI を使ってイベントをトリガーする方法を示します。 代わりに、[Event Grid パブリッシャー アプリケーション](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher)を使うこともできます。

まず、カスタム トピックの URL とキーを取得します。 もう一度、`<topic_name>` に実際のカスタム トピック名を使用します。

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

この記事では、単純化するために、サンプル イベント データをカスタム トピックに送信します。 通常はイベント データをアプリケーションまたは Azure サービスから送信することになります。 CURL は、HTTP 要求を送信するユーティリティです。 この記事では、CURL を使用して、イベントをカスタム トピックに送信します。

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

リスナー アプリケーションはイベント メッセージを受信することになります｡

## <a name="clean-up-resources"></a>リソースをクリーンアップする
引き続きこのイベントを使用する場合は、この記事で作成したリソースをクリーンアップしないでください。 それ以外の場合は、次のコマンドを使用して、この記事で作成したリソースを削除します。

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>次のステップ

トピックを作成し、イベントをサブスクライブする方法がわかったら、Event Grid でできることについて、さらに情報を収集しましょう。

- [Event Grid について](overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Azure Event Grid と Logic Apps で仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)