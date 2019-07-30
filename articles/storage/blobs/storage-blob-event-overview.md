---
title: Azure Blob Storage イベントへの対応 | Microsoft Docs
description: Blob Storage のイベントをサブスクライブするには、Azure Event Grid を使用します。
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444683"
---
# <a name="reacting-to-blob-storage-events"></a>Blob Storage イベントへの対応

Azure Storage イベントをアプリケーションで使用すると、最新のサーバーレスなアーキテクチャを使用して BLOB の作成、削除などのイベントに対応できます。 複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。

イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を通して、Azure Functions、Azure Logic Apps、またはユーザー独自のカスタム HTTP リスナーにプッシュされ、料金は使用したものだけで済みます。

Blob Storage イベントは Event Grid Service に確実に送信されます。Event Grid Service では豊富な再試行ポリシーおよび配信不能メッセージ配信を使用してご利用のアプリケーションに信頼性の高い配信サービスが提供されます。

Blob Storage イベントの一般的なシナリオとしては、画像やビデオの処理、検索インデックスの作成、ファイル指向のワークフローなどがあります。 非同期のファイル アップロードは、イベントに最適です。 変更の頻度が低くても、即時の応答性が必要なシナリオでは、イベント ベースのアーキテクチャは特に効果的です。

これを今すぐ試す場合は、次のクイック スタートの記事のいずれかを参照してください。

|使うツール:    |参照する記事: |
|--|-|
|Azure Portal    |[クイック スタート:Azure portal で Blob Storage のイベントを Web エンドポイントにルーティングする](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[クイック スタート:PowerShell を使用してストレージ イベントを Web エンドポイントにルーティングする](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Powershell    |[クイック スタート:Azure CLI を使用してストレージ イベントを Web エンドポイントにルーティングする](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>イベント モデル

Event Grid は、[イベント サブスクリプション](../../event-grid/concepts.md#event-subscriptions)を使用して、イベント メッセージをサブスクライバーにルーティングします。 この図は、イベント発行元、イベント サブスクリプション、およびイベント ハンドラーの間の関係を示しています。

![Event Grid モデル](./media/storage-blob-event-overview/event-grid-functional-model.png)

最初に、イベントにエンドポイントをサブスクライブします。 その後、イベントがトリガーされると、Event Grid サービスにより、そのイベントに関するデータがエンドポイントに送信されます。

[Blob Storage イベントのスキーマ](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関する記事を参照し、以下を確認してください。

> [!div class="checklist"]
> * Blob Storage イベントの全一覧と各イベントがトリガーされる方法。
> * これらの各イベントに対して Event Grid が送信するデータの例。
> * データに表示される各キー値ペアの目的。

## <a name="filtering-events"></a>イベントのフィルター処理

BLOB イベントのサブスクリプションは、イベントの種類に基づき、作成または削除されたオブジェクトのコンテナー名と BLOB 名で、フィルター処理することができます。  イベント サブスクリプションの[作成](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)中、または[作成後](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest)に、イベント サブスクリプションをフィルター処理することができます。 Event Grid のサブジェクト フィルターは、"次で始まる" と "次で終わる" という一致条件によって動作し、サブジェクトが一致するイベントはサブスクライバーに配信されます。

フィルターを適用する方法の詳細については、「[Event Grid のイベントのフィルター処理](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)」をご覧ください。

Blob Storage イベントのサブジェクトには次の形式が使われます。

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

ストレージ アカウントのすべてのイベントと一致させるには、サブジェクト フィルターを空のままにできます。

プレフィックスを共有する一連のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。

```
/blobServices/default/containers/containerprefix
```

特定のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。

```
/blobServices/default/containers/containername/
```

BLOB 名プレフィックスを共有する特定のコンテナーで作成された BLOB からのイベントと一致させるには、次のような `subjectBeginsWith` フィルターを使います。

```
/blobServices/default/containers/containername/blobs/blobprefix
```

BLOB サフィックスを共有する特定のコンテナーで作成された BLOB からのイベントと一致させるには、".log" や ".jpg" などの `subjectEndsWith` フィルターを使います。 詳しくは、「[Event Grid の概念](../../event-grid/concepts.md#event-subscriptions)」をご覧ください。

## <a name="practices-for-consuming-events"></a>イベントの使用に関する手法

Blob Storage イベントを処理するアプリケーションは、いくつかの推奨される手法に従う必要があります。
> [!div class="checklist"]
> * 同じイベント ハンドラーにイベントをルーティングするように複数のサブスクリプションが構成されている可能性があるので、イベントが特定のソースからのものであると思い込まず、メッセージのトピックを調べて、想定しているストレージ アカウントからのものであることを確認することが重要です。
> * 同様に、受信するすべてのイベントが予期した種類のものであると想定してはならず、イベントの種類が処理できるものであることを確認する必要があります。
> * メッセージは順不同で到着したり、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。  また、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。
> * blobType フィールドを使って、BLOB に対して許可されている操作の種類と、BLOB にアクセスするために使う必要があるクライアント ライブラリの種類を確認します。 有効な値は `BlockBlob` または `PageBlob` です。 
> * `CloudBlockBlob` および `CloudAppendBlob` コンストラクターでは、url フィールドを使って BLOB にアクセスします。
> * わからないフィールドは無視します。 この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
> * ブロック BLOB が完全にコミットされた場合に限り **Microsoft.Storage.BlobCreated** イベントがトリガーされるようにするには、`CopyBlob`、`PutBlob`、`PutBlockList`、または `FlushWithClose` REST API 呼び出しのイベントをフィルター処理します。 データがブロック BLOB に完全にコミットされた後でのみ、これらの API 呼び出しによって **Microsoft.Storage.BlobCreated** イベントがトリガーされます。 フィルターの作成方法の詳細については、「[Event Grid のイベントのフィルター処理](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)」をご覧ください。


## <a name="next-steps"></a>次の手順

Event Grid の詳細について理解し、Blob Storage イベントを試してみてください。

- [Event Grid について](../../event-grid/overview.md)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](storage-blob-event-quickstart.md)
