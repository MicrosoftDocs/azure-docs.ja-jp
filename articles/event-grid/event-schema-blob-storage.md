---
title: Azure Event Grid Blob Storage イベント スキーマ
description: Blob Storage イベントに関して Azure Event Grid に用意されているプロパティについて説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: bed6c3f1efcb2d0ef34e827ddb2b521f8c038940
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445778"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>Azure Event Grid の Blob Storage 用のイベント スキーマ

この記事では、Blob Storage イベントのプロパティとスキーマについて説明します。 イベント スキーマの概要については、「[Azure Event Grid イベント スキーマ](event-schema.md)」を参照してください。

サンプル スクリプトとチュートリアルの一覧については、[ストレージのイベント ソース](event-sources.md#storage)に関する記事をご覧ください。

## <a name="list-of-events-for-blob-rest-apis"></a>BLOB REST API のイベント一覧

これらのイベントは、クライアントが BLOB REST API を呼び出して BLOB を作成、置換、または削除するときにトリガーされます。

 |イベント名 |説明|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |BLOB が作成または置換されたときにトリガーされます。 <br>具体的には、クライアントが BLOB REST API で使用可能な `PutBlob`、`PutBlockList`、または `CopyBlob` 操作を使用した場合に、このイベントがトリガーされます。   |
 |**Microsoft.Storage.BlobDeleted** |BLOB が削除されたときにトリガーされます。 <br>具体的には、クライアントが BLOB REST API で使用可能な `DeleteBlob` 操作を呼び出した場合に、このイベントがトリガーされます。 |

> [!NOTE]
> ブロック BLOB が完全にコミットされた場合に限り **Microsoft.Storage.BlobCreated** イベントがトリガーされることを確認する場合、`CopyBlob`、`PutBlob`、および `PutBlockList` REST API 呼び出しのイベントをフィルター処理します。 データがブロック BLOB に完全にコミットされた後でのみ、これらの API によって **Microsoft.Storage.BlobCreated** イベントがトリガーされます。 フィルターの作成方法の詳細については、「[Event Grid のイベントのフィルター処理](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)」をご覧ください。

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST API のイベント一覧

ストレージ アカウントの階層型名前空間を有効にした状態でクライアントが Azure Data Lake Storage Gen2 REST API を呼び出すと、これらのイベントがトリガーされます。

> [!NOTE]
> これらのイベントはパブリック プレビュー段階で、**米国西部 2**および**米国中西部**リージョンでのみ使用できます。

 |イベント名|説明|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** | BLOB が作成または置換されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `CreateFile` および `FlushWithClose` 操作を使用した場合に、このイベントがトリガーされます。 |
 |**Microsoft.Storage.BlobDeleted** |BLOB が削除されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `DeleteFile` 操作を呼び出した場合に、このイベントもトリガーされます。 |
 |**Microsoft.Storage.BlobRenamed**|BLOB の名前が変更されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `RenameFile` 操作を使用した場合に、このイベントがトリガーされます。|
 |**Microsoft.Storage.DirectoryCreated**|ディレクトリが作成されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `CreateDirectory` 操作を使用した場合に、このイベントがトリガーされます。|
 |**Microsoft.Storage.DirectoryRenamed**|ディレクトリの名前が変更されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `RenameDirectory` 操作を使用した場合に、このイベントがトリガーされます。|
 |**Microsoft.Storage.DirectoryDeleted**|ディレクトリが削除されたときにトリガーされます。 <br>具体的には、クライアントが Azure Data Lake Storage Gen2 REST API で使用可能な `DeleteDirectory` 操作を使用した場合に、このイベントがトリガーされます。|

> [!NOTE]
> ブロック BLOB が完全にコミットされた場合に限り **Microsoft.Storage.BlobCreated** イベントがトリガーされることを確認する場合、`FlushWithClose` REST API 呼び出しのイベントをフィルター処理します。 データがブロック BLOB に完全にコミットされた後でのみ、この API によって **Microsoft.Storage.BlobCreated** イベントがトリガーされます。 フィルターの作成方法の詳細については、「[Event Grid のイベントのフィルター処理](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)」をご覧ください。

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>イベント応答の内容

イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがサブスクライブしているエンドポイントに送信されます。

このセクションには、各 BLOB ストレージ イベントでそのデータがどのように見えるかの例が含まれています。

### <a name="microsoftstorageblobcreated-event"></a>Microsoft.Storage.BlobCreated イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
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
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobCreated イベント (Data Lake Storage Gen2)

BLOB ストレージ アカウントに階層型名前空間がある場合、以下の変更点を除き、データは前の例と同じようになります。

* `dataVersion` キーは値 `2` に設定されます。

* `data.api` キーは文字列 `CreateFile` または `FlushWithClose` に設定されます。

* `contentOffset` キーはデータセットに含まれます。

> [!NOTE]
> アプリケーションが `PutBlockList` 操作を使用してアカウントに新しい BLOB をアップロードする場合、データにこれらの変更は含まれません。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "0x8D4BCC2E4835CD0",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>Microsoft.Storage.BlobDeleted イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>Microsoft.Storage.BlobDeleted イベント (Data Lake Storage Gen2)

BLOB ストレージ アカウントに階層型名前空間がある場合、以下の変更点を除き、データは前の例と同じようになります。

* `dataVersion` キーは値 `2` に設定されます。

* `data.api` キーは文字列 `DeleteFile` に設定されます。

* `url` キーはパス `dfs.core.windows.net` を含みます。

> [!NOTE]
> アプリケーションが `DeleteBlob` 操作を使用してアカウントから BLOB を削除する場合、データにこれらの変更は含まれません。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>Microsoft.Storage.BlobRenamed イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>Microsoft.Storage.DirectoryCreated イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>Microsoft.Storage.DirectoryRenamed イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>Microsoft.Storage.DirectoryDeleted イベント

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>イベントのプロパティ

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| topic | string | イベント ソースの完全なリソース パス。 このフィールドは書き込み可能ではありません。 この値は Event Grid によって指定されます。 |
| subject | string | 発行元が定義したイベントの対象のパス。 |
| eventType | string | このイベント ソース用に登録されたイベントの種類のいずれか。 |
| eventTime | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| id | string | イベントの一意識別子。 |
| data | object | Blob Storage イベントのデータ。 |
| dataVersion | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| metadataVersion | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| api | string | イベントのトリガーとなった操作。 |
| clientRequestId | string | ストレージ API 操作に対するクライアントで提供された要求 ID です。 この ID は、ログの "client-request-id" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、クライアント要求で "x-ms-client-request-id" ヘッダーを使って提供できます。 「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。 |
| requestId | string | ストレージ API 操作に対するサービスで生成された要求 ID です。 ログの "request-id-header" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、開始 API 呼び出しから "x-ms-request-id" ヘッダーで返されます。 「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。 |
| eTag | string | この値を使用することで、条件に応じて操作を実行することができます。 |
| contentType | string | BLOB に関して指定されたコンテンツの種類。 |
| contentLength | integer | BLOB のサイズ (単位: バイト)。 |
| blobType | string | BLOB の種類。 有効な値は "BlockBlob" または "PageBlob" です。 |
| contentOffset | number | イベントをトリガーしたアプリケーションがファイルへの書き込みを完了した時点で実行される書き込み操作のバイト単位のオフセット。 <br>階層型名前空間を持つ BLOB ストレージ アカウントでトリガーされるイベントに対してのみ表示されます。|
| destinationUrl |string | 操作の完了後に存在するファイルの url。 たとえば、ファイルの名前が変更された場合、`destinationUrl` プロパティには新しいファイル名の url が含まれています。 <br>階層型名前空間を持つ BLOB ストレージ アカウントでトリガーされるイベントに対してのみ表示されます。|
| sourceUrl |string | 操作の前に存在するファイルの url。 たとえば、ファイルの名前が変更された場合、`sourceUrl` には名前変更操作が行われる前の元のファイル名の url が含まれています。 <br>階層型名前空間を持つ BLOB ストレージ アカウントでトリガーされるイベントに対してのみ表示されます。 |
| url | string | BLOB へのパス。 <br>クライアントが BLOB REST API を使用する場合、url は *\<storage-account-name\>.blob.core.windows.net/\<container-name\>/\<file-name\>* という構造になります。 <br>クライアントが Data Lake Storage REST API を使用する場合、url は *\<storage-account-name\>.dfs.core.windows.net/\<file-system-name\>/\<file-name\>* という構造になります。
|
| recursive| string| すべての子ディレクトリに対して操作を実行する場合は `True`、それ以外の場合は `False`。 <br>階層型名前空間を持つ BLOB ストレージ アカウントでトリガーされるイベントに対してのみ表示されます。 |
| sequencer | string | 特定の BLOB 名に対するイベントの論理シーケンスを表す非透過的な文字列値です。  ユーザーは、標準的な文字列比較を使って、同じ BLOB 名での 2 つのイベントの相対的な順序を理解できます。 |
| storageDiagnostics | object | Azure Storage サービスによって追加されることがある診断データです。 含まれる場合、イベントのコンシューマーは無視する必要があります。 |

|プロパティ|Type|説明|
 |-------------------|------------------------|-----------------------------------------------------------------------|

## <a name="next-steps"></a>次の手順

* Azure Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Azure Event Grid サブスクリプションの作成の詳細については、[Event Grid サブスクリプション スキーマ](subscription-creation-schema.md)に関する記事を参照してください。
* Blob Storage のイベントを処理する方法の概要については、[Azure CLI での Blob Storage イベントのルーティング](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)に関するページを参照してください。 
