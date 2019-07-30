---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8785f89a335f0ae7d983f267176da1656aee57a0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181781"
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

1. 次のコードを `index.html` ファイルの `<script></script>` タグ内に追加します。

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
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

ユーザーが初めて **[サインイン]** ボタンをクリックすると、`signIn` メソッドによって、ユーザーがサインインするための `loginPopup` が呼び出されます。 このメソッドで、"*Microsoft ID プラットフォーム エンドポイント*" のポップアップ ウィンドウが開かれて、ユーザーの資格情報が要求され、検証が行われます。 サインインに成功すると、ユーザーは元の *index.html* ページにリダイレクトされ、トークンが受信されて `msal.js` によって処理され、トークンに含まれる情報がキャッシュされます。 このトークンは *ID トークン*と呼ばれ、ユーザー表示名などのユーザーに関する基本情報が含まれます。 何らかの目的のためにこのトークンが提供する任意のデータを使用する予定がある場合、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、このトークンがバックグラウンド サーバーで確実に検証される必要があります。

このガイドで生成する SPA は、ユーザー プロファイル情報のため、`acquireTokenSilent`、`acquireTokenPopup`、またはその両方を呼び出して、Microsoft Graph API の照会に使用される*アクセス トークン*を取得します。 ID トークンを検証するサンプルが必要な場合は、[こちら](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 sample")にある GitHub のサンプル アプリケーションを確認してください。このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="getting-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後に、リソースにアクセスするためにトークンを要求するたびにユーザーを再認証しなくてすむようにするには、トークンを取得する多くの場合に *acquireTokenSilent* を使用する必要があります。 ただし、次の例のように、ユーザーが Microsoft ID プラットフォーム エンドポイントとやり取りを行う必要がある状況があります。

- パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある
- ご使用のアプリケーションが、ユーザーによる同意が必要なリソースへのアクセスを要求している
- 2 要素認証が必須である

*acquireTokenPopup* を呼び出すとポップアップ ウィンドウが表示されます (または、*acquireTokenRedirect* を呼び出すとユーザーが Microsoft ID プラットフォーム エンドポイントにリダイレクトされます)。ユーザーはそこでやり取りをして、自分の資格情報の確認、要求されたリソースへの同意、または 2 要素認証の完了を行う必要があります。

#### <a name="getting-a-user-token-silently"></a>ユーザー トークンを自動で取得する

`acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 初めて `loginPopup` (または `loginRedirect`) が実行された後、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。トークンの要求や更新のための呼び出しは自動で行われるためです。
`acquireTokenSilent` は、ユーザーのパスワードの期限が切れている場合などに失敗することがあります。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1. すぐに `acquireTokenPopup` を呼び出し、ユーザーにサインインを求める。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。

2. ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `acquireTokenSilent` を再試行できるようにする。 アプリケーションにユーザーが使用できる非認証コンテンツが含まれている場合など、アプリケーションの他の機能を中断せずに使用できる場合は、一般に、この方法で処理します。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

> [!NOTE]
> このクイック スタートでは、使用されるブラウザーが Internet Explorer である場合、Internet Explorer ブラウザーによるポップアップ ウィンドウの処理に関連した[既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)のために `loginRedirect` および `acquireTokenRedirect` メソッドを使用します。
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

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

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成したサンプル アプリケーションでは、`callMSGraph()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

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
