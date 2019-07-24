---
title: Azure Event Grid の Event Hubs 用のイベント スキーマ
description: Azure Event Grid の Event Hubs イベントに対して用意されているプロパティについて説明します
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475409"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid の Event Hubs 用のイベント スキーマ

この記事では、Event Hubs イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

サンプル スクリプトとチュートリアルの一覧については、[Event Hubs のイベント ソース](event-sources.md#event-hubs)に関する記事をご覧ください。

### <a name="available-event-types"></a>使用可能なイベントの種類

Event Hubs は、キャプチャ ファイルが作成されたときに、種類が **Microsoft.EventHub.CaptureFileCreated** であるイベントを出力します。

## <a name="example-event"></a>イベントの例

このサンプル イベントは、ファイルがキャプチャ機能によって保存されるときに発生する Event Hubs イベントのスキーマを示しています。 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| topic | 文字列 | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | 文字列 | 発行元が定義したイベントの対象のパス。 |
| eventType | 文字列 | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | 文字列 | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | 文字列 | イベントの一意識別子。 |
| data | オブジェクト | Event Hub イベントのデータ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| fileUrl | 文字列 | キャプチャ ファイルのパス。 |
| fileType | 文字列 | キャプチャ ファイルのファイルの種類。 |
| partitionId | 文字列 | シャード ID。 |
| sizeInBytes | integer | ファイル サイズ。 |
| eventCount | integer | ファイル内のイベントの数。 |
| firstSequenceNumber | integer | キューの最小のシーケンス番号。 |
| lastSequenceNumber | integer | キューの最後のシーケンス番号。 |
| firstEnqueueTime | 文字列 | キューの最初の時間。 |
| lastEnqueueTime | 文字列 | キューの最後の時間。 |

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* Event Hubs イベントの処理については、「[ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md)」を参照してください。