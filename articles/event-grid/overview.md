---
title: Azure Event Grid とは
description: Azure Event Grid を使用してソースからハンドラーにイベント データを送信します。 イベント ベースのアプリケーションを構築し、Azure サービスと統合します。
ms.topic: overview
ms.date: 01/28/2021
ms.openlocfilehash: e53665c88c3860d37b3512c6498ab626b02a6400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99055138"
---
# <a name="what-is-azure-event-grid"></a>Azure Event Grid とは

Azure Event Grid では、イベント ベースのアーキテクチャを備えたアプリケーションを簡単に作成することができます。 最初にサブスクライブ対象の Azure リソースを選択して、イベントの送信先となるイベント ハンドラーまたは Webhook エンドポイントを指定します。 Event Grid には、Storage Blob やリソース グループなどの Azure サービスから送信されるイベントに対するサポートが組み込まれています。 さらに Event Grid では、カスタム トピックを使用した独自のイベントもサポートされます。 

フィルターを使用することで、特定のイベントをさまざまなエンドポイントにルーティングしたり、複数のエンドポイントにマルチキャストしたり、イベントを確実に配信したりできます。

Azure Event Grid は、すべてのリージョンの複数の障害ドメインに、および可用性ゾーン (それらをサポートするリージョン内の) にネイティブに分散させることによって、可用性を最大化するようにデプロイされます。 Event Grid でサポートされているリージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)」を参照してください。

この記事では、Azure Event Grid の概要を示します。 Event Grid の使用をすぐに開始するには、「[Azure Event Grid を使ったカスタム イベントの作成とルーティング](custom-event-quickstart.md)」を参照してください。 

:::image type="content" source="./media/overview/functional-model.png" alt-text="ソースとハンドラーの Event Grid モデル" lightbox="./media/overview/functional-model-big.png":::

この図は、Event Grid によってソースとハンドラーが接続されるようすを示すもので、サポートされる統合の包括的な一覧ではありません。

## <a name="event-sources"></a>イベント ソース

現在、次の Azure サービスは Event Grid へのイベントの送信をサポートしています。 一覧のソースの詳細については、リンクを選択してください。

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure Blob Storage](event-schema-blob-storage.md)
- [Azure Communication Services](event-schema-communication-services.md) 
- [Azure Container Registry](event-schema-container-registry.md)
- [Azure Event Hubs](event-schema-event-hubs.md)
- [Azure IoT Hub](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure Machine Learning](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure Media Services](event-schema-media-services.md)
- [Azure リソース グループ](event-schema-resource-groups.md)
- [Azure Service Bus](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure サブスクリプション](event-schema-subscriptions.md)
- [Azure Cache for Redis](event-schema-azure-cache.md)

## <a name="event-handlers"></a>イベント ハンドラー

各ハンドラーの機能の完全な詳細のほか、関連記事については、[イベント ハンドラー](event-handlers.md)に関する記事を参照してください。 現在、次の Azure サービスは Event Grid からのイベントの処理をサポートしています。 

* [Azure Automation](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [Event Hubs](handler-event-hubs.md)
* [ハイブリッド接続のリレー](handler-relay-hybrid-connections.md)
* [Logic Apps](handler-webhooks.md#logic-apps)
* [Power Automate (旧称 Microsoft Flow)](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [Service Bus](handler-service-bus.md)
* [Queue Storage](handler-storage-queues.md)
* [WebHooks](handler-webhooks.md)

## <a name="concepts"></a>概念

Azure Event Grid には、作業開始にあたり理解する必要がある、5 つの概念があります。

* **イベント** - 発生内容
* **イベント ソース** - イベントの発生元
* **トピック** - 発行元がイベントを送信するエンドポイント
* **イベント サブスクリプション** - イベントを (ときには複数のハンドラーに) ルーティングするエンドポイントまたは組み込みメカニズム。 サブスクリプションは、受信イベントをインテリジェントにフィルター処理するために、ハンドラーによっても使用されます。
* **イベント ハンドラー** - イベントに反応するアプリまたはサービス

これらの概念の詳細については、「[Azure Event Grid の概念](concepts.md)」を参照してください。

## <a name="capabilities"></a>機能

Azure Event Grid の主要な特長を次に示します。

* **簡単** - Azure リソースから任意のイベント ハンドラーまたはエンドポイントまで、イベントにカーソルを合わせてクリックするだけで利用できます。
* **高度なフィルター処理** - イベントの種類またはイベント発行パスに基づいてフィルター処理することで、イベント ハンドラーが関連するイベントのみを受け取るようにできます。
* **ファンアウト** - 複数のエンドポイントを同じイベントにサブスクライブし、必要な数の場所にイベントのコピーを送信できます。
* **信頼性** - 指数バックオフによる 24 時間の再試行で、確実にイベントが配信されるようにします。
* **イベントごとの支払** - Event Grid の使用量に対して料金を支払います。
* **高スループット** - Event Grid で大量のワークロードを作成できます。
* **組み込みイベント** - リソース定義の組み込みイベントにより、迅速に開始および実行できます。
* **カスタム イベント** - Event Grid ルートを使用し、フィルター処理を行い、信頼性の高い方法でアプリにカスタム イベントを配信します。

Event Grid、Event Hubs、および Service Bus の比較については、「[Choose between Azure services that deliver messages (メッセージを配信する Azure サービスの選択)](compare-messaging-services.md)」を参照してください。

## <a name="what-can-i-do-with-event-grid"></a>Event Grid でできること

Azure Event Grid は、サーバーレス、操作の自動化、および[統合](https://azure.com/integration)作業を大幅に向上する複数の機能を提供します。 

### <a name="serverless-application-architectures"></a>サーバーレス アプリケーション アーキテクチャ

![サーバーレス アプリケーション アーキテクチャ](./media/overview/serverless_web_app.png)

Event Grid はデータ ソースとイベント ハンドラーを接続します。 たとえば、Event Grid を使用して、Blob Storage コンテナーに追加されるとイメージを分析するサーバーレス関数をトリガーします。 

### <a name="ops-automation"></a>操作の自動化

![操作の自動化](./media/overview/Ops_automation.png)

Event Grid を使用すると、自動化を迅速にして、ポリシーの適用を簡素化できます。 たとえば、Event Grid を使用して、Azure SQL で仮想マシンやデータベースが作成されたら Azure Automation に通知します。 イベントを使用して、サービス構成が準拠していることの自動確認、操作ツールへのメタデータの配置、仮想マシンのタグ付け、作業項目を登録などを実行します。

### <a name="application-integration"></a>アプリケーションの統合

![Azure とのアプリケーションの統合](./media/overview/app_integration.png)

Event Grid はお客様のアプリを他のサービスにつなげます。 たとえば、お客様のアプリのイベント データを Event Grid に送信するカスタム トピックを作成すると、Event Grid の信頼性の高い配信、高度なルーティング、Azure との直接統合を利用できます。 または、Event Grid を Logic Apps と共に使用して、コードを作成することなく、場所を問わずにデータを処理することもできます。 

## <a name="how-much-does-event-grid-cost"></a>Event Grid のコスト

Azure Event Grid では、イベントごとに課金される価格モデルを使用しているため、使用した分だけお支払いいただきます。 毎月の最初の 100,000 操作は無料です。 操作は、イベント イングレス、サブスクリプション配信の試行、管理呼び出し、サブジェクト サフィックスによるフィルター処理と定義されます。 詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/event-grid/)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Storage Blob のイベントをルーティングする](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  Event Grid を使用して Storage Blob のイベントに応答します。
* [カスタム イベントを作成してサブスクライブする](custom-event-quickstart.md)  
  Azure Event Grid のクイックスタートを使用して、任意のエンドポイントへの独自のカスタム イベントの送信をすぐに始めることができます。
* [Logic Apps をイベント ハンドラーとして使用する](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  Event Grid によってプッシュされるイベントに対応するアプリを Logic Apps を使用して作成するためのチュートリアルです。
* [ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)  
  Azure Functions を使用して Event Hubs から Azure Synapse Analytics にデータをストリーミングするチュートリアルです。
* [Event Grid REST API リファレンス](/rest/api/eventgrid)  
  イベントのサブスクリプション、ルーティング、フィルター処理を管理するためのリファレンス コンテンツを提供します。