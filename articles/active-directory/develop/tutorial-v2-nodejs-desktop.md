---
title: チュートリアル:Electron デスクトップ アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインを処理すると共に、認証コード フローを使用して Microsoft ID プラットフォームからアクセス トークンを取得し、Microsoft Graph API を呼び出すことができる Electron デスクトップ アプリを構築します。
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644291"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>チュートリアル:Electron デスクトップ アプリでユーザーのサインインと Microsoft Graph API の呼び出しを行う

このチュートリアルでは、ユーザーのサインインを行い、PKCE による認可コード フローを使用して Microsoft Graph を呼び出す Electron デスクトップ アプリケーションを構築します。 構築するデスクトップ アプリでは、[Node.js 用の Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) を使用します。

このチュートリアルでは、次の手順に従います。

> [!div class="checklist"]
> - Azure portal でアプリケーションを登録する
> - Electron デスクトップ アプリ プロジェクトを作成する
> - アプリに認証ロジックを追加する
> - Web API を呼び出すメソッドを追加する
> - アプリの登録の詳細を追加する
> - アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

## <a name="register-the-application"></a>アプリケーションを登録する

まず、[Microsoft ID プラットフォームへのアプリケーションの登録](quickstart-register-app.md)に関するページの手順に従って、アプリを登録します。

アプリの登録には、次の設定を使用します。

- 名前: `ElectronDesktopApp` (推奨)
- サポートされているアカウントの種類: **任意の組織のディレクトリ (Azure AD ディレクトリ - マルチテナント) 内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox など)**
- プラットフォームの種類:**モバイル アプリケーションとデスクトップ アプリケーション**
- リダイレクト URI: `msal://redirect`

## <a name="create-the-project"></a>プロジェクトを作成する

アプリケーションをホストするフォルダーを作成します (例: *ElectronDesktopApp*)。

1. 最初に、ターミナル内のプロジェクト ディレクトリに移動し、次の `npm` コマンドを実行します。

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. 次に、*App* という名前のフォルダーを作成します。 このフォルダー内に、UI として機能する *index.html* という名前のファイルを作成します。 そこに、次のコードを追加します。

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. 次に、*main.js* という名前のファイルを作成し、次のコードを追加します。

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

上記のコード スニペットでは、Electron メイン ウィンドウ オブジェクトを初期化し、Electron ウィンドウとやり取りするためのイベント ハンドラーをいくつか作成します。 また、構成パラメーターをインポートし、サインイン、サインアウト、トークンの取得を処理するための *authProvider* クラスを初期化して、Microsoft Graph API を呼び出します。

4. 同じフォルダー (*App*) 内に、*renderer.js* という名前の別のファイルを作成し、次のコードを追加します。

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. 最後に、アプリケーションの **イベント** を記述するための文字列定数を格納する *constants.js* という名前のファイルを作成します。

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

これで、Electron アプリのシンプルな GUI と対話が作成できました。 チュートリアルの残りの部分を完了すると、このプロジェクトのファイルとフォルダーの構造は次のようになります。

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>アプリに認証ロジックを追加する

*App* フォルダーに、*AuthProvider.js* という名前のファイルを作成します。 ここには、MSAL Node を使用して、ログイン、ログアウト、トークンの取得、アカウントの選択、および関連する認証タスクを処理する認証プロバイダー クラスを含めます。 そこに、次のコードを追加します。

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

上記のコード スニペットでは、まず、構成オブジェクト (`msalConfig`) を渡すことによって MSAL Node `PublicClientApplication` を初期化しました。 次に、メイン モジュール (*main.js*) によって呼び出される `login`、`logout`、`getToken` の各メソッドを公開しました。 `login` と `getToken` では、まず認可コードを要求し、次に MSAL Node `acquireTokenByCode` パブリック API を使用してこれをトークンと交換することにより、それぞれ ID トークンとアクセス トークンを取得します。

## <a name="add-a-method-to-call-a-web-api"></a>Web API を呼び出すメソッドを追加する

*fetch.js* という名前の別のファイルを作成します。 このファイルには、Microsoft Graph API に対して REST 呼び出しを行うための Axios HTTP クライアントを含めます。

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>アプリの登録の詳細を追加する

最後に、トークンを取得するときに使用されるアプリ登録の詳細を格納するための環境ファイルを作成します。 これを行うには、サンプルのルート フォルダー (*ElectronDesktopApp*) 内に *.env* という名前のファイルを作成し、次のコードを追加します。

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

これらの詳細には、Azure アプリ登録ポータルから取得した値を入力します。

- `Enter_the_Tenant_Id_here` は、次のいずれかにする必要があります。
  - ご自分のアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **テナント ID** または **テナント名** に置き換えます。 たとえば、「 `contoso.microsoft.com` 」のように入力します。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" がサポートされる場合は、この値を `common` に置き換えます。
  - "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
- `Enter_the_Application_Id_Here`:登録したアプリケーションの **アプリケーション (クライアント) ID**。
- `Enter_the_Cloud_Instance_Id_Here`:アプリケーションが登録されている Azure クラウド インスタンス。
  - メイン ("*グローバル*") Azure クラウドの場合は、「`https://login.microsoftonline.com/`」と入力します。
  - **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」に適切な値が記載されています。
- `Enter_the_Graph_Endpoint_Here` は、アプリケーションが通信する必要がある、Microsoft Graph API のインスタンスです。
  - **グローバル** Microsoft Graph API エンドポイントの場合は、この文字列の両方のインスタンスを `https://graph.microsoft.com/` に置き換えます。
  - **各国** のクラウドのデプロイにおけるエンドポイントの場合は、Microsoft Graph のドキュメントで「[各国のクラウドでのデプロイ](/graph/deployments)」を参照してください。

## <a name="test-the-app"></a>アプリをテストする

これでアプリケーションの作成が完了し、Electron デスクトップ アプリを起動して、アプリの機能をテストする準備ができました。

1. プロジェクト フォルダーのルート内から次のコマンドを実行して、アプリを起動します。

```console
electron App/main.js
```

2. アプリケーションのメイン ウィンドウには、*index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

## <a name="test-sign-in-and-sign-out"></a>サインインとサインアウトをテストする

*index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォームにサインインするように求められます。

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="サインイン プロンプト":::

要求されたアクセス許可に同意すると、Web アプリケーションにはユーザー名が表示されます。これは、ログインが成功したことを示しています。

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="サインインに成功":::

## <a name="test-web-api-call"></a>Web API 呼び出しをテストする

サインインした後、 **[See Profile]** を選択して、Microsoft Graph API への呼び出しからの応答で返されるユーザー プロファイル情報を表示します。

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="Microsoft Graph からのプロファイル情報":::

**[Read Mails]** を選択して、ユーザーのアカウント内のメッセージを表示します。 同意画面が表示されます。

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="read.mail アクセス許可の同意画面":::

同意すると、Microsoft Graph API への呼び出しからの応答で返されるメッセージが表示されます。

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Microsoft Graph からのメール情報":::

## <a name="how-the-application-works"></a>アプリケーションの動作

ユーザーが初めて **[サインイン]`getTokenInteractive` ボタンを選択すると、*AuthProvider.js* の get**  メソッドが呼び出されます。 このメソッドは、"*Microsoft ID プラットフォーム エンドポイント*" を使用してユーザーをサインインにリダイレクトし、ユーザーの資格情報を検証して、**認可コード** を取得します。 次に、このコードは、MSAL Node の `acquireTokenByCode` パブリック API を使用してアクセス トークンと交換されます。

この時点で、PKCE で保護された認証コードが CORS によって保護されたトークン エンドポイントに送信され、トークンと交換されます。 アプリケーションによって ID トークン、アクセス トークン、および更新トークンが受信され、MSAL Node によって処理されて、トークンに含まれる情報がキャッシュされます。

ID トークンには、表示名など、ユーザーについての基本的な情報が含まれています。 アクセス トークンの有効期間は限られており、24 時間後に有効期限が切れます。 保護されたリソースにアクセスするためにこれらのトークンを使用する予定がある場合は、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、バックエンド サーバーでトークンを検証する "*必要があります*"。

このチュートリアルで作成したデスクトップ アプリでは、アクセス トークンを要求ヘッダーのベアラー トークン ([RFC 6750](https://tools.ietf.org/html/rfc6750)) として使用して、Microsoft Graph API への REST 呼び出しを行います。

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 既定では、このスコープは、Azure portal に登録されているすべてのアプリケーションに自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API では、ユーザーのメールを一覧表示するために *Mail.Read* スコープが必要です。

スコープを追加すると、追加したスコープに対して追加の同意を求めるメッセージがユーザーに表示される場合があります。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順

Microsoft ID プラットフォームでの Node.js および Electron デスクトップ アプリケーションの開発についてさらに詳しく知りたい場合は、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ:Web API を呼び出すデスクトップ アプリ](scenario-desktop-overview.md)
