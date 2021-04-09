---
title: Azure Blob Storage イベントへの対応 | Microsoft Docs
description: Blob Storage イベントをサブスクライブして対応するには、Azure Event Grid を使用します。 イベント モデル、イベントのフィルター処理、およびイベントの使用に関する手法について説明します。
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: dineshm
ms.openlocfilehash: f07c249e3b7cb54283959df410d51ca18998f2cf
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181518"
---
# <a name="reacting-to-blob-storage-events"></a>Blob Storage イベントへの対応

Azure Storage イベントをアプリケーションで使用すると、BLOB の作成、削除などのイベントに対応できます。 複雑なコードや、高価で非効率的なポーリング サービスは必要ありません。 最もよい点は、使用した分だけ支払うということです。

Blob Storage イベントは、[Azure Event Grid](https://azure.microsoft.com/services/event-grid/) を使用して、Azure Functions、Azure Logic Apps などのサブスクライバー、または独自の HTTP リスナーにもプッシュされます。 Event Grid は、豊富な再試行ポリシーおよび配信不能を使用して、ご利用のアプリケーションに信頼性の高いイベント配信を提供します。

Blob Storage でサポートされるイベントの完全な一覧については、[Blob Storage イベントのスキーマ](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関する記事を参照してください。

Blob Storage イベントの一般的なシナリオとしては、画像やビデオの処理、検索インデックスの作成、ファイル指向のワークフローなどがあります。 非同期のファイル アップロードは、イベントに最適です。 変更の頻度が低くても、即時の応答性が必要なシナリオでは、イベント ベースのアーキテクチャは特に効果的です。

Blob Storage イベントを試す場合は、次のクイック スタートの記事のいずれかを参照してください。

|使うツール:    |参照する記事: |
|--|-|
|Azure portal    |[クイック スタート: Azure portal で Blob Storage のイベントを Web エンドポイントにルーティングする](../../event-grid/blob-event-quickstart-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[クイック スタート: PowerShell を使用してストレージ イベントを Web エンドポイントにルーティングする](./storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[クイック スタート: Azure CLI を使用してストレージ イベントを Web エンドポイントにルーティングする](./storage-blob-event-quickstart.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Azure 関数を使用した BLOB ストレージ イベントへの対応の詳細な例については、以下の記事をご覧ください。

- [チュートリアル:Azure Data Lake Storage Gen2 イベントを使用して Databricks Delta テーブルを更新する](data-lake-storage-events.md)
- [チュートリアル:Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](../../event-grid/resize-images-on-storage-blob-upload-event.md?tabs=dotnet)

>[!NOTE]
> イベントの統合をサポートしているのは、**StorageV2 (汎用 v2)** 、**BlockBlobStorage**、および **BlobStorage** の種類のストレージ アカウントだけです。 **Storage (汎用 v1)** では、Event Grid との統合はサポート "*されていません*"。

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

BLOB イベントは、イベントの種類、コンテナー名、作成または削除されたオブジェクトの名前によって[フィルター処理できます](/cli/azure/eventgrid/event-subscription)。 Event Grid のフィルターは、サブジェクトの先頭または末尾を照合するため、件名が一致したイベントはサブスクライバーに送られます。

フィルターを適用する方法の詳細については、「[Event Grid のイベントのフィルター処理](../../event-grid/how-to-filter-events.md)」をご覧ください。

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
> * メッセージは、少し遅れて到着する可能性があるので、etag フィールドを使って、オブジェクトに関する情報がまだ最新の状態かどうかを確認します。 etag フィールドの使用方法については、「[BLOB ストレージ内でのコンカレンシーの管理](./concurrency-manage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)」を参照してください。
> * メッセージは順不同で到着する可能性があるので、sequencer フィールドを使って、特定のオブジェクトに対するイベントの順序を確認します。 sequencer フィールドは、特定の BLOB 名に対するイベントの論理シーケンスを表す文字列値です。 標準的な文字列比較を使って、同じ BLOB 名での 2 つのイベントの相対的な順序を理解できます。
> * Storage イベントにより、サブスクライバーへの少なくとも 1 回の配信が保証され、すべてのメッセージが確実に出力されます。 ただし、バックエンド ノードとサービス間の再試行、またはサブスクリプションの可用性のために、メッセージの重複が発生することがあります。 メッセージの配信と再試行の詳細については、「[Event Grid によるメッセージの配信と再試行](../../event-grid/delivery-and-retry.md)」を参照してください。
> * blobType フィールドを使って、BLOB に対して許可されている操作の種類と、BLOB にアクセスするために使う必要があるクライアント ライブラリの種類を確認します。 有効な値は `BlockBlob` または `PageBlob` です。 
> * `CloudBlockBlob` および `CloudAppendBlob` コンストラクターでは、url フィールドを使って BLOB にアクセスします。
> * わからないフィールドは無視します。 この手法に従うと、将来追加されるかもしれない新しい機能に弾力的に対応できます。
> * ブロック BLOB が完全にコミットされた場合に限り **Microsoft.Storage.BlobCreated** イベントがトリガーされるようにするには、`CopyBlob`、`PutBlob`、`PutBlockList`、または `FlushWithClose` REST API 呼び出しのイベントをフィルター処理します。 データがブロック BLOB に完全にコミットされた後でのみ、これらの API 呼び出しによって **Microsoft.Storage.BlobCreated** イベントがトリガーされます。 フィルターの作成方法の詳細については、「[Event Grid のイベントのフィルター処理](../../event-grid/how-to-filter-events.md)」をご覧ください。


## <a name="next-steps"></a>次のステップ

Event Grid の詳細について理解し、Blob Storage イベントを試してみてください。

- [Event Grid について](../../event-grid/overview.md)
- [Blob Storage イベントのスキーマ](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Blob Storage のイベントをカスタム Web エンドポイントにルーティングする](storage-blob-event-quickstart.md)
