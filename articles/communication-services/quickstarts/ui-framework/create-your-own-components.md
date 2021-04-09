---
title: 独自の UI フレームワーク コンポーネントを作成する
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、UI フレームワークと互換性のあるカスタム コンポーネントを作成する方法について説明します
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 093fcfd95d291d959ed49cc39a227a99f14a0383
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488246"
---
# <a name="quickstart-create-your-own-ui-framework-component"></a>クイックスタート: 独自の UI フレームワーク コンポーネントを作成する

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

UI フレームワークを使用して通信エクスペリエンスをアプリケーションに迅速に統合することにより、Azure Communication Services の使用を開始します。

このクイックスタートでは、UI フレームワークによって提供される定義済みの状態インターフェイスを使用して、独自のコンポーネントを作成する方法について説明します。 このアプローチは、より多くのカスタマイズを必要とし、独自のデザイン アセットを使用してエクスペリエンスを実現する開発者に適しています。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)。アクティブ LTS およびメンテナンス LTS バージョン (Node 12 を推奨)。
- アクティブな Communication Services リソース。 [Communication Services リソースを作成します](./../create-communication-resource.md)。
- 通話クライアントをインスタンス化するためのユーザー アクセス トークン。 [ユーザー アクセス トークンを作成して管理する](./../access-tokens.md)方法を参照してください。

UI フレームワークでは、React 環境を設定する必要があります。 これは、次に行う予定です。 既に React アプリをお持ちの場合は、このセクションをスキップしてかまいません。

### <a name="set-up-react-app"></a>React アプリを設定する

このクイックスタートでは、create-react-app テンプレートを使用します。 詳細については、次を参照してください。[React の概要](https://reactjs.org/docs/create-a-new-react-app.html)に関するページ

```console

npx create-react-app my-app

cd my-app

```

このプロセスが終了すると、`my-app` フォルダー内に完全なアプリケーションが作成されます。 このクイックスタートでは、`src` フォルダー内のファイルを変更します。

### <a name="install-the-package"></a>パッケージをインストールする

`npm install` コマンドを使用して、JavaScript 用の Azure Communication Services 通話クライアント ライブラリをインストールします。 提供された tarball (プライベート プレビュー) を my-app ディレクトリに移動します。

```console

//For Private Preview install tarball

npm install --save ./{path for tarball}

```

`--save` オプションを使用すると、**package.json** ファイル内の依存関係としてライブラリが表示されます。

### <a name="run-create-react-app"></a>create-react-app を実行する

次を実行して、create-react-app のインストールをテストしましょう。

```console

npm run start   

```

## <a name="object-model"></a>オブジェクト モデル

Azure Communication Services UI クライアント ライブラリが備える主な機能のいくつかは、次のクラスとインターフェイスにより処理されます。

| 名前                                  | 説明                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| プロバイダー| 開発者が基になる Fluent UI コンポーネントを変更できるようにする Fluent UI プロバイダー|
| CallingProvider| 通話をインスタンス化するための通話プロバイダー。 基本コンポーネントを追加するために必要です|
| ChatProvider | チャット スレッドをインスタンス化するためのチャット プロバイダー。 基本コンポーネントを追加するために必要です|
| connectFuncsToContext | マッパーを使用して、基になるプロバイダーに UI フレームワーク コンポーネントを接続するメソッド |
| MapToChatMessageProps | コンポーネントにチャット メッセージのプロパティを提供するチャット メッセージ データ レイヤー マッパー |


## <a name="initialize-chat-providers-using-azure-communication-services-credentials"></a>Azure Communication Services の資格情報を使用してチャット プロバイダーを初期化する

このクイックスタートでは、例としてチャットを使用します。通話の詳細については、[基本コンポーネントのクイックスタート](./get-started-with-components.md)と[複合コンポーネントのクイックスタート](./get-started-with-composites.md)を参照してください。

`my-app` 内の `src` フォルダーに移動し、`app.js` ファイルを探します。 ここに次のコードを追加して、チャット プロバイダーを初期化します。 このプロバイダーは、通話およびチャット エクスペリエンスのコンテキストを維持する役割を担います。 コンポーネントを初期化するには、Azure Communication Services から取得したアクセス トークンが必要です。 アクセス トークンを取得する方法の詳細については、[アクセス トークンの作成と管理](./../access-tokens.md)に関する記事を参照してください。

UI フレームワーク コンポーネントは、サービスの他の部分と同じ一般的なアーキテクチャに従っています。 アクセス トークン、グループ ID、スレッド ID は、コンポーネントによって生成されるわけではありません。 これらの要素はサービスから取得します。サービスでは、これらの ID を生成してクライアント アプリケーションに渡すための適切な手順が実行されます。 詳細については、「[クライアントとサーバーのアーキテクチャ](./../../concepts/client-and-server-architecture.md)」を参照してください。

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"

function App(props) {

  return (
    <ChatProvider
    token={/*Insert the Azure Communication Services access token*/}
    userId={/*Insert the Azure Communication Services user id*/}
    displayName={/*Insert Display Name to be used for the user*/}
    threadId={/*Insert id for group chat thread to be joined*/}
    endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
    refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
    >
        //  Add Chat Components Here
    </ChatProvider>
  );
}

export default App;

```

このプロバイダーを初期化したら、UI フレームワークのコンポーネントと追加のレイアウト ロジックを使用して、独自のレイアウトを作成できるようになります。 このプロバイダーによって、基になるすべてのロジックが初期化され、さまざまなコンポーネントが適切に接続されます。 次に、UI フレームワーク マッパーを使用してカスタム コンポーネントを作成し、チャット プロバイダーに接続します。


## <a name="create-a-custom-component-using-mappers"></a>マッパーを使用してカスタム コンポーネントを作成する

まず、`SimpleChatThread.js` という名前の新しいファイルを作成し、ここにコンポーネントを作成します。 最初に、必要な UI フレームワークのコンポーネントをインポートします。 ここでは、そのまま使用できる html と React を使用して、単純なチャット スレッド用の完全なカスタム コンポーネントを作成します。 `connectFuncsToContext` メソッドを使用して、`MapToChatMessageProps` マッパーで `SimpleChatThread` カスタム コンポーネントにプロパティをマップします。 これらのプロパティを使用すると、送受信されるチャット メッセージにアクセスして、単純なスレッドにメッセージを取り込むことができます。

`SimpleChatThread.js`
```javascript

import {connectFuncsToContext, MapToChatMessageProps} from "@azure/acs-ui-sdk"

function SimpleChatThread(props) {

    return (
        <div>
            {props.chatMessages?.map((message) => (
                <div key={message.id ?? message.clientMessageId}> {`${message.senderDisplayName}: ${message.content}`}</div>
            ))}
        </div>
    );
}

export default connectFuncsToContext(SimpleChatThread, MapToChatMessageProps);

```

## <a name="add-your-custom-component-to-your-application"></a>カスタム コンポーネントをアプリケーションに追加する

カスタム コンポーネントの準備ができたので、これをインポートしてレイアウトに追加します。

```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import SimpleChatThread from "./SimpleChatThread"

function App(props) {

  return (
        <ChatProvider ... >
            <SimpleChatThread />
        </ChatProvider>
  );
}

export default App;

```

## <a name="run-quickstart"></a>クイックスタートを実行する

上記のコードを実行するには、次のコマンドを使用します。

```console

npm run start   

```

機能を完全にテストするには、単純なチャット スレッドによって受信されるメッセージを送信するチャット機能を備えた 2 つ目のクライアントが必要です。 選択肢の 1 つとして、[通話のヒーロー サンプル](./../../samples/calling-hero-sample.md)と[チャットのヒーロー サンプル](./../../samples/chat-hero-sample.md)を確認してみてください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [UI フレームワークの複合コンポーネントを試す](./get-started-with-composites.md)

詳細については、次のリソースを参照してください。
- [UI フレームワークの概要](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI フレームワークの機能](./../../concepts/ui-framework/ui-sdk-features.md)
- [UI フレームワークの基本コンポーネントのクイックスタート](./get-started-with-components.md)
