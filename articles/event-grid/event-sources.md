---
title: Azure Event Grid のイベント ソース
description: Azure Event Grid でサポートされるイベント ソースについて説明します
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: ad9bb5c135684c4573195298fb8e55a08208f6b6
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785814"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure Event Grid のイベント ソース

イベント ソースは、イベントの発生場所です。 一部の Azure サービスは、イベントを送信するように自動的に構成されます。 イベントを送信するカスタム アプリケーションを作成することもできます。 Event Grid を使ってイベントを配布するために、カスタム アプリケーションを Azure でホストする必要はありません。

この記事では、各イベント ソースに関するコンテンツへのリンクを示します。

## <a name="azure-subscriptions"></a>Azure サブスクリプション

Azure サブスクリプションでのリソースの変更に対応するために、Azure サブスクリプションのイベントをサブスクライブします。

|タイトル |説明  |
|---------|---------|
| [チュートリアル:Azure Automation と Event Grid および Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |イベントを送信する仮想マシンを作成します。 このイベントは、仮想マシンをタグ付けする Automation Runbook をトリガーし、Microsoft Teams チャネルに送信されるメッセージをトリガーします。 |
| [方法: ポータルを使用したイベントのサブスクライブ](subscribe-through-portal.md) | ポータルを使用して Azure サブスクリプションのイベントにサブスクライブします。 |
| [Azure CLI: Azure サブスクリプションのイベントのサブスクライブ](./scripts/event-grid-cli-azure-subscription.md) |Azure サブスクリプションへの Event Grid サブスクリプションを作成し、イベントを Webhook に送信するサンプル スクリプト。 |
| [PowerShell: Azure サブスクリプションのイベントのサブスクライブ](./scripts/event-grid-powershell-azure-subscription.md)| Azure サブスクリプションへの Event Grid サブスクリプションを作成し、イベントを Webhook に送信するサンプル スクリプト。 |
| [イベント スキーマ](event-schema-subscriptions.md) | Azure サブスクリプション イベントのフィールドを示します。 |

## <a name="container-registry"></a>Container Registry

イメージ内の変更に対応するために、Container Registry イベントにサブスクライブします。

|タイトル |説明  |
|---------|---------|
| [クイック スタート: コンテナー レジストリ イベントを送信する](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Container Registry イベントを送信する方法を示します。 |
| [イベント スキーマ](event-schema-container-registry.md) | Container Registry イベント内のフィールドを表示します。 |

## <a name="custom-topics"></a>カスタム トピック

アプリケーションのイベントに対応するために、カスタム トピックをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI を使ったカスタム イベントの作成とルーティング](custom-event-quickstart.md) | Azure CLI を使ってカスタム イベントを送信する方法を示します。 |
| [クイック スタート: Azure PowerShell を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-powershell.md) | Azure PowerShell を使ってカスタム イベントを送信する方法を示します。 |
| [クイック スタート: Azure portal を使ったカスタム イベントの作成とルーティング](custom-event-quickstart-portal.md) | ポータルを使ってカスタム イベントを送信する方法を示します。 |
| [クイック スタート: Azure Queue Storage へのカスタム イベントのルーティング](custom-event-to-queue-storage.md) | Queue Storage にカスタム イベントを送信する方法について説明します。 |
| [方法: カスタム トピックへの投稿](post-to-custom-topic.md) | カスタム トピックにイベントを投稿する方法を示します。 |
| [Azure CLI: Event Grid のカスタム トピックの作成](./scripts/event-grid-cli-create-custom-topic.md)|カスタム トピックを作成するサンプル スクリプトです。 このスクリプトは、エンドポイントとキーを取得します。|
| [Azure CLI: カスタム トピックのイベントのサブスクライブ](./scripts/event-grid-cli-subscribe-custom-topic.md)|カスタム トピックのサブスクリプションを作成するサンプル スクリプトです。 Webhook にイベントを送信します。|
| [PowerShell: Event Grid のカスタム トピックの作成](./scripts/event-grid-powershell-create-custom-topic.md)|カスタム トピックを作成するサンプル スクリプトです。 このスクリプトは、エンドポイントとキーを取得します。|
| [PowerShell: カスタム トピックのイベントのサブスクライブ](./scripts/event-grid-powershell-subscribe-custom-topic.md)|カスタム トピックのサブスクリプションを作成するサンプル スクリプトです。 Webhook にイベントを送信します。|
| [Resource Manager テンプレート: カスタム トピックと Webhook エンドポイント](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | カスタム トピックとそのカスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Webhook にイベントを送信します。 |
|
| [Resource Manager テンプレート: カスタム トピックと Event Hubs エンドポイント](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| カスタム トピックのサブスクリプションを作成する Resource Manager テンプレート。 Azure Event Hubs にイベントを送信します。 |
| [イベント スキーマ](event-schema.md) | カスタム イベントのフィールドを示します。 |

## <a name="event-hubs"></a>Event Hubs

Capture ファイル イベントに対応するために、Event Hubs のイベントをサブスクライブします。 Event Hubs は、イベント ソースまたはイベント ハンドラーとして動作させることができます。 以下の記事では、Event Hubs をソースとして使用する方法を示します。

|タイトル  |説明  |
|---------|---------|
| [チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [イベント スキーマ](event-schema-event-hubs.md) | Event Hubs イベントのフィールドを示します。 |

ハンドラーとしての Event Hubs の例については、[Event Hubs ハンドラー](event-handlers.md#event-hubs)に関するページを参照してください。

## <a name="iot-hub"></a>IoT Hub

デバイスの作成、削除、接続、切断、テレメトリのイベントに対応するために、IoT Hub のイベントをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [Logic Apps を使用して Azure IoT Hub イベントに関する電子メール通知を送信する](publish-iot-hub-events-to-logic-apps.md) | ロジック アプリは、お使いの IoT Hub にデバイスが追加されるたびに、通知メールを送信します。 |
| [Event Grid を使用し IoT Hub のイベントに対応してアクションをトリガーする](../iot-hub/iot-hub-event-grid.md) | IoT Hub と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-iot-hub.md) | IoT Hub イベントのフィールドを示します。 |
| [デバイス接続イベントおよびデバイス切断イベントの順序を設定する](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | デバイス接続状態イベントの順序付け方法を示します。 |

## <a name="media-services"></a>Media Services

ジョブ状態のイベントに対応するため、Media Services のイベントをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [概要: Media Services イベントへの対応](../media-services/latest/reacting-to-media-services-events.md) | Media Services と Event Grid の統合の概要です。 |
| [チュートリアル: CLI を使用して Azure Media Services のイベントをカスタム Web エンドポイントにルーティングする](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services からイベントを送信する方法を示します。 |
| [イベント スキーマ](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Media Services イベントのフィールドを示します。 |

## <a name="resource-groups"></a>リソース グループ

リソース グループのリソースの変更に対応するため、リソース グループのイベントをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [チュートリアル: Azure Event Grid と Logic Apps を使用して仮想マシンの変更を監視する](monitor-virtual-machine-changes-event-grid-logic-app.md) | ロジック アプリは仮想マシンへの変更を監視し、それらの変更に関する電子メールを送信します。 |
| [Azure CLI: リソース グループのイベントのサブスクライブ](./scripts/event-grid-cli-resource-group.md)| リソース グループのイベントにサブスクライブするサンプル スクリプトです。 Webhook にイベントを送信します。 |
| [Azure CLI: リソース グループのイベントのサブスクライブとリソースのフィルタリング](./scripts/event-grid-cli-resource-group-filter.md) | リソース グループのイベントにサブスクライブし、1 つのリソースのイベントをフィルター処理するサンプル スクリプトです。 |
| [PowerShell: リソース グループのイベントのサブスクライブ](./scripts/event-grid-powershell-resource-group.md) | リソース グループのイベントにサブスクライブするサンプル スクリプトです。 Webhook にイベントを送信します。 |
| [PowerShell: リソース グループのイベントのサブスクライブとリソースのフィルタリング](./scripts/event-grid-powershell-resource-group-filter.md) | リソース グループのイベントにサブスクライブし、1 つのリソースのイベントをフィルター処理するサンプル スクリプトです。 |
| [Resource Manager テンプレート: リソースのサブスクリプション](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | Azure サブスクリプションまたはリソース グループのイベントにサブスクライブします。 Webhook にイベントを送信します。 |
| [イベント スキーマ](event-schema-resource-groups.md) | リソース グループ イベントのフィールドを示します。 |

## <a name="service-bus"></a>Service Bus

アクティブなリスナーのいないメッセージに対応するため、Service Bus のイベントをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid は、Service Bus トピックから関数アプリとロジック アプリにメッセージを送信します。 |
| [概要:Azure Service Bus の Event Grid への統合](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | Service Bus と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-service-bus.md) | Service Bus イベントのフィールドを示します。 |

## <a name="storage"></a>Storage

BLOB の作成および削除のイベントに対応するために、Blob Storage のイベントをサブスクライブします。

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: Azure CLI で Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Blob Storage イベントを Webhook に送信する方法を示します。 |
| [クイック スタート: PowerShell を使って Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure PowerShell を使って Blob Storage イベントを Webhook に送信する方法を示します。 |
| [クイック スタート: Azure portal を使用した Blob Storage イベントの作成とルーティング](blob-event-quickstart-portal.md) | ポータルを使って Blob Storage イベントを Webhook に送信する方法を示します。 |
| [Azure CLI: Blob Storage アカウントのイベントのサブスクライブ](./scripts/event-grid-cli-blob.md) | BLOB ストレージ アカウントのイベントにサブスクライブするサンプル スクリプトです。 Webhook にイベントを送信します。 |
| [PowerShell: Blob Storage アカウントのイベントのサブスクライブ](./scripts/event-grid-powershell-blob.md) | BLOB ストレージ アカウントのイベントにサブスクライブするサンプル スクリプトです。 Webhook にイベントを送信します。 |
| [Resource Manager テンプレート:BLOB ストレージとサブスクリプションの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | Azure Blob Storage アカウントをデプロイし、そのストレージ アカウントのイベントをサブスクライブします。 Webhook にイベントを送信します。 |
| [概要: Blob Storage イベントへの対応](../storage/blobs/storage-blob-event-overview.md) | Blob Storage と Event Grid の統合の概要です。 |
| [イベント スキーマ](event-schema-blob-storage.md) | Blob Storage イベントのフィールドを示します。 |

## <a name="maps"></a>マップ
ジオフェンス イベントに応答するには、Azure Maps イベントをサブスクライブします。 たとえば、デバイスがジオフェンスに入ったり出たりするたびに、アプリケーションは電子メール通知を配信できます。

|タイトル  |説明  |
|---------|---------|
| [Event Grid を使用して Azure Maps イベントに反応する](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure Maps と Event Grid の統合の概要。 |
| [チュートリアル:ジオフェンスを設定する](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | このチュートリアルでは、Azure Maps を使用してジオフェンスを設定するための基本的な手順について説明します。 Azure Event Grid を使用してジオフェンスの結果をストリーミングし、ジオフェンスの結果に基づいて通知を設定します。 |
| [イベント スキーマ](event-schema-azure-maps.md) | Azure Maps イベントでのフィールドを示します。 |

## <a name="app-configuration"></a>App Configuration
Azure App Configuration イベントをサブスクライブして、キーと値の変更イベントに応答します。

|タイトル | 説明 |
|---------|---------|
| [Event Grid を使用して Azure App Configuration イベントに応答する](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure App Configuration と Event Grid の統合の概要。 |
| [クイックスタート: Azure CLI を使用して Azure App Configuration イベントをカスタム Web エンドポイントにルーティングする](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Azure CLI を使って Azure App Configuration イベントを Webhook に送信する方法を示します。 |
| [イベント スキーマ](event-schema-app-configuration.md) | Azure App Configuration イベントのフィールドを表示します。 |

## <a name="azure-signalr"></a>Azure SignalR
クライアント接続のイベントに応答するには、Azure SignalR Service のイベントをサブスクライブします。

|タイトル | 説明 |
|---------|---------|
| [Event Grid を使用して Azure SignalR Service のイベントに反応する](../azure-signalr/signalr-concept-event-grid-integration.md) | Azure SignalR Service と Event Grid の統合の概要。 |
| [Azure SignalR Service のイベントを Event Grid に送信する方法](../azure-signalr/signalr-howto-event-grid-integration.md) | Event Grid を通して Azure SignalR Service のイベントをアプリケーションに送信する方法を示します。 |
| [イベント スキーマ](event-schema-azure-signalr.md) | Azure SignalR Service のイベントのフィールドを示します。 |



## <a name="next-steps"></a>次の手順

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。
