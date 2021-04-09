---
title: Web API を呼び出すトークンを取得する (シングルページ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: シングルページ アプリケーション (API を呼び出すトークンを取得する) を構築する方法を説明します
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 83896b2599f03961b2dcaf34ea9b55fe16c13b9e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756443"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>シングルページ アプリケーション：API を呼び出すトークンを取得する

MSAL.js を使用して API 用のトークンを取得するパターンは、`acquireTokenSilent` メソッドを使用してサイレント トークン要求を最初に試行することです。 このメソッドが呼び出されると、ライブラリでは、まずブラウザー ストレージ内のキャッシュに有効なトークンが存在するかどうかが確認され、それが返されます。 キャッシュ内に有効なトークンがない場合は、非表示の iframe から Azure Active Directory (Azure AD) にサイレント トークン要求が送信されます。 このメソッドを使用すれば、ライブラリでトークンを更新することもできます。 シングル サインオン セッションおよび Azure AD 内のトークン有効期間値の詳細については、[トークンの有効期間](active-directory-configurable-token-lifetimes.md)に関するページを参照してください。

Azure AD へのサイレント トークン要求は、Azure AD セッションの有効期限切れやパスワードの変更などの理由により、失敗する場合があります。 その場合は、トークンを取得するために対話型メソッド (ユーザーにプロンプトを表示する) のいずれかを呼び出すことができます。

* `acquireTokenPopup` を使用して[ポップアップ ウィンドウ](#acquire-a-token-with-a-pop-up-window)でトークンを取得する
* `acquireTokenRedirect` を使用して[リダイレクト](#acquire-a-token-with-a-redirect)によりトークンを取得する

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>ポップアップ エクスペリエンスか、リダイレクト エクスペリエンスを選択

 ご利用のアプリケーション内で、ポップアップ メソッドとリダイレクト メソッドの両方を使用することはできません。 ポップアップまたはリダイレクト エクスペリエンスのいずれを選択するかは、ご利用のアプリケーション フローに依存します。

* 認証中にユーザーにメイン アプリケーション ページから移動してほしくない場合は、ポップアップ メソッドをお勧めします。 認証リダイレクトはポップアップ ウィンドウで行われるため、メイン アプリケーションの状態は保持されます。

* ユーザーが使用しているブラウザーに制約またはポリシーが存在し、ポップアップ ウィンドウが無効になっている場合は、リダイレクト メソッドを使用することができます。 [Internet Explorer のポップアップ ウィンドウには既知の問題](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) があるので、Internet Explorer ブラウザーでは、リダイレクト メソッドを使用してください。

アクセス トークン要求を作成するときにアクセス トークンに含める API スコープを設定することができます。 要求されたすべてのスコープがアクセス トークンに付与されるとは限らないことに注意してください。 これは、ユーザーの同意によって異なります。

## <a name="acquire-a-token-with-a-pop-up-window"></a>ポップアップ ウィンドウを使用してトークンを取得する

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次のコードでは、前に説明したパターンと、ポップアップ エクスペリエンスのメソッドを組み合わせています。

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

MSAL Angular ラッパーを使用すると、自動的にアクセス トークンをサイレントに取得して API への HTTP 要求にそれを添付する HTTP インターセプターが提供されます。

API のスコープは、`protectedResourceMap` 構成オプションで指定できます。 `MsalInterceptor` は、トークンを自動的に取得するときにこれらのスコープを要求します。

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

サイレント トークン取得の成功と失敗については、MSAL Angular からコールバックが提供され、それはサブスクライブすることができます。 サブスクライブ解除を忘れずに行うことも重要です。

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

あるいは、コア MSAL.js ライブラリ内に記述されているように、取得トークン メソッドを使用してトークンを明示的に取得することができます。

---

## <a name="acquire-a-token-with-a-redirect"></a>リダイレクトを使用してトークンを取得する

# <a name="javascript"></a>[JavaScript](#tab/javascript)

次のパターンは前述したとおりですが、トークンを対話形式で取得するリダイレクト メソッドを使用して示しています。 前述のようにリダイレクト コールバックを登録する必要があります。

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>省略可能な要求を要請する

次の目的で省略可能な要求を使用できます。

- アプリケーションのトークンに省略可能な要求を含める。
- Azure AD からトークンで返される特定の要求の動作を変更する。
- アプリケーションのカスタムの要求を追加してアクセスする。

`IdToken` で省略可能な要求を要請するには、`AuthenticationParameters.ts` クラスの `claimsRequest` フィールドに文字列化された要求オブジェクトを送信します。

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

詳細については、[省略可能な要求](active-directory-optional-claims.md)に関するページを参照してください。

# <a name="angular"></a>[Angular](#tab/angular)

このコードは前述と同じです。

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である [Web API の呼び出し](scenario-spa-call-api.md)に関する記事に進みます。