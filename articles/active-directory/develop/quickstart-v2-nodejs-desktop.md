---
title: 'クイックスタート: Node.js デスクトップ アプリケーションから Microsoft Graph を呼び出す | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Node.js Electron デスクトップ アプリケーションでユーザーのサインイン処理を行い、アクセス トークンを取得して、Microsoft ID プラットフォーム エンドポイントによって保護された API を呼び出す方法について説明します。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653271"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>クイックスタート: Electron デスクトップ アプリケーションからアクセス トークンを取得して Microsoft Graph API を呼び出す

このクイックスタートでは、Electron デスクトップ アプリケーションでユーザーのサインイン処理を行い、アクセス トークンを取得して Microsoft Graph API を呼び出す方法を示すコード サンプルをダウンロードして実行します。

このクイックスタートでは、[PKCE を使用した認可コード フロー](v2-oauth2-auth-code-flow.md)で Node.js 用 [Microsoft Authentication Library (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) を使用します。

## <a name="prerequisites"></a>前提条件

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>サンプル アプリケーションを登録してダウンロードする
>
> まず、以下の手順に従ってください。
>
> #### <a name="step-1-register-the-application"></a>手順 1:アプリケーションを登録する
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **名前** を入力します (例: `msal-node-desktop`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[登録]** を選択して、アプリケーションを作成します。
> 1. **[管理]** で、 **[認証]** を選択します。
> 1. **[プラットフォームを追加]**  >  **[モバイル アプリケーションとデスクトップ アプリケーション]** を選択します。
> 1. **[リダイレクト URI]** セクションで、「`msal://redirect`」と入力します。
> 1. **[構成]** をクリックします。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
> このクイックスタートのサンプル コードを動作させるには、応答 URL として **msal://redirect** を追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-windows-desktop/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-electron-sample-project"></a>手順 2: Electron サンプル プロジェクトのダウンロード

> [!div renderon="docs"]
> [コード サンプルをダウンロードします](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>手順 3: Electron サンプル プロジェクトの構成
>
> 1. ディスクのルートに近いローカル フォルダー (例: *C:/Azure-Samples*) に ZIP ファイルを展開します。
> 1. *.env* を編集し、`TENANT_ID` および `CLIENT_ID` フィールドの値を次のスニペットに置き換えます。
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    各値の説明:
>    - `Enter_the_Application_Id_Here` - 登録したアプリケーションの **アプリケーション (クライアント) ID**。
>    - `Enter_the_Tenant_Id_Here` - この値を **テナント ID** または **テナント名** (例: contoso.microsoft.com) に置き換えます。
>
> > [!TIP]
> > **アプリケーション (クライアント) ID** と **ディレクトリ (テナント) ID** の値を見つけるには、Azure portal 上でアプリの **[概要]** ページに移動します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

このサンプルの依存関係を 1 回インストールする必要があります。

```console
npm install
```

次に、コマンド プロンプトまたはコンソールを使用して、アプリケーションを実行します。

```console
npm start
```

**[ サインイン ]** ボタンを備えたアプリケーションの UI が表示されます。

## <a name="about-the-code"></a>コードについて

以降、サンプル アプリケーションのいくつかの重要な要素について説明します。

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) はユーザーのサインインを処理し、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 デスクトップ アプリケーションでの MSAL Node の使用方法の詳細については、[この記事](scenario-desktop-overview.md)を参照してください。

MSAL Node は、次の npm コマンドを実行してインストールできます。

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL Node への参照を追加するには、次のコードを追加します。

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

続いて、次のコードを使用して MSAL を初期化します。

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | 各値の説明: |説明 |
> |---------|---------|
> | `clientId` | Azure portal に登録されているアプリケーションの "**アプリケーション (クライアント) ID**"。 この値は、Azure portal のアプリの **[概要]** ページで確認できます。 |
> | `authority`    | ユーザーが認証するための STS エンドポイント。 通常、パブリック クラウド上では `https://login.microsoftonline.com/{tenant}` です。{tenant} はご自分のテナントの名前またはテナント ID です。|

### <a name="requesting-tokens"></a>トークンの要求

PKCE を使用した認可コード フローの第 1 段階では、適切なパラメーターを指定して認可コード要求を作成し、送信します。 次に、フローの第 2 段階で、認可コードの応答をリッスンします。 コードを取得したら、それと引き換えにトークンを取得します。

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |各値の説明:| 説明 |
> |---------|---------|
> | `authWindow` | 現在実行中の Electron ウィンドウ。 |
> | `tokenRequest` | 要求するスコープを含む (Microsoft Graph 用の `"User.Read"` またはカスタム Web API 用の `"api://<Application ID>/access_as_user"` など) |

## <a name="next-steps"></a>次の手順

MSAL Node を使用した Electron デスクトップ アプリケーション開発については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: Electron デスクトップ アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う](tutorial-v2-nodejs-desktop.md)