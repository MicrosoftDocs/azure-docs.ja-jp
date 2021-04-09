---
title: Azure Event Grid での CloudEvents v1.0 スキーマ
description: CloudEvents v1.0 スキーマを Azure Event Grid 内のイベント用に使用する方法について説明します。 サービスでは、CloudEvents の JSON 実装でのイベントをサポートしています。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d17e92c28784ca31f3c9809c93e885b22c6a38d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91324180"
---
# <a name="cloudevents-v10-schema-with-azure-event-grid"></a>Azure Event Grid での CloudEvents v1.0 スキーマ

Azure Event Grid は、[既定のイベント スキーマ](event-schema.md)に加え、[CloudEvents v1.0](https://github.com/cloudevents/spec/blob/v1.0/json-format.md) および [HTTP プロトコル バインディング](https://github.com/cloudevents/spec/blob/v1.0/http-protocol-binding.md)の JSON 実装のイベントをネイティブでサポートします。 [CloudEvents](https://cloudevents.io/) は、イベント データを記述するための[オープンな仕様](https://github.com/cloudevents/spec/blob/v1.0/spec.md)です。

CloudEvents を使用すると、クラウド ベースのイベントを発行したり使用したりするための共通のイベント スキーマを提供し、相互運用性を簡略化することができます。 このスキーマを使用すれば、ツールを統一化したり、イベントのルーティングや処理方法を標準化したり、外部のイベント スキーマを共通の方法で逆シリアル化することができます。 共通のスキーマを使用することで、プラットフォーム間での作業をより簡単に統合できます。

CloudEvents は、[Cloud Native Computing Foundation](https://www.cncf.io/) を通じ、複数の[コラボレーター](https://github.com/cloudevents/spec/blob/master/community/contributors.md) (マイクロソフトを含む) によって構築されています。 現在、バージョン 1.0 が提供されています。

この記事では、Event Grid での CloudEvents スキーマについて説明します。

## <a name="sample-event-using-cloudevents-schema"></a>CloudEvents スキーマを使用したサンプル イベント

次に示すのは、CloudEvents 形式の Azure Blob Storage イベントの例です。

``` JSON
{
    "specversion": "1.0",
    "type": "Microsoft.Storage.BlobCreated",  
    "source": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}",
    "id": "9aeb0fdf-c01e-0131-0922-9eb54906e209",
    "time": "2019-11-18T15:13:39.4589254Z",
    "subject": "blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "dataschema": "#",
    "data": {
        "api": "PutBlockList",
        "clientRequestId": "4c5dd7fb-2c48-4a27-bb30-5361b5de920a",
        "requestId": "9aeb0fdf-c01e-0131-0922-9eb549000000",
        "eTag": "0x8D76C39E4407333",
        "contentType": "image/png",
        "contentLength": 30699,
        "blobType": "BlockBlob",
        "url": "https://gridtesting.blob.core.windows.net/testcontainer/{new-file}",
        "sequencer": "000000000000000000000000000099240000000000c41c18",
        "storageDiagnostics": {
            "batchId": "681fe319-3006-00a8-0022-9e7cde000000"
        }
    }
}
```

CloudEvents v1.0 で使用できるフィールド、その種類、定義の詳細については、[こちら](https://github.com/cloudevents/spec/blob/v1.0/spec.md#required-attributes)を参照してください。

CloudEvents スキーマと Event Grid スキーマで配信されるイベントのヘッダー値は同じです (`content-type` を除く)。 CloudEvents スキーマの場合、そのヘッダー値は `"content-type":"application/cloudevents+json; charset=utf-8"` です。 Event Grid スキーマの場合、そのヘッダー値は `"content-type":"application/json; charset=utf-8"` です。

## <a name="event-grid-for-cloudevents"></a>CloudEvents 用の Event Grid

Event Grid は、CloudEvents スキーマ内のイベントの入力と出力の両方に使用できます。 CloudEvents は、システム イベント (Blob Storage イベントや IoT Hub イベントなど) とカスタム イベントに使用できます。 また、これらのイベントをネットワーク上で相互に変換することもできます。


| 入力スキーマ       | 出力スキーマ
|--------------------|---------------------
| CloudEvents 形式 | CloudEvents 形式
| Event Grid 形式  | CloudEvents 形式
| Event Grid 形式  | Event Grid 形式

いずれのイベント スキーマについても、Event Grid では、イベント グリッド トピックへの発行時やイベント サブスクリプションの作成時に検証が必要です。 詳細については、「[Event Grid security and authentication](security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。

## <a name="next-steps"></a>次のステップ
「[Event Grid に CloudEvents v1.0 スキーマを使用する](cloudevents-schema.md)」を参照してください。  
