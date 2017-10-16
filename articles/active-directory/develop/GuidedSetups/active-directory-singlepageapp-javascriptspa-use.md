---
title: "Azure AD v2 JS SPA のガイド付きセットアップ - 使用 | Microsoft Docs"
description: "JavaScript SPA アプリケーションで、アクセス トークンを必要とする API を Azure Active Directory v2 エンドポイントから呼び出す方法"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/01/2017
ms.author: andret
ms.openlocfilehash: f52157df298ddfc1c1b29a18dc9a54aae59b52a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>ユーザーのサインインに Microsoft Authentication Library (MSAL) を使用する

1.  `app.js` という名前でファイルを作成します。 Visual Studio を使用している場合は、プロジェクト (プロジェクト ルート フォルダー) を選択し、右クリックして `Add` > `New Item` > `JavaScript File` を選択します。
2.  次のコードを `app.js` ファイルに追加します。

```javascript
// Graph API endpoint to show user profile
var graphApiEndpoint = "https://graph.microsoft.com/v1.0/me";

// Graph API scope used to obtain the access token to read user profile
var graphAPIScopes = ["https://graph.microsoft.com/user.read"];

// Initialize application
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});

//Previous version of msal uses redirect url via a property
if (userAgentApplication.redirectUri) {
    userAgentApplication.redirectUri = msalconfig.redirectUri;
}

window.onload = function () {
    // If page is refreshed, continue to display user info
    if (!userAgentApplication.isCallback(window.location.hash) && window.parent === window && !window.opener) {
        var user = userAgentApplication.getUser();
        if (user) {
            callGraphApi();
        }
    }
}

/**
 * Call the Microsoft Graph API and display the results on the page. Sign the user in if necessary
 */
function callGraphApi() {
    var user = userAgentApplication.getUser();
    if (!user) {
        // If user is not signed in, then prompt user to sign in via loginRedirect.
        // This will redirect user to the Azure Active Directory v2 Endpoint
        userAgentApplication.loginRedirect(graphAPIScopes);
        // The call to loginRedirect above frontloads the consent to query Graph API during the sign-in.
        // If you want to use dynamic consent, just remove the graphAPIScopes from loginRedirect call.
        // As such, user will be prompted to give consent when requested access to a resource that 
        // he/she hasn't consented before. In the case of this application - 
        // the first time the Graph API call to obtain user's profile is executed.
    } else {
        // If user is already signed in, display the user info
        var userInfoElement = document.getElementById("userInfo");
        userInfoElement.parentElement.classList.remove("hidden");
        userInfoElement.innerHTML = JSON.stringify(user, null, 4);

        // Show Sign-Out button
        document.getElementById("signOutButton").classList.remove("hidden");

        // Now Call Graph API to show the user profile information:
        var graphCallResponseElement = document.getElementById("graphResponse");
        graphCallResponseElement.parentElement.classList.remove("hidden");
        graphCallResponseElement.innerText = "Calling Graph ...";

        // In order to call the Graph API, an access token needs to be acquired.
        // Try to acquire the token used to query Graph API silently first:
        userAgentApplication.acquireTokenSilent(graphAPIScopes)
            .then(function (token) {
                //After the access token is acquired, call the Web API, sending the acquired token
                callWebApiWithToken(graphApiEndpoint, token, graphCallResponseElement, document.getElementById("accessToken"));

            }, function (error) {
                // If the acquireTokenSilent() method fails, then acquire the token interactively via acquireTokenRedirect().
                // In this case, the browser will redirect user back to the Azure Active Directory v2 Endpoint so the user 
                // can reenter the current username/ password and/ or give consent to new permissions your application is requesting.
                // After authentication/ authorization completes, this page will be reloaded again and callGraphApi() will be executed on page load.
                // Then, acquireTokenSilent will then get the token silently, the Graph API call results will be made and results will be displayed in the page.
                if (error) {
                    userAgentApplication.acquireTokenRedirect(graphAPIScopes);
                }
            });

    }
}

/**
 * Callback method from sign-in: if no errors, call callGraphApi() to show results.
 * @param {string} errorDesc - If error occur, the error message
 * @param {object} token - The token received from login
 * @param {object} error - The error string
 * @param {string} tokenType - the token type: usually id_token
 */
function loginCallback(errorDesc, token, error, tokenType) {
    if (errorDesc) {
        showError(msal.authority, error, errorDesc);
    } else {
        callGraphApi();
    }
}

/**
 * Show an error message in the page
 * @param {string} endpoint - the endpoint used for the error message
 * @param {string} error - Error string
 * @param {string} errorDesc - Error description
 */
function showError(endpoint, error, errorDesc) {
    var formattedError = JSON.stringify(error, null, 4);
    if (formattedError.length < 3) {
        formattedError = error;
    }
    document.getElementById("errorMessage").innerHTML = "An error has occurred:<br/>Endpoint: " + endpoint + "<br/>Error: " + formattedError + "<br/>" + errorDesc;
    console.error(error);
}

```

<!--start-collapse-->
### <a name="more-information"></a>詳細情報

初めて *[Call Microsoft Graph API]\(Microsoft Graph API の呼び出し\)* ボタンをクリックすると、`callGraphApi` メソッドによって、ユーザーがサインインするための `loginRedirect` が呼び出されます。 このメソッドで、ユーザーは "*Microsoft Azure Active Directory v2 エンドポイント*" にリダイレクトされ、ユーザーの資格情報が要求されて、検証が行われます。 サインインに成功すると、ユーザーは元の *index.html* ページにリダイレクトされ、トークンが受信されて `msal.js` によって処理され、トークンに含まれる情報がキャッシュされます。 このトークンは *ID トークン*と呼ばれ、ユーザー表示名などのユーザーに関する基本情報が含まれます。 何らかの目的のためにこのトークンが提供する任意のデータを使用する予定がある場合、アプリケーションの有効なユーザーに対してトークンが発行されたことを保証するために、このトークンがバックグラウンド サーバーで確実に検証される必要があります。

このガイドで生成する SPA では、ID トークンを直接活用していません。代わりに、`acquireTokenSilent`、`acquireTokenRedirect`、またはその両方を呼び出して、Microsoft Graph API の照会に使用される "*アクセス トークン*" を取得します。 ID トークンを検証するサンプルが必要な場合は、[こちら](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "Github active-directory-javascript-singlepageapp-dotnet-webapi-v2 sample")にある GitHub のサンプル アプリケーションを確認してください。このサンプルでは、トークンの検証に ASP.NET Web API を使用しています。

#### <a name="getting-a-user-token-interactively"></a>ユーザー トークンを対話形式で取得する

最初のサインインの後に、リソースにアクセスするためにトークンを要求されるたびにユーザーが再認証しなくて済むようにするには、トークンを取得する多くの場合に *acquireTokenSilent* を使用する必要があります。 しかし、以下のように、ユーザーが Azure Active Directory v2 エンドポイントとやり取りを行うことが必要になる場合もあります。
-   パスワードの有効期限が切れているため、ユーザーは資格情報を再入力する必要がある
-   ご使用のアプリケーションが、ユーザーによる同意が必要なリソースへのアクセスを要求している
-   2 要素認証が必須である

*acquireTokenRedirect(scope)* を呼び出すと、ユーザーは Azure Active Directory v2 エンドポイントにリダイレクトされ (または *acquireTokenPopup(scope)* を呼び出すと、ポップアップ ウィンドウが表示され)、ユーザーはここでやり取りをして、自分の資格情報の確認、要求されたリソースへの同意、または 2 要素認証の完了を行う必要があります。

#### <a name="getting-a-user-token-silently"></a>ユーザー トークンを自動で取得する
` acquireTokenSilent` メソッドは、ユーザーの操作なしでトークンの取得や更新を処理します。 初めて `loginRedirect` (または `loginPopup`) が実行された後、その後の呼び出しでは、保護されたリソースにアクセスするトークンを取得するために `acquireTokenSilent` メソッドが通常使用されます。トークンの要求や更新のための呼び出しは自動で行われるためです。
`acquireTokenSilent` は、ユーザーのパスワードの期限が切れている場合などに失敗することがあります。 アプリケーションでは、この例外を 2 つの方法で処理できます。

1.  すぐに `acquireTokenRedirect` を呼び出し、ユーザーにサインインを求める。 オンライン アプリケーション (ユーザーが使用できる非認証コンテンツが含まれていないアプリケーション) の場合は、一般に、この方法で処理します。 このガイドの設定で生成したサンプルでは、このパターンを使用しています。

2. ユーザーに対してアプリケーションで視覚的に対話形式でのサインインを求めることで、ユーザーが適切なタイミングでサインインできるようにし、アプリケーションがあとで `acquireTokenSilent` を再試行できるようにする。 アプリケーションにユーザーが使用できる非認証コンテンツが含まれている場合など、アプリケーションの他の機能を中断せずに使用できる場合は、一般に、この方法で処理します。 この場合、ユーザーは、保護されたリソースにアクセスしたり、古くなった情報を更新したりするためにサインインするタイミングを決定できます。

<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>取得したトークンを使用して Microsoft Graph API を呼び出す

次のコードを `app.js` ファイルに追加します。

```javascript
/**
 * Call a Web API using an access token.
 * @param {any} endpoint - Web API endpoint
 * @param {any} token - Access token
 * @param {object} responseElement - HTML element used to display the results
 * @param {object} showTokenElement = HTML element used to display the RAW access token
 */
function callWebApiWithToken(endpoint, token, responseElement, showTokenElement) {
    var headers = new Headers();
    var bearer = "Bearer " + token;
    headers.append("Authorization", bearer);
    var options = {
        method: "GET",
        headers: headers
    };

    fetch(endpoint, options)
        .then(function (response) {
            var contentType = response.headers.get("content-type");
            if (response.status === 200 && contentType && contentType.indexOf("application/json") !== -1) {
                response.json()
                    .then(function (data) {
                        // Display response in the page
                        console.log(data);
                        responseElement.innerHTML = JSON.stringify(data, null, 4);
                        if (showTokenElement) {
                            showTokenElement.parentElement.classList.remove("hidden");
                            showTokenElement.innerHTML = token;
                        }
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            } else {
                response.json()
                    .then(function (data) {
                        // Display response as error in the page
                        showError(endpoint, data);
                    })
                    .catch(function (error) {
                        showError(endpoint, error);
                    });
            }
        })
        .catch(function (error) {
            showError(endpoint, error);
        });
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>保護された API に対する REST 呼び出しの実行についての詳細

このガイドで作成したサンプル アプリケーションでは、`callWebApiWithToken()` メソッドを使用して、トークンが必要な保護されたリソースに対して HTTP `GET` 要求を実行し、呼び出し元にその内容を返します。 このメソッドは、取得したトークンを *HTTP Authorization ヘッダー* に追加します。 このガイドで作成したサンプル アプリケーションのリソースは、ユーザーのプロファイル情報を表示する Microsoft Graph API *me* エンドポイントです。

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>ユーザーをサインアウトするメソッドの追加

次のコードを `app.js` ファイルに追加します。

```javascript
/**
 * Sign-out the user
 */
function signOut() {
    userAgentApplication.logout();
}
```
