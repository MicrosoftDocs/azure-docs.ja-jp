---
title: Azure Communication Services UI フレームワークの SDK 複合コンポーネントの概要
titleSuffix: An Azure Communication Services quickstart
description: このクイックスタートでは、UI フレームワークの複合コンポーネントの概要について説明します
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 7356fb90914e948b6a74a478ce1e19722b224346
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488059"
---
# <a name="quickstart-get-started-with-ui-framework-composite-components"></a>クイックスタート: UI フレームワークの複合コンポーネントの概要

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

UI フレームワークを使用して通信エクスペリエンスをアプリケーションに迅速に統合することにより、Azure Communication Services の使用を開始します。 このクイックスタートでは、UI フレームワークの複合コンポーネントをアプリケーションに統合して、通信エクスペリエンスを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)。アクティブ LTS およびメンテナンス LTS バージョン (Node 12 を推奨)。
- アクティブな Communication Services リソース。 [Communication Services リソースを作成します](./../create-communication-resource.md)。
- 通話複合をインスタンス化するためのユーザー アクセス トークン。 [ユーザー アクセス トークンを作成して管理する](./../access-tokens.md)方法を参照してください。

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

//Private Preview install tarball

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
| GroupCall | 参加者ギャラリーとコントロールを使用して、グループ通話エクスペリエンスをレンダリングする複合コンポーネント。 |
| GroupChat (グループ チャット) | チャット スレッドと入力を使用して、グループ チャット エクスペリエンスをレンダリングする複合コンポーネント |


## <a name="initialize-group-call-and-group-chat-composite-components"></a>グループ通話およびグループ チャット複合コンポーネントを初期化する

`my-app` 内の `src` フォルダーに移動し、`app.js` ファイルを探します。 ここに次のコードを追加して、グループ チャット用および通話用の複合コンポーネントを初期化します。 どちらを使用するかは、構築している通信エクスペリエンスの種類に応じて選択できます。 必要な場合は、両方を同時に使用することもできます。 コンポーネントを初期化するには、Azure Communication Services から取得したアクセス トークンが必要です。 アクセス トークンを取得する方法の詳細については、[ユーザー アクセス トークンの作成と管理](./../access-tokens.md)に関する記事を参照してください。

> [!NOTE]
> アクセス トークン、グループ ID、スレッド ID は、コンポーネントによって生成されるわけではありません。 これらの要素はサービスから取得します。サービスでは、これらの ID を生成してクライアント アプリケーションに渡すための適切な手順が実行されます。 詳細については、次を参照してください。「[クライアントとサーバーのアーキテクチャ](./../../concepts/client-and-server-architecture.md)」。
> 
> たとえば次のようになります。グループ チャット複合は、自身の初期化に使用される `token` に関連付けられている `userId` が、提供された `threadId` に既に参加していることを前提にしています。 トークンがスレッド ID に渡されていない場合、グループ チャット複合は失敗します。 チャットの詳細については、[チャットの使用開始](./../chat/get-started.md)に関するページをご覧ください


`App.js`
```javascript

import {GroupCall, GroupChat} from "@azure/acs-ui-sdk"

function App(){

    return(<>
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "50rem", float: "left"}}>
            <GroupCall
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                refreshTokenCallback={CALLBACK} //Optional, Callback to refresh the token in case it expires
                groupId={GROUPID} //Required, Id for group call that will be joined. (GUID)
                onEndCall = { () => {
                    //Optional, Action to be performed when the call ends
                }}
            />
        </div>

        {/*Note: Make sure that the userId associated to the token has been added to the provided threadId*/}
        {/* Example styling provided, developers can provide their own styling to position and resize components */}
        <div style={{height: "35rem", width: "30rem", float: "left"}}>
            <GroupChat 
                displayName={DISPLAY_NAME} //Required, Display name for the user entering the call
                token={TOKEN} // Required, Azure Communication Services access token retrieved from authentication service
                threadId={THREADID} //Required, Id for group chat thread that will be joined.
                endpointUrl={ENDPOINT_URL} //Required, URL for Azure endpoint being used for Azure Communication Services
                onRenderAvatar = { (acsId) => {
                    //Optional, function to override the avatar image on the chat thread. Function receives one parameters for the Azure Communication Services Identity. Must return a React element.
                }}
                refreshToken = { () => {
                    //Optional, function to refresh the access token in case it expires
                }}
                options = {{
                    //Optional, options to define chat behavior
                    sendBoxMaxLength: number | undefined //Optional, Limit the max send box length based on viewport size change.
                }}
            />
        </div>
    </>);
}

export default App;

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
> [UI フレームワークの基本コンポーネントを試す](./get-started-with-components.md)

詳細については、次のリソースを参照してください。
- [UI フレームワークの概要](../../concepts/ui-framework/ui-sdk-overview.md)
- [UI フレームワークの機能](./../../concepts/ui-framework/ui-sdk-features.md)
