---
title: Azure Event Grid イベント ハンドラー
description: Azure Event Grid でサポートされるイベント ハンドラーについて説明します
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 6093e1017af2fb8c54eaf1c3192f937172567982
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080551"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure Event Grid のイベント ハンドラー

イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。 イベント処理のための Webhook を使うこともできます。 Webhook は、イベント処理用に Azure でホストする必要はありません。 Event Grid では、HTTPS Webhook エンドポイントのみがサポートされています。

この記事では、各イベント ハンドラーに関するコンテンツへのリンクを示します。

## <a name="azure-automation"></a>Azure Automation

Azure Automation を使うと、自動化された Runbook でイベントを処理できます。

|タイトル  |説明  |
|---------|---------|
|[チュートリアル:Azure Automation と Event Grid および Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |イベントを送信する仮想マシンを作成します。 このイベントは、仮想マシンをタグ付けする Automation Runbook をトリガーし、Microsoft Teams チャネルに送信されるメッセージをトリガーします。 |

## <a name="azure-functions"></a>Azure Functions

Azure Functions を使うと、サーバーを介さずにイベントに応答できます。

ハンドラーとして Azure Functions を使用する場合は、汎用 HTTP トリガーではなく Event Grid トリガーを使用します。 Event Grid では、Event Grid 関数トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](security-authentication.md#webhook-event-delivery)を実装する必要があります。

|タイトル  |説明  |
|---------|---------|
| [Azure Functions の Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md) | 関数での Event Grid トリガーの使用に関する概要です。 |
| [チュートリアル: Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](resize-images-on-storage-blob-upload-event.md) | ユーザーは、Web アプリを使ってストレージ アカウントにイメージをアップロードします。 ストレージ BLOB が作成されると、Event Grid によって関数アプリにイベントが送信され、アップロードされたイメージのサイズが変更されます。 |
| [チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |

## <a name="event-hubs"></a>Event Hubs

ソリューションで処理できるよりも速くイベントが取得される場合に、Event Hubs を使用します。 アプリケーションでは、独自のスケジュールで Event Hubs からのイベントを処理します。 イベント処理をスケーリングして、受信イベントを処理することができます。

Event Hubs は、イベント ソースまたはイベント ハンドラーとして動作させることができます。 次の記事では、ハンドラーとして Event Hubs を使用する方法を示します。

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI と Event Grid を使用してカスタム イベントを Azure Event Hubs にルーティングする](custom-event-to-eventhub.md) | アプリケーションで処理するためにカスタム イベントをイベント ハブに送信します。 |
| [Resource Manager テンプレート: カスタム トピックと Event Hubs エンドポイント](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |

ソースとしての Event Hubs の例については、[Event Hubs ソース](event-sources.md#event-hubs)に関するページを参照してください。

## <a name="hybrid-connections"></a>ハイブリッド接続と

Azure Relay ハイブリッド接続を使用して、エンタープライズ ネットワーク内にあり、パブリックにアクセスできるエンドポイントがないアプリケーションにイベントを送信します。

|タイトル  |説明  |
|---------|---------|
| [チュートリアル: ハイブリッド接続にイベントを送信する](custom-event-to-hybrid-connection.md) | リスナー アプリケーションで処理するために既存のハイブリッド接続にカスタム イベントを送信します。 |

## <a name="logic-apps"></a>Logic Apps

Logic Apps を使うと、イベントに応答するためのビジネス プロセスを自動化できます。

|タイトル  |説明  |
|---------|---------|
| [チュートリアル: Azure Event Grid と Logic Apps を使用して仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md) | ロジック アプリは仮想マシンへの変更を監視し、それらの変更に関する電子メールを送信します。 |
| [チュートリアル: Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、IoT ハブにデバイスが追加されるたびに、通知の電子メールを送信します。 |
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |

## <a name="service-bus-queue-preview"></a>Service Bus キュー (プレビュー)

Event Grid のイベントをエンタープライズ アプリケーションでのバッファー処理またはコマンドおよびコントロール シナリオで使用するために Service Bus キューに直接ルーティングするには、Service Bus をイベント ハンドラーとして使用します。 このプレビューは Service Bus のトピックやセッションでは機能しませんが、すべてのレベルの Service Bus キューで機能します。

ハンドラーとしての Service Bus はパブリック プレビュー段階ですが、これを使用してイベント サブスクリプションを作成する場合は CLI または PowerShell 拡張機能をインストールする必要があることに注意してください。

### <a name="install-extension-for-azure-cli"></a>Azure CLI 用の拡張機能のインストール

Azure CLI の場合は、[Event Grid 拡張機能](/cli/azure/azure-cli-extensions-list)が必要です。

[CloudShell](/azure/cloud-shell/quickstart) の場合:

* 拡張機能を以前にインストールしている場合は、`az extension update -n eventgrid` を使って更新します。
* 拡張機能を以前にインストールしていない場合は、`az extension add -n eventgrid` を使ってインストールします。

ローカル インストールの場合:

1. [Azure CLI のインストール](/cli/azure/install-azure-cli)を実行します。 `az --version` を使って確認し、最新バージョンがあることを確認します。
1. `az extension remove -n eventgrid` を使って、以前のバージョンの拡張機能をアンインストールします。
1. `az extension add -n eventgrid` を使って `eventgrid` 拡張機能をインストールします

### <a name="install-module-for-powershell"></a>PowerShell 用のモジュールのインストール

PowerShell の場合は、[AzureRM.EventGrid モジュール](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview)が必要です。

[CloudShell](/azure/cloud-shell/quickstart-powershell) の場合:

* `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を使ってモジュールをインストールします。

ローカル インストールの場合:

1. 管理者として PowerShell コンソールを開きます。
1. `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` を使ってモジュールをインストールします。

`-AllowPrerelease`パラメーターが使用可能でない場合は、次のステップを実行します。

1. `Install-Module PowerShellGet -Force` を実行します。
1. `Update-Module PowerShellGet` を実行します。
1. PowerShell コンソールを閉じます。
1. 管理者として PowerShell を再起動します。
1. モジュール `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery` をインストールします。

### <a name="using-cli-to-add-a-service-bus-handler"></a>CLI を使って Service Bus ハンドラーを追加する

Azure CLI の場合、次の例で Event Grid トピックを Service Bus キューにサブスクライブして接続します。

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
    --name <my-event-subscription> \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1 \
    --endpoint-type servicebusqueue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.ServiceBus/namespaces/ns1/queues/queue1
```

## <a name="queue-storage"></a>Queue Storage

キュー ストレージを使うと、プルする必要があるイベントを受信できます。 応答に時間がかかりすぎる長期実行プロセスの場合に、Queue Storage を使用できます。 イベントを Queue Storage に送信することで、アプリでは独自のスケジュールでイベントをプルして処理することができます。

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI と Event Grid を使ってカスタム イベントを Azure Queue Storage にルーティングする](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |

## <a name="webhooks"></a>WebHook

Webhook は、イベントに応答するカスタマイズ可能なエンドポイントに対して使います。

|タイトル  |説明  |
|---------|---------|
| クイック スタート: [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md)、および[ポータル](custom-event-quickstart-portal.md)を使用してカスタム イベントを作成およびルーティングする。 | カスタム イベントを Webhook に送信する方法を示します。 |
| クイック スタート: [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json)、および[ポータル](blob-event-quickstart-portal.md)を使用して、Blob Storage イベントをカスタム Web エンドポイントにルーティングする。 | Blob Storage イベントを Webhook に送信する方法を示します。 |
| [クイック スタート: コンテナー レジストリ イベントを送信する](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Container Registry イベントを送信する方法を示します。 |
| [概要: HTTP エンドポイントへのイベントを受け取る](receive-events.md) | イベント サブスクリプションからイベントを受信する HTTP エンドポイントを検証した後、イベントを逆シリアル化する方法について説明します。 |

## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
