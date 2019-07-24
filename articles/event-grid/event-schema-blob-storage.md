---
title: Azure Event Grid Blob Storage イベント スキーマ
description: Blob Storage イベントに関して Azure Event Grid に用意されているプロパティについて説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 401eb660d7e5ddc68bc7422ef9f2e600295d2aea
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2019
ms.locfileid: "54469741"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid の Blob Storage 用のイベント スキーマ

この記事では、Blob Storage イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

サンプル スクリプトとチュートリアルの一覧については、[ストレージのイベント ソース](event-sources.md#storage)に関する記事をご覧ください。

## <a name="available-event-types"></a>使用可能なイベントの種類

Blob Storage から出力されるイベントの種類は次のとおりです。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Storage.BlobCreated | BLOB が作成されたときに発生します。 |
| Microsoft.Storage.BlobDeleted | BLOB が削除されたときに発生します。 |

## <a name="example-event"></a>イベントの例

次の例は、BlobCreated イベントのスキーマを示しています。 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

同様のスキーマが BlobDeleted イベントにも使用されています。 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/testfile.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://example.blob.core.windows.net/testcontainer/testfile.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
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
| data | オブジェクト | Blob Storage イベントのデータ。 |
| dataVersion | 文字列 | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | 文字列 | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | type | 説明 |
| -------- | ---- | ----------- |
| api | 文字列 | イベントのトリガーとなった操作。 |
| clientRequestId | 文字列 | クライアントによって生成される opaque 値 (文字数の上限は 1 KB)。 Storage Analytics のログを有効にすると、Analytics のログに記録されます。 |
| requestId | 文字列 | 要求の一意の識別子。 要求のトラブルシューティングに使用されます。 |
| eTag | 文字列 | この値を使用することで、条件に応じて操作を実行することができます。 |
| contentType | 文字列 | BLOB に関して指定されたコンテンツの種類。 |
| contentLength | integer | BLOB のサイズ (単位: バイト)。 |
| blobType | 文字列 | BLOB の種類。 有効な値は "BlockBlob" または "PageBlob" です。 |
| url | 文字列 | BLOB へのパス。 |
| sequencer | 文字列 | ユーザーによって制御される値。要求を追跡する際に使用できます。 |
| storageDiagnostics | オブジェクト | ストレージ診断に関する情報。 |
 
## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* Blob Storage のイベントを処理する方法の概要については、[Azure CLI での Blob Storage イベントのルーティング](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)に関するページを参照してください。 
