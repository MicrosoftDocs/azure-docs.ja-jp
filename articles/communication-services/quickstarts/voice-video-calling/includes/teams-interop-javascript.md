---
title: クイックスタート - Web アプリから Teams 会議に参加する
description: このチュートリアルでは、JavaScript 用 Azure Communication Services 通話クライアント ライブラリを使用して Teams 会議に参加する方法について説明します
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 43f6241f0da0ecc9c68cf60e9f1a0482509374f3
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488002"
---
このクイックスタートでは、JavaScript 用 Azure Communication Services 通話クライアント ライブラリを使用して Teams 会議に参加する方法について説明します。

## <a name="prerequisites"></a>前提条件

- 実際に動作する [Communication Services 通話 Web アプリ](../getting-started-with-calling.md)。
- [Teams のデプロイ](/deployoffice/teams-install)。


## <a name="add-the-teams-ui-controls"></a>Teams の UI コントロールを追加する

index.html のコードを次のスニペットに置き換えます。
テキスト ボックスは、Teams の会議のコンテキストを入力する目的で、また、ボタンは、指定した会議に参加する目的で使用されます。

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>Teams の UI コントロールを有効にする

client.js ファイルの内容を次のスニペットに置き換えます。

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Teams 会議のリンクを取得する

Teams 会議のリンクは、Graph API を使用して取得できます。 この点については、[Graph のドキュメント](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)で詳しく説明されています。
Communication Services 通話 SDK は、Teams 会議のフル リンクを受け入れます。 このリンクは、`onlineMeeting` リソースの一部として返され、[`joinWebUrl` プロパティ](/graph/api/resources/onlinemeeting?view=graph-rest-beta)からアクセスできます。必要な会議情報は、Teams 会議の招待自体に含まれる **[会議に参加]** の URL から取得することもできます。

## <a name="run-the-code"></a>コードの実行

Webpack ユーザーは、`webpack-dev-server` を使用してアプリをビルドし、実行することができます。 ローカルの Web サーバーにアプリケーション ホストをバンドルするには、次のコマンドを実行します。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="完成した JavaScript アプリケーションのスクリーンショット。":::

Communication Services アプリケーション内から Teams の会議に参加するには、Teams のコンテキストをテキスト ボックスに挿入し、 *[Join Teams Meeting]\(Teams の会議に参加\)* を押します。
