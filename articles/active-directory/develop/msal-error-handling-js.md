---
title: MSAL.js におけるエラーと例外の処理
titleSuffix: Microsoft identity platform
description: MSAL.js アプリケーションで、エラーと例外、条件付きアクセス、クレーム チャレンジ、再試行を処理する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761334"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>MSAL.js におけるエラーと例外の処理

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>MSAL.js でのエラー処理

MSAL.js には、さまざまな種類の一般的エラーを抽象化して分類するエラー オブジェクトが用意されています。 エラーを適切に処理するために、エラー メッセージなど、エラーの特定の詳細にアクセスするためのインターフェイスも提供されています。

### <a name="error-object"></a>エラー オブジェクト

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

エラー クラスを拡張することで、次のプロパティにアクセスできます。
- `AuthError.message`:`errorMessage` と同一。
- `AuthError.stack`:スローされたエラーのスタック トレースです。

### <a name="error-types"></a>エラーの種類

次のエラーの種類を使用できます。

- `AuthError`:MSAL.js ライブラリの基本エラー クラス、予期しないエラーにも使用されます。

- `ClientAuthError`:クライアント認証の問題を示すエラー クラス。 ライブラリから発生するほとんどのエラーは ClientAuthError です。 これらのエラーは、ログインが既に進行中のときにログイン メソッドを呼び出した場合、ユーザーがログインをキャンセルした場合などに発生します。

- `ClientConfigurationError`:特定のユーザー構成パラメーターが正しくないか見つからないときに、要求が行われる前にスローされる、`ClientAuthError` を拡張するエラー クラス。

- `ServerError`:認証サーバーによって送信されるエラー文字列を表すエラー クラス。 無効な要求形式やパラメーターなどのエラー、またはサーバーがユーザーを認証または承認できない他のエラーです。

- `InteractionRequiredAuthError`:対話型の呼び出しを必要とするサーバー エラーを表す、`ServerError` を拡張するエラー クラス。 このエラーは、認証/承認に対する資格情報または同意を提供するためにユーザーがサーバーと対話する必要がある場合に、`acquireTokenSilent` によってスローされます。 エラーコードには、`"interaction_required"`、`"login_required"`、`"consent_required"` が含まれます。

リダイレクト メソッド (`loginRedirect`、`acquireTokenRedirect`) での認証フローにおけるエラー処理の場合、次のように、`handleRedirectCallback()` メソッドを使用するリダイレクトの後に成功または失敗で呼び出されるコールバックを登録する必要があります。

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

ポップアップ エクスペリエンスのメソッド (`loginPopup`、`acquireTokenPopup`) は promise を返すので、promise パターン (.then と .catch) を使用して、次のように処理できます。

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>対話を必要とするエラー

`acquireTokenSilent` などのトークンを取得する非対話型メソッドを使用しようとしましたが、MSAL ではそれをサイレントで行うことができないとき、エラーが返されます。

次のような原因が考えられます。

- サインインする必要がある
- 同意する必要がある
- 多要素認証エクスペリエンスを経由する必要がある。

修復するには、`acquireTokenPopup` や `acquireTokenRedirect` などの対話型メソッドを呼び出します。

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL.js を使用してトークンをサイレントで取得するとき (`acquireTokenSilent` を使用)、アクセスしようとしている API で MFA ポリシーなどの[条件付きアクセス クレーム チャレンジ](../azuread-dev/conditional-access-dev-guide.md)が必要な場合、アプリケーションはエラーを受け取ることがあります。

このエラーを処理するパターンは、次の例のように、`acquireTokenPopup` や `acquireTokenRedirect` など、MSAL.js でトークンを取得する対話型の呼び出しを行うことです。

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

対話形式でトークンを取得すると、ユーザーにメッセージが表示され、ユーザーは必要な条件付きアクセス ポリシーを満たす機会を与えられます。

条件付きアクセスを必要とする API を呼び出すと、API からのエラーでクレーム チャレンジを受け取ることがあります。 この場合、エラーで返されたクレームを `AuthenticationParameters.ts` クラスの `claimsRequest` フィールドに渡すと、該当するポリシーを満たすことができます。 

詳細については、「[方法:Azure AD アプリに省略可能な要求を提供する](active-directory-optional-claims.md)」を参照してください。


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>次のステップ

問題の診断とデバッグに役立つように、[MSAL.js でログ記録](msal-logging-js.md)を有効にすることを検討してください。
