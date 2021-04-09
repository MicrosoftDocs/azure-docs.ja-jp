---
title: クイックスタート - アプリにビデオ通話を追加する (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、Azure Communication Services を使用してアプリにビデオ通話機能を追加する方法について説明します。
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 82f4d9028fa94d4df0ff089fda213d64e13d56ec
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487872"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>クイックスタート - アプリに 1 対 1 のビデオ通話を追加する (JavaScript)

## <a name="download-code"></a>コードをダウンロードする

このクイックスタートの最終的なコードは [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) にあります

## <a name="prerequisites"></a>前提条件
- アクティブなサブスクリプションが含まれる Azure アカウントを取得します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/en/)。アクティブ LTS およびメンテナンス LTS のバージョン (8.11.1 および 10.14.1)。
- アクティブな Communication Services リソースを作成します。 [Communication Services のリソースを作成する](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp)。
- 通話クライアントのインスタンスを作成するためのユーザー アクセス トークンを作成します。 [ユーザー アクセス トークンを作成および管理する方法に関するページを参照してください](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-csharp)。

## <a name="setting-up"></a>設定
### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する
ターミナルまたはコマンド ウィンドウを開き、自分のアプリ用に新しいディレクトリを作成し、そこに移動します。
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>パッケージをインストールする
`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話クライアント ライブラリをインストールします。

このクイックスタートでは、Azure Communication の通話クライアント ライブラリ`1.0.0.beta-6` を使用しました。 

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

このクイックスタートでは、webpack を使用してアプリケーション資産をバンドルします。 次のコマンドを実行して `webpack`、`webpack-cli`、`webpack-dev-server` の 3 つの npm パッケージをインストールし、開発の依存関係として `package.json` 内でリスト化します。

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
自分のプロジェクトのルート ディレクトリに、`index.html` ファイルを作成します。 このファイルを使用して、ユーザーが 1 対 1 のビデオ通話を開始できるようにする基本的なレイアウトを構成します。

コードは次のとおりです。
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

このクイックスタートのアプリケーション ロジックを格納するために、`client.js` という名前のファイルを自分のプロジェクトのルート ディレクトリに作成します。 通話クライアントをインポートし、DOM 要素に対する参照を取得するために、次のコードを追加します。

```JavaScript
import { CallClient, CallAgent, Renderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前      | 説明 | 
| :---        |    :----   |
| CallClient  | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。      |
| CallAgent  | CallAgent は、通話を開始および管理するために使用します。        |
| DeviceManager | DeviceManager は、メディア デバイスの管理に使用します。    |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationTokenCredential インターフェイスが実装されます。        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>クライアントを認証し、DeviceManager にアクセスする

<USER_ACCESS_TOKEN> の部分は、リソースの有効なユーザー アクセス トークンに置き換える必要があります。 まだトークンを入手していない場合は、ユーザー アクセス トークンに関するドキュメントを参照してください。 CallClient を使用し、CallAgent インスタンスを CommunicationUserCredential で初期化します。これにより、通話の発信と着信が可能になります。 DeviceManager にアクセスするには、先に callAgent インスタンスを作成しておく必要があります。 その後、`CallClient` インスタンスで `getDeviceManager` メソッドを使用すると、`DeviceManager` を取得できます。

`client.js` に次のコードを追加します。

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>1 名のユーザーに対して 1 対 1 のビデオ通話を発信する

`callButton` がクリックされたときに通話を開始するイベント リスナーを追加します。

まずは、deviceManager の getCameraList API を使用して、ローカル カメラを列挙する必要があります。 このクイックスタートでは、コレクションの最初のカメラを使用します。 カメラを選択すると、LocalVideoStream インスタンスが作成され、videoOptions の中で localVideoStream 配列の項目として call メソッドに渡されます。 通話がつながると、相手方に対するビデオ ストリームの送信が自動的に始まります。 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
`LocalVideoStream` のレンダリングには、`Renderer` の新しいインスタンスを作成したうえで、非同期の `createView` メソッドを使用して新しい RendererView インスタンスを作成する必要があります。 その後、任意の UI 要素に `view.target` をアタッチできます。 

```JavaScript
async function localVideoView() {
    rendererLocal = new Renderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
リモート参加者は全員、通話インスタンスの `remoteParticipants` コレクションを通じて利用できます。 追加されたリモート参加者をサブスクライブするには、現在の通話のリモート参加者をサブスクライブし、イベント `remoteParticipantsUpdated` をリッスンする必要があります。

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.remoteParticipants.forEach( p => {
        subscribeToRemoteParticipant(p);
    })
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToRemoteParticipant(p);
        })
    });   
}
```
現在の通話の `remoteParticipants` コレクションをサブスクライブし、`videoStreams` コレクションを検査すると、各参加者のストリームを一覧表示できます。 また、追加されたリモート参加者の処理には、remoteParticipantsUpdated イベントのサブスクライブも必要です。 

```JavaScript
function subscribeToRemoteParticipant(remoteParticipant) {
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
}
```
`remoteVideoStream` をレンダリングするには、`isAvailableChanged` イベントをサブスクライブする必要があります。 `isAvailable` プロパティが `true` に変更された場合、リモート参加者はストリームを送信しています。 リモート ストリームの使用可否が変わるたびに、`Renderer` 全体を破棄するか、特定の `RendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
`RemoteVideoStream` のレンダリングには、`Renderer` の新しいインスタンスを作成したうえで、非同期の `createView` メソッドを使用して新しい `RendererView` インスタンスを作成する必要があります。 その後、任意の UI 要素に `view.target` をアタッチできます。 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new Renderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>着信した通話に出る
通話の着信を処理するためには、`callAgent` の `incomingCall` イベントをリッスンする必要があります。 通話を着信したときは、ローカル カメラの列挙のほか、相手方にビデオ ストリームを送信する `LocalVideoStream` インスタンスの作成が必要です。 また、リモート ビデオ ストリームを処理するための `remoteParticipants` にサブスクライブする必要もあります。 通話に出たり、通話を拒否したりするには、`incomingCall` インスタンスを使用します。 

通話の着信を処理する実装は、`init()` に配置します。 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);   
});
```
## <a name="end-the-current-call"></a>現在の通話を終了する
`hangUpButton` がクリックされたときに現在の通話を終了するイベント リスナーを追加します。
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>通話の更新をサブスクライブする
リモート参加者が通話を終了した場合にビデオ レンダラーとトグル ボタンの状態を破棄できるよう、イベントをサブスクライブする必要があります。 

`callsUpdated` イベントをサブスクライブする実装は、init () に配置します。 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>通話中にビデオを開始および終了する
[stop video]\(ビデオの停止\) ボタンを対象とするイベント リスナーを追加し、`localVideoStream` のレンダラーを破棄することによって、通話中にビデオを停止できます。 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
[start video]\(ビデオの開始\) ボタンを対象とするイベント リスナーを追加すると、現在の通話中に停止したビデオの使用を再開できます。 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>コードの実行
アプリをビルドして実行するには、`webpack-dev-server` を使用します。 次のコマンドを実行して、ローカルの Web サーバーにアプリケーション ホストをバンドルします。

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
ブラウザーを開き、http://localhost:8080/ に移動します。 次のように表示されます。

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 対 1 のビデオ通話のページ":::

1 対 1 のビデオ通話を発信するときは、テキスト フィールドにユーザー ID を指定し、[start call]\(通話の開始\) ボタンをクリックします。 

## <a name="sample-code"></a>サンプル コード
サンプル アプリは [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) からダウンロードできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
詳細については、次の記事を参照してください。
- Microsoft の [Web 通話のサンプル](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)を確認する
- [通話クライアント ライブラリ機能](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)について学習する
- [通話のしくみ](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)の詳細について確認する
