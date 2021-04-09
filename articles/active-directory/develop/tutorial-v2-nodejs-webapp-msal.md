---
title: チュートリアル:Node.js + Express Web アプリにユーザーをサインインさせる | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Web アプリでのユーザーのサインインのサポートを追加します。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648992"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>チュートリアル:Node.js + Express Web アプリにユーザーをサインインさせる

このチュートリアルでは、ユーザーをサインインさせる Web アプリを作成します。 作成する Web アプリでは、[Node 用の Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) を使用します。

このチュートリアルでは、次の手順に従います。

> [!div class="checklist"]
> - Azure portal でアプリケーションを登録する
> - Express Web アプリ プロジェクトを作成する
> - 認証ライブラリ パッケージをインストールする
> - アプリの登録の詳細を追加する
> - ユーザー ログインのコードを追加する
> - アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

## <a name="register-the-application"></a>アプリケーションを登録する

まず、[Microsoft ID プラットフォームへのアプリケーションの登録](quickstart-register-app.md)に関するページの手順に従って、アプリを登録します。

アプリの登録には、次の設定を使用します。

- 名前: `ExpressWebApp` (推奨)
- サポートされているアカウントの種類: **任意の組織のディレクトリ (Azure AD ディレクトリ - マルチテナント) 内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox など)**
- プラットフォームの種類:**Web**
- リダイレクト URI: `http://localhost:3000/redirect`
- クライアント シークレット: `*********` (後の手順で使用するためにこの値を記録します。これは 1 回しか表示されません)

## <a name="create-the-project"></a>プロジェクトを作成する

アプリケーションをホストするフォルダーを作成します (例: *ExpressWebApp*)。

1. 最初に、ターミナル内のプロジェクト ディレクトリに移動し、次の `npm` コマンドを実行します。

```console
    npm init -y
    npm install --save express
```

2. 次に、*index.js* という名前のファイルを作成し、次のコードを追加します。

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

これで、ポート 3000 で実行される簡単な Web サーバーが完成しました。 このプロジェクトのファイルとフォルダーの構造は次のようになります。

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>認証ライブラリをインストールする

ターミナルでプロジェクト ディレクトリのルートを見つけ、NPM を使用して MSAL Node パッケージをインストールします。

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>アプリの登録の詳細を追加する

先ほど作成した *index.js* ファイルに、次のコードを追加します。

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

これらの詳細には、Azure アプリ登録ポータルから取得した値を入力します。

- `Enter_the_Tenant_Id_here` は、次のいずれかにする必要があります。
  - ご自分のアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **テナント ID** または **テナント名** に置き換えます。 たとえば、「 `contoso.microsoft.com` 」のように入力します。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" がサポートされる場合は、この値を `common` に置き換えます。
  - "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
- `Enter_the_Application_Id_Here`:登録したアプリケーションの **アプリケーション (クライアント) ID**。
- `Enter_the_Cloud_Instance_Id_Here`:アプリケーションが登録されている Azure クラウド インスタンス。
  - メイン ("*グローバル*") Azure クラウドの場合は、「`https://login.microsoftonline.com`」と入力します。
  - **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」に適切な値が記載されています。
- `Enter_the_Client_secret`:この値を、前に作成したクライアント シークレットで置き換えます。 新しいキーを生成するには、Azure portal でアプリの登録設定の **[証明書とシークレット]** を使用します。

> [!WARNING]
> ソース コードでシークレットがプレーンテキストになっていると、セキュリティ リスクが増大します。 この記事でプレーンテキストのクライアント シークレットを使用しているのは、あくまで簡潔にするためです。 機密性の高いクライアント アプリケーション、特に運用環境へのデプロイを予定しているアプリでは、クライアント シークレットではなく、[証明書の資格情報](active-directory-certificate-credentials.md)を使用してください。

## <a name="add-code-for-user-login"></a>ユーザー ログインのコードを追加する

先ほど作成した *index.js* ファイルに、次のコードを追加します。

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>サインインをテストする

これでアプリケーションの作成が完了し、アプリの機能をテストする準備ができました。

1. プロジェクト フォルダーのルート内から次のコマンドを実行して、Node.js コンソール アプリを起動します。

```console
   node index.js
```

2. ブラウザー ウィンドウを開き、`http://localhost:3000` に移動します。 サインイン画面が表示されます。

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="表示される Azure AD サインイン画面":::

3. 資格情報を入力すると、アプリのアクセス許可を承認するよう求める同意画面が表示されます。

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="表示される Azure AD 同意画面":::

## <a name="how-the-application-works"></a>アプリケーションの動作

このチュートリアルでは、Azure portal で Azure AD アプリの登録から取得したパラメーターを含む構成オブジェクト (*msalConfig*) を渡すことによって、MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) オブジェクトを初期化しました。 作成した Web アプリでは、[OAuth 2.0 承認コード付与フロー](./v2-oauth2-auth-code-flow.md)を使用してユーザーをサインインさせ、ID とアクセス トークンを取得します。

## <a name="next-steps"></a>次の手順

Microsoft ID プラットフォームでの Node.js + Express Web アプリケーションの開発についてさらに詳しく知りたい場合は、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ: ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)