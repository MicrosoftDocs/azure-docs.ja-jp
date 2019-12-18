---
title: Microsoft ID プラットフォーム JavaScript シングルページ アプリケーション ガイド | Azure
description: JavaScript SPA アプリケーションで、Azure Active Directory v2.0 エンドポイントからのアクセス トークンを必要とする API を呼び出す方法
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3576c015fcca7eb9c390f38c6527d76353efe06
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919394"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>ユーザーをサインインして、JavaScript シングルページ アプリケーション (SPA) から Microsoft Graph API を呼び出す

このガイドでは、JavaScript のシングルページ アプリケーション (SPA) で次のことを行う方法について説明します。
- 個人用アカウントと職場または学校アカウントへのサインイン 
- アクセス トークンの取得
- *Microsoft ID プラットフォーム エンドポイント*のアクセス トークンを必要とする Microsoft Graph API などの API の呼び出し

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

このガイドで作成したサンプル アプリケーションにより、JavaScript SPA で、Microsoft Graph API、または Microsoft ID プラットフォーム エンドポイントのトークンを受け取る Web API に対してクエリを実行できるようになります。 このシナリオでは、ユーザーのサインイン後に、アクセス トークンが要求され、Authorization ヘッダーを介して HTTP 要求に追加されます。 トークンの取得と更新は、Microsoft Authentication Library (MSAL) で処理されます。

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript プレビュー用の Microsoft Authentication Library|

> [!NOTE]
> *msal.js* は、Microsoft ID プラットフォーム エンドポイントを対象とします。これにより、個人用アカウントと職場または学校アカウントでサインインして、トークンを取得することができます。 Microsoft ID プラットフォーム エンドポイントには[いくつかの制限](azure-ad-endpoint-comparison.md#limitations)があります。
> v1.0 のエンドポイントと v2.0 のエンドポイントの相違点を理解するには、[エンドポイントの比較ガイド](azure-ad-endpoint-comparison.md)を参照してください。

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Web サーバーまたはプロジェクトの設定

> 代わりにこのサンプルのプロジェクトをダウンロードすることもできます。 次のいずれかを実行します。
> 
> - Node.js などのローカル Web サーバーを使用してプロジェクトを実行するには、[プロジェクト ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)します。
>
> - (省略可能) Microsoft インターネット インフォメーション サービス (IIS) サーバーを使用してプロジェクトを実行するには、[Visual Studio プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)します。
>
> コード サンプルを実行する前に構成する場合は、[構成手順](#register-your-application)に進んでください。

## <a name="prerequisites"></a>前提条件

* このチュートリアルを実行するには、[Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core)、[Visual Studio 2017](https://www.visualstudio.com/downloads/) と統合された IIS Express などのローカル Web サーバーが必要です。

* Node.js を使用してプロジェクトを実行する場合は、[Visual Studio Code](https://code.visualstudio.com/download) などの 統合開発環境 (IDE) をインストールしてプロジェクト ファイルを編集します。

* このガイドの手順は Node.js と Visual Studio 2017 の両方に基づいていますが、その他の任意の開発環境または Web サーバーを使用できます。

## <a name="create-your-project"></a>プロジェクトを作成する

> ### <a name="option-1-nodejs-or-other-web-servers"></a>オプション 1:Node.js またはその他の Web サーバー
> [Node.js](https://nodejs.org/en/download/) がインストールされていることを確認し、アプリケーションをホストするフォルダーを作成します。
>
> ### <a name="option-2-visual-studio"></a>オプション 2:Visual Studio
> Visual Studio を使用していて、新しいプロジェクトを作成する場合は、次の手順に従います。
> 1. Visual Studio で、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
> 1. **[Visual C#\Web]** で **[ASP.NET Web アプリケーション (.NET Framework)]** を選択します。
> 1. アプリケーションの名前を入力し、 **[OK]** を選択します。
> 1. **[新しい ASP.NET Web アプリケーション]** で **[空]** を選択します。

## <a name="create-the-spa-ui"></a>SPA UI を作成する
1. JavaScript SPA の *index.html* ファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択します。 右クリックして **[追加]**  >  **[新しい項目]**  >  **[HTML ページ]** の順に選択し、ファイルに *index.html* という名前を付けます。

1. *Index.html* ファイルに、次のコードを追加します。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 上記のスクリプトにある MSAL.js のバージョンを、[MSAL.js リリース](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)の最新のリリース バージョンに置き換えることができます。

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

次のコードを `index.html` ファイルの `<script></script>` タグ内に追加します。

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

ユーザーが初めて **[Sign In]** ボタンを選択すると、`signIn` メソッドによって、ユーザーがサインインするための `loginPopup` が呼び出されます。 このメソッドによって、"*Microsoft ID プラットフォーム エンドポイント*" のポップアップ ウィンドウが開き、ユーザーの資格情報が要求されて検証が行われます。 サインインに成功すると、ユーザーは元の *index.html* ページにリダイレクトされます。 トークンが受信されて `msal.js` によって処理されると、トークンに含まれる情報がキャッシュされます。 このトークンは *ID トークン*と呼ばれ、ユーザー表示名などのユーザーに関する基本情報が含まれます。 何らかの目的のためにこのトークンが提供する任意のデータを使用する予定がある場合、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、このトークンがバックグラウンド サーバーで確実に検証される必要があります。

このガイドで生成する SPA は、ユーザー プロファイル情報のため、`acquireTokenSilent`、`acquireTokenPopup`、またはその両方を呼び出して、Microsoft Graph API の照会に使用される*アクセス トークン*を取得します。 ID トークンを検証するサンプルが必要な場合は、GitHub で[この](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 サンプル")サンプル アプリケーションを参照してください。 このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="getting-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後、リソースにアクセスするためのトークンを要求するたびにユーザーに再認証を求めるのは、あまり好ましくありません。 そこで、ほとんどの場合は、*acquireTokenSilent* を使用してトークンを取得することをお勧めします。 ただし、ユーザーに Microsoft ID プラットフォーム エンドポイントとのやり取りを強制しなければならない場合があります。 たとえば、次のようになります。

- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある。
- アプリケーションがリソースへのアクセスを要求し、ユーザーの同意が必要である。
- 2 要素認証が必須である。

*acquireTokenPopup* を呼び出すとポップアップ ウィンドウが開きます (または *acquireTokenRedirect* によって Microsoft ID プラットフォーム エンドポイントにユーザーがリダイレクトされます)。 ユーザーはそのウィンドウ内で、自分の資格情報の確認、必要なリソースへの同意、2 要素認証の完了のいずれかの方法で操作を行う必要があります。

#### <a name="getting-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 最初に `loginPopup` (または `loginRedirect`) を実行した後、後続の呼び出しでは、通常、`acquireTokenSilent` メソッドを使用して、保護されたリソースにアクセスするためのトークンを取得します (トークンを要求または更新するための呼び出しは自動的に行われます)。場合によっては、`acquireTokenSilent` が失敗することがあります。 たとえば、ユーザーのパスワードの期限が切れている場合です。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1. すぐに `acquireTokenPopup` を呼び出し、ユーザー サインイン プロンプトをトリガーします。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。

2. ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `acquireTokenSilent` を再試行できるようにする。 他に中断なく使用できる機能がアプリケーションにある場合は、一般に、この方法が使用されます。 たとえば、使用可能な非認証コンテンツがアプリケーションに含まれている場合が考えられます。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

> [!NOTE]
> このクイックスタートでは、使用されるブラウザーが Internet Explorer である場合、`loginRedirect` および `acquireTokenRedirect` メソッドを使用します。 この方法を採用しているのは、Internet Explorer でのポップアップ ウィンドウの処理方法に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)があるためです。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

次のコードを `index.html` ファイルの `<script></script>` タグ内に追加します。

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成するサンプル アプリケーションでは、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行します。 その後、この要求からその内容が呼び出し元に返されます。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー*に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

次のコードを `index.html` ファイルの `<script></script>` タグ内に追加します。

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>アプリケーションの登録

1. [Azure Portal](https://portal.azure.com/) にサインインします。

1. お使いのアカウントで複数のテナントにアクセスできる場合は、右上でそのアカウントを選択した後、使用する Azure AD テナントにポータル セッションを設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[アプリケーションの登録]** ページが表示されたら、アプリケーションの名前を入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント\)** を選択します。
1. **[リダイレクト URI]** セクションで、ドロップダウン リストから **Web** プラットフォームを選択し、お使いの Web サーバーに基づいたアプリケーション URL に値を設定します。

   Node.js および Visual Studio でリダイレクト URL を設定したり取得したりする方法については、次の「Node.js でリダイレクト URL を設定する」セクションおよび「[Visual Studio でリダイレクト URL を設定する](#set-a-redirect-url-for-visual-studio)」を参照してください。

1. **[登録]** を選択します。
1. 後で使用するために、アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を書き留めます。
1. このクイック スタートでは、[暗黙的な許可フロー](v2-oauth2-implicit-grant-flow.md)を有効にする必要があります。 登録済みのアプリケーションの左側のウィンドウで、 **[認証]** を選択します。
1. **[詳細設定]** の **[暗黙的な許可]** で、 **[ID トークン]** チェック ボックスと **[アクセス トークン]** チェック ボックスをオンにします。 このアプリではユーザーのサインインを実行して API を呼び出す必要があるため、ID トークンとアクセス トークンが必要です。
1. **[保存]** を選択します。

> #### <a name="set-a-redirect-url-for-nodejs"></a>Node.js でリダイレクト URL を設定する
> Node.js の場合は、Web サーバーのポートを *server.js* ファイルで設定できます。 このチュートリアルでは、ポート 30662 を使用しますが、使用可能なその他の任意のポートを使用できます。
>
> アプリケーション登録情報の中にリダイレクト URL を設定するには、 **[アプリケーションの登録]** ウィンドウに切り替え、以下のいずれかを行います。
>
> - **リダイレクト URL** として *`http://localhost:30662/`* を設定します。
> - カスタム TCP ポートを使用している場合は、 *`http://localhost:<port>/`* を使用します (ここで、 *\<port>* はカスタム TCP ポート番号です)。
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Visual Studio でリダイレクト URL を設定する
> Visual Studio のリダイレクト URL を取得するには、次の手順に従います。
> 1. ソリューション エクスプローラーでプロジェクトを選択します。
>
>    **[プロパティ]** ウィンドウが開きます。 動作しなければ、F4 キーを押します。
>
>    ![[JavaScriptSPA プロジェクトのプロパティ] ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. **[URL]** の値をコピーします。
> 1. **[アプリケーション登録]** ウィンドウに切り替え、コピーした値を **リダイレクト URL** として貼り付けます。

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA の構成

1. プロジェクトの設定中に作成した *index.html* ファイルに、アプリケーション登録情報を追加します。 ファイルの先頭で、`<script></script>` タグの中に、次のコードを追加します。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    各値の説明:
    - *\<Enter_the_Application_Id_here>* は、登録したアプリケーションの**アプリケーション (クライアント) ID** です。
    - *\<Enter_the_Tenant_info_here>* は、次のオプションのいずれかに設定されます。
       - お使いのアプリケーションで "*この組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を**テナント ID** または**テナント名** (例: *contoso.microsoft.com*) に置き換えます。
       - お使いのアプリケーションで "*任意の組織のディレクトリ内のアカウント*" がサポートされる場合は、この値を **organizations** に置き換えます
       - お使いのアプリケーションで "*任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント*" がサポートされる場合は、この値を **common** に置き換えます。 "*個人用の Microsoft アカウントのみ*" にサポートを制限するには、この値を **consumers** に置き換えます。

## <a name="test-your-code"></a>コードのテスト

次のいずれかの環境で、自分のコードをテストします。

### <a name="test-with-nodejs"></a>Node.js でテストする

Visual Studio を使用していない場合は、お使いの Web サーバーが開始されていることを確認します。

1. *index.html* ファイルの場所に基づく TCP ポートをリッスンするように、サーバーを構成します。 Node.js では、アプリケーション フォルダーからコマンドライン プロンプトで次のコマンドを実行することで、Web サーバーを起動してポートをリッスンします。

    ```bash
    npm install
    node server.js
    ```
1. ブラウザーで、「**http://\<span>\</span>localhost:30662**」または「**http://\<span>\</span>localhost:{port}** 」と入力します (ここで、*port* は Web サーバーがリッスンしているポートです)。 *index.html* ファイルの内容と **[サインイン]** ボタンが表示されるはずです。

### <a name="test-with-visual-studio"></a>Visual Studio でのテスト

Visual Studio を使用している場合は、プロジェクト ソリューションを選択し、F5 キーを押して自分のプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{port} の場所を開くと、 **[サインイン]** ボタンが表示されるはずです。

## <a name="test-your-application"></a>アプリケーションのテスト

ブラウザーに *index.html* ファイルが読み込まれたら、 **[サインイン]** を選択します。 Microsoft ID プラットフォーム エンドポイントにサインインするように求められます。

![JavaScript SPA アカウント サインイン ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>アプリケーションによるアクセスに同意する

アプリケーションへの初回サインイン時には、お使いのプロファイルへのアクセスを許可してサインインすることを求められます。

![[Permissions requested]\(アクセス許可が要求されています\) ウィンドウ](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインすると、自分のユーザー プロファイル情報が Microsoft Graph API の応答で返されて表示されます。

![Microsoft Graph API 呼び出しの結果](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>スコープと委任されたアクセス許可の詳細

Microsoft Graph API には、ユーザーのプロファイルを読み取るための *user.read* スコープが必要です。 このスコープは既定で、登録ポータルに登録されているすべてのアプリケーションで自動的に追加されます。 Microsoft Graph の他の API や、バックエンド サーバーのカスタム API には、追加のスコープが必要な場合があります。 たとえば、Microsoft Graph API には、ユーザーの予定表を表示するための *Calendars.Read* スコープが必要です。

アプリケーションのコンテキストでユーザーの予定表にアクセスするには、*Calendars.Read* の委任されたアクセス許可をアプリケーション登録情報に追加します。 次に、*Calendars.Read* スコープを `acquireTokenSilent` 呼び出しに追加します。

>[!NOTE]
>スコープの数を増やすと、ユーザーは追加の同意を求められることがあります。

バックエンド API でスコープを必要としない (推奨されません) 場合は、トークンを取得するための呼び出しでスコープとして *clientId* を使用できます。

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID プラットフォームの改善にご協力ください。 簡単な 2 つの質問からなるアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
