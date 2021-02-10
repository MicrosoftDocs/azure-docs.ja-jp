---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 2c894ea4bcb9701b8b65bcb9cd0b4b82c1898448
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500173"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- 省略可能:[アプリケーションへの通話の追加の概要](../getting-started-with-calling.md)に関するクイックスタートを完了します

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

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
| AzureCommunicationUserCredential | AzureCommunicationUserCredential クラスによって、CallAgent のインスタンス化に使用する CommunicationUserCredential インターフェイスが実装されます。 |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>CallClient を初期化し、Callclient を作成して、DeviceManager にアクセスする

新しい `CallClient` インスタンスをインスタンス化します。 Logger インスタンスなどのカスタム オプションを使用して構成できます。
`CallClient` がインスタンス化されたら、`CallClient` インスタンスで `createCallAgent` メソッドを呼び出すことによって、`CallAgent` インスタンスを作成できます。 これにより、`CallAgent` インスタンス オブジェクトが非同期に返されます。
`createCallAgent` メソッドが引数として受け取る `CommunicationUserCredential` では、[ユーザー アクセス トークン](../../access-tokens.md)が受け入れられます。
`DeviceManager` にアクセスするには、先に callAgent インスタンスを作成する必要があります。 その後、`CallClient` インスタンスで `getDeviceManager` メソッドを使用して、DeviceManager を取得できます。

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>発信通話を行う

通話を作成して開始するには、CallAgent で API の 1 つを使用し、Communication Services 管理クライアント ライブラリを使用して作成したユーザーを指定する必要があります。

通話の作成と開始は同期的に行います。 Call インスタンスを使用すると、通話イベントをサブスクライブできます。

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>ユーザーに対する 1:1 の通話、またはユーザーおよび PSTN と 1:n の通話を行う

別の Communication Services ユーザーへの通話を行うには、`callAgent` で `call` メソッドを呼び出し、[Communication Services 管理ライブラリで作成した](../../access-tokens.md) CommunicationUser を渡します。

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーおよび PSTN と 1:n の通話を行う

ユーザーおよび PSTN 番号と 1:n の通話を行うには、両方の通話先に対する CommunicationUser と電話番号を指定する必要があります。

PSTN 通話を許可するように Communication Services リソースを構成する必要があります。
```js

const userCallee = { communicationUserId: <ACS_USER_ID> };
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>ビデオ カメラを使用して 1:1 の通話を行う
> [!WARNING]
> 現在、発信ローカル動画ストリームは 1 つしか使用できません。
動画通話を行うには、deviceManager の `getCameraList` API を使用してローカル カメラを列挙する必要があります。
目的のカメラを選択したら、それを使用して `LocalVideoStream` インスタンスを構築し、それを `call` メソッドへの `localVideoStream` 配列内の項目として、`videoOptions` に渡します。
通話が接続されると、選択したカメラから他の参加者へのビデオ ストリームの送信が自動的に開始されます。

これは、Call.accept() ビデオ オプションと CallAgent.join() ビデオ オプションにも適用されます。
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="receiving-an-incoming-call"></a>着信呼び出しの受信
```js
callAgent.on('callsUpdated', e => {
    e.added.forEach(addedCall => {
        if(addedCall.isIncoming) {
        addedCall.accept();
    }
    });
})
```

### <a name="join-a-group-call"></a>グループ通話に参加する
新しいグループ通話を始めるか、進行中のグループ通話に参加するには、"join" メソッドを使用して、`groupId` プロパティを含むオブジェクトを渡します。 値には GUID を指定する必要があります。
```js

const locator = { groupId: <GUID>}
const call = callAgent.join(locator);

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

* 通話が着信の場合の呼び出し元の ID。 ID は `Identifier` 型の 1 つです
```js

const callerIdentity = call.callerIdentity;

```

* 通話の状態を取得します。
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
* "Hold" - 通話は保留になっており、ローカル エンドポイントとリモート参加者の間でメディアは送信されていません
* "Disconnecting" - 通話は、"Disconnected" 状態になる前の移行状態です
* "Disconnected" - 通話の最終状態です。
   * ネットワーク接続が失われると、状態は約 2 分後に "Disconnected" になります。


* 特定の通話が終了した理由を確認するには、`callEndReason` プロパティを調べます。
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* 現在の通話が着信通話かどうかを確認するには、`isIncoming` プロパティを調べます。これは `Boolean` を返します。
```js
const isIncoming = call.isIncoming;
```

* 通話が記録されているかどうかを確認するには、`isRecordingActive` プロパティを調べます。これは `Boolean` を返します。
```js
const isResordingActive = call.isRecordingActive;
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

### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用できます。

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する


動画を開始するには、`deviceManager` オブジェクトで `getCameraList` メソッドを使用して、カメラを列挙する必要があります。 次に、目的のカメラを引数として `startVideo` メソッドに渡して `LocalVideoStream` の新しいインスタンスを作成します。


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
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```
### <a name="faq"></a>よく寄せられる質問
 * ネットワーク接続が失われると、通話の状態は "Disconnected" に変化しますか。
    * はい。ネットワーク接続が 2 分以上失われると、通話は "Disconnected" 状態に変わり、終了します。

## <a name="remote-participants-management"></a>リモート参加者の管理

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者の一覧を取得する
`remoteParticipants` コレクションで、特定の通話におけるリモート参加者の一覧が返されます。

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>リモート参加者のプロパティ
リモート参加者には、プロパティのセットとコレクションが関連付けられています

* このリモート参加者の識別子を取得します。
ID は "Identifier" 型の 1 つです
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* このリモート参加者の状態を取得します。
```js

const state = remoteParticipant.state;
```
状態は次のいずれかです
* "Idle" - 初期状態です
* "Connecting" - 参加者が通話に接続している間の遷移状態です
* "Connected" - 参加者は通話に接続されています
* "Hold" - 参加者は保留中です
* "EarlyMedia" - 参加者が通話に接続される前に、アナウンスが再生されています
* "Disconnected" - 最終状態 - 参加者は通話から切断されました。
   * リモート参加者がネットワーク接続を失った場合、リモート参加者の状態は約 2 分後に "Disconnected" になります。

参加者が通話を終了した理由を確認するには、`callEndReason` プロパティを調べます。
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* このリモート参加者がミュートされているかどうかを確認するには、`isMuted` プロパティを調べます。これは `Boolean` を返します
```js
const isMuted = remoteParticipant.isMuted;
```

* このリモート参加者が話しているかどうかを確認するには、`isSpeaking` プロパティを調べます。これは `Boolean` を返します
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* 特定の参加者がこの通話で送信しているすべてのビデオ ストリームを調べるには、`videoStreams` コレクションを確認します。これには、`RemoteVideoStream` オブジェクトが含まれています
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者を追加するには (ユーザーまたは電話番号のいずれか)、`addParticipant` を呼び出します。
"Identifier" 型のいずれかを指定します。
これにより、リモート参加者インスタンスが同期的に返されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>通話から参加者を削除する

通話から参加者を削除するには (ユーザーまたは電話番号のいずれか)、`removeParticipant` を呼び出します。
"Identifier" 型のいずれかを渡す必要があります。これは、参加者が通話から削除されると、非同期的に解決されます。
参加者は、`remoteParticipants` コレクションからも削除されます。

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者の動画ストリームと画面共有ストリームの一覧を取得するには、`videoStreams` コレクションを調べます。

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
`RemoteVideoStream` をレンダリングするには、`isAvailableChanged` イベントをサブスクライブする必要があります。
`isAvailable` プロパティが `true` に変更された場合、リモート参加者はストリームを送信しています。
それが発生したら、`Renderer` の新しいインスタンスを作成し、非同期 `createView` メソッドを使用して新しい `RendererView` インスタンスを作成します。  その後、任意の UI 要素に `view.target` をアタッチできます。
リモート ストリームの使用可能性が変わるたびに、レンダラー全体を破棄するか、特定の `RendererView` を破棄するか、それらを保持するかを選択できますが、これによって空の動画フレームが表示されます。

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>リモート動画ストリームのプロパティ
リモート動画ストリームには次のプロパティがあります。

* `Id` - リモート ビデオ ストリームの ID です
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` - リモート ビデオ ストリームのサイズ (幅と高さ) です
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` - "Video" または "ScreenSharing" になります
```js
const type: MediaStreamType = remoteVideoStream.type;
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
`RendererView` を作成するときに、`scalingMode` および `mirrored` プロパティを指定できます。
スケーリング モードは "Stretch"、"Crop"、"Fit" です。`Mirrored` を指定すると、レンダリングされたストリームは垂直方向に反転されます。

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
特定の `RendererView` インスタンスには、レンダリング サーフェイスを表す `target` プロパティがあります。 これを、アプリケーションの UI にアタッチする必要があります。
```js
document.body.appendChild(rendererView.target);
```

後で `updateScalingMode` メソッドを呼び出すことによって、スケーリング モードを更新できます。
```js
view.updateScalingMode('Crop')
```
### <a name="faq"></a>よく寄せられる質問
* リモート参加者がネットワーク接続を失った場合、その状態は "Disconnected" になりますか。
    * はい。リモート参加者が 2 分以上ネットワーク接続を失った場合、その状態は "Disconnected" に変わり、通話が削除されます。
## <a name="device-management"></a>デバイス管理

`DeviceManager` を使用すると、オーディオと動画のストリームを送信する呼び出しで使用できるローカル デバイスを列挙できます。 また、ネイティブ ブラウザー API を使用してマイクやカメラにアクセスするために、ユーザーにアクセス許可を要求することもできます。

`callClient.getDeviceManager()` メソッドを呼び出すことによって `deviceManager` にアクセスできます。
> [!WARNING]
> 現在、DeviceManager にアクセスするには、`callAgent` オブジェクトを最初にインスタンス化する必要があります

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>ローカル デバイスを列挙する

ローカル デバイスにアクセスするには、デバイス マネージャーで列挙メソッドを使用します。 列挙は同期アクションです。

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>既定のマイクとスピーカーを設定する

デバイス マネージャーを使用すると、通話の開始時に使用される既定のデバイスを設定できます。
クライアントの既定値が設定されていない場合、Communication Services は OS の既定値にフォールバックします。

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>ローカル カメラのプレビュー

`DeviceManager` と `Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>カメラとマイクへのアクセス許可を要求する

次のようにして、カメラとマイクのアクセス許可を付与するようにユーザーに要求します。

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
これは、`audio` および `video` のアクセス許可が付与されたかどうかを示すオブジェクトで非同期的に解決されます。
```js
console.log(result.audio);
console.log(result.video);
```

`getPermissionState` を呼び出すことによって、特定の型に対する現在のアクセス許可の状態を調べることができます。

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>イベント モデル

値が変更されたときに通知を受け取るように、ほとんどのプロパティとコレクションをサブスクライブすることができます。

### <a name="properties"></a>Properties
`property changed` イベントをサブスクライブするには:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>コレクション
`collection updated` イベントをサブスクライブするには:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
