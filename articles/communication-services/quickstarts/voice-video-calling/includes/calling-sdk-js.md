---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 41a5f05f016a876894949c35a6610b2b49f9eb9d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488116"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- 省略可能:[アプリケーションへの通話の追加の概要](../getting-started-with-calling.md)に関するクイックスタートを完了します

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

> [!NOTE]
> このドキュメントでは、呼び出し元のクライアント ライブラリのバージョン 1.0.0-beta.6 を使用します。

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話および共通クライアント ライブラリをインストールします。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                             | 説明                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。                                                                       |
| CallAgent                        | CallAgent は、通話を開始および管理するために使用します。                                                                                            |
| DeviceManager                    | DeviceManager は、メディア デバイスを管理するために使用します                                                                                           |
| AzureCommunicationTokenCredential | AzureCommunicationTokenCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationTokenCredential インターフェイスが実装されます。 |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>CallClient を初期化し、Callclient を作成して、DeviceManager にアクセスする

新しい `CallClient` インスタンスをインスタンス化します。 Logger インスタンスなどのカスタム オプションを使用して構成できます。
`CallClient` がインスタンス化されたら、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出すことによって、`CallAgent` インスタンスを作成できます。 これにより、`CallAgent` インスタンス オブジェクトが非同期に返されます。
`createCallAgent` メソッドが引数として受け取る `CommunicationTokenCredential` では、[ユーザー アクセス トークン](../../access-tokens.md)が受け入れられます。
`DeviceManager` にアクセスするには、先に callAgent インスタンスを作成する必要があります。 その後、`CallClient` インスタンスで `getDeviceManager` メソッドを使用して、DeviceManager を取得できます。

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>発信通話を行う

通話を作成して開始するには、CallAgent で API の 1 つを使用し、Communication Services ID クライアント ライブラリを使用して作成したユーザーを指定する必要があります。

通話の作成と開始は同期的に行います。 Call インスタンスを使用すると、通話イベントをサブスクライブできます。

## <a name="place-a-call"></a>通話を行う

### <a name="place-a-11-call-to-a-user-or-pstn"></a>ユーザーまたは PSTN と 1:1 の通話を行う
別の Communication Services ユーザーへの通話を行うには、`callAgent` の `startCall` メソッドを呼び出し、[Communication Services ID ライブラリで作成した](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)呼び出し先の CommunicationUserIdentifier を渡します。

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

PSTN の通話を行うには `callAgent` で `startCall` メソッドを呼び出し、呼び出し先の PhoneNumberIdentifier を渡します。
PSTN 通話を許可するように Communication Services リソースを構成する必要があります。
PSTN 番号に電話をかけるときは、代替の発信者 ID を指定する必要があります。 代替の発信者 ID は、PSTN 通話の呼び出し元を識別する電話番号 (E.164 標準に基づく) を示します。 たとえば、PSTN 通話に代替の発信者 ID を指定すると、通話を着信したときに、その電話番号が呼び出し先に表示されます。

> [!WARNING]
> PSTN 通話は、現在プライベート プレビュー段階にあります。 アクセスするには、[早期導入者プログラムに申し込みます](https://aka.ms/ACS-EarlyAdopter)。
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーと PSTN で 1:n の通話を行う
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う
> [!WARNING]
> 現在、発信ローカル動画ストリームは 1 つしか使用できません。
動画通話を行うには、deviceManager の `getCameras()` API を使用してローカル カメラを列挙する必要があります。
目的のカメラを選択したら、それを使用して `LocalVideoStream` インスタンスを構築し、それを `startCall` メソッドへの `localVideoStream` 配列内の項目として、`videoOptions` に渡します。
通話が接続されると、選択したカメラから他の参加者へのビデオ ストリームの送信が自動的に開始されます。 これは、Call.Accept() ビデオ オプションと CallAgent.join() ビデオ オプションにも適用されます。
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>グループ通話に参加する
新しいグループ通話を始めるか、進行中のグループ通話に参加するには、"join" メソッドを使用して、`groupId` プロパティを含むオブジェクトを渡します。 値には GUID を指定する必要があります。
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Teams 会議に参加する
Teams 会議に参加するには、'join' メソッドを使用し、会議リンクまたは会議の座標を渡します。
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>着信呼び出しの受信

ログインしている ID が着信呼び出しを受信すると、`CallAgent` インスタンスで `incomingCall` イベントが生成されます。 このイベントをリッスンするには、次の方法でサブスクライブします。

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall` イベントにより、呼び出しを受け入れるか、拒否することができる `IncomingCall` のインスタンスが提供されます。


## <a name="call-management"></a>通話の管理

通話の間に、通話のプロパティにアクセスしてさまざまな操作を実行し、動画やオーディオに関連する設定を管理できます。

### <a name="call-properties"></a>通話のプロパティ
* この通話の一意の ID (文字列) を取得します。
```js

const callId: string = call.id;

```

* 通話の他の参加者について知るには、`call` インスタンスの `remoteParticipant` コレクションを調べます。 配列には、リスト `RemoteParticipant` オブジェクトが含まれています
```js
const remoteParticipants = call.remoteParticipants;
```

* 通話が着信の場合の呼び出し元の識別子です。 識別子は `CommunicationIdentifier` 型の 1 つです
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
これにより、通話の現在の状態を表す文字列が返されます。
* "None" - 通話の初期状態です
* "Incoming" - 通話を着信中であり、受諾または拒否する必要があることを示します
* "Connecting" - 通話が発信または受諾された後の初期遷移状態です
* "Ringing" - 発信通話の場合 - リモート参加者に対して通話が発信されていることを示します。そちら側ではこれは "Incoming" です
* "EarlyMedia" - 通話が接続される前に、アナウンスが再生されている状態を示します
* "Connected" - 通話は接続されています
* "LocalHold" - 通話はローカル参加者によって保留にされており、ローカル エンドポイントとリモート参加者の間でメディアは送信されていません
* "RemoteHold" - 通話はリモート参加者によって保留にされており、ローカル エンドポイントとリモート参加者の間でメディアは送信されていません
* "Disconnecting" - 通話は、"Disconnected" 状態になる前の移行状態です
* "Disconnected" - 通話の最終状態です
  * ネットワーク接続が失われると、状態は約 2 分後に "Disconnected" になります。

* 特定の通話が終了した理由を確認するには、`callEndReason` プロパティを調べます。
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 現在の通話が着信通話か発信通話かを確認するには、`direction` プロパティを調べます。これにより `CallDirection` が返されます。
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  現在マイクがミュートされているかどうかを確認するには、`muted` プロパティを調べます。これは `Boolean` を返します。
```js

const muted = call.isMicrophoneMuted;

```

* 画面共有ストリームが特定のエンドポイントから送信されているかどうかを確認するには、`isScreenSharingOn` プロパティを調べます。これは `Boolean` を返します。
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* アクティブなビデオ ストリームを調べるには、`localVideoStreams` コレクションを確認します。これには、`LocalVideoStream` オブジェクトが含まれています
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>通話終了イベント

`Call` インスタンスにより、通話が終了したときに `callEnded` イベントが生成されます。 このイベントをリッスンするには、次の方法でサブスクライブします。

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用できます。

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する


動画を開始するには、`deviceManager` オブジェクトで `getCameras` メソッドを使用して、カメラを列挙する必要があります。 次に、目的のカメラを引数として `startVideo` メソッドに渡して `LocalVideoStream` の新しいインスタンスを作成します。


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

動画の送信が正常に開始されると、通話インスタンスの `localVideoStreams` コレクションに `LocalVideoStream` インスタンスが追加されます。

```js

call.localVideoStreams[0] === localVideoStream;

```

ローカル動画を停止するには、`localVideoStreams` コレクションで使用可能な `localVideoStream` インスタンスを渡します。

```js

await call.stopVideo(localVideoStream);

```

`localVideoStream` インスタンスで `switchSource` を呼び出すことにより、動画の送信中に別のカメラ デバイスに切り替えることができます。

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>リモート参加者の管理

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者の一覧を取得する
`remoteParticipants` コレクションで、特定の通話におけるリモート参加者の一覧が返されます。

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>リモート参加者のプロパティ
リモート参加者には、プロパティのセットとコレクションが関連付けられています
#### <a name="communicationidentifier"></a>CommunicationIdentifier
このリモート参加者の識別子を取得します。
ID は "CommunicationIdentifier" 型の 1 つです。
```js
const identifier = remoteParticipant.identifier;
```
'CommunicationIdentifier' 型のいずれかになります。
  * { communicationUserId: '<ACS_USER_ID'> } - ACS ユーザーを表すオブジェクト
  * { phoneNumber: '<E.164>' } - E.164 形式で電話番号を表すオブジェクト
  * { microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" } - チーム ユーザーを表すオブジェクト

#### <a name="state"></a>状態
このリモート参加者の状態を取得します。
```js

const state = remoteParticipant.state;
```
状態は次のいずれかです
* "Idle" - 初期状態です
* "Connecting" - 参加者が通話に接続している間の遷移状態です
* 'Ringing' - 参加者が発信しています
* "Connected" - 参加者は通話に接続されています
* "Hold" - 参加者は保留中です
* "EarlyMedia" - 参加者が通話に接続される前に、アナウンスが再生されています
* "Disconnected" - 最終状態 - 参加者は通話から切断されました
  * リモート参加者がネットワーク接続を失った場合、リモート参加者の状態は約 2 分後に "Disconnected" になります。

#### <a name="call-end-reason"></a>通話終了の理由
参加者が通話を終了した理由を確認するには、`callEndReason` プロパティを調べます。
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>ミュートされている
このリモート参加者がミュートされているかどうかを確認するには、`isMuted` プロパティを調べます。これは `Boolean` を返します
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>話し中である
このリモート参加者が話しているかどうかを確認するには、`isSpeaking` プロパティを調べます。これは `Boolean` を返します
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>ビデオ ストリーム
特定の参加者がこの通話で送信しているすべてのビデオ ストリームを調べるには、`videoStreams` コレクションを確認します。これには、`RemoteVideoStream` オブジェクトが含まれています
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者を追加するには (ユーザーまたは電話番号のいずれか)、`addParticipant` を呼び出します。
"Identifier" 型のいずれかを指定します。
これにより、リモート参加者インスタンスが同期的に返されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>通話から参加者を削除する

通話から参加者を削除するには (ユーザーまたは電話番号のいずれか)、`removeParticipant` を呼び出します。
"Identifier" 型のいずれかを渡す必要があります。これは、参加者が通話から削除されると、非同期的に解決されます。
参加者は、`remoteParticipants` コレクションからも削除されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者の動画ストリームと画面共有ストリームの一覧を取得するには、`videoStreams` コレクションを調べます。

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

`RemoteVideoStream` をレンダリングするには、`isAvailableChanged` イベントをサブスクライブする必要があります。
`isAvailable` プロパティが `true` に変更された場合、リモート参加者はストリームを送信しています。
それが発生したら、`Renderer` の新しいインスタンスを作成し、非同期 `createView` メソッドを使用して新しい `RendererView` インスタンスを作成します。  その後、任意の UI 要素に `view.target` をアタッチできます。
リモート ストリームの使用可能性が変わるたびに、レンダラー全体を破棄するか、特定の `RendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>リモート動画ストリームのプロパティ
リモート動画ストリームには次のプロパティがあります。

* `Id` - リモート ビデオ ストリームの ID です
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` - リモート動画ストリームのサイズ (幅と高さ)
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` - "Video" または "ScreenSharing" になります
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` - リモート参加者のエンドポイントでストリームをアクティブに送信されているかどうかを示します
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Renderer のメソッドとプロパティ

* 後でアプリケーションの UI にアタッチしてリモート動画ストリームをレンダリングできる `RendererView` インスタンスを作成します。
```js
renderer.createView()
```

* レンダラーと、それに関連付けられているすべての `RendererView` インスタンスを破棄します。
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView のメソッドとプロパティ
`RendererView` を作成するときに、`scalingMode` および `isMirrored` プロパティを指定できます。
スケーリング モードは "Stretch"、"Crop"、"Fit" です。`isMirrored` を指定すると、レンダリングされたストリームは垂直方向に反転されます。

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
特定の `RendererView` インスタンスには、レンダリング サーフェイスを表す `target` プロパティがあります。 これを、アプリケーションの UI にアタッチする必要があります。
```js
document.body.appendChild(rendererView.target);
```

後で `updateScalingMode` メソッドを呼び出すことによって、スケーリング モードを更新できます。
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>デバイス管理

`DeviceManager` を使用すると、オーディオと動画のストリームを送信する呼び出しで使用できるローカル デバイスを列挙できます。 また、ネイティブ ブラウザー API を使用してマイクやカメラにアクセスするために、ユーザーにアクセス許可を要求することもできます。

`callClient.getDeviceManager()` メソッドを呼び出すことによって `deviceManager` にアクセスできます。
> [!WARNING]
> 現在、DeviceManager にアクセスするには、`callAgent` オブジェクトを最初にインスタンス化する必要があります

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>ローカル デバイスを列挙する

ローカル デバイスにアクセスするには、デバイス マネージャーで列挙メソッドを使用します。 列挙は非同期アクションです。

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>既定のマイクとスピーカーを設定する

デバイス マネージャーを使用すると、通話の開始時に使用される既定のデバイスを設定できます。
クライアントの既定値が設定されていない場合、Communication Services は OS の既定値にフォールバックします。

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>ローカル カメラのプレビュー

`DeviceManager` と `Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>カメラとマイクへのアクセス許可を要求する

次のようにして、カメラとマイクのアクセス許可を付与するようにユーザーに要求します。

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
これは、`audio` および `video` のアクセス許可が付与されたかどうかを示すオブジェクトで非同期的に解決されます。
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>通話記録の管理

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

通話記録は、コア `Call` API の拡張機能です。 まず、記録機能 API オブジェクトを取得する必要があります。

```js
const callRecordingApi = call.api(Features.Recording);
```

次に、通話が記録されているかどうかを確認するには、`callRecordingApi` の `isRecordingActive` プロパティを調べます。これにより `Boolean` が返されます。

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

また、変更の記録をサブスクライブすることもできます。

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>通話転送の管理

通話転送は、コア `Call` API の拡張機能です。 まず、転送機能 API オブジェクトを取得する必要があります。

```js
const callTransferApi = call.api(Features.Transfer);
```

通話転送には、*転送者*、*転送元*、および *転送先* の 3 者が関与します。 転送フローは次のように動作します。

1. *転送者* と *転送元* の間に接続済みの通話が既に存在します
2. *転送者* が通話を転送することを決定します (*転送元* -> *転送先*)
3. *転送者* が `transfer` API を呼び出します
4. *転送元* は、`transferRequested` イベント経由で、*転送先* への転送要求を `accept` するか、`reject` するかを決定します。
5. *転送先* は *転送元* が転送要求を `accept` した場合にのみ着信通話を受信します

### <a name="transfer-terminology"></a>転送の用語

- 転送者 - 転送要求を開始するユーザー
- 転送元 - 転送者によって、転送先に転送されるユーザー
- 転送先 - 転送先のターゲットとなるユーザー

現在の通話を転送するには、`transfer` 同期 API を使用できます。 `transfer` は、`disableForwardingAndUnanswered` フラグを設定できる省略可能な `TransferCallOptions` を受け取ります。

- `disableForwardingAndUnanswered` = false - *転送先* が転送通話に応答しない場合、*転送先* の転送および未回答の設定に従います
- `disableForwardingAndUnanswered` = true - *転送先* が転送通話に応答しない場合、転送試行は終了します

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

転送により、`transferStateChanged` イベントと `transferRequested` イベントにサブスクライブできます。 `transferRequsted` イベントが `call` インスタンスから生成され、`transferStateChanged` イベントと転送 `state` と `error` が `transfer` インスタンスから生成されます

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

転送元は、転送者によって `transferRequested` イベントで開始された転送要求を、`transferRequestedEventArgs` の `accept()` または `reject()` を介して、受け入れるか拒否することができます。 `transferRequestedEventArgs` で、`targetParticipant` 情報、`accept` メソッド、`reject` メソッドにアクセスできます。

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>イベント モデル
現在の値を検査し、将来の値の更新イベントをサブスクライブする必要があります。

### <a name="properties"></a>プロパティ

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>コレクション
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
