---
title: Azure Event Grid でのシステム トピック
description: Azure Event Grid でのシステム トピックについて説明します。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b1fbecb1e372602f9c252d43d2a1f93524ef1846
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052967"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure Event Grid でのシステム トピック
Event Grid でのシステム トピックは、Azure Storage や Azure Event Hubs などの Azure サービスによって発行された 1 つ以上のイベントを表します。 たとえば、システム トピックは **すべての BLOB イベント**、または **特定のストレージ アカウント** に対して発行された **BLOB 作成** および **BLOB 削除** イベントのみを表す可能性があります。 この例では、BLOB がストレージ アカウントにアップロードされると、Azure Storage サービスは Event Grid でのシステム トピックに **BLOB 作成** イベントを発行します。その後、そのイベントはそこから、イベントを受信して処理するトピックの [サブスクライバー](event-handlers.md)に転送されます。 

> [!NOTE] 
> システム トピックにイベントを発行できるのは Azure サービスだけです。 そのため、カスタム トピックまたはドメインの場合とは異なり、イベントを発行するために使用できるエンドポイントまたはアクセス キーは取得しません。

## <a name="azure-services-that-support-system-topics"></a>システム トピックをサポートする Azure サービス
自身でのシステム トピックの作成をサポートする Azure サービスの現在の一覧を次に示します。

- [Azure App Configuration](event-schema-app-configuration.md)
- [Azure App Service](event-schema-app-service.md)
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

## <a name="system-topics-as-azure-resources"></a>Azure リソースとしてのシステム トピック
以前は、システム トピックは暗黙的であり、簡潔にするために公開されていませんでした。 システム トピックは Azure リソースとして表示されるようになり、次の機能を提供します。

- [Azure portal でシステム トピックを表示する](create-view-manage-system-topics.md#view-all-system-topics)
- Azure portal でシステム トピックやイベント サブスクリプションのための Resource Manager テンプレートをエクスポートする
- [システム トピックの診断ログを設定する](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 発行および配信エラーに関するアラートを設定する 

## <a name="lifecycle-of-system-topics"></a>システム トピックのライフサイクル
システム トピックは、次の 2 つの方法で作成できます。 

- [Azure リソースのイベント サブスクリプションを拡張機能リソースとして](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)作成します。これにより、`<Azure resource name>-<GUID>` という形式の名前を持つシステム トピックが自動的に作成されます。 この方法で作成されたシステム トピックは、そのトピックの最後のイベント サブスクリプションが削除されると自動的に削除されます。 
- Azure リソースのシステム トピックを作成してから、そのシステム トピックのイベント サブスクリプションを作成します。 この方法を使用する場合は、システム トピックの名前を指定できます。 このシステム トピックは、最後のイベント サブスクリプションが削除されても自動的には削除されません。 手動で削除する必要があります。 

    Azure portal を使用する場合は、常にこの方法が使用されます。 [Azure リソースの **[イベント]** ページ](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)を使用してイベント サブスクリプションを作成すると、まずシステム トピックが作成され、次にそのトピックのサブスクリプションが作成されます。 まず [ **[Event Grid システム トピック]** ページ](create-view-manage-system-topics.md#create-a-system-topic)を使用し、次にそのトピックのサブスクリプションを作成することによって、システム トピックを明示的に作成できます。 

[CLI](create-view-manage-system-topics-cli.md)、[REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)、または [Azure Resource Manager テンプレート](create-view-manage-system-topics-arm.md)を使用する場合は、上の方法のどちらかを選択できます。 まずシステム トピックを作成し、次にそのトピックのサブスクリプションを作成することをお勧めします。それは、これがシステム トピックを作成するための最新の方法であるためです。

Azure ポリシーを Event Grid サービスがシステム トピックを作成できないように設定している場合は、システム トピックの作成が失敗します。 たとえば、サブスクリプション内の特定の種類のリソース (Azure Storage や Azure Event Hubs など) の作成のみを許可するポリシーにしている場合があります。 

## <a name="location-and-resource-group-for-a-system-topic"></a>システム トピックのための場所とリソース グループ
特定のリージョンまたは場所にある Azure イベント ソースの場合、システム トピックは Azure イベント ソースと同じ場所に作成されます。 たとえば、米国東部の Azure Blob Storage に対してイベント サブスクリプションを作成すると、米国東部にシステム トピックが作成されます。 Azure サブスクリプション、リソース グループ、Azure Maps などのグローバルな Azure イベント ソースの場合は、Event Grid によって **グローバル** な場所にシステム トピックが作成されます。 

一般に、システム トピックは Azure イベント ソースと同じリソース グループに作成されます。 Azure サブスクリプションのスコープで作成されたイベント サブスクリプションの場合、システム トピックは **米国西部 2** リージョンの **Default-EventGrid** リソース グループに作成されます。 リソース グループが存在しない場合は、システム トピックが作成される前に、Azure Event Grid によってリソース グループが作成されます。 

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。 

- [Azure portal を使用してシステム トピックを作成、表示、管理する](create-view-manage-system-topics.md)
- [Azure CLI を使用して Event Grid システム トピックを作成、表示、管理する](create-view-manage-system-topics-cli.md)
- [Azure Resource Manager テンプレートを使用して Event Grid システム トピックを作成する](create-view-manage-system-topics-arm.md)
