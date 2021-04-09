---
title: Azure Communication Services UI フレームワークの基本コンポーネントの概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、UI フレームワークの基本コンポーネントの概要について説明します
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6f4a8e8f26e88a73fc73c309ef336813282589f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488178"
---
# <a name="quickstart-get-started-with-ui-framework-base-components"></a>クイックスタート: UI フレームワークの基本コンポーネントの概要

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

UI フレームワークを使用して通信エクスペリエンスをアプリケーションに迅速に統合することにより、Azure Communication Services の使用を開始します。 このクイックスタートでは、UI フレームワークの基本コンポーネントをアプリケーションに統合して、通信エクスペリエンスを構築する方法について説明します。 

UI フレームワークのコンポーネントには、基本と複合の 2 種類があります。

- **基本コンポーネント** は個別の通信機能を表します。これらは、複雑な通信エクスペリエンスの構築に使用できる基本的な構成要素です。 
- **複合コンポーネント** は、一般的な通信シナリオに対応したターンキー エクスペリエンスです。これらは、構成要素として **基本コンポーネント** を使用して構築され、アプリケーションに簡単に統合できるようにパッケージ化されています。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)。アクティブ LTS およびメンテナンス LTS バージョン (Node 12 を推奨)。
- アクティブな Communication Services リソース。 [Communication Services リソースを作成します](./../create-communication-resource.md)。
- 通話クライアントをインスタンス化するためのユーザー アクセス トークン。 [ユーザー アクセス トークンを作成して管理する](./../access-tokens.md)方法を参照してください。

## <a name="setting-up"></a>設定

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
| CallingProvider| 通話をインスタンス化するための通話プロバイダー。 他のコンポーネントを追加するために必要です|
| ChatProvider | チャット スレッドをインスタンス化するためのチャット プロバイダー。 他のコンポーネントを追加するために必要です|
| MediaGallery | 通話参加者とそのリモート ビデオ ストリームを表示する基本コンポーネント |
| MediaControls | ミュート、ビデオ、画面の共有など、通話を制御する基本コンポーネント |
| ChatThread | 入力インジケーターや開封確認メッセージなどを含むチャット スレッドをレンダリングする基本コンポーネント |
| SendBox | 参加しているスレッドに送信されるメッセージをユーザーが入力できるようにする基本コンポーネント|

## <a name="initialize-calling-and-chat-providers-using-azure-communication-services-credentials"></a>Azure Communication Services の資格情報を使用して通話およびチャット プロバイダーを初期化する

`my-app` 内の `src` フォルダーに移動し、`app.js` ファイルを探します。 ここに次のコードを追加して、通話およびチャット プロバイダーを初期化します。 これらのプロバイダーは、通話およびチャット エクスペリエンスのコンテキストを維持する役割を担っています。 どちらを使用するかは、構築している通信エクスペリエンスの種類に応じて選択できます。 必要な場合は、両方を同時に使用することもできます。 コンポーネントを初期化するには、Azure Communication Services から取得したアクセス トークンが必要です。 アクセス トークンの取得方法の詳細については、[アクセス トークンの作成と管理](./../access-tokens.md)に関する記事を参照してください。

> [!NOTE]
> アクセス トークン、グループ ID、スレッド ID は、コンポーネントによって生成されるわけではありません。 これらの要素はサービスから取得します。サービスでは、これらの ID を生成してクライアント アプリケーションに渡すための適切な手順が実行されます。 詳細については、次を参照してください。「[クライアントとサーバーのアーキテクチャ](./../../concepts/client-and-server-architecture.md)」。
> 
> たとえば次のようになります。チャット プロバイダーは、その初期化に使用される `token` に関連付けられた `userId` が、提供される `threadId` に既に参加していることを想定しています。 トークンがスレッド ID とつながっていない場合、チャット プロバイダーは失敗します。 チャットの詳細については、[チャットの使用開始](./../chat/get-started.md)に関するページを参照してください。

Fluent UI テーマを使用して、アプリケーションの外観を改良します。

`App.js`
```javascript

import {CallingProvider, ChatProvider} from "@azure/acs-ui-sdk"
import { mergeThemes, teamsTheme } from '@fluentui/react-northstar';
import { Provider } from '@fluentui/react-northstar/dist/commonjs/components/Provider/Provider';
import { svgIconStyles } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconStyles';
import { svgIconVariables } from '@fluentui/react-northstar/dist/es/themes/teams/components/SvgIcon/svgIconVariables';
import * as siteVariables from '@fluentui/react-northstar/dist/es/themes/teams/siteVariables';

const iconTheme = {
  componentStyles: {
    SvgIcon: svgIconStyles
  },
  componentVariables: {
    SvgIcon: svgIconVariables
  },
  siteVariables
};

function App(props) {

  return (
    <Provider theme={mergeThemes(iconTheme, teamsTheme)}>
        <CallingProvider
        displayName={/*Insert Display Name to be used for the user*/}
        groupId={/*Insert GUID for group call to be joined*/}
        token={/*Insert the Azure Communication Services access token*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            // Add Calling Components Here
        </CallingProvider>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}

        <ChatProvider
        token={/*Insert the Azure Communication Services access token*/}
        displayName={/*Insert Display Name to be used for the user*/}
        threadId={/*Insert id for group chat thread to be joined*/}
        endpointUrl={/*Insert the environment URL for the Azure Resource used*/}
        refreshTokenCallback={/*Optional, Insert refresh token call back function*/}
        >
            //  Add Chat Components Here
        </ChatProvider>
    </Provider>
  );
}

export default App;

```

このプロバイダーが初期化されると、UI フレームワークの基本コンポーネントと追加のレイアウト ロジックを使用して、独自のレイアウトを作成できるようになります。 このプロバイダーによって、基になるすべてのロジックが初期化され、さまざまなコンポーネントが適切に接続されます。 次に、UI フレームワークが提供するさまざまな基本コンポーネントを使用して、通信エクスペリエンスを構築します。 これらのコンポーネントのレイアウトをカスタマイズしたり、それらを使用してレンダリングする他のカスタム コンポーネントを追加したりできます。 

## <a name="build-ui-framework-calling-component-experiences"></a>UI フレームワークの通話コンポーネント エクスペリエンスを構築する

通話では、`MediaGallery` および `MediaControls` コンポーネントを使用します。 これらの詳細については、「[UI フレームワークの機能](./../../concepts/ui-framework/ui-sdk-features.md)」を参照してください。 開始するには、`src` フォルダーに `CallingComponents.js` という名前の新しいファイルを作成します。 ここでは、後で `app.js` にインポートする基本コンポーネントを保持する関数コンポーネントを初期化します。 コンポーネントのレイアウトとスタイルを追加することができます。 

`CallingComponents.js`
```javascript

import {MediaGallery, MediaControls, MapToCallConfigurationProps, connectFuncsToContext} from "@azure/acs-ui-sdk"

function CallingComponents(props) {

  if (props.isCallInitialized) {props.joinCall()}

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <MediaGallery/>
        <MediaControls/>
    </div>
  );
}

export default connectFuncsToContext(CallingComponents, MapToCallConfigurationProps);

```

このファイルの一番下で、UI フレームワークの `connectFuncsToContext` メソッドを使用して通話コンポーネントをエクスポートし、マッピング関数 `MapToCallingSetupProps` を使用して通話 UI コンポーネントを基になる状態に接続しました。 このメソッドにより、プロパティが設定されたコンポーネントが生成されます。その後、これを使用して状態を確認し、通話に参加します。 `isCallInitialized` プロパティを使用して `CallAgent` の準備ができているかどうかを確認したら、`joinCall` メソッドを使用して参加します。 UI フレームワークでは、コンポーネントにデータがプッシュされるしくみを開発者が制御するシナリオに使用されるカスタム マッピング関数がサポートされています。

## <a name="build-ui-framework-chat-component-experiences"></a>UI フレームワークのチャット コンポーネント エクスペリエンスを構築する

チャットでは、`ChatThread` および `SendBox` コンポーネントを使用します。 これらのコンポーネントの詳細については、「[UI フレームワークの機能](./../../concepts/ui-framework/ui-sdk-features.md)」を参照してください。 開始するには、`src` フォルダーに `ChatComponents.js` という名前の新しいファイルを作成します。 ここでは、後で `app.js` にインポートする基本コンポーネントを保持する関数コンポーネントを初期化します。

`ChatComponents.js`
```javascript

import {ChatThread, SendBox} from '@azure/acs-ui-sdk'

function ChatComponents() {

  return (
    <div style = {{height: '35rem', width: '30rem', float: 'left'}}>
        <ChatThread />
        <SendBox />
    </div >
  );
}

export default ChatComponents;

```

## <a name="add-calling-and-chat-components-to-the-main-application"></a>メイン アプリケーションに通話およびチャット コンポーネントを追加する

`app.js` ファイルに戻って、以下に示すように、コンポーネントを `CallingProvider` と `ChatProvider` に追加します。

`App.js`
```javascript

import ChatComponents from './ChatComponents';
import CallingComponents from './CallingComponents';

<Provider ... >
    <CallingProvider .... >
        <CallingComponents/>
    </CallingProvider>

    <ChatProvider .... >
        <ChatComponents />
    </ChatProvider>
</Provider>

```

## <a name="run-quickstart"></a>クイックスタートを実行する

上記のコードを実行するには、次のコマンドを使用します。

```console

npm run start   

```

機能を十分にテストするには、通話とチャットのスレッドに参加するための通話およびチャット機能を備えたクライアントがもう 1 つ必要です。 選択肢の 1 つとして、[通話のヒーロー サンプル](./../../samples/calling-hero-sample.md)と[チャットのヒーロー サンプル](./../../samples/chat-hero-sample.md)を確認してみてください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Communication Services サブスクリプションをクリーンアップして解除する場合は、リソースまたはリソース グループを削除できます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。 詳細については、[リソースのクリーンアップ](../create-communication-resource.md#clean-up-resources)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [UI フレームワークの複合コンポーネントを試す](./get-started-with-composites.md)

詳細については、次のリソースを参照してください。
- [UI フレームワークの概要](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI フレームワークの機能](./../../concepts/ui-framework/ui-sdk-features.md)
