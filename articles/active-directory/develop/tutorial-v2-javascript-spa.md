---
title: チュートリアル:認証に Microsoft ID プラットフォームを使用する JavaScript シングルページ アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Microsoft ID プラットフォームを使用してユーザーのサインインを処理する JavaScript シングルページ アプリ (SPA) を作成し、アクセス トークンを取得して、そのユーザーに代わって Microsoft Graph API を呼び出します。
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: dab4ac22a8b0da927f05376755463885ce32c343
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103007"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>チュートリアル:ユーザーをサインインして、JavaScript シングルページ アプリケーション (SPA) から Microsoft Graph API を呼び出す

このチュートリアルでは、ユーザーのサインインを行い、暗黙的なフローを使用して Microsoft Graph を呼び出す JavaScript シングルページ アプリケーション (SPA) を構築します。 構築する SPA では、JavaScript v1.0 用の Microsoft Authentication Library (MSAL) を使用します。

このチュートリアルの内容:

> [!div class="checklist"]
> * `npm` を使用して JavaScript プロジェクトを作成する
> * Azure portal でアプリケーションを登録する
> * ユーザーのサインインとサインアウトをサポートするコードを追加する
> * Microsoft Graph API を呼び出すコードを追加する
> * アプリケーションをテストする

>[!TIP]
> このチュートリアルでは MSAL.js v1.x を使用します。これは、シングルページ アプリケーションに対して暗黙的な許可フローを使用するように制限されています。 すべての新しいアプリケーションでは代わりに、[PKCE および CORS がサポートされる MSAL.js 2.x と承認コード フロー](tutorial-v2-javascript-auth-code.md)を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

* ローカル Web サーバーを実行するための [Node.js](https://nodejs.org/en/download/)。
* プロジェクト ファイルを編集するためのエディター ([Visual Studio Code](https://code.visualstudio.com/download) など)。
* 最新の Web ブラウザー このチュートリアルで作成するアプリでは、[ES6](http://www.ecma-international.org/ecma-262/6.0/) 規則が使用されているため、**Internet Explorer** は **サポートされていません**。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API、または Microsoft ID プラットフォームのトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 このトークンは、**MS Graph API** からユーザーのプロファイルとメールを取得する際に使用します。

トークンの取得と更新は、[Microsoft Authentication Library (MSAL) for JavaScript](https://github.com/AzureAD/microsoft-authentication-library-for-js) で処理されます。

## <a name="set-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルのプロジェクトをダウンロードすることもできます。 [プロジェクト ファイルのダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
>
> コード サンプルを実行する前に構成する場合は、[構成手順](#register-your-application)に進んでください。

## <a name="create-your-project"></a>プロジェクトを作成する

[Node.js](https://nodejs.org/en/download/) がインストールされていることを確認し、アプリケーションをホストするフォルダーを作成します。 そこに、`index.html` ファイルを提供する簡単な [Express](https://expressjs.com/) Web サーバーを実装します。

1. ターミナル (Visual Studio Code 統合ターミナルなど) を使用して、プロジェクト フォルダーを検索し、次のように入力します。

   ```console
   npm init
   ```

2. 次に、必要な依存関係をインストールします。

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. `server.js` という名前の .js ファイルを作成し、次のコードを追加します。

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

これで、SPA を提供する簡単なサーバーが完成しました。 このチュートリアルの最後に完成する予定のフォルダー構造は次のとおりです。

![完成予定の SPA フォルダー構造を表すテキスト](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>SPA UI を作成する

1. JavaScript SPA の `index.html` ファイルを作成します。 このファイルは、**Bootstrap 4 Framework** で作成された UI を実装し、構成、認証、API 呼び出しのためのスクリプト ファイルをインポートします。

   `index.html` ファイルに、次のコードを追加します。

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
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

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > 上記のスクリプトにある MSAL.js のバージョンを、[MSAL.js リリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)の最新のリリース バージョンに置き換えることができます。

2. 次に、DOM 要素にアクセスして更新する `ui.js` という名前の .js ファイルを作成し、次のコードを追加します。

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
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

認証に進む前に、アプリケーションを **Azure Active Directory** に登録します。

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
1. アプリケーションの **[名前]** を入力します。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. **[リダイレクト URI]** セクションで、ドロップダウン リストから **Web** プラットフォームを選択し、お使いの Web サーバーに基づいたアプリケーション URL に値を設定します。
1. **[登録]** を選択します。
1. 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
1. **[管理]** で、 **[認証]** を選択します。
1. **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** セクションで、 **[ID トークン]** と **[アクセス トークン]** を選択します。 このアプリではユーザーのサインインを実行して API を呼び出す必要があるため、ID トークンとアクセス トークンが必要です。
1. **[保存]** を選択します。

> ### <a name="set-a-redirect-url-for-nodejs"></a>Node.js でリダイレクト URL を設定する
>
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、ポート 3000 を使用しますが、使用可能なその他の任意のポートを使用できます。
>
> アプリケーション登録情報の中にリダイレクト URL を設定するには、 **[アプリケーションの登録]** ウィンドウに切り替え、以下のいずれかを行います。
>
> - **リダイレクト URL** として *`http://localhost:3000/`* を設定します。
> - カスタム TCP ポートを使用している場合は、 *`http://localhost:<port>/`* を使用します (ここで、 *\<port>* はカスタム TCP ポート番号です)。
>   1. **[URL]** の値をコピーします。
>   1. **[アプリケーション登録]** ウィンドウに切り替え、コピーした値を **リダイレクト URL** として貼り付けます。
>

### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

`authConfig.js` という名前の新しい .js ファイルを作成します。このファイルには、認証用の構成パラメーターを記述することになります。次のコードを追加します。

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 各値の説明:
 - *\<Enter_the_Application_Id_Here>* は、登録したアプリケーションの **アプリケーション (クライアント) ID** です。
 - *\<Enter_the_Cloud_Instance_Id_Here>* は、Azure クラウドのインスタンスです。 メイン (グローバル) Azure クラウドの場合は、単に「 *https://login.microsoftonline.com* 」と入力します。 **各国** のクラウド (中国など) の場合は、「[各国のクラウド](./authentication-national-cloud.md)」を参照してください。
 - *\<Enter_the_Tenant_info_here>* には、次のオプションのいずれかを設定します。
   - アプリケーションで "*この組織のディレクトリ内のアカウントのみ*" がサポートされる場合は、この値を **テナント ID** または **テナント名** (例: *contoso.microsoft.com*) に置き換えます。
   - アプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます。
   - アプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

`authPopup.js` という名前の新しい .js ファイルを作成します。このファイルには、認証とトークン取得のロジックを記述することになります。次のコードを追加します。

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>詳細情報

ユーザーが初めて **[Sign In]** ボタンを選択すると、`signIn` メソッドによって、ユーザーがサインインするための `loginPopup` が呼び出されます。 このメソッドによって、"*Microsoft ID プラットフォーム エンドポイント*" のポップアップ ウィンドウが開き、ユーザーの資格情報が要求されて検証が行われます。 サインインに成功すると、ユーザーは元の *index.html* ページにリダイレクトされます。 トークンが受信されて `msal.js` によって処理されると、トークンに含まれる情報がキャッシュされます。 このトークンは *ID トークン* と呼ばれ、ユーザー表示名などのユーザーに関する基本情報が含まれます。 なんらかの目的で、このトークンによって提供されるデータを使用する予定がある場合は、このトークンがアプリケーションの有効なユーザーに対して発行されたことを保証するために、バックエンド サーバーによってトークンが検証されていることを確認します。

このガイドで生成する SPA は、ユーザー プロファイル情報のため、`acquireTokenSilent`、`acquireTokenPopup`、またはその両方を呼び出して、Microsoft Graph API の照会に使用される *アクセス トークン* を取得します。 ID トークンを検証するサンプルが必要な場合は、GitHub で[この](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 サンプル")サンプル アプリケーションを参照してください。 このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="get-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後、リソースにアクセスするためのトークンを要求するたびにユーザーに再認証を求めるのは、あまり好ましくありません。 そこで、ほとんどの場合は、*acquireTokenSilent* を使用してトークンを取得することをお勧めします。 ただし、ユーザーに Microsoft ID プラットフォームとのやり取りを強制する場合があります。 たとえば、次のようになります。

- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
- アプリケーションがリソースへのアクセスを要求し、ユーザーの同意が必要である。
- 2 要素認証が必須である。

*acquireTokenPopup* を呼び出すとポップアップ ウィンドウが開きます (または *acquireTokenRedirect* によって Microsoft ID プラットフォームにユーザーがリダイレクトされます)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

#### <a name="get-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginPopup` (または `loginRedirect`) を実行した後、後続の呼び出しでは、通常、`acquireTokenSilent` メソッドを使用して、保護されたリソースにアクセスするためのトークンを取得します (トークンを要求または更新するための呼び出しは自動的に行われます)。場合によっては、`acquireTokenSilent` が失敗することがあります。 たとえば、ユーザーのパスワードの期限が切れている場合です。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1. すぐに `acquireTokenPopup` を呼び出し、ユーザー サインイン プロンプトをトリガーします。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。

1. ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `acquireTokenSilent` を再試行できるようにする。 他に中断なく使用できる機能がアプリケーションにある場合は、一般に、この方法が使用されます。 たとえば、使用可能な非認証コンテンツがアプリケーションに含まれている場合が考えられます。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

> [!NOTE]
> このクイックスタートでは、既定では `loginPopup` メソッドと `acquireTokenPopup` メソッドを使用します。 ブラウザーとして Internet Explorer を使用している場合は、`loginRedirect` メソッドと `acquireTokenRedirect` メソッドを使用することをお勧めします。Internet Explorer には、ポップアップ ウィンドウの処理に関して[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるためです。 `Redirect methods` を使用して同じ結果を実現する方法については、[こちらを参照](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js)してください。

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

1. まず、`graphConfig.js` という名前の .js ファイルを作成します。このファイルには、REST エンドポイントを記述することになります。 次のコードを追加します。

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   各値の説明:
   - *\<Enter_the_Graph_Endpoint_Here>* は、MS Graph API のインスタンスです。 グローバル MS Graph API エンドポイントの場合は、この文字列を `https://graph.microsoft.com` に置き換えるだけでかまいません。 国内クラウド デプロイの場合は、[Graph API のドキュメント](/graph/deployments)を参照してください。

1. 次に、`graph.js` という名前の .js ファイルを作成します。このファイルには、Microsoft Graph API の REST 呼び出しを記述することになります。次のコードを追加します。

   ```javascript
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
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成するサンプル アプリケーションでは、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

## <a name="test-your-code"></a>コードのテスト

1. *index.html* ファイルの場所に基づく TCP ポートをリッスンするように、サーバーを構成します。 Node.js では、アプリケーション フォルダーからコマンドライン プロンプトで次のコマンドを実行することで、Web サーバーを起動してポートをリッスンします。

   ```bash
   npm install
   npm start
   ```
1. ブラウザーに「 **http://localhost:3000** 」または「 **http://localhost:{port}** 」と入力します。*port* には、実際の Web サーバーのリッスン ポートを指定してください。 *index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

ブラウザーに *index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォームにサインインするように求められます。

![JavaScript SPA アカウント サインイン ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスを許可してサインインすることを求められます。

![[Permissions requested]\(アクセス許可が要求されています\) ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインすると、自分のユーザー プロファイル情報が Microsoft Graph API の応答で返されて表示されます。

![Microsoft Graph API 呼び出しの結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーのメールを表示するための *Mail.Read* スコープが必要です。

> [!NOTE]
> スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

Microsoft ID プラットフォームにおけるシングルページ アプリケーション (SPA) 開発の詳細を、複数のパートから成る一連のシナリオで参照してください。

> [!div class="nextstepaction"]
> [シナリオ:シングルページ アプリ](scenario-spa-overview.md)
