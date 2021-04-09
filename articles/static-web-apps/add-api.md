---
title: Azure Functions を使用して Azure Static Web Apps に API を追加する
description: Azure Functions を使用して、静的 Web アプリにサーバーレス API を追加することで、Azure Static Web Apps の使用を開始します。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 8a42ca4cf69515c99bb2e0bd28bb62dfed0e18f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100650688"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions を使用して Azure Static Web Apps プレビューに API を追加する

Azure Functions との統合を使用して、Azure Static Web Apps にサーバーレス API を追加できます。 この記事では、Azure Static Web Apps サイトに API を追加してデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。
  - アカウントがない場合は、[アカウントを無料で作成](https://azure.microsoft.com/free)できます。
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 拡張機能。
- API アプリをローカルで実行するための [Node.js](https://nodejs.org/download/)

## <a name="create-a-git-repository"></a>Git リポジトリを作成する

次の手順では、新しいリポジトリを作成し、お使いのコンピューターにファイルを複製する方法を示します。

1. GitHub にログインしていることを確認し、[https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) に移動して新しいリポジトリを作成します。
1. _[リポジトリ名]_ ボックスに、「**my-vanilla-api**」と入力します。
1. **[Create repository from template]\(テンプレートからリポジトリを作成する\)** をクリックします。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="vanilla-basic から新しいリポジトリを作成する":::

プロジェクトが作成されたら、ブラウザー内の新しいリポジトリの URL をコピーします。 この URL を Visual Studio Code で使用して、Git リポジトリを複製します。

1. コマンド パレットでコマンドを開くには、**F1** キーを押します。
1. URL を _Git:Clone_ プロンプトに貼り付け、**Enter** キーを押します。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code を使用して GitHub プロジェクトを複製する":::

    画面の指示に従って、プロジェクトを複製するリポジトリの場所を選択します。

## <a name="create-the-api"></a>API の作成

次に、アプリケーションの API として Azure Functions プロジェクトを作成します。 

1. _my-vanilla-api_ プロジェクト内に、**api** という名前のサブフォルダーを作成します。
1. **F1** キーを押してコマンド パレットを開きます。
1. 「**Azure Functions:Create New Project...** 」と入力します
1. **Enter** キーを押します
1. **[参照]** を選択します
1. プロジェクト ワークスペースのディレクトリとして **api** フォルダーを選択します
1. **[選択]** を選択します

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="スクリーンショットには、A P I フォルダーと [選択] ボタンが示されています。":::

1. プロンプトで、次の情報を入力します。

    - _Select a language (言語を指定してください)_ : **[JavaScript]** を選択します
    - _Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)_ : **[HTTP トリガー]** を選択します
    - _Provide a function name (関数名を指定してください):_ 「**GetMessage**」と入力します
    - _承認レベル_: **[匿名]** を選択します。これにより、すべてのユーザーが関数エンドポイントを呼び出せるようになります。
        - 承認レベルの詳細については、[承認キー](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)に関するページを参照してください。

HTTP によってトリガーされた関数により、Visual Studio Code で Azure Functions プロジェクトが生成されます。

これで、アプリのプロジェクト構造が次の例のようになります。

```files
├── api
│   ├── GetMessage
│   │   ├── function.json
│   │   ├── index.js
│   │   └── sample.dat
│   ├── host.json
│   ├── local.settings.json
│   ├── package.json
│   └── proxies.json
├── index.html
├── readme.md
└── styles.css
```

次に、メッセージをフロントエンドに返すように `GetMessage` 関数を変更します。

1. 以下のコードを使用して、_api/GetMessage/index.js_ の下の `GetMessage` 関数を更新します。

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. 以下の設定を使用して `api/GetMessage/function.json` の下の `GetMessage` 構成を更新します。

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```

上記の設定では、API エンドポイントは次のようになります。

- 関数に対して HTTP 要求が行われたときにトリガーされます
- 認証状態に関係なくすべての要求で使用できます
- _/api/message_ ルートを介して公開されます

## <a name="run-the-api-locally"></a>API をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](../azure-functions/functions-run-local.md) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

> [!TIP]
> 続行する前に、[前提条件](#prerequisites)セクションに記載されているすべてのリソースがインストールされていることを確認してください。

1. **F5** キーを押して関数を実行し、Functions アプリを起動します。

1. Azure Functions Core Tools がまだインストールされていない場合は、プロンプトで **[インストール]** を選択します。

    Core Tools では、実行中のアプリケーションの出力が _[TERMINAL]\(ターミナル\)_ パネルに表示されます。 出力の一部として、HTTP によってトリガーされる関数の URL エンドポイントがローカルで実行されていることを確認できます。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="スクリーンショットには、U R L が表示されている [TERMINAL]\(ターミナル\) タブが示されています。":::

1. Core Tools が実行されている状態で、次の URL に移動して、API が正しく実行されていることを確認します。`http://localhost:7071/api/message`

   ブラウザーの応答は、次の例のようになります。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt text スクリーンショットには、ブラウザー上のテキスト応答が示されています。":::

1. デバッグ セッションを停止するには、**Shift + F5** キーを押します。

### <a name="call-the-api-from-the-application"></a>アプリケーションから API を呼び出す

Azure にデプロイすると、API への要求は、`api` ルートに送信される要求については Functions アプリに自動的にルーティングされます。 ローカルで作業する場合は、ローカル API に要求をプロキシするようにアプリケーション設定を構成する必要があります。

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>API にアクセスするための HTML ファイルを更新する

1. 次に、以下のコードを使用して _index.html_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading content from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

1. **F5** キーを押して、API プロジェクトを開始します。

1. **F1** キーを押し、**Live Server:Open with Live Server** を選択して Live Server を起動します。

    これで、Web ページに API メッセージが表示されます。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="スクリーンショットには、ブラウザー上の A P I メッセージが示されています。":::

   > [!NOTE]
   > 他の HTTP サーバーまたはプロキシを使用して、`index.html` ファイルを処理できます。 `file:///` から `index.html` にアクセスすることはできません。

1. **Shift + F5** キーを押して、API プロジェクトを停止します。

### <a name="commit-and-push-your-changes-to-github"></a>変更をコミットして GitHub にプッシュする

Visual Studio Code を使用して、変更をコミットし、リモート Git リポジトリにプッシュします。

1. **F1** キーを押してコマンド パレットを開きます。
1. 「**Git:Commit All**」と入力します
1. コミット メッセージを追加して **Enter** キーを押します
1. **F1** キーを押します
1. 「**Git: push**」と入力し、**Enter** キーを押します

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** をクリックします
1. 「**Static Web App**」で検索します
1. **[Static Web App (Preview)]\(静的 Web アプリ (プレビュー)\)** をクリックします
1. **[作成]** をクリックします。

次に、アプリ固有の設定を追加します。

1. お使いの "_Azure サブスクリプション_" を選択します
1. "_リソース グループ_" を選択するか、新しく作成します
1. アプリに **my-vanilla-api** という名前を付けます
1. 最も近い "_リージョン_" を選択します
1. **[無料]** _SKU_ を選択します
1. **[GitHub でサインイン]** ボタンをクリックし、GitHub で認証します
1. 希望する "_組織_" を選択します
1. **[リポジトリ]** ドロップダウンから _my-vanilla-api_ を選択します
1. _[ブランチ]_ ドロップダウンから **[main]** を選択します
1. _[Build Presets]\(ビルド プリセット\)_ ドロップダウンから、ビルド構成に対して任意のフレームワークを選択します。

 > これらのフィールドは、アプリの種類の既定のプロジェクト構造を反映します。 アプリに合わせて値を変更します。

次に、次のビルドの詳細を追加します。

1. _[App location]\(アプリの場所\)_ に「 **/** 」と入力します。
1. _[Api location]\(Api の場所\)_ ボックスに「**api**」と入力します。
1. _[App artifact location]\(アプリ成果物の場所\)_ から既定値をクリアし、このボックスを空のままにします。
1. **[Review + create]\(レビュー + 作成\)** をクリックします。
1. **[作成]** ボタンをクリックします

    _[作成]_ ボタンをクリックすると、Azure によって 2 つの処理が行われます。 まず、アプリをサポートするように基盤となるクラウド サービスが作成されます。 次に、バックグラウンド プロセスによって、アプリケーションのビルドと配置が開始されます。

1. **[リソースに移動]** ボタンをクリックして、Web アプリの "_概要_" ページに移動します。

    アプリがバックグラウンドでビルドされているときに、リンクを含むバナーをクリックして、ビルドの状態を表示できます。

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="GitHub ワークフロー":::

1. 配置が完了したら、"_概要_" ページの _[URL]_ リンクをクリックすると、Web アプリに移動できます。

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Azure portal から静的アプリの URL にアクセスする":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを今後の使用のために保持したくない場合は、次の手順に従って、Azure の静的 Web アプリとその関連リソースを削除できます。

1. [Azure Portal](https://portal.azure.com) に移動します
1. 上部の検索バーで、 「**リソース グループ**」 と入力します
1. **[リソース グループ]** をクリックします
1. **[myResourceGroup]** を選択します
1. _[myResourceGroup]_ ページで、一覧表示されたリソースが、削除しようとするリソースであることを確認します。
1. **[削除]** を選択します
1. テキスト ボックスに「**myResourceGroup**」と入力します
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](./application-settings.md)
