---
title: Azure Communication Services でのチャットの概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services のチャットの概念について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 22ffa59339d85af80a7398e581862d19eef17222
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490655"
---
# <a name="chat-concepts"></a>チャットに関する概念

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Azure Communication Services の Chat クライアント ライブラリを使用して、アプリケーションにリアルタイムのテキスト チャットを追加できます。 このページには、チャットの主な概念と機能がまとめられています。

特定のクライアント ライブラリの言語と機能の詳細については、「[Communication Services の Chat クライアント ライブラリの概要](./sdk-features.md)」を参照してください。

## <a name="chat-overview"></a>チャットの概要

チャット会話は、チャット スレッド内で行われます。 チャット スレッドには、多くのメッセージと多くのユーザーを含めることができます。 各メッセージは 1 つのスレッドに属しており、1 人のユーザーは 1 つまたは複数のスレッドに参加できます。

チャット スレッド内の各ユーザーは、メンバーと呼ばれます。 チャット スレッドには最大 250 名のメンバーを含めることができます。 スレッド メンバーだけが、チャット スレッドでメッセージを送受信したり、メンバーを追加/削除したりできます。 許可されている最大メッセージ サイズは約 28 KB です。 1 つのチャット スレッド内のすべてのメッセージを取得するには、`List/Get Messages` 操作を使用します。 Communication Services では、チャット スレッドまたはメッセージに対して削除操作を実行するまで、またはチャット スレッドにメンバーがいなくなった時点で孤立し、削除対象として処理されるまで、チャット履歴を保存します。

20 名を超えるメンバーを持つチャット スレッドの場合は、開封確認メッセージと入力インジケーター機能が無効になっています。

## <a name="chat-architecture"></a>チャットのアーキテクチャ

チャットのアーキテクチャには、次の2つの主要な部分があります。1) 信頼されたサービスと 2) クライアント アプリケーション。

:::image type="content" source="../../media/chat-architecture.png" alt-text="Communication Services のチャットのアーキテクチャを示す図。":::

 - **信頼されたサービス:** チャット セッションを適切に管理するには、リソース接続文字列を使用して Communication Services に接続できるようにするサービスが必要です。 このサービスで、チャット スレッドの作成、スレッド メンバーシップの管理、ユーザーへのアクセス トークンの提供を行います。 アクセス トークンの詳細については、[アクセス トークン](../../quickstarts/access-tokens.md)のクイックスタートを参照してください。

 - **クライアント アプリ:** クライアント アプリケーションが信頼されたサービスに接続してアクセス トークンを受け取り、これを使って Communication Services に直接接続します。 この接続が確立されると、クライアント アプリがメッセージを送受信できるようになります。

信頼済みのサービス レベルを使用してアクセス トークンを生成することをお勧めします。 このシナリオでは、ユーザーの作成と管理、さらに、そのトークンの発行をサーバー側が担います。

## <a name="message-types"></a>メッセージの種類

Communication Services のチャットでは、ユーザーが生成したメッセージだけでなく、**スレッド アクティビティ** と呼ばれるシステム生成メッセージも共有されます。 スレッド アクティビティは、チャット スレッドが更新されたときに生成されます。 チャット スレッドで `List Messages` または `Get Messages` を呼び出すと、結果には、ユーザーが生成したテキスト メッセージとシステム メッセージが時系列順に含まれます。 これは、メンバーが追加または削除された日時や、チャット スレッドのトピックが更新された日時を特定するのに役立ちます。 サポートされているメッセージの種類は次のとおりです。

 - `Text`:チャット会話の一部としてユーザーが作成して送信するプレーンテキスト メッセージ。
 - `RichText/HTML`:書式設定されたテキスト メッセージ。 Communication Services ユーザーは現在、RichText メッセージを送信できないことに注意してください。 このメッセージ型は、Teams の相互運用シナリオで、Teams ユーザーから Communication Services ユーザーに送信されるメッセージでサポートされています。

 - `ThreadActivity/ParticipantAdded`: 1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。 次に例を示します。

```
{
            "id": "1613589626560",
            "type": "participantAdded",
            "sequenceId": "7",
            "version": "1613589626560",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:26Z"
        }
```

- `ThreadActivity/ParticipantRemoved`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。 次に例を示します。

```
{
            "id": "1613589627603",
            "type": "participantRemoved",
            "sequenceId": "8",
            "version": "1613589627603",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:27Z"
        }
```

- `ThreadActivity/TopicUpdate`: スレッド トピックが更新されたことを示すシステム メッセージ。 次に例を示します。

```
{
            "id": "1613589623037",
            "type": "topicUpdated",
            "sequenceId": "2",
            "version": "1613589623037",
            "content":
            {
                "topic": "New topic",
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:23Z"
        }
```

## <a name="real-time-signaling"></a>リアルタイムの通知

Chat JavaScript クライアント ライブラリには、リアルタイムの通知が含まれています。 これにより、クライアントは API をポーリングしなくても、チャット スレッドに対するリアルタイムの更新と受信メッセージをリッスンできます。 使用できるイベントは以下のとおりです。

 - `ChatMessageReceived` - ユーザーがメンバーになっているチャット スレッドに新しいメッセージが送信されたとき。 このイベントは、前のトピックで説明した自動生成されたシステム メッセージには送信されません。
 - `ChatMessageEdited` -ユーザーがメンバーになっているチャット スレッド内でメッセージが編集されたとき。
 - `ChatMessageDeleted` -ユーザーがメンバーになっているチャット スレッド内でメッセージが削除されたとき。
 - `TypingIndicatorReceived` -ユーザーがメンバーになっているチャット スレッド内で別のメンバーがメッセージを入力しているとき。
 - `ReadReceiptReceived` -ユーザーがチャット スレッドで送信したメッセージを別のユーザーが読んだとき。

## <a name="chat-events"></a>チャット イベント

リアルタイムの通知を使用すると、ユーザーはリアルタイムでチャットできます。 Azure Event Grid を使用して、チャット関連のイベントをサブスクライブできます。 詳細については、[イベント処理の概念](../event-handling.md)に関するトピックをご覧ください。

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Cognitive Services を Chat クライアント ライブラリと共に使用してインテリジェントな機能を有効にする

[Azure Cognitive API](../../../cognitive-services/index.yml) を Chat クライアント ライブラリと共に使用して、インテリジェントな機能をアプリケーションに追加できます。 たとえば、次のように操作できます。

- ユーザーがさまざまな言語で互いにチャットできるようにする。
- サポート エージェントがチケットの優先順位を設定するために、顧客から受信した問題から否定的なセンチメントを検出する。
- 受信メッセージのキー検出とエンティティ認識を分析し、メッセージの内容に基づいてアプリ内でユーザーに関連情報を表示する。

これを実現する 1 つの方法として、信頼されたサービスをチャット スレッドのメンバーとして機能させることができます。 たとえば、言語の翻訳を有効にするとします。 このサービスは、他のメンバーが交換するメッセージをリッスンし [1]、Cognitive API を呼び出してその内容を目的の言語に翻訳し [2、3]、翻訳結果をチャット スレッドのメッセージとして送信する [4] という役割を担います。

これにより、メッセージ履歴には元のメッセージと翻訳されたメッセージの両方が含まれます。 クライアント アプリケーションに、元のメッセージまたは翻訳されたメッセージを表示するロジックを追加できます。 Cognitive API を使用してテキストを別の言語に翻訳する方法については、[こちらのクイックスタート](../../../cognitive-services/translator/quickstart-translator.md)をご覧ください。

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Communication Services と対話する Cognitive Services を示す図。":::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [チャットを開始する](../../quickstarts/chat/get-started.md)

次のドキュメントもご覧ください。

- [Chat クライアント ライブラリ](sdk-features.md)について理解する
