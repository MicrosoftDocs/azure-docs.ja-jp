---
title: Azure API Management で Azure Event Hubs にイベントを記録する方法 | Microsoft Docs
description: Azure API Management で Azure Event Hubs にイベントを記録する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073479"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Azure API Management で Azure Event Hubs にイベントを記録する方法
Azure Event Hubs は、1 秒間に数百万件のイベントを取り込むことができる高度にスケーラブルなデータ受信サービスであり、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。 Event Hubs はイベント パイプラインの「玄関」として機能し、Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーまたはバッチ処理/ストレージ アダプターを使用して変換および格納できます。 Event Hubs はイベント ストリームの生成とイベントの使用を分離し、イベント コンシューマーが独自のスケジュールでイベントにアクセスできるようにします。

この記事は [Azure API Management と Event Hubs の統合](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) 動画に付随するものであり、Azure Event Hubs を利用して API Management イベントをログに記録する方法を説明しています。

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します

イベント ハブを作成し、イベント ハブでイベントの送受信を行うために必要な接続文字列を取得する方法の詳しい手順については、「[Azure Portal を使用して Event Hubs 名前空間とイベント ハブを作成する](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)」をご覧ください。

## <a name="create-an-api-management-logger"></a>API Management ロガーの作成
イベント ハブが完成したら、そこにイベントを記録できるようにするための構成を API Management サービスの [ロガー](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) に対して行います。

API Management のロガーは、 [API Management REST API](https://aka.ms/smapi)を使用して構成します。 REST API を初めて使用する前には、[前提条件](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest)を確認し、[REST API へのアクセスを有効化](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI)してください。

ロガーを作成するには、次の URL テンプレートを使用して HTTP PUT 要求を送信します。

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* `{your service}` は、実際の API Management サービス インスタンスの名前です。
* `{new logger name}` は、新しいロガーに付ける名前です。 [log-to-eventhub](/azure/api-management/api-management-advanced-policies#log-to-eventhub) ポリシーを構成するときに、この名前を参照します。

次のヘッダーを要求に追加します。

* Content-Type : application/json
* Authorization :SharedAccessSignature 58...
  * `SharedAccessSignature` を生成する手順については、 [Azure API Management REST API の認証に関するページ](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication)を参照してください。

次のテンプレートを使用して要求の本文を指定します。

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType` は `AzureEventHub` に設定する必要があります。
* `description` は、ロガーの説明です (省略可能)。必要に応じて、長さゼロの文字列にしてください。
* `credentials` には、Azure Event Hubs の `name` と `connectionString` が含まれます。

この要求を実行すると、ロガーが作成された場合、`201 Created` の状態コードが返されます。 上の要求の例に基づいた応答の例を次に示します。

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> その他のリターン コードとその理由については、 [ロガーの作成に関するページ](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)を参照してください。 その他の操作 (リスト、更新、削除など) の実行方法については、 [ロガー](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) エンティティのドキュメントを参照してください。
>
>

## <a name="configure-log-to-eventhubs-policies"></a>log-to-eventhub ポリシーの構成

API Management でロガーを構成したら、必要なイベントを記録するための構成を log-to-eventhub ポリシーに対して行います。 log-to-eventhub ポリシーは、inbound と outbound のどちらかのポリシー セクションで使用します。

1. APIM インスタンスを参照します。
2. [API] タブを選択します。
3. ポリシーを追加する API を選択します。 この例では、全製品 ( **[無制限]** ) の **[Echo API]** にポリシーを追加しています。
4. **[すべての操作]** を選択します。
5. 画面の上部の [デザイン] タブを選択します。
6. [受信処理] または [送信処理] ウィンドウで、(鉛筆の横にある) 三角形をクリックします。
7. [コード エディター] を選択します。 詳細については、[ポリシーの設定または編集方法](set-edit-policies.md)に関する記事をご覧ください。
8. `inbound` または `outbound` ポリシー セクションにカーソルを置きます。
9. 右側のウィンドウで、 **[詳細なポリシー]**  >  **[Log to EventHub]\(EventHub へのログ記録\)** を選択します。 これにより、`log-to-eventhub` ポリシー ステートメント テンプレートが挿入されます。

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
`logger-id` を、前の手順でロガーを作成するために URL の `{new logger name}` に使用した値に置き換えます。

`log-to-eventhub` 要素の値は、文字列を返す式であれば何でもかまいません。 この例では、日付と時刻、サービス名、要求 ID、要求の IP アドレス、操作の名前から成る文字列が記録されます。

**[保存]** をクリックして、更新済みのポリシーの構成を保存します。 保存後すぐポリシーが有効となり、指定したイベント ハブにイベントが記録されます。

## <a name="next-steps"></a>次の手順
* Azure Event Hubs の詳細
  * [Azure Event Hubs の使用](../event-hubs/event-hubs-c-getstarted-send.md)
  * [EventProcessorHost を使用したメッセージの受信](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)
* API Management と Event Hubs の統合の詳細
  * [ロガーのエンティティ リファレンス](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [log-to-eventhub ポリシー リファレンス](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Azure API Management、Event Hubs、Moesif を使用した API の監視](api-management-log-to-eventhub-sample.md)  
* [Azure Application Insights との統合](api-management-howto-app-insights.md)について学習する

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
