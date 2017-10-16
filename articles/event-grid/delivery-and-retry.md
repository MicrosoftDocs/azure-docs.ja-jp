---
title: "Azure Event Grid による配信と再試行"
description: "Azure Event Grid がイベントをどのように配信し、未配信メッセージをどのように処理するかについて説明します。"
services: event-grid
author: djrosanova
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/11/2017
ms.author: darosa
ms.openlocfilehash: e0f8afdfd84ea3c0c061459c27da285f6ae8957e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid によるメッセージの配信と再試行 

この記事では、配信が確認されないときに Azure Event Grid がイベントをどのように処理するかについて説明します。

Event Grid は、持続性のある配信を提供します。 各サブスクリプションに対して、最低 1 回は各メッセージを配信します。 イベントは、各サブスクリプションの登録済みの Webhook にすぐに送信されます。 1 回目の配信で、Webhook がイベントの受信を 60 秒以内に確認しなかった場合、Event Grid はそのイベントの配信を再試行します。

## <a name="message-delivery-status"></a>メッセージの配信状態

Event Grid は、HTTP 応答コードを使用してイベントの受信を確認します。 

### <a name="success-codes"></a>成功コード

次の HTTP 応答コードは、イベントが Webhook に正常に配信されていることを示します。 Event Grid は、配信が完了したとみなします。

- 200 OK
- 202 受理されました

### <a name="failure-codes"></a>エラー コード

次の HTTP 応答コードは、イベントの配信が失敗したことを示します。 Event Grid は、イベントの送信を再試行します。 

- 400 無効な要求です
- 401 権限がありません
- 404 見つかりません
- 408 要求がタイムアウトしました
- 414 URI が長すぎます
- 500 内部サーバー エラー
- 503 サービス利用不可
- 504 ゲートウェイ タイムアウト

その他の応答コードや応答がないことはエラーを示します。 Event Grid は配信を再試行します。 

## <a name="retry-intervals"></a>再試行間隔

Event Grid は、イベント配信に対して指数バックオフ再試行ポリシーを使用します。 Webhook が応答しない、またはエラー コードが返らない場合、Event Grid は、次のスケジュールで配信を再試行します。

1. 10 秒
2. 30 秒
3. 1 分
4. 5 分
5. 10 分
6. 30 分
7. 1 時間

Event Grid は、すべての再試行間隔に小さなランダム化を追加します。

## <a name="retry-duration"></a>再試行期間

プレビュー中の Azure Event Grid では、2 時間内に配信されないすべてのイベントは有効期限切れになっています。 一般公開の前に、この時間は 24 時間に延長される予定です。 

## <a name="next-steps"></a>次のステップ

* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Grid の使用をすぐに開始するには、[Azure Event Grid でのカスタム イベントの作成とルーティング](custom-event-quickstart.md)に関する記事を参照してください。