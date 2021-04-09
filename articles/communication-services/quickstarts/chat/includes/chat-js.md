---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 9f62f262e1baa70982e667379a9bf4357197ecb4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495472"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。
- [Node.js](https://nodejs.org/en/download/) アクティブ LTS およびメンテナンス LTS バージョン (8.11.1 および 10.14.1 を推奨) をインストールします。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイントを記録する** 必要があります。
- "*3 人*" の ACS ユーザーを作成し、それに対して [ユーザー アクセス トークン](../../access-tokens.md)を発行します。 スコープは必ず **chat** に設定し、**トークン文字列と userId 文字列をメモ** してください。 完全なデモでは、最初の 2 名の参加者でスレッドを作成し、3 人目の参加者をスレッドに追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-web-application"></a>新しい Web アプリケーションを作成する

まず、ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成して、そこに移動します。

```console
mkdir chat-quickstart && cd chat-quickstart
```

既定の設定で `npm init -y` を実行して、**package.json** ファイルを作成します。

```console
npm init -y
```

### <a name="install-the-packages"></a>パッケージのインストール

`npm install` コマンドを使用して、以下の JavaScript 用の Communication Services クライアント ライブラリをインストールします。

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して、webpack、webpack-cli、および webpack-dev-server npm パッケージをインストールし、**package.json** 内の開発依存関係として表示します。

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

自分のプロジェクトのルート ディレクトリに、**index.html** ファイルを作成します。 このファイルをテンプレートとして使い、JavaScript 用 Azure Communication チャット クライアント ライブラリを使用してチャット機能を追加します。

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

このクイックスタートのアプリケーション ロジックを格納するために、**client.js** という名前のファイルを自分のプロジェクトのルート ディレクトリに作成します。

### <a name="create-a-chat-client"></a>チャット クライアントを作成する

Web アプリにチャット クライアントを作成するには、Communications Service **エンドポイント** と、前提条件の手順で生成された **アクセス トークン** を使用します。

ユーザーのアクセス トークンを使用することで、Azure Communication Services に対して直接認証を行うクライアント アプリケーションを作成できます。 このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しません。 チャット アーキテクチャの詳細については、[チャットの概念](../../../concepts/chat/concepts.md)、アクセス トークンの詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

**client.js** 内では、下のコードのエンドポイントとアクセス トークンを使用し、JavaScript 用 Azure Communication チャット クライアント ライブラリを使用してチャット機能を追加します。

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- **endpointUrl** は、Communication Services リソースのエンドポイントに置き換えます。まだご覧になっていない場合は、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。
- **userAccessToken** は、発行したトークンに置き換えます。


### <a name="run-the-code"></a>コードの実行

アプリをビルドして実行するには、`webpack-dev-server` を使用します。 次のコマンドを実行して、ローカルの Web サーバーにアプリケーション ホストをバンドルします。
```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
ブラウザーを開き、http://localhost:8080/ に移動します。
ブラウザー内の開発者ツール コンソールには、次のように表示されます。

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>オブジェクト モデル
JavaScript 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認、チャット イベントのサブスクライブを行います。 |


## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

チャット スレッドは、`createThread` メソッドを使用して作成します。

スレッド要求は、`createThreadRequest` を使用して記述します。

- このチャットにトピックを指定するには、`topic` を使用します。 チャット スレッドの作成後に、`UpdateThread` 関数を使用してトピックを更新することができます。
- チャット スレッドに追加する参加者をリストアップするには、`participants` を使用します。

解決されると、`createChatThread` メソッドから `CreateChatThreadResult` が返されます。 このモデルには `chatThread` プロパティが含まれており、そこから、新しく作成されたスレッドの `id` にアクセスすることができます。 その後、`id` を使用して `ChatThreadClient` のインスタンスを取得できます。 さらに、`ChatThreadClient` を使用すると、メッセージの送信や参加者の一覧表示など、スレッド内で操作を実行できます。

```JavaScript
async function createChatThread() {
    let createThreadRequest = {
        topic: 'Preparation for London conference',
        participants: [{
                    id: { communicationUserId: '<USER_ID_FOR_JACK>' },
                    displayName: 'Jack'
                }, {
                    id: { communicationUserId: '<USER_ID_FOR_GEETA>' },
                    displayName: 'Geeta'
                }]
    };
    let createChatThreadResult = await chatClient.createChatThread(createThreadRequest);
    let threadId = createChatThreadResult.chatThread.id;
    return threadId;
    }

createChatThread().then(async threadId => {
    console.log(`Thread created:${threadId}`);
    // PLACEHOLDERS
    // <CREATE CHAT THREAD CLIENT>
    // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
    // <SEND MESSAGE TO A CHAT THREAD>
    // <LIST MESSAGES IN A CHAT THREAD>
    // <ADD NEW PARTICIPANT TO THREAD>
    // <LIST PARTICIPANTS IN A THREAD>
    // <REMOVE PARTICIPANT FROM THREAD>
    });
```

**USER_ID_FOR_JACK** と **USER_ID_FOR_GEETA** は、ユーザーとトークンの作成 ([ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照) で取得したユーザー ID に置き換えてください。

ブラウザー タブを最新の情報に更新すると、コンソールに以下が表示されます。
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

`getChatThreadClient` メソッドは、既に存在するスレッドの `chatThreadClient` を返します。 これは、作成したスレッドに対し、参加者の追加、メッセージの送信などの操作を実行する場合に使用できます。threadId は、既存のチャット スレッドの一意の ID です。

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
**client.js** の `<CREATE CHAT THREAD CLIENT>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認すると、以下が表示されます。
```console
Chat Thread client for threadId: <threadId>
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

threadId で識別されるスレッドにメッセージを送信するには、`sendMessage` メソッドを使用します。

メッセージ要求は、`sendMessageRequest` を使用して記述します。

- チャット メッセージの内容は、`content` を使用して設定します。

操作の省略可能なパラメーターを記述するには、`sendMessageOptions` を使用します。

- 送信者の表示名を指定するには、`senderDisplayName` を使用します。
- 'text' や 'html' などのメッセージの種類を指定するには、`type` を使用します。

`SendChatMessageResult` は、メッセージの送信から返された応答です。ここには ID (メッセージの一意の ID) が含まれています。

```JavaScript
let sendMessageRequest =
{
    content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
    senderDisplayName : 'Jack',
    type: 'text'
};
let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
let messageId = sendChatMessageResult.id;
```

**client.js** の `<SEND MESSAGE TO A CHAT THREAD>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認します。
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>チャット スレッドからチャット メッセージを受信する

リアルタイム シグナリングを使用すると、新しい受信メッセージをサブスクライブしてリッスンし、それに応じてメモリ内の現在のメッセージを更新することができます。 Azure Communication Services は、[サブスクライブ可能な一連のイベント](../../../concepts/chat/concepts.md#real-time-signaling)をサポートしています。

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    // your code here
});

```
**client.js** の `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` コメントをこのコードで置き換えます。
ブラウザー タブを最新の情報に更新すると、コンソールに "`Notification chatMessageReceived`" というメッセージが表示されます。

チャット メッセージは、特定の間隔で `listMessages` メソッドをポーリングすることによって取得することもできます。

```JavaScript

let pagedAsyncIterableIterator = await chatThreadClient.listMessages();
let nextMessage = await pagedAsyncIterableIterator.next();
    while (!nextMessage.done) {
        let chatMessage = nextMessage.value;
        console.log(`Message :${chatMessage.content}`);
        // your code here
        nextMessage = await pagedAsyncIterableIterator.next();
    }

```
**client.js** の `<LIST MESSAGES IN A CHAT THREAD>` コメントをこのコードで置き換えます。
タブを最新の情報に更新すると、このチャット スレッドで送信されたメッセージの一覧がコンソールに表示されます。


`listMessages` は、メッセージに対して `updateMessage` や `deleteMessage` を使用して行われた編集や削除を含む、最新バージョンのメッセージを返します。
削除されたメッセージについては、そのメッセージがいつ削除されたかを示す datetime 値が `chatMessage.deletedOn` から返されます。 編集されたメッセージについては、メッセージがいつ編集されたかを示す datetime が `chatMessage.editedOn` から返されます。 メッセージの最初の作成日時には、`chatMessage.createdOn` を使用してアクセスできます。これをメッセージの並べ替えに使用することができます。

`listMessages` は、`chatMessage.type` で識別できるさまざまな種類のメッセージを返します。 次の種類があります。

- `Text`:スレッド参加者によって送信された通常のチャット メッセージ。

- `ThreadActivity/TopicUpdate`:トピックが更新されたことを示すシステム メッセージ。

- `ThreadActivity/AddParticipant`:1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。

- `ThreadActivity/RemoveParticipant`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。

詳細については、「[メッセージの種類](../../../concepts/chat/concepts.md#message-types)」を参照してください。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

チャット スレッドの作成後、そこにユーザーを追加したり削除したりすることができます。 追加したユーザーには、チャット スレッドにメッセージを送信したり、他の参加者を追加、削除したりできるアクセス権が与えられます。

`addParticipants` メソッドを呼び出す前に必ず、そのユーザーの新しいアクセス トークンと ID を取得しておいてください。 チャット クライアントを初期化するためには、ユーザーにアクセス トークンが必要となります。

`addParticipantsRequest` は要求オブジェクトを表します。その中では、チャット スレッドに追加する参加者が `participants` を使用してリストアップされます。
- `id` (必須) は、チャット スレッドに追加するコミュニケーション識別子です。
- `displayName` (省略可) は、スレッド参加者の表示名です。
- `shareHistoryTime` (省略可) は、参加者との間でチャット履歴が共有される際の起点となる時刻です。 チャット スレッドの始めから履歴を共有する場合は、スレッドの作成日時と同じかそれ以前の任意の日付にこのプロパティを設定してください。 参加者が追加された時点よりも前の履歴は共有しない場合は、現在の日付に設定します。 履歴を部分的に共有するには、目的の日付に設定します。

```JavaScript

let addParticipantsRequest =
{
    participants: [
        {
            id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
            displayName: 'Jane'
        }
    ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
**NEW_PARTICIPANT_USER_ID** は、[新しいユーザー ID](../../access-tokens.md) に置き換えます。このコードは、**client.js** の `<ADD NEW PARTICIPANT TO THREAD>` コメントの代わりに追加してください。

## <a name="list-users-in-a-chat-thread"></a>チャット スレッド内のユーザーをリストアップする
```JavaScript
async function listParticipants() {
   let pagedAsyncIterableIterator = await chatThreadClient.listParticipants();
   let next = await pagedAsyncIterableIterator.next();
   while (!next.done) {
      let user = next.value;
      console.log(`User :${user.displayName}`);
      next = await pagedAsyncIterableIterator.next();
   }
}
await listParticipants();
```
**client.js** の `<LIST PARTICIPANTS IN A THREAD>` コメントをこのコードで置き換え、ブラウザー タブを最新の情報に更新してコンソールを確認すると、スレッド内のユーザーについての情報が表示されます。

## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

参加者の追加と同様、チャット スレッドから参加者を削除することもできます。 削除するには、追加した参加者の ID を追跡する必要があります。

`removeParticipant` メソッドを使用します。`participant` は、スレッドから削除するコミュニケーション ユーザーです。

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
**PARTICIPANT_ID** は、前の手順で使用したユーザー ID (<NEW_PARTICIPANT_USER_ID>) に置き換えてください。
**client.js** の `<REMOVE PARTICIPANT FROM THREAD>` コメントをこのコードで置き換えます。
