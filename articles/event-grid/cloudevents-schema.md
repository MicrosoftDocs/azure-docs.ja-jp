---
title: CloudEvents スキーマ内のイベントで Azure Event Grid を使用する
description: CloudEvents スキーマを Azure Event Grid 内のイベント用に設定する方法について説明します。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: babanisa
ms.openlocfilehash: 0195ce82396a7b05335242a38a2881e1b2d1afb3
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317495"
---
# <a name="use-cloudevents-schema-with-event-grid"></a>CloudEvents スキーマを Event Grid で使用する

Azure Event Grid では、[既定のイベント スキーマ](event-schema.md)に加えて、[CloudEvents JSON スキーマ](https://github.com/cloudevents/spec/blob/master/json-format.md)内のイベントをネイティブ サポートしています。 [CloudEvents](https://cloudevents.io/) は、イベント データを記述するための[オープンな仕様](https://github.com/cloudevents/spec/blob/master/spec.md)です。

CloudEvents を使用すると、クラウド ベースのイベントを発行したり使用したりするための共通のイベント スキーマを提供し、相互運用性を簡略化することができます。 このスキーマを使用すれば、ツールを統一化したり、イベントのルーティングや処理方法を標準化したり、外部のイベント スキーマを共通の方法で逆シリアル化することができます。 共通のスキーマを使用することで、プラットフォーム間での作業をより簡単に統合できます。

CloudEvents は、[Cloud Native Computing Foundation](https://www.cncf.io/) を通じ、複数の[コラボレーター](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (マイクロソフトを含む) によって構築されています。 現在、バージョン 0.1 が提供されています。

この記事では、Event Grid で CloudEvents スキーマを使用する方法について説明します。

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>プレビュー機能のインストール

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>CloudEvent スキーマ

次に示すのは、CloudEvents 形式の Azure Blob Storage イベントの例です。

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 では、次のプロパティが使えます。

| CloudEvents        | Type     | JSON 値の例             | 説明                                                        | Event Grid のマッピング
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | String   | "com.example.someevent"          | 発生したオカレンスの種類                                   | eventType
| eventTypeVersion   | String   | "1.0"                            | eventType のバージョン (省略可能)                            | dataVersion
| cloudEventsVersion | String   | "0.1"                            | イベントで使用される CloudEvents 仕様のバージョン        | *パススルー*
| source             | URI      | "/mycontext"                     | イベント プロデューサーの説明                                       | topic#subject
| eventID            | String   | "1234-1234-1234"                 | イベントの ID                                                    | id
| eventTime          | Timestamp| "2018-04-05T17:31:00Z"           | イベントが発生したときのタイムスタンプ (省略可能)                    | eventTime
| schemaURL          | URI      | "https:\//myschema.com"           | データ属性が準拠しているスキーマへのリンク (省略可能) | *使用されません*
| contentType        | String   | "application/json"               | データ エンコード形式の説明 (省略可能)                       | *使用されません*
| 拡張機能         | マップ      | { "extA": "vA", "extB", "vB" }  | 追加のメタデータ (省略可能)                                 | *使用されません*
| data               | Object   | { "objA": "vA", "objB", "vB" }  | イベント ペイロード (省略可能)                                       | data

詳しくは、[CloudEvents の仕様](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes)をご覧ください。

CloudEvents スキーマと Event Grid スキーマで配信されるイベントのヘッダー値は同じです (`content-type` を除く)。 CloudEvents スキーマの場合、そのヘッダー値は `"content-type":"application/cloudevents+json; charset=utf-8"` です。 Event Grid スキーマの場合、そのヘッダー値は `"content-type":"application/json; charset=utf-8"` です。

## <a name="configure-event-grid-for-cloudevents"></a>Event Grid を CloudEvents 用に構成する

Event Grid は、CloudEvents スキーマ内のイベントの入力と出力の両方に使用できます。 CloudEvents は、システム イベント (Blob Storage イベントや IoT Hub イベントなど) とカスタム イベントに使用できます。 また、これらのイベントをネットワーク上で相互に変換することもできます。


| 入力スキーマ       | 出力スキーマ
|--------------------|---------------------
| CloudEvents 形式 | CloudEvents 形式
| Event Grid 形式  | CloudEvents 形式
| CloudEvents 形式 | Event Grid 形式
| Event Grid 形式  | Event Grid 形式

いずれのイベント スキーマについても、Event Grid では、イベント グリッド トピックへの発行時やイベント サブスクリプションの作成時に検証が必要です。 詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

### <a name="input-schema"></a>入力スキーマ

カスタム トピックを作成するときは、カスタム トピックに対する入力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

PowerShell では、次を使用します。

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName gridResourceGroup `
  -Location westcentralus `
  -Name <topic_name> `
  -InputSchema CloudEventV01Schema
```

CloudEvents の現在のバージョンでは、イベントのバッチ処理はサポートされていません。 CloudEvent スキーマを使ったイベントをトピックに 対して発行するには、各イベントを個別に発行してください。

### <a name="output-schema"></a>出力スキーマ

イベント サブスクリプションを作成するときは、出力スキーマを設定します。

Azure CLI では、次を使用します。

```azurecli-interactive
topicID=$(az eventgrid topic show --name <topic-name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --source-resource-id $topicID \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

PowerShell では、次を使用します。
```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name <topic-name>).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeliverySchema CloudEventV01Schema
```

CloudEvents の現在のバージョンでは、イベントのバッチ処理はサポートされていません。 CloudEvent スキーマ用に構成されたイベント サブスクリプションでは、各イベントが個別に受信されます。 現時点では、CloudEvents スキーマでイベントを配信する際に、Azure Functions アプリの Event Grid トリガーを使用することはできません。 HTTP トリガーを使用します。 CloudEvents スキーマでイベントを受け取る HTTP トリガーを実装する例については、「[Event Grid トリガーとして HTTP トリガーを使用する](../azure-functions/functions-bindings-event-grid.md#use-an-http-trigger-as-an-event-grid-trigger)」を参照してください。

## <a name="next-steps"></a>次の手順

* イベント配信の監視について詳しくは、「[Event Grid メッセージ配信の監視](monitor-event-delivery.md)」をご覧ください。
* CloudEvents テストし、問題についてコメントし、改良に[協力](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md)する。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
