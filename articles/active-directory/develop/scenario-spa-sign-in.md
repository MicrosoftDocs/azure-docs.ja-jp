---
title: シングルページ アプリのサインインとサインアウト
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (サインイン) を構築する方法を学習する
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bdfffbf8d2e416c87dd5abb8f6383c58ad270231
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584365"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>シングルページ アプリケーション：サインインとサインアウト

ご利用のシングルページ アプリケーションのコードにサインインを追加する方法について学びます。

アプリケーション内の API にアクセスするトークンを取得する前に、認証されたユーザー コンテキストが必要です。 MSAL.js 内のアプリケーションにユーザーをサインインさせる方法には、次の 2 つがあります。

* `loginPopup` メソッドを使用して[ウィンドウをポップアップ](#sign-in-with-a-pop-up-window) する
* `loginRedirect` メソッドを使用して[リダイレクト](#sign-in-with-redirect)する

また、オプションで、サインイン時にユーザーに同意してもらう必要がある API のスコープを渡すこともできます。

> [!NOTE]
> 認証されたユーザー コンテキストまたは ID トークンに、ご利用のアプリケーションから既にアクセスできる場合は、ログイン手順を省略して直接トークンを取得することができます。 詳細については、「[SSO without MSAL.js login](msal-js-sso.md#sso-without-msaljs-login)」を参照してください。

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

ご利用のアプリケーション内で、ポップアップ メソッドとリダイレクト メソッドの両方を使用することはできません。 ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドをお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* ポップアップウィンドウが無効になっているブラウザの制約またはポリシーがある場合は、リダイレクト方法を使用できます。 [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) があるので、Internet Explorer ブラウザーでは、リダイレクト メソッドを使用してください。

## <a name="sign-in-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してサインインする


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular ラッパーを使用すると、ルート定義に `MsalGuard` を追加するだけで、ご利用のアプリケーション内の特定のルートを保護することができます。 このガードにより、そのルートにアクセスすると、サインインのためのメソッドが呼び出されます。

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

ポップアップ ウィンドウ エクスペリエンスの場合は、`popUp` 構成オプションを有効にします。 次のように同意を必要とするスコープを渡すこともできます。

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>リダイレクトを使用してサインインする

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

リダイレクトメソッドは、メインアプリから離れるため、Promise　を返しません。 返されたトークンを処理してアクセスするには、リダイレクト メソッドを呼び出す前に成功とエラーのコールバックを登録します。

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

このコードは、ポップアップウィンドウを使用したサインインに関するセクションで説明したものと同じです。 既定のフローはリダイレクトです。

---

## <a name="sign-out"></a>サインアウト

MSAL ライブラリには、`logout` メソッドが用意されています。これにより、ブラウザー ストレージ内のキャッシュがクリアされ、Azure Active Directory (Azure AD) にサインアウト要求が送信されます。 サインアウト後、ライブラリは既定でアプリケーションの開始ページにリダイレクトします。

`postLogoutRedirectUri` を設定することで、サインアウト後に、リダイレクトされる URI を構成することができます。 この URI は、アプリケーションの登録のログアウト URI として登録する必要もあります。

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2.x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1.x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリのトークンの取得](scenario-spa-acquire-token.md)に関する記事に進みます。