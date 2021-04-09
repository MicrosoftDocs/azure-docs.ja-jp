---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e9c889dcffe42fde244f8a35ce42032e84d78fff
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488114"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- デプロイ済みの Communication Services リソース。 [Communication Services リソースを作成します](../../create-communication-resource.md)。
- 通話クライアントを有効にするための `User Access Token`。 [`User Access Token` を取得する方法](../../access-tokens.md)についての詳細
- 省略可能:[アプリケーションへの通話の追加の概要](../getting-started-with-calling.md)に関するクイックスタートを完了します

## <a name="setting-up"></a>設定

### <a name="creating-the-xcode-project"></a>Xcode プロジェクトを作成する

> [!NOTE]
> このドキュメントでは、呼び出し元のクライアント ライブラリのバージョン 1.0.0-beta.8 を使用します。

Xcode で、新しい iOS プロジェクトを作成し、 **[単一ビュー アプリ]** テンプレートを選択します。 このクイックスタートでは [SwiftUI フレームワーク](https://developer.apple.com/xcode/swiftui/)を使用します。そのため、 **[言語]** を **[Swift]** に設定し、 **[ユーザー インターフェイス]** を **[SwiftUI]** に設定する必要があります。 このクイックスタートでは、単体テストと UI テストは作成しません。 **[Include Unit Tests]\(単体テストを含める\)** のチェックをオフにし、また、 **[Include UI Tests]\(UI テストを含める\)** のチェックもオフにしてかまいません。

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Xcode 内での新たな [新しいプロジェクト] ウィンドウの作成を示すスクリーンショット。":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods でパッケージと依存関係をインストールする

1. 次のようにして、アプリケーションのポッドファイルを作成します。

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. `pod install` を実行します。
3. XCode を使用して `.xcworkspace` を開きます。

### <a name="request-access-to-the-microphone"></a>マイクへのアクセスを要求する

デバイスのマイクにアクセスするには、アプリの情報プロパティ リストを `NSMicrophoneUsageDescription` によって更新する必要があります。 関連付けられた値は `string` に設定します。これは、ユーザーからのアクセスの要求を求めるためにシステムが使用するダイアログに含まれます。

プロジェクト ツリーの `Info.plist` のエントリを右クリックし、 **[Open As]\(形式を指定して開く\)**  >  **[Source Code]\(ソース コード\)** の順に選択します。 最上位の `<dict>` セクションに以下の行を追加してから、ファイルを保存します。

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクトの **ContentView. swift** ファイルを開き、ファイルの先頭に `import` 宣言を追加して `AzureCommunicationCalling library` をインポートします。 さらに、`AVFoundation` をインポートします。これは、コード内のオーディオ アクセス許可要求に必要になります。

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>オブジェクト モデル

iOS 用 Azure Communication Services 通話クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。


| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient は、通話クライアント ライブラリへのメイン エントリ ポイントです。|
| CallAgent | CallAgent は、通話を開始および管理するために使用します。 |
| CommunicationTokenCredential | CommunicationTokenCredential は、CallAgent をインスタンス化するためのトークン資格情報として使用されます。| 
| CommunicationIdentifier | CommunicationIdentifier はユーザーの ID を表すために使用され、次のいずれかになります: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication。 |

> [!NOTE]
> イベント デリゲートを実装する場合、アプリケーションによって、イベント サブスクリプションを必要とするオブジェクトへの強い参照が保持される必要があります。 たとえば、`call.addParticipant` メソッドの呼び出し時に `RemoteParticipant` オブジェクトが返され、アプリケーションによって `RemoteParticipantDelegate` でリッスンするようにデリゲートが設定された場合には、そのアプリケーションによって `RemoteParticipant` オブジェクトへの強い参照が保持される必要があります。 それ以外の場合に、このオブジェクトが収集されると、呼び出し元の SDK によってオブジェクトの呼び出しが試行されるときに、デリゲートによって致命的な例外がスローされます。

## <a name="initialize-the-callagent"></a>CallAgent を初期化する

`CallClient` から `CallAgent` インスタンスを作成するには、初期化されると `CallAgent` オブジェクトを非同期に返す `callClient.createCallAgent` メソッドを使用する必要があります。

通話クライアントを作成するには、`CommunicationTokenCredential` オブジェクトを渡す必要があります。

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

上記で作成された `CommunicationTokenCredential` オブジェクトを `CallClient` に渡し、表示名を設定します。

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>発信通話を行う

通話を作成して開始するには、`CallAgent` でいずれかの API を呼び出し、Communication Services 管理クライアント ライブラリを使用してプロビジョニングしたユーザーの Communication Services ID を指定する必要があります。

通話の作成と開始は同期的に行います。 通話のすべてのイベントにサブスクライブできる通話インスタンスを受信します。

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>ユーザーに対する 1:1 の通話、またはユーザーおよび PSTN と 1:n の通話を行う

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>ユーザーと PSTN で 1:n の通話を行う
PSTN への通話を行うには、Communication Services で取得した電話番号を指定する必要があります
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>動画を使用して 1:1 の通話を行う
デバイス マネージャー インスタンスを取得するには、[こちら](#device-management)を参照してください

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>グループ通話に参加する
通話に参加するには、*CallAgent* でいずれかの API を呼び出す必要があります

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>電話の着信をサブスクライブする
電話の着信イベントをサブスクライブする

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>電話の着信を受け入れる
着信を受け入れるには、呼び出しオブジェクトに対して "accept" メソッドを呼び出します。
CallAgent にデリゲートを設定する 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>プッシュ通知

モバイル プッシュ通知は、モバイル デバイスで受け取るポップアップ通知です。 通話に関しては、Microsoft では VoIP (ボイス オーバー IP 通話) プッシュ通知に注目しています。 プッシュ通知に登録し、プッシュ通知を処理し、プッシュ通知を登録解除する機能が提供される予定です。

### <a name="prerequisite"></a>前提条件

- 手順 1:[Xcode] -> [Signing & Capabilities]\(署名と機能\) -> [Add Capability]\(機能の追加\) -> [Push Notifications]\(プッシュ通知\)
- 手順 2:[Xcode] -> [Signing & Capabilities]\(署名と機能\) -> [Add Capability]\(機能の追加\) -> [Background Modes]\(バックグラウンド モード\)
- 手順 3:[Background Modes]\(バックグラウンド モード\)-> [Voice over IP]\(ボイスオーバー IP\) と [リモート通知] を選択する

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Xcode での機能の追加方法を示すスクリーンショット。" lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>プッシュ通知に登録する

プッシュ通知に登録するには、デバイス登録トークンを使用して *CallAgent* インスタンスで registerPushNotification() を呼び出します。

初期化が正常に完了した後、プッシュ通知の登録が呼び出される必要があります。 `callAgent` オブジェクトが破棄されると、`logout` が呼び出されて、プッシュ通知が自動的に登録解除されます。


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

#### <a name="push-notification-handling"></a>プッシュ通知の処理
着信通話プッシュ通知を受信するには、ディクショナリ ペイロードを設定して *CallAgent* インスタンスで *handlePushNotification()* を呼び出します。

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
#### <a name="unregister-push-notification"></a>プッシュ通知の登録を解除する

アプリケーションによって、プッシュ通知の登録はいつでも解除できます。 *CallAgent* で `unregisterPushNotification` メソッドを呼び出すだけです。
> [!NOTE]
> ログアウト時に、プッシュ通知からアプリケーションが自動的に登録解除されることはありません。

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>通話中の操作

通話の間にさまざまな操作を実行し、動画やオーディオに関連する設定を管理できます。

### <a name="mute-and-unmute"></a>ミュートとミュート解除

ローカル エンドポイントをミュートまたはミュート解除するには、非同期 API の `mute` と `unmute` を使用できます。

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

[非同期] ローカル ミュート解除

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>ローカル動画の送信を開始および停止する

通話で他の参加者へのローカル動画の送信を開始するには、`startVideo` API を使用し、`camera` によって `localVideoStream` を渡します

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

動画の送信を開始すると、`LocalVideoStream` インスタンスが通話インスタンスの `localVideoStreams` コレクションに追加されます。

```swift

call.localVideoStreams[0]

```

[非同期] ローカル動画を停止するには、`call.startVideo` の呼び出しから返された `localVideoStream` を渡します。

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>リモート参加者の管理

すべてのリモート参加者は `RemoteParticipant` 型で表され、通話インスタンスの `remoteParticipants` コレクションを通して使用できます。

### <a name="list-participants-in-a-call"></a>通話の参加者を一覧表示する

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>リモート参加者のプロパティ

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>通話に参加者を追加する

通話に参加者を追加するには (ユーザーまたは電話番号のいずれか)、`addParticipant` を呼び出します。 これにより、リモート参加者インスタンスが同期的に返されます。

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>通話から参加者を削除する
通話から参加者を削除するには (ユーザーまたは電話番号のいずれか)、`removeParticipant` API を呼び出します。 これは、非同期に解決されます。

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>リモート参加者の動画ストリームをレンダリングする

リモート参加者は、通話中に動画または画面共有を開始できます。

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>リモート参加者の動画/画面共有ストリームを処理する

リモート参加者のストリームを一覧表示するには、`videoStreams` コレクションを調べます。

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>リモート動画ストリームのプロパティ

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>リモート参加者のストリームをレンダリングする

リモート参加者のストリームのレンダリングを開始するには、次を利用します。

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>リモート動画レンダラーのメソッドとプロパティ

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>デバイス管理

`DeviceManager` を使用すると、オーディオと動画のストリームを送信するために通話内で使用できるローカル デバイスを列挙できます。 また、マイクやカメラにアクセスするための許可をユーザーに要求することもできます。 `callClient` オブジェクトの `deviceManager` にアクセスできます。

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>ローカル デバイスを列挙する

ローカル デバイスにアクセスするには、デバイス マネージャーで列挙メソッドを使用します。 列挙は同期アクションです。

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>既定のマイクとスピーカーを設定する

デバイス マネージャーを使用すると、通話の開始時に使用される既定のデバイスを設定できます。 スタックの既定値が設定されていない場合、Communication Services は OS の既定値にフォールバックします。

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>ローカル カメラのプレビュー

`Renderer` を使用して、ローカル カメラからのストリームのレンダリングを開始できます。 このストリームは、他の参加者には送信されません。ローカル プレビュー フィードです。 これは、非同期アクションです。

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>ローカル カメラのプレビューのプロパティ

レンダラーには、レンダリングを制御できるプロパティとメソッドのセットが用意されています。

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>イベント モデル

値が変更されたときに通知を受け取るように、ほとんどのプロパティとコレクションをサブスクライブすることができます。

### <a name="properties"></a>Properties
`property changed` イベントをサブスクライブするには:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>コレクション
`collection updated` イベントをサブスクライブするには:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
