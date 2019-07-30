---
title: エラーと例外 (MSAL) | Azure
description: MSAL アプリケーションでエラーと例外、条件付きアクセス、クレーム チャレンジを処理する方法について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 30ab8a3fec459bef1a85c44e9a7cdb91b541fa2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111372"
---
# <a name="handling-exceptions-and-errors-using-msal"></a>MSAL を使用して例外とエラーを処理する
Microsoft Authentication Library (MSAL) での例外は、エンド ユーザーに表示するためのものではなく、アプリ開発者がトラブルシューティングに使うことが意図されています。 例外のメッセージはローカライズされていません。

例外やエラーを処理するときは、例外の種類自体とエラー コードを使って、例外を区別できます。  エラー コードの一覧については、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」をご覧ください。

## <a name="net-exceptions"></a>.NET の例外
例外を処理するときは、例外の種類自体と `ErrorCode` メンバーを使って、例外を区別できます。 `ErrorCode` の値は、[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet) 型の定数です。

[MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)、[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) の各フィールドを確認することもできます。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) がスローされた場合、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」で検索できるコードがエラー コードに含まれている可能性があります。

### <a name="common-exceptions"></a>一般的な例外
スローされる可能性がある一般的な例外と、可能な軽減策を次に示します。

| 例外 | エラー コード | 対応策|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: ユーザーまたは管理者は、ID が '{appId}' で名前が '{appName}' であるアプリケーションを使用することに同意していません。 このユーザーとリソースに、対話形式の承認要求を送信してください。| 先にユーザーの同意を得る必要があります。 .NET Core (Web UI はありません) を使用していない場合は、`AcquireTokeninteractive` を (1 回だけ) 呼び出します。 .NET Core を使用している場合、または `AcquireTokenInteractive` を行いたくない場合は、ユーザーは次の URL に移動して同意することができます: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read 。 `AcquireTokenInteractive` を呼び出すには: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: ユーザーは、多要素認証の使用を要求されています。| 軽減策はありません。テナントに MFA が構成されていて、AAD がそれを適用することを決定した場合、`AcquireTokenInteractive` や `AcquireTokenByDeviceCode` などの対話型フローにフォールバックする必要があります。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: */common* または */consumers* エンドポイントではサポートされていない付与タイプです。 */organizations* またはテナント固有のエンドポイントを使用してください。 */common* を使用しました。| Azure AD からのメッセージで説明されているように、機関はテナントを持っている必要があり、それ以外の場合は */organizations* です。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: 要求本文には、次のパラメーターが含まれる必要があります: 'client_secret または client_assertion'。| これは、アプリケーションが Azure AD でパブリック クライアント アプリケーションとして登録されていなかった場合に発生する可能性があります。 Azure portal でアプリケーションのマニフェストを編集し、`allowPublicClient` を `true` に設定します。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| unknown_user メッセージ: ログインしているユーザーを識別できませんでした| ライブラリで現在 Windows にログインしているユーザーのクエリを実行できなかったか、またはこのユーザーは AD または AAD に参加していません (ワークプレース参加ユーザーはサポートされません)。 軽減策 1: UWP で、アプリケーションに次の機能があることを確認します: エンタープライズ認証、プライベート ネットワーク (クライアントとサーバー)、ユーザー アカウント情報。 軽減策 2: 独自のロジックを実装してユーザー名をフェッチし (たとえば、john@contoso.com)、ユーザー名を受け取る `AcquireTokenByIntegratedWindowsAuth` フォームを使用します。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| このメソッドは、Active Directory (AD) で公開されているプロトコルに依存します。 ユーザーが AD サポートなしに Azure Active Directory で作成された場合 ("マネージド" ユーザー)、このメソッドは失敗します。 AD で作成されて AAD によってサポートされているユーザーは ("フェデレーション" ユーザー)、この非対話型メソッドの認証を利用できます。 軽減策: 対話型認証を使用します。|

## <a name="javascript-errors"></a>JavaScript のエラー

MSAL.js で提供されている Error オブジェクトでは、さまざまな種類の一般的なエラーが抽象化および分類されており、エラーを適切に処理するためのエラーの具体的な詳細 (エラー メッセージなど) にアクセスするインターフェイスがあります。

**Error オブジェクト**

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
* **AuthError.message:** これは、errorMessage と同じです。
* **AuthError.stack:** スローされたエラーのスタック トレースです。 エラーの発生ポイントまでトレースできます。

**エラーの種類**

次のエラーの種類を使用できます。

* *AuthError:* MSAL.js ライブラリの基本エラー クラス、予期しないエラーにも使用されます。

* *ClientAuthError:* クライアント認証の問題を示すエラー クラス。 ライブラリから発生するほとんどのエラーは ClientAuthError です。 ログインが進行中のログイン メソッドの呼び出しや、ユーザーによるログインのキャンセルなどのエラーがあります。

* *ClientConfigurationError:* 特定のユーザー構成パラメーターが正しくないか見つからないときに、要求が行われる前にスローされる、ClientAuthError を拡張するエラー クラス。

* *ServerError:* 認証サーバーによって送信されるエラー文字列を表すエラー クラス。 無効な要求形式やパラメーターなどのエラー、またはサーバーがユーザーを認証または承認できない他のエラーです。

* *InteractionRequiredAuthError:* 対話型の呼び出しを必要とするサーバー エラーを表す、ServerError を拡張するエラー クラス。 これは、認証/承認に対する資格情報または同意を提供するためにユーザーがサーバーと対話する必要がある場合に、`acquireTokenSilent` によってスローされます。 エラー コードには、"interaction_required"、"login_required"、"consent_required" が含まれます。

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

### <a name="interaction-required-errors"></a>対話が必要であるというエラー

UI の対話が必要な場合、エラーが返されます。 つまり、トークンを取得する非対話型メソッドを使用しようとしましたが (たとえば、`acquireTokenSilent`)、MSAL はそれをサイレントで行うことができませんでした。 次のような原因が考えられます。

* サインインする必要がある
* 同意する必要がある
* 多要素認証エクスペリエンスを経由する必要がある。

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

## <a name="conditional-access-and-claims-challenges"></a>条件付きアクセスとクレーム チャレンジ
トークンをサイレントで取得するとき、アクセスしようとしている API で MFA ポリシーなどの[条件付きアクセス クレーム チャレンジ](conditional-access-dev-guide.md)が必要な場合、アプリケーションはエラーを受け取ることがあります。

このエラーを処理するパターンは、MSAL を使用して対話形式でトークンを取得することです。 対話形式でトークンを取得すると、ユーザーにメッセージが表示され、ユーザーは必要な条件付きアクセス ポリシーを満たす機会を与えられます。

条件付きアクセスを必要とする API を呼び出す特定のケースでは、API からのエラーでクレーム チャレンジを受け取ることがあります。 たとえば、条件付きアクセス ポリシーがマネージド デバイス (Intune) を使用するものである場合、エラーは [AADSTS53000: Your device is required to be managed to access this resource](reference-aadsts-error-codes.md) (このリソースにアクセスするには、デバイスがマネージドである必要があります) のようなものになります。 この場合、トークン取得呼び出しでクレームを渡して、適切なポリシーを満たすようユーザーに求めることができます。

### <a name="net"></a>.NET
MSAL.NET の条件付きアクセスを必要とする API を呼び出すとき、アプリケーションではクレーム チャレンジの例外を処理する必要があります。 これは、[Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims?view=azure-dotnet) プロパティが空ではない [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) として示されます。

クレーム チャレンジを処理するには、`PublicClientApplicationBuilder` クラスの `.WithClaim()` メソッドを使用する必要があります。

### <a name="javascript"></a>JavaScript
MSAL.js を使用してトークンをサイレントで取得するとき (`acquireTokenSilent` を使用)、アクセスしようとしている API で MFA ポリシーなどの[条件付きアクセス クレーム チャレンジ](conditional-access-dev-guide.md)が必要な場合、アプリケーションはエラーを受け取ることがあります。

このエラーを処理するパターンは、次の例のように、`acquireTokenPopup` や `acquireTokenRedirect` など、MSAL.js でトークンを取得する対話型の呼び出しを行うことです。

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function (accessTokenResponse) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    myMSALObj.acquireTokenPopup(accessTokenRequest).then(
        function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
});
```

対話形式でトークンを取得すると、ユーザーにメッセージが表示され、ユーザーは必要な条件付きアクセス ポリシーを満たす機会を与えられます。

条件付きアクセスを必要とする API を呼び出すと、API からのエラーでクレーム チャレンジを受け取ることがあります。 この場合、トークンを取得するための呼び出しの `extraQueryParameters` として、エラーで返されたクレームを渡すことにより、適切なポリシーを満たすようユーザーに求めることができます。

```javascript
var request = {
    scopes: ["user.read"],
    extraQueryParameters: {claims: claims}
}

myMSALObj.acquireTokenPopup(request);
```

## <a name="retrying-after-errors-and-exceptions"></a>エラーおよび例外の後の再試行

### <a name="http-error-codes-500-600"></a>HTTP エラー コード 500 - 600
MSAL.NET では、HTTP エラー コード 500 - 600 のエラーに対して、簡単な 1 回再試行のメカニズムが実装されています。

### <a name="http-429"></a>HTTP 429
サービス トークン サーバー (STS) は、"要求が多すぎる" ためにビジー状態になると、HTTP エラー 429 と共に、再試行できるタイミングについてのヒント (Retry-After 応答フィールド) を遅延秒数または日付として返します。

#### <a name="net"></a>.NET
[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) 例外では、`namedHeaders` プロパティとして `System.Net.Http.Headers.HttpResponseHeaders` が示されます。 そのため、エラー コードからの追加情報を利用して、アプリケーションの信頼性を向上させることができます。 ここで説明したケースでは、`RetryAfterproperty` (`RetryConditionHeaderValue` 型) を使用して、再試行するタイミングを計算できます。

次に示すのはデーモン アプリケーションの例ですが (クライアント資格情報フローを使用)、トークンを取得する任意のメソッドに使用できます。

```csharp
do
{
 retry = false;
 TimeSpan? delay;
 try
 {
  result = await publicClientApplication.AcquireTokenForClient(scopes, account)
                                        .ExecuteAsync();
 }
 catch (MsalServiceException serviceException)
 {
  if (ex.ErrorCode == "temporarily_unavailable")
  {
   RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
   if (retryAfter.Delta.HasValue)
   {
    delay = retryAfter.Delta;
   }
   else if (retryAfter.Date.HasValue)
   {
    delay = retryAfter.Date.Value.Offset;
   }
  }
 }

    …
 if (delay.HasValue)
 {
  Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
  retry = true;
 }
} while (retry);
```
