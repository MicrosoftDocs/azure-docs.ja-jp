---
title: クイックスタート - Teams の会議に参加する
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: dd183e9088f24aa8b94955bc8ed2a68b4a7eb27c
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496179"
---
## <a name="joining-the-meeting-chat"></a>会議チャットへの参加 

Teams の相互運用性が有効になると、Communication Services ユーザーは、呼び出し元のクライアント ライブラリを使用して外部ユーザーとして Teams の通話に参加できます。 通話に参加すると、会議チャットにも参加者として追加されます。会議チャットでは、通話の他のユーザーとメッセージを送受信できます。 ユーザーは、通話に参加する前に送信されたチャット メッセージにはアクセスできません。 会議に参加してチャットを開始するには、次の手順に従います。

## <a name="install-the-chat-packages"></a>チャット パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の必要な Communication Services クライアント ライブラリをインストールします。

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

index.html のコードを次のスニペットに置き換えます。
ページの上部にあるテキスト ボックスは、Teams 会議のコンテキストと会議スレッド ID を入力するために使用されます。 [Join Teams Meeting]\(Teams の会議に参加\) ボタンは、指定した会議に参加するために使用します。
ページの下部にチャットのポップアップが表示されます。 これは、会議スレッドでメッセージを送信するために使用でき、ACS ユーザーがメンバーである間にそのスレッドで送信されたメッセージがリアルタイムで表示されます。

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

client.js ファイルの内容を次のスニペットに置き換えます。

スニペット内で、以下を置き換えます 
- `SECRET CONNECTION STRING` を Communication Services の接続文字列に 
- `ENDPOINT URL` を Communication Services のエンドポイント URL に

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Communication Services ユーザーの Teams 会議チャット スレッドを取得する

Teams 会議のリンクとチャットは Graph API を使用して取得できます。詳細については、[Graphのドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)を参照してください。 Communication Services 通話 SDK は、Teams 会議のフル リンクを受け入れます。 このリンクは `onlineMeeting` リソースの一部として返され、[Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) 使用して [`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta)の下でアクセスできます。また、`threadId` を取得することもできます。 応答には、`threadID` を含む `chatInfo` オブジェクトがあります。 

必要な会議情報とスレッド ID は、Teams 会議の招待状自体に含まれている **[Join Meeting]\(会議に参加\)** URL から取得することもできます。
Teams の会議リンクは、`https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` のようになっています。 `threadId` は、このリンクの `meeting_chat_thread_id` 部分になります。 使用する前に、`meeting_chat_thread_id` がエスケープされていないことを確認してください。 これは、`19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2` という形式になっている必要があります。


## <a name="run-the-code"></a>コードの実行

Webpack ユーザーは、`webpack-dev-server` を使用してアプリをビルドし、実行することができます。 ローカルの Web サーバーにアプリケーション ホストをバンドルするには、次のコマンドを実行します。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

Teams 会議のリンクとスレッド ID を各テキスト ボックスに挿入します。 Teams 会議に参加するには、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。 ACS ユーザーが会議への参加を許可されたら、Communication Services アプリケーション内からチャットを行うことができます。 チャットを開始するには、ページの下部にあるボックスに移動します。

> [!NOTE] 
> 現在、Teams との相互運用性のシナリオでは、メッセージの送受信と編集のみがサポートされています。 入力インジケーターや、Communication Services ユーザーが Teams 会議に他のユーザーを追加または削除するなどの機能は、まだサポートされていません。  
