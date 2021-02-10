---
title: クイックスタート - Teams の会議に参加する
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932297"
---
## <a name="join-the-meeting-chat"></a>会議チャットに参加する 

Teams の相互運用性が有効になると、Communication Services ユーザーは、呼び出し元のクライアント ライブラリを使用してゲスト ユーザーとして Teams の通話に参加することができます。 通話に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、通話の他のユーザーとメッセージを送受信できます。 ユーザーは、通話に参加する前に送信されたチャット メッセージにはアクセスできません。 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services ユーザーの Teams 会議チャット スレッドを取得する

まず、会議チャット スレッドの `ChatThreadClient` のインスタンスを作成します。 会議リンクを解析するか、Graph API と会議 ID を使用して、スレッド ID を取得します。 

- Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 スレッド ID は、このリンクの `meeting_chat_thread_id` の部分です。 
- 会議 ID を保持している場合は、[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) を使用してスレッド ID を取得できます。 [GET API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta) 応答には、`threadID` を含む `chatInfo` オブジェクトがあります。 

チャット スレッド ID を取得したら、JavaScript チャット クライアント ライブラリを使用してチャット スレッド クライアントを取得できます。 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient` を使用すると、チャット スレッド内のメンバーの一覧表示、チャット履歴の取得、メッセージの送信を行うことができます。  

## <a name="send-and-receive-messages"></a>メッセージを送受信する  

会議チャットにメッセージを送信するには、`SendMessage` を使用します。 受信メッセージの受信については、このシナリオではリアルタイムの通知がまだ有効になっていないため、`chatMessageReceived` のようなイベントをサブスクライブする機能はサポートされていません。 最新のメッセージを取得するには、 `ListMessages` API をポーリングします。 相互運用性のシナリオについては、`ListMessages` API によって、3 つの新しいメッセージ型を返す機能がサポートされるようになりました。
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

メッセージ型の詳細については、[こちら](../../../concepts/chat/concepts.md)を参照してください。 

**注** - 現在、Teams との相互運用性のシナリオでは、メッセージの送受信のみがサポートされています。 入力インジケーターや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。  

