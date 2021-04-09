---
title: Azure Static Web Apps 用にローカル開発環境を設定する
description: Azure Static Web Apps 用のローカル開発環境を設定する方法について説明します
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-js
ms.openlocfilehash: 4d6dae8a4f4ed83af3103e95e711bacdb62cf522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326169"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビュー用にローカル開発環境を設定する

Azure Static Web Apps インスタンスは、異なる 2 種類のアプリケーションで構成されています。 1 つ目は、静的コンテンツ用の Web アプリです。 多くの場合、Web アプリはフロントエンドのフレームワークやライブラリ、または静的サイト ジェネレーターを使用して作成されます。 2 つ目の側面は API です。これは、充実したバックエンド開発環境を提供する Azure Functions アプリです。

クラウド内で実行されている場合、Azure Static Web Apps では、`api` ルートへの要求が Web アプリから Azure Functions アプリにシームレスにマップされ、CORS 構成は必要ありません。 ローカルでは、この動作を模倣するためにアプリケーションを構成する必要があります。

この記事では、ローカル開発用の推奨されるベストプラクティスについて説明します。これには以下の概念が含まれます。

- 静的コンテンツ用の Web アプリの設定
- アプリケーションの API の Azure Functions アプリの構成
- アプリケーションのデバッグと実行
- アプリのファイルとフォルダーの構造に関するベストプラクティス

## <a name="prerequisites"></a>前提条件

- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- Visual Studio Code 用 [Live Server 拡張機能](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)
  - フロントエンド JavaScript フレームワーク、または静的サイト ジェネレーターの CLI を使用していない場合にのみ必要です

## <a name="run-projects-locally"></a>ローカルでプロジェクトを実行する

Azure Static Web App をローカルで実行する場合、プロジェクトに API が含まれているかどうかに応じて、3 つのプロセスが伴います。

- ローカル Web サーバーの実行
- API の実行
- Web プロジェクトと API の接続

Web サイトの構築方法によっては、ブラウザーでアプリケーションを実行するためにローカル Web サーバーが必要になる場合と、ならない場合があります。 フロントエンド JavaScript フレームワークと静的サイト ジェネレーターを使用する場合、この機能はそれぞれの CLI (コマンド ライン インターフェイス) に組み込まれています。 次のリンクは、フレームワーク、ライブラリ、およびジェネレーターの選択に応じた CLI リファレンスに移動します。

### <a name="javascript-frameworks-and-libraries"></a>JavaScript フレームワークとライブラリ

- [Angular CLI](https://angular.io/cli)
- [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html)
- [React CLI](https://create-react-app.dev/)

### <a name="static-site-generators"></a>静的サイト ジェネレーター

- [Gatsby CLI](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

CLI ツールを使用してサイトにサービスを提供する場合は、[API の実行](#run-api-locally)に関するセクションまでスキップできます。

### <a name="running-a-local-web-server-with-live-server"></a>Live Server を使用してローカル Web サーバーを実行する

Visual Studio Code 用 Live Server 拡張機能には、静的コンテンツを提供するローカル開発用 Web サーバーが用意されています。

#### <a name="create-a-repository"></a>リポジトリを作成する

1. GitHub にログインしていることを確認し、[https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) に移動して、このテンプレートを使用して **vanilla-api** という名前の新しい GitHub プロジェクトを作成します。

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="GitHub の新しいリポジトリ ウィンドウ":::

1. Visual Studio Code を開きます。

1. **F1** キーを押してコマンド パレットを開きます。

1. 検索ボックスに「**clone**」と入力して、 **[Git:Clone]\(Git: クローン\)** を選択します。

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Visual Studio Code での git clone オプション":::

1. **[リポジトリの URL]** に次の値を入力します。

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. 新しいプロジェクト用のフォルダーの場所を選択します。

1. クローンされたリポジトリを開くようにメッセージが表示されたら、 **[開く]** を選択します。

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="新しいウィンドウで開く":::

Visual Studio Code により、クローンされたプロジェクトがエディターで開かれます。

### <a name="run-the-website-locally-with-live-server"></a>Live Server を使用してローカルで Web サイトを実行する

1. **F1** キーを押してコマンド パレットを開きます。

1. 検索ボックスに「**Live Server**」と入力して、 **[Live Server:Open with Live Server]\(Live Server: Live Server で開く\)** を選択します

    ブラウザー タブが開き、アプリケーションが表示されます。

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="ブラウザーで実行されているシンプルな静的サイト":::

    このアプリケーションでは、`api/message` エンドポイントに対して HTTP 要求が行われます。 このアプリケーションの API 部分を開始する必要があるため、現時点ではこの要求は失敗します。

### <a name="run-api-locally"></a>ローカルで API を実行する

Azure Static Web Apps の API では、Azure Functions が利用されています。 Azure Static Web Apps プロジェクトに API を追加する方法の詳細については、[Azure Functions を使用した Azure Static Web Apps への API の追加](add-api.md)に関する記事をご覧ください。

API 作成プロセスの一環として、Visual Studio Code の起動構成が作成されます。 この構成は _.vscode_ フォルダーに配置されます。 このフォルダーには、API をローカルでビルドして実行するために必要なすべての設定が含まれています。

1. Visual Studio Code で、**F5** キーを押して API を開始します。

1. 新しいターミナル インスタンスが開き、API ビルド プロセスからの出力が表示されます。

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="Visual Studio Code ターミナルで実行されている API":::

   Visual Studio Code のステータス バーがオレンジ色になりました。 この色は、API が実行中であり、デバッガーがアタッチされていることを示します。

1. 次に、**Ctrl/Cmd** キーを押し、ターミナル内の URL をクリックして、API を呼び出すブラウザー ウィンドウを開きます。

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="API 呼び出しの結果を表示するブラウザー":::

### <a name="debugging-the-api"></a>API のデバッグ

1. Visual Studio Code で _api/GetMessage/index.js_ ファイルを開きます。

1. 2 行目の左側の余白をクリックして、ブレークポイントを設定します。 ブレークポイントが設定されていることを示す赤い点が表示されます。

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Visual Studio Code でのブレークポイント":::

1. ブラウザーで、<http://127.0.0.1:7071/api/message> で実行されるページを更新します。

1. Visual Studio Code でブレークポイントがヒットし、プログラムの実行が一時停止されます。

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Visual Studio Code でのブレークポイントのヒット":::

   API に対する完全な[デバッグ エクスペリエンスを Visual Studio Code で利用できます](https://code.visualstudio.com/Docs/editor/debugging)。

1. デバッグ バーの **[続行]** ボタンを押して、実行を続行します。

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Visual Studio Code の [続行] ボタン":::

### <a name="calling-the-api-from-the-application"></a>アプリケーションからの API の呼び出し

デプロイされると、Azure Static Web Apps では、これらの要求が _api_ フォルダー内のエンドポイントに自動的にマップされます。 このマッピングにより、アプリケーションから API への要求が次の例のようになることが保証されます。

```javascript
let response = await fetch("/api/message");
```

アプリケーションを JavaScript フレームワーク CLI を使用して構築するかどうかによって、アプリケーションをローカルで実行する際に `api` ルートへのパスを構成する方法が 2 つあります。

- 環境構成ファイル (JavaScript フレームワークとライブラリに推奨)
- ローカル プロキシ

### <a name="environment-configuration-files"></a>環境構成ファイル

CLI のあるフロントエンド フレームワークを使用してアプリを構築する場合は、環境構成ファイルを使用する必要があります。 各フレームワークまたはライブラリによって、これらの環境構成ファイルを処理する方法が異なります。 アプリケーションをローカルで実行するときに使用する開発用の構成ファイルと、アプリケーションを運用環境で実行するときに使用する運用環境用の構成ファイルを用意するのが一般的です。 使用する JavaScript フレームワークまたは静的サイト ジェネレーターの CLI では、Azure Static Web Apps によってアプリがビルドされるときに、ローカルでの開発用ファイルと運用ファイルの使用が自動的に認識されます。

開発用構成ファイルでは、API へのパスを指定できます。これは、サイトの API がローカルで実行されているローカルの場所 `http:127.0.0.1:7071` を指します。

```
API=http:127.0.0.1:7071/api
```

運用構成ファイルでは、API へのパスを `api` として指定します。 これにより、アプリケーションを運用環境で実行したときに、"yoursite.com/api" を使用して api を呼び出すことができます。

```
API=api
```

これらの構成値は、Web アプリの JavaScript で Node 環境変数として参照できます。

```js
let response = await fetch(`${process.env.API}/message`);
```

CLI を使用して開発モードでサイトを実行したり、運用環境用のサイトを構築したりする場合、`process.env.API` の値は適切な構成ファイルの値に置き換えられます。

フロントエンド JavaScript フレームワークおよびライブラリ用に環境ファイルを構成する方法の詳細については、次の記事を参照してください。

- [Angular 環境変数](https://angular.io/guide/build#configuring-application-environments)
- [React - カスタム環境変数の追加](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - モードと環境変数](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Live Server の再起動

1. Visual Studio Code で、**F1** キーを押してコマンド パレットを開きます。

1. 「**Live Server**」と入力して、 **[Live Server:Stop Live Server]\(Live Server: Live Server を停止する\)** を選択します。

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Visual Studio コマンド パレットでの Live Server の停止コマンド":::

1. **F1** キーを押してコマンド パレットを開きます。

1. 「**Live Server**」と入力して、**Live Server:Open with Live Server** を選択して Live Server を起動します。

1. `http://locahost:3000` で実行されているアプリケーションを更新します。 ブラウザーには、API から返されたメッセージが表示されるようになりました。

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="ブラウザーに Hello from API が表示される":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](application-settings.md)
