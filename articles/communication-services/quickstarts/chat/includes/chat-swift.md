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
ms.openlocfilehash: dedea2a622cb0eece92bb8b57871c76daa05fb68
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495471"
---
## <a name="prerequisites"></a>前提条件
開始する前に、必ず次のことを行ってください。

- アクティブなサブスクリプションがある Azure アカウントを作成します。 詳細については、[アカウントの無料作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)に関するページを参照してください。 
- [Xcode](https://developer.apple.com/xcode/) と [Cocoapods](https://cocoapods.org/) をインストールします。Xcode は、このクイックスタート用の iOS アプリケーションの作成に、Cocoapods は依存関係のインストールに使用します。
- Azure Communication Services リソースを作成します。 詳細については、[Azure Communication リソースの作成](../../create-communication-resource.md)に関するページを参照してください。 このクイックスタート用に、自分のリソースの **エンドポイントを記録する** 必要があります。
- **2 人** の ACS ユーザーを作成し、それに対して [ユーザー アクセス トークン](../../access-tokens.md)を発行します。 スコープは必ず **chat** に設定し、**トークン文字列と userId 文字列をメモ** してください。 このクイックスタートでは、最初の参加者でスレッドを作成した後、そのスレッドに 2 人目の参加者を追加します。

## <a name="setting-up"></a>設定

### <a name="create-a-new-ios-application"></a>新しい iOS アプリケーションを作成する

Xcode を開き、 `Create a new Xcode project` を選択します。

次のウィンドウで、プラットフォームとして `iOS` を選択し、そのテンプレートに `App` を選択します。

オプションを選択する際に、プロジェクト名として「`ChatQuickstart`」と入力します。 インターフェイスとして `Storyboard` を、ライフ サイクルとして `UIKit App Delegate` を、言語として `Swift` を選択します。

[次へ] をクリックし、プロジェクトの作成先ディレクトリを選択します。

### <a name="install-the-libraries"></a>ライブラリをインストールする

ここでは Cocoapods を使用して、必要な Communication Services の依存関係をインストールします。

コマンド ラインから、`ChatQuickstart` iOS プロジェクトのルート ディレクトリに移動します。

ポッドファイルを作成します (`pod init`)。

そのポッドファイルを開き、`ChatQuickstart` ターゲットに次の依存関係を追加します。
```
pod 'AzureCommunication', '~> 1.0.0-beta.9'
pod 'AzureCommunicationChat', '~> 1.0.0-beta.9'
```

依存関係をインストール (`pod install`) すると、Xcode ワークスペースも作成されます。

**ポッドのインストールを実行した後、新しく作成した `.xcworkspace` を選択して、Xcode でプロジェクトを再度開きます。**

### <a name="setup-the-placeholders"></a>プレースホルダーを設定する

Xcode でワークスペース `ChatQuickstart.xcworkspace` を開いてから、`ViewController.swift` を開きます。

このクイックスタートでは、`viewController` にコードを追加し、その出力を Xcode コンソールで表示します。 このクイックスタートでは、iOS の UI 作成については取り上げません。 

`viewController.swift` の先頭で、`AzureCommunication` および `AzureCommunicatonChat` ライブラリをインポートします。

```
import AzureCommunication
import AzureCommunicationChat
```

`ViewController` の `viewDidLoad()` メソッドに次のコードをコピーします。

```
override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.

        let semaphore = DispatchSemaphore(value: 0)
        DispatchQueue.global(qos: .background).async {
            do {
                // <CREATE A CHAT CLIENT>
                
                // <CREATE A CHAT THREAD>
                
                // <CREATE A CHAT THREAD CLIENT>
                
                // <SEND A MESSAGE>
                
                // <ADD A USER>
                
                // <LIST USERS>
                
                // <REMOVE A USER>
            } catch {
                print("Quickstart failed: \(error.localizedDescription)")
            }
        }
    }
```

デモンストレーション用に、コードの同期にはセマフォを使用します。 以降の手順では、Azure Communication Services の Chat ライブラリを使用して、プレースホルダーをサンプル コードに置き換えていきます。


### <a name="create-a-chat-client"></a>チャット クライアントを作成する

`<CREATE A CHAT CLIENT>` コメントを、次のコードに置き換えます。

```
let endpoint = "<ACS_RESOURCE_ENDPOINT>"
    let credential =
    try CommunicationTokenCredential(
        token: "<ACCESS_TOKEN>"
    )
    let options = AzureCommunicationChatClientOptions()

    let chatClient = try ChatClient(
        endpoint: endpoint,
        credential: credential,
        withOptions: options
    )
```

`<ACS_RESOURCE_ENDPOINT>` は、ACS リソースのエンドポイントに置き換えます。
`<ACCESS_TOKEN>` は、有効な ACS アクセストークンに置き換えます。

このクイックスタートでは、チャット アプリケーションのトークンを管理するためのサービス レベルの作成については説明しませんが、サービス レベルの使用をお勧めします。 詳細については、[チャットのアーキテクチャ](../../../concepts/chat/concepts.md)に関するドキュメントを参照してください

詳細については、[ユーザー アクセス トークン](../../access-tokens.md)に関するページを参照してください。

## <a name="object-model"></a>オブジェクト モデル 
JavaScript 用 Azure Communication Services チャット クライアント ライブラリが備える主な機能のいくつかは、以下のクラスとインターフェイスにより処理されます。

| 名前                                   | 説明                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | このクラスは、チャット機能に必要となります。 サブスクリプション情報を使用してインスタンス化し、それを使用してスレッドを作成、取得、削除します。 |
| ChatThreadClient | このクラスはチャット スレッド機能に必要です。 ChatClient を介してインスタンスを取得し、それを使用して、メッセージの送信、受信、更新、削除、ユーザーの追加、削除、取得、入力通知の送信、開封確認、チャット イベントのサブスクライブを行います。 |

## <a name="start-a-chat-thread"></a>チャット スレッドを開始する

ここでは、`ChatClient` を使用して、最初のユーザーで新しいスレッドを作成します。

`<CREATE A CHAT THREAD>` コメントを、次のコードに置き換えます。

```
let request = CreateThreadRequest(
    topic: "Quickstart",
    participants: [
        Participant(
            id: CommunicationUserIdentifier("<USER_ID>"),
            displayName: "Jack"
        )
    ]
)

var threadId: String?
chatClient.create(thread: request) { result, _ in
    switch result {
    case let .success(result):
        threadId = result.thread?.id

    case .failure:
        fatalError("Failed to create thread.")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>` は、有効な Communication Services のユーザー ID に置き換えます。

ここでセマフォを使用して、処理を続行する前に完了ハンドラーを待機します。 完了ハンドラーに返された応答の `threadId` を後続の手順で使用します。

## <a name="get-a-chat-thread-client"></a>チャット スレッド クライアントを取得する

チャット スレッドを作成したら、そのスレッド内で操作を実行する `ChatThreadClient` を取得します。

`<CREATE A CHAT THREAD CLIENT>` コメントを、次のコードに置き換えます。

```
let chatThreadClient = try chatClient.createClient(forThread: threadId!)
```

## <a name="send-a-message-to-a-chat-thread"></a>チャット スレッドにメッセージを送信する

`<SEND A MESSAGE>` コメントを、次のコードに置き換えます。

```
let message = SendChatMessageRequest(
    content: "Hello!",
    senderDisplayName: "Jack"
)

chatThreadClient.send(message: message) { result, _ in
    switch result {
    case let .success(result):
        print("Message sent, message id: \(result.id)")
    case .failure:
        print("Failed to send message")
    }
    semaphore.signal()
}
semaphore.wait()
```

まず、コンテンツと送信者の表示名を含む `SendChatMessageRequest` を作成します (必要に応じて、共有履歴時刻を含めることもできます)。 完了ハンドラーに返された応答には、送信されたメッセージの ID が含まれています。

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>チャット スレッドに参加者としてユーザーを追加する

`<ADD A USER>` コメントを、次のコードに置き換えます。

```
let user = Participant(
    id: CommunicationUserIdentifier("<USER_ID>"),
    displayName: "Jane"
)

chatThreadClient.add(participants: [user]) { result, _ in
    switch result {
    case let .success(result):
        (result.errors != nil) ? print("Added participant") : print("Error adding participant")
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```

`<USER_ID>` は、追加するユーザーの ACS ユーザー ID に置き換えます。

スレッドに参加者を追加すると、完了時に返された応答にエラーが含まれる場合があります。 これらのエラーは、特定の参加者を追加できなかったことを表します。

## <a name="list-users-in-a-thread"></a>スレッド内のユーザーを一覧表示する

コメント `<LIST USERS>` を、次のコードに置き換えます。

```
chatThreadClient.listParticipants { result, _ in
    switch result {
    case let .success(participants):
        var iterator = participants.syncIterator
        while let participant = iterator.next() {
            let user = participant.id as! CommunicationUserIdentifier
            print(user.identifier)
        }
    case .failure:
        print("Failed to list participants")
    }
    semaphore.signal()
}
semaphore.wait()
```


## <a name="remove-user-from-a-chat-thread"></a>チャット スレッドからユーザーを削除する

コメント `<REMOVE A USER>` を、次のコードに置き換えます。

```
chatThreadClient
    .remove(
        participant: CommunicationUserIdentifier("<USER_ID>")
    ) { result, _ in
        switch result {
        case .success:
            print("Removed user from the thread.")
        case .failure:
            print("Failed to remove user from the thread.")
        }
    }
```

`<USER ID>` は、削除する参加者の Communication Services ユーザー ID に置き換えます。

## <a name="run-the-code"></a>コードの実行

Xcode で [Run]\(実行\) ボタンをクリックし、プロジェクトをビルドして実行します。 コンソールには、そのコードからの出力と ChatClient からのロガー出力が表示されます。


