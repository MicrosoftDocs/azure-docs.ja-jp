---
title: Azure Event Grid による配信と再試行
description: Azure Event Grid がイベントをどのように配信し、未配信メッセージをどのように処理するかについて説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991208"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid によるメッセージの配信と再試行

この記事では、配信が確認されないときに Azure Event Grid がイベントをどのように処理するかについて説明します。

Event Grid は、持続性のある配信を提供します。 各サブスクリプションに対して、最低 1 回は各メッセージを配信します。 イベントは、直ちに各サブスクリプションの登録済みのエンドポイントに送信されます。 エンドポイントがイベントの受信を確認しない場合、Event Grid はイベントの配信を再試行します。

現時点では、Event Grid はサブスクライバーへ各イベントを個別に送信します。 サブスクライバーは、イベント 1 つが格納された配列を受け取ります。

## <a name="retry-schedule-and-duration"></a>再試行のスケジュールと期間

Event Grid は、イベント配信に対して指数バックオフ再試行ポリシーを使用します。 エンドポイントが応答しないか、またはエラー コードを返す場合、Event Grid では次善策として、次のスケジュールで配信を再試行します。

1. 10 秒
1. 30 秒
1. 1 分
1. 5 分
1. 10 分
1. 30 分
1. 1 時間
1. 最大24 時間で、1時間ごと

Event Grid では、すべての再試行の手順に小規模なランダム化を追加します。また、エンドポイントが一貫して正常ではない、長期間ダウンしている、または圧迫されていることがわかっている場合は、状況に応じて、特定の再試行をスキップできます。

決定論的な動作の場合は、[サブスクリプション再試行ポリシー](manage-event-delivery.md)の中で有効なイベント タイムと最大配信試行回数を設定します。

Event Grid では、既定で 24 時間内に配信されないすべてのイベントが有効期限切れになります。 イベント サブスクリプションの作成時には、[再試行ポリシーをカスタマイズする](manage-event-delivery.md)ことができます。 配信の最大試行回数 (既定値は 30) と、イベントの有効期限 (既定値は 1,440 分) を指定します。

## <a name="dead-letter-events"></a>配信不能イベント

Event Grid がイベントを配信できない場合は、配信不能イベントをストレージ アカウントに送信できます。 このプロセスは配信不能処理と呼ばれます。 既定では、Event Grid は配信不能処理を有効にしません。 この処理を有効にするには、イベント サブスクリプションの作成時に、配信不能イベントを保持するようにストレージ アカウントを指定する必要があります。 このストレージ アカウントからイベントをプルして配信を解決します。

Event Grid は、そのすべての再試行を試行し終わると、配信不能の場所にイベントを送信します。 Event Grid は、400 (正しくない要求) または 413 (要求のエンティティが大きすぎます) の応答コードを受信した場合、直ちにそのイベントを配信不能エンドポイントに送信します。 これらの応答コードは、イベントの配信が決して成功しないことを示します。

イベントの配信を最後に試してから配信不能メッセージがその宛先に届くまで、5 分間の遅延があります。 この遅延の目的は、BLOB ストレージの操作数を減らすことにあります。 配信不能の場所が 4 時間にわたって使用できない場合、そのイベントは破棄されます。

配信不能の場所を設定するには、コンテナーを含むストレージ アカウントが必要です。 イベント サブスクリプションの作成時に、このコンテナーのエンドポイントを指定します。 エンドポイントの形式は次のとおりです。`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

イベントが配信不能の場所に送信されたら通知を受け取るようにすることもできます。 Event Grid を使用して配信不能イベントに応答するには、配信不能 Blob ストレージ用の[イベント サブスクリプションを作成](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)します。 配信不能 Blob ストレージが配信不能イベントを受信するたびに、Event Grid はハンドラーに通知します。 ハンドラーは、配信不能イベントを調整するためのアクションで応答します。

配信不能の場所の設定の例については、「[配信不能と再試行に関する方針](manage-event-delivery.md)」を参照してください。

## <a name="message-delivery-status"></a>メッセージの配信状態

Event Grid は、HTTP 応答コードを使用してイベントの受信を確認します。 

### <a name="success-codes"></a>成功コード

次の HTTP 応答コードは、イベントが Webhook に正常に配信されていることを示します。 Event Grid は、配信が完了したとみなします。

- 200 OK
- 202 受理されました

### <a name="failure-codes"></a>エラー コード

次の HTTP 応答コードは、イベントの配信が失敗したことを示します。

- 400 Bad Request
- 401 権限がありません
- 404 見つかりません
- 408 要求がタイムアウトしました
- 413 要求のエンティティが大きすぎます
- 414 URI が長すぎます
- 429 Too Many Requests
- 500 内部サーバー エラー
- 503 サービス利用不可
- 504 ゲートウェイ タイムアウト

## <a name="next-steps"></a>次の手順

* イベント配信のステータスを表示するには、[Event Grid によるメッセージの配信の監視](monitor-event-delivery.md) に関する記事をご覧ください。
* イベント配信オプションをカスタマイズするには、「[配信不能と再試行に関する方針](manage-event-delivery.md)」を参照してください。