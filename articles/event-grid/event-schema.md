---
title: Azure Event Grid イベント スキーマ
description: Azure Event Grid のイベントに対して用意されているプロパティについて説明します
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 8a8193d21bbc1d0af933657705e605ce31589cbf
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785846"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid イベント スキーマ

この記事では、すべてのイベントに存在するプロパティとスキーマについて説明します。 イベントは、5 つの必須文字列プロパティと 1 つの必須データ オブジェクトで構成されます。 プロパティは、すべてのイベントに共通であり、発行元を問いません。 データ オブジェクトには、各発行元に固有のプロパティが含まれています。 システム トピックの場合、これらのプロパティは、リソース プロバイダー (Azure Storage や Azure Event Hubs など) に固有です。

イベント ソースは、複数のイベント オブジェクトを含めることができる配列で Azure Event Grid にイベントを送信します。 Event Grid トピックへイベントを送信する際の、配列の合計サイズの上限は 1 MB です。 配列内の各イベントは 64 KB (一般提供) または 1 MB (プレビュー) に制限されます。 イベントまたは配列がサイズ制限を超えた場合は、 **[413 ペイロードが大きすぎます]** という応答を受信します。

> [!NOTE]
> 一般提供 (GA) のサービス レベル アグリーメント (SLA) では、最大 64 KB のサイズのイベントが有効範囲に含まれます。 最大 1 MB のサイズのイベントのサポートは現在、プレビュー段階です。 64 KB を超えるイベントは、64 KB の増分単位で課金されます。 

Event Grid は、1 つのイベントを含む配列でサブスクライバーにイベントを送信します。 この動作は、今後変更される可能性があります。

Event Grid イベントおよび各 Azure パブリッシャーのデータ ペイロードの JSON スキーマは、[イベント スキーマ ストア](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane)にあります。

## <a name="event-schema"></a>イベント スキーマ

すべてのイベント発行元に使用されているプロパティの例を次に示します。

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

たとえば、Azure BLOB ストレージ イベントに対して次のようなスキーマが発行されます。

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
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
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>イベントのプロパティ

すべてのイベントには、次の同じ最上位レベルのデータが含まれています。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | リソース プロバイダーに固有のイベント データ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトのプロパティの詳細については、イベント ソースを参照してください。

* [Azure サブスクリプション (管理操作)](event-schema-subscriptions.md)
* [コンテナー レジストリ](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [リソース グループ (管理操作)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)

カスタム トピックの場合、イベントの発行元がデータ オブジェクトを決定します。 最上位レベルのデータには、リソースによって定義された標準のイベントと同じフィールドを含める必要があります。

イベントをカスタム トピックに発行する場合は、サブスクライバーがそのイベントに関心があるかどうかを簡単に知ることができるイベントの件名を作成してください。 サブスクライバーは、その件名を使用してイベントをフィルター処理したり、ルーティングしたりします。 そのイベントが発生したパスを示すことにより、サブスクライバーがそのパスのセグメントでフィルター処理できるように考慮してください。 このパスにより、サブスクライバーはイベントを狭く、または幅広くフィルター処理できます。 たとえば、`/A/B/C` のように件名に 3 つのセグメント パスを示した場合、サブスクライバーは最初のセグメント `/A` でフィルター処理して幅広い一連のイベントを取得できます。 これらのサブスクライバーは、`/A/B/C` や `/A/D/E` などの件名を持つイベントを取得します。 他のサブスクライバーは、`/A/B` でフィルター処理して、より狭い一連のイベントを取得できます。

件名に、何が起こったかに関するより詳細な情報が必要になる場合があります。 たとえば、コンテナーにファイルが追加された場合、**ストレージ アカウント** パブリッシャーは件名 `/blobServices/default/containers/<container-name>/blobs/<file>` を指定します。 サブスクライバーはパス `/blobServices/default/containers/testcontainer` でフィルター処理することにより、ストレージ アカウント内の他のコンテナーではなく、そのコンテナーのすべてのイベントを取得できます。 サブスクライバーはまた、テキスト ファイルのみを操作するために、サフィックス `.txt` でフィルター処理またはルーティングすることもできます。

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
