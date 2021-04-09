---
title: チュートリアル:認証コード フローを使用する JavaScript シングルページ アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、ユーザーのサインインを処理すると共に、認証コード フローを使用して Microsoft ID プラットフォームからアクセス トークンを取得し、Microsoft Graph API を呼び出すことができる JavaScript SPA を作成します。
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 1ec046ca6b42a5ca8f33b0347c562c85abd42684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756178"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>チュートリアル:認証コード フローを使用して、ユーザーをサインインさせ、JavaScript シングルページ アプリ (SPA) から Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを行い、PKCE による認証コード フローを使用して Microsoft Graph を呼び出す JavaScript シングルページ アプリケーション (SPA) を構築します。 構築する SPA では、JavaScript v2.0 用の Microsoft Authentication Library (MSAL) を使用します。

このチュートリアルの内容:
> [!div class="checklist"]
> * PKCE を使用した、OAuth 2.0 認証コード フローの実行
> * 個人用 Microsoft アカウントと職場または学校アカウントへのサインイン
> * アクセス トークンの取得
> * Microsoft ID プラットフォームから取得したアクセス トークンを必要とする Microsoft Graph または独自 API の呼び出し

MSAL.js 2.0 は、ブラウザーで暗黙的な許可のフローではなく承認コード フローをサポートすることで、MSAL.js 1.0 よりも強化されています。 MSAL.js 2.0 では、暗黙的フローはサポートされて **いません**。

## <a name="prerequisites"></a>前提条件

* ローカル Web サーバーを実行するための [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) または別のコード エディター

## <a name="how-the-tutorial-app-works"></a>チュートリアル アプリの動作

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="シングルページ アプリケーションでの認証コード フローを示す図":::

このガイドで作成するアプリケーションにより、JavaScript SPA では、Microsoft ID プラットフォームからセキュリティ トークンを取得して、Microsoft Graph API に対してクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、HTTP 要求の Authorization ヘッダーに追加されます。 トークンの取得と更新は、Microsoft Authentication Library for JavaScript (MSAL.js) によって処理されます。

このチュートリアルでは、次のライブラリを使用します。

[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) JavaScript v2.0 ブラウザー パッケージ用の Microsoft Authentication Library

## <a name="get-the-completed-code-sample"></a>完成したコード サンプルを入手する

代わりに、このチュートリアルの完成したサンプル プロジェクトをダウンロードすることもできます。 Node.js などのローカル Web サーバーを使用してプロジェクトを実行するには、[ms-identity-javascript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) リポジトリを複製します。

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

その後、コード サンプルを実行前に構成するために、[構成手順](#register-your-application)に進みます。

チュートリアルを続行してアプリケーションを自分でビルドする場合は、次のセクション「[前提条件](#prerequisites)」に進みます。

## <a name="create-your-project"></a>プロジェクトを作成する

[Node.js](https://nodejs.org/en/download/) のインストール後、アプリケーションをホストするフォルダー (たとえば *msal-spa-tutorial*) を作成します。

次に、*index.html* ファイルを提供する小規模な [Express](https://expressjs.com/) Web サーバーを実装します。

1. 最初に、ターミナル内のプロジェクト ディレクトリに移動し、次の `npm` コマンドを実行します。
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. 次に、*server.js* という名前のファイルを作成し、次のコードを追加します。

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

これで、SPA を提供する小規模な Web サーバーができました。 チュートリアルの残りの部分を完了すると、このプロジェクトのファイルとフォルダーの構造は次のようになります。

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>SPA UI を作成する

1. プロジェクト ディレクトリに *app* フォルダーを作成し、その中に JavaScript SPA 用の *index.html* ファイルを作成します。 このファイルは、**Bootstrap 4 Framework** で作成された UI を実装し、構成、認証、および API 呼び出しのためのスクリプト ファイルをインポートします。

    *Index.html* ファイルに、次のコードを追加します。

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. 次に、再び *app* フォルダーで、*ui.js* という名前のファイルを作成し、次のコードを追加します。 このファイルは、DOM 要素にアクセスして更新します。

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>アプリケーションの登録

「[シングルページ アプリケーション: アプリの登録](scenario-spa-app-registration.md)」の手順に従って、SPA のアプリ登録を作成します。

「[リダイレクト URI: MSAL.js 2.0 と認証コード フロー](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow)」の手順で、このチュートリアルのアプリケーションを実行する既定の場所である「`http://localhost:3000`」を入力します。

別のポートを使用する場合は、「`http://localhost:<port>`」と入力します。ここで、`<port>` は使用する TCP ポートの番号です。 `3000` 以外のポート番号を指定した場合は、必要に応じて *server.js* も、使用するポート番号で更新します。

### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

*app* フォルダー内に *authConfig.js* という名前のファイルを作成し、認証用の構成パラメーターを記述します。さらに、次のコードを追加します。

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

次の説明に従って、`msalConfig` セクションの値を変更します。

- `Enter_the_Application_Id_Here`:登録したアプリケーションの **アプリケーション (クライアント) ID**。
- `Enter_the_Cloud_Instance_Id_Here`:アプリケーションが登録されている Azure クラウド インスタンス。
  - メイン ("*グローバル*") Azure クラウドの場合は、「`https://login.microsoftonline.com`」と入力します。
  - **各国** のクラウド (中国など) の場合は、「[各国のクラウド](authentication-national-cloud.md)」に適切な値が記載されています。
- `Enter_the_Tenant_info_here` は、次のいずれかにする必要があります。
  - ご自分のアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **テナント ID** または **テナント名** に置き換えます。 たとえば、「 `contoso.microsoft.com` 」のように入力します。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を `organizations` に置き換えます。
  - アプリケーションで "*任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント*" がサポートされる場合は、この値を `common` に置き換えます。
  - "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を `consumers` に置き換えます。
- `Enter_the_Redirect_Uri_Here` は `http://localhost:3000` です。

グローバル Azure クラウドを使用している場合、*authConfig.js* の `authority` の値は、次のようになります。

```javascript
authority: "https://login.microsoftonline.com/common",
```

引き続き *app* フォルダー内で、*graphConfig.js* という名前のファイルを作成します。 次のコードを追加して、Microsoft Graph API を呼び出すための構成パラメーターをアプリケーションに指定します。

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

次の説明に従って、`graphConfig` セクションの値を変更します。

- `Enter_the_Graph_Endpoint_Here` は、アプリケーションが通信する必要がある、Microsoft Graph API のインスタンスです。
  - **グローバル** Microsoft Graph API エンドポイントの場合は、この文字列の両方のインスタンスを `https://graph.microsoft.com` に置き換えます。
  - **各国** のクラウドのデプロイにおけるエンドポイントの場合は、Microsoft Graph のドキュメントで「[各国のクラウドでのデプロイ](/graph/deployments)」を参照してください。

グローバル エンドポイントを使用している場合、*graphConfig.js* 内の `graphMeEndpoint` および `graphMailEndpoint` の値は、次のようにします。

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

### <a name="pop-up"></a>ポップアップ

*app* フォルダーで *authPopup.js* という名前のファイルを作成し、ログイン ポップアップ用に次の認証とトークン取得のコードを追加します。

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>リダイレクト

*app* フォルダーに *authRedirect.js* という名前のファイルを作成し、ログイン リダイレクト用に次の認証とトークン取得コードを追加します。

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>コードの動作

ユーザーが初めて **[Sign In]** ボタンを選択すると、`signIn` メソッドによって、ユーザーがサインインするための `loginPopup` が呼び出されます。 `loginPopup` メソッドによって、"*Microsoft ID プラットフォーム エンドポイント*" でポップアップ ウィンドウが開き、ユーザーの資格情報が要求されて検証が行われます。 サインインに成功すると、*msal.js* によって [認証コード フロー](v2-oauth2-auth-code-flow.md)が開始されます。

この時点で、PKCE で保護された認証コードが CORS によって保護されたトークン エンドポイントに送信され、トークンと交換されます。 アプリケーションによって ID トークン、アクセス トークン、および更新トークンが受信され、*msal.js* によって処理されて、トークンに含まれる情報がキャッシュされます。

ID トークンには、表示名など、ユーザーについての基本的な情報が含まれています。 ID トークンによって提供されるデータを使用する予定がある場合は、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、バックエンド サーバーはトークンを検証する "*必要があります*"。

アクセス トークンの有効期間は限られており、24 時間後に有効期限が切れます。 更新トークンは、新しいアクセス トークンを自動的に取得するために使用できます。

このチュートリアルで作成した SPA は、`acquireTokenSilent`、`acquireTokenPopup`、またはその両方を呼び出して、Microsoft Graph API に対するユーザー プロファイル情報の照会に使用される "*アクセス トークン*" を取得します。 ID トークンを検証するサンプルが必要な場合は、GitHub の [active-directory-javascript-singlepageapp-dotnet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) サンプル アプリケーションを参照してください。 このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後は、保護されたリソースへのアクセスが必要になるたびに、アプリがユーザーに再認証を行うように要求する (つまり、トークンを要求する) べきではありません。 このような再認証要求を防ぐには、`acquireTokenSilent` を呼び出します。 ただし、ユーザーに Microsoft ID プラットフォームとのやり取りを強制しなければならない場合があります。 次に例を示します。

- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
- アプリケーションがリソースへのアクセスを要求し、ユーザーの同意が必要である。
- 2 要素認証が必須である。

`acquireTokenPopup` を呼び出すと、ポップアップ ウィンドウが表示されます (または、`acquireTokenRedirect` によってユーザーが Microsoft ID プラットフォームにリダイレクトされます)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginPopup` (または `loginRedirect`) を実行した後、後続の呼び出しでは、通常、`acquireTokenSilent` メソッドを使用して、保護されたリソースにアクセスするためのトークンを取得します (トークンを要求または更新するための呼び出しは自動的に行われます)。場合によっては、`acquireTokenSilent` が失敗することがあります。 たとえば、ユーザーのパスワードの期限が切れている場合です。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1. すぐに `acquireTokenPopup` を呼び出し、ユーザー サインイン プロンプトをトリガーします。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。
1. ユーザーが適切なタイミングでサインインできるようにするか、アプリケーションが後で `acquireTokenSilent` を再試行できるように、ユーザーに対して対話形式でのサインインが必要であることを視覚的に示します。 他に中断なく使用できる機能がアプリケーションにある場合は、一般に、この方法が使用されます。 たとえば、使用可能な非認証コンテンツがアプリケーションに含まれている場合が考えられます。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

> [!NOTE]
> このチュートリアルでは、既定で `loginPopup` および `acquireTokenPopup` メソッドを使用します。 Internet Explorer を使用している場合は、Internet Explorer とポップアップ ウィンドウに[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるため、`loginRedirect` および `acquireTokenRedirect` メソッドを使用することをお勧めします。 リダイレクト メソッドを使用して同じ結果を実現する例については、GitHub の [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) を参照してください。

## <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す

*app* フォルダーに *graph.js* という名前のファイルを作成し、Microsoft Graph API への REST 呼び出しを行うために、次のコードを追加します。

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

このチュートリアルで作成するサンプル アプリケーションでは、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このチュートリアルで作成したサンプル アプリケーションでは、保護されているリソースは、サインイン ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

## <a name="test-your-application"></a>アプリケーションのテスト

これでアプリケーションの作成が完了し、Node.js Web サーバーを起動して、アプリの機能をテストする準備ができました。

1. プロジェクト フォルダーのルート内から次のコマンドを実行して、Node.js Web サーバーを起動します。

   ```console
   npm start
   ```
1. ブラウザーで `http://localhost:3000` または `http://localhost:<port>` に移動します。ここで、`<port>` は、Web サーバーがリッスンしているポートです。 *index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

### <a name="sign-in-to-the-application"></a>アプリケーションにサインインする

ブラウザーに *index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォームにサインインするように求められます。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="サインイン ダイアログを表示している Web ブラウザー":::

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスを許可してサインインすることを求められます。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="Web ブラウザーに表示されているコンテンツ ダイアログ":::

要求されたアクセス許可に同意すると、Web アプリケーションにはユーザー名が表示されます。これは、ログインが成功したことを示しています。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="Web ブラウザーでの成功したサインインの結果":::

### <a name="call-the-graph-api"></a>Graph API を呼び出す

サインインした後、 **[See Profile]** を選択して、Microsoft Graph API への呼び出しからの応答で返されるユーザー プロファイル情報を表示します。

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="ブラウザーに表示される Microsoft Graph からのプロファイル情報":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 既定では、このスコープは、Azure portal に登録されているすべてのアプリケーションに自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API では、ユーザーのメールを一覧表示するために *Mail.Read* スコープが必要です。

スコープを追加すると、追加したスコープに対して追加の同意を求めるメッセージがユーザーに表示される場合があります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出しでのスコープとして `clientId` を使用できます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームでの JavaScript シングルページ アプリケーションの開発についてさらに詳しく知りたい場合は、複数のパートで構成される次のシナリオ シリーズを参照してください。

> [!div class="nextstepaction"]
> [シナリオ:シングルページ アプリ](scenario-spa-overview.md)