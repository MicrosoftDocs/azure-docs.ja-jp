---
title: "Azure Blob Storage のイベントへの対応 (プレビュー) | Microsoft Docs"
description: "Blob Storage のイベントをサブスクライブするには、Azure Event Grid を使います。"
services: storage,event-grid
keywords: 
author: cbrooksmsft
ms.author: cbrooks
ms.date: 08/25/2017
ms.topic: article
ms.service: storage
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: b9b117bdeb62f5ebb2e4e3fbfe71572068927082
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="reacting-to-blob-storage-events-preview"></a>Blob Storage のイベントへの対応

Azure Blob Storage のイベントをアプリケーションで使うと、最新のサーバーレス アーキテクチャを使って、BLOB の作成と削除に対応できます。複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。  イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通して、[Azure Functions](https://azure.microsoft.com/services/functions/)、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)、またはユーザー独自のカスタム HTTP リスナーにプッシュされ、料金は使ったものだけで済みます。

Blob Storage イベントの一般的なシナリオとしては、画像やビデオの処理、検索インデックスの作成、ファイル指向のワークフローなどがあります。  非同期のファイル アップロードは、イベントに最適です。  変更の頻度が低くても、即時の応答性が必要なシナリオでは、イベント ベースのアーキテクチャは特に効果的です。

![Event Grid モデル](./media/storage-blob-event-overview/event-grid-functional-model.png)

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="join-the-preview"></a>プレビューに参加する
Blob Storage イベントはプレビューできます。  ユーザーは、サブスクリプションに対して次のコマンドを発行することで、プレビューへの参加を要求できます。
```azurecli-interactive
az provider register --namespace  Microsoft.EventGrid
az feature register --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
使用可能な容量がある場合は、サブスクリプションがプレビュー プログラムに追加されます。  要求の状態は、次のコマンドを発行することで監視できます。
```azurecli-interactive
az feature show --name storageEventSubscriptions --namespace Microsoft.EventGrid
```
登録の状態が "Registered" に変化すると、プレビュー プログラムへの参加が許可され、"*米国中西部*" の場所でのアカウントに対する Blob Storage イベントにサブスクライブできます。  簡単な例については、「[Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](storage-blob-event-quickstart.md)」をご覧ください。

## <a name="blob-storage-accounts"></a>BLOB ストレージ アカウント
Blob Storage イベントは、[Blob Storage アカウント](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) で使用できます (汎用ストレージ アカウントでは使用できません)。  BLOB ストレージ アカウントとは、Azure Storage に BLOB (オブジェクト) として非構造化データを格納するための特殊なストレージ アカウントです。 Blob Storage アカウントは、汎用ストレージ アカウントと同様に、現在使われているすべての優れた耐久性、可用性、スケーラビリティ、およびパフォーマンス機能を共有します。たとえば、ブロック BLOB と追加 BLOB の 100% の API 整合性などです。 ブロックまたは追加 Blob Storage のみを必要とするアプリケーションでは、BLOB ストレージ アカウントを使用することをお勧めします。

## <a name="available-blob-storage-events"></a>使用できる Blob Storage イベント
Event Grid は、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使って、イベント メッセージをサブスクライバーにルーティングします。  Blob Storage のイベント サブスクリプションは、2 種類のイベントを含むことができます。  

> |イベント名|Description|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|`PutBlob`、`PutBlockList`、または `CopyBlob` 操作によって BLOB が作成または置換されると生成されます|
> |`Microsoft.Storage.BlobDeleted`|`DeleteBlob` 操作によって BLOB が削除されると生成されます|

## <a name="event-schema"></a>イベント スキーマ
Blob Storage イベントには、データの変更に対応するために必要なすべての情報が含まれます。  Blob Storage イベントは eventType プロパティが "Microsoft.Storage" で始まっていることで識別できます。  
Event Grid イベントのプロパティの使用法について詳しくは、「[Event Grid イベント スキーマ](../../event-grid/event-schema.md)」をご覧ください。  

> |プロパティ|型|説明|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|string|イベントを生成したストレージ アカウントの完全な Azure Resource Manager ID です。|
> |subject|string|イベントの対象であるオブジェクトへの相対リソース パスです。Azure RBAC のストレージ アカウント、サービス、およびコンテナーの記述に使うのと同じ拡張 Azure Resource Manager 形式を使います。  この形式には、大文字と小文字が区別される BLOB 名が含まれます。|
> |eventTime|string|イベントが生成された日時です (ISO 8601 形式)。|
> |eventType|string|"Microsoft.Storage.BlobCreated" または "Microsoft.Storage.BlobDeleted" です。|
> |ID|string|このイベントの一意識別子です。|
> |data|オブジェクト|Blob Storage 固有のイベント データのコレクションです。|
> |data.contentType|string|BLOB のコンテンツの種類です。BLOB から Content-Type ヘッダーで返されます。|
> |data.contentLength|number|BLOB のサイズです (バイト数を表す整数)。BLOB から Content-Length ヘッダーで返されます。  BlobCreated イベントでは送信されますが、BlobDeleted では送信されません。|
> |data.url|string|イベントの対象であるオブジェクトの URL です。|
> |data.eTag|string|このイベントが発生したときのオブジェクトの etag です。  BlobDeleted イベントでは使用できません。|
> |data.api|string|このイベントをトリガーした API 操作の名前です。  BlobCreated イベントの場合、この値は "PutBlob"、"PutBlockList"、または "CopyBlob" です。  BlobDeleted イベントの場合、この値は "DeleteBlob" です。  これらの値は、Azure Storage 診断ログに記録されるものと同じ API 名です。  「[Storage Analytics によって記録される操作やステータス メッセージ](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)」をご覧ください。|
> |data.sequencer|string|特定の BLOB 名に対するイベントの論理シーケンスを表す非透過的な文字列値です。  ユーザーは、標準的な文字列比較を使って、同じ BLOB 名での 2 つのイベントの相対的な順序を理解できます。|
> |data.requestId|string|ストレージ API 操作に対するサービスで生成された要求 ID です。  ログの "request-id-header" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、開始 API 呼び出しから "x-ms-request-id" ヘッダーで返されます。 「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。|
> |data.clientRequestId|string|ストレージ API 操作に対するクライアントで提供された要求 ID です。  ログの "client-request-id" フィールドを使って Azure Storage 診断ログに関連付けるために使うことができ、クライアント要求で "x-ms-client-request-id" ヘッダーを使って提供できます。 「[Storage Analytics のログの形式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)」をご覧ください。|
> |data.storageDiagnostics|オブジェクト|Azure Storage サービスによって追加されることがある診断データです。  含まれる場合、イベントのコンシューマーは無視する必要があります。|

BlobCreated イベントの例を次に示します。
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  }
}]

```

詳しくは、[Blob Storage のイベント スキーマに関する記事](../../event-grid/event-schema.md#azure-blob-storage)をご覧ください。

## <a name="filtering-events"></a>イベントのフィルター処理
BLOB イベントのサブスクリプションは、イベントの種類に基づき、作成または削除されたオブジェクトのコンテナー名と BLOB 名で、フィルター処理することができます。  Event Grid のサブジェクト フィルターは "で始まる" および "で終わる" の一致に基づいて動作し、サブジェクトが一致するイベントはサブスクライバーに配信されます。
Blob Storage イベントのサブジェクトには次の形式が使われます。
```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```
ストレージ アカウントのすべてのイベントと一致させるには、サブジェクト フィルターを空のままにできます。

プレフィックスを共有する一連のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。
```json
/blobServices/default/containers/containerprefix
```
特定のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。
```json
/blobServices/default/containers/containername/
```
BLOB 名プレフィックスを共有する特定のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。
```json
/blobServices/default/containers/containername/blobs/blobprefix
```

BLOB サフィックスを共有する特定のコンテナーで作成された BLOB からのイベントと一致させるには、".log" や ".jpg" などの `subjectEndsWith` フィルターを使います。

詳しくは、「[Event Grid の概念](../../event-grid/concepts.md#filters)」をご覧ください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法
Blob Storage イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。
> [!div class="checklist"]
> * 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションが構成されている可能性があるので、イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているストレージ アカウントからのものであることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * メッセージは順不同で到着したり、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。  また、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。
> * blobType フィールドを使って、BLOB に対して許可されている操作の種類と、BLOB にアクセスするために使う必要があるクライアント ライブラリの種類を確認します。
> * `CloudBlockBlob` および `CloudAppendBlob` コンストラクターでは、url フィールドを使って BLOB にアクセスします。
> * わからないフィールドは無視します。  この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。


## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Blob Storage イベントを試してみてください。

- [Event Grid について](../../event-grid/overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](storage-blob-event-quickstart.md)
