---
title: エラーと例外 (MSAL)
titleSuffix: Microsoft identity platform
description: MSAL アプリケーションでエラーと例外、条件付きアクセス、クレーム チャレンジを処理する方法について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/22/2019
ms.author: twhitney
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7f903ca541582dfa0f3980bb65a3fef3c4b774a7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74916776"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL の例外とエラーを処理する

この記事では、さまざまな種類のエラーの概要と、一般的なサインイン エラーを処理するための推奨事項を提供します。

## <a name="msal-error-handling-basics"></a>MSAL のエラー処理の基本

Microsoft Authentication Library (MSAL) での例外は、エンド ユーザーに表示するためのものではなく、アプリ開発者がトラブルシューティングに使うことが意図されています。 例外のメッセージはローカライズされていません。

例外やエラーを処理するときは、例外の種類自体とエラー コードを使って、例外を区別できます。  エラー コードの一覧については、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」をご覧ください。

サインイン中に、同意、条件付きアクセス (MFA、デバイス管理、場所に基づく制限)、トークンの発行と引き換え、ユーザー プロパティに関するエラーが発生することがあります。

アプリのエラー処理の詳細については、以下で、お使いの言語のセクションを参照してください。

## <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

.NET 例外を処理するときは、例外の種類自体と `ErrorCode` メンバーを使用して例外を区別できます。 `ErrorCode` の値は、[MsalError](/dotnet/api/microsoft.identity.client.msalerror?view=azure-dotnet) 型の定数です。

[MsalClientException](/dotnet/api/microsoft.identity.client.msalexception?view=azure-dotnet)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet)、[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) のフィールドを確認することもできます。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) がスローされた場合は、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」を試して、コードがそこに一覧表示されているかどうかを確認します。

### <a name="common-net-exceptions"></a>一般的な .NET の例外

スローされる可能性がある一般的な例外と、可能な軽減策を次に示します。  

| 例外 | エラー コード | 対応策|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS65001: ユーザーまたは管理者は、ID が '{appId}' で名前が '{appName}' であるアプリケーションを使用することに同意していません。 このユーザーとリソースに、対話形式の承認要求を送信してください。| 先にユーザーの同意を得る必要があります。 .NET Core (Web UI はありません) を使用していない場合は、`AcquireTokeninteractive` を (1 回だけ) 呼び出します。 .NET Core を使用している場合、または `AcquireTokenInteractive` を行わない場合は、ユーザーは次の URL に移動して同意できます: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read 。 `AcquireTokenInteractive` を呼び出すには: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception?view=azure-dotnet) | AADSTS50079: ユーザーは、多要素認証 (MFA) を使用する必要があります。| 軽減策はありません。 テナントに MFA が構成されていて、Azure Active Directory (AAD) でそれを適用することを決定した場合、`AcquireTokenInteractive` や `AcquireTokenByDeviceCode` などの対話型フローにフォールバックする必要があります。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) |AADSTS90010: 付与タイプが */common* または */consumers* エンドポイントでサポートされていません。 */organizations* またはテナント固有のエンドポイントを使用してください。 */common* を使用しました。| Azure AD からのメッセージで説明されているように、機関はテナントを持っている必要があり、それ以外の場合は */organizations* です。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) | AADSTS70002: 要求本文には、次のパラメーターが含まれる必要があります: `client_secret or client_assertion`。| この例外は、アプリケーションが Azure AD でパブリック クライアント アプリケーションとして登録されていなかった場合にスローされる可能性があります。 Azure portal でアプリケーションのマニフェストを編集し、`allowPublicClient` を `true` に設定します。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)| `unknown_user Message`:ログインしているユーザーを識別できませんでした| ライブラリで現在 Windows にログインしているユーザーのクエリを実行できなかったか、またはこのユーザーは AD または AAD に参加していません (ワークプレースに参加しているユーザーはサポートされません)。 軽減策 1: UWP で、アプリケーションに次の機能があることを確認します: エンタープライズ認証、プライベート ネットワーク (クライアントとサーバー)、ユーザー アカウント情報。 軽減策 2: 独自のロジックを実装してユーザー名をフェッチし (たとえば、john@contoso.com)、ユーザー名を受け取る `AcquireTokenByIntegratedWindowsAuth` フォームを使用します。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception?view=azure-dotnet)|integrated_windows_auth_not_supported_managed_user| このメソッドは、Active Directory (AD) で公開されているプロトコルに依存します。 ユーザーが AD サポートなしに Azure Active Directory で作成された場合 ("マネージド" ユーザー)、このメソッドは失敗します。 AD で作成されて AAD によってサポートされているユーザーは ("フェデレーション" ユーザー)、この非対話型メソッドの認証を利用できます。 軽減策: 対話型認証を使用します。|

### `MsalUiRequiredException`

`AcquireTokenSilent()` を呼び出したときに MSAL.NET から返される一般的なステータスコードの 1 つが `MsalError.InvalidGrantError` です。 この状態コードは、アプリケーションから認証ライブラリが、対話モードで再度呼び出される必要があることを意味しています (パブリック クライアント アプリケーションの場合は AcquireTokenInteractive または AcquireTokenByDeviceCodeFlow。Web アプリではチャレンジを実行します)。 この理由としては、認証トークンを発行するには、ユーザーによる追加の対話が必要になるためです。

ほとんどの場合、`AcquireTokenSilent` が失敗するのは、要求に一致するトークンがトークン キャッシュにないことが原因です。 アクセス トークンは 1 時間で有効期限切れになり、`AcquireTokenSilent` では更新トークンに基づいて新しいトークンの取得が試みられます (OAuth2 の用語では、これは "更新トークン" フローです)。 このフローは、さまざまな理由 (テナント管理者がより厳しいログイン ポリシーを構成している場合など) により失敗する可能性があります。 

対話の目的は、ユーザーにアクションを実行させることです。 これらの条件には、ユーザーが簡単に解決できるものもあれば (たとえば、シングル クリックで使用条件に同意する)、現在の構成では解決できないものもあります (たとえば、問題のマシンが特定の企業ネットワークに接続する必要がある)。 ユーザーが多要素認証を設定したり、自分のデバイスに Microsoft Authenticator をインストールしたりする際に役立つものがあります。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分類列挙型

MSAL では `Classification` フィールドが公開されます。このフィールドを読み取ることで、ユーザー エクスペリエンスを向上させることができます。たとえば、パスワードの有効期限が切れたことや、一部のリソースの使用に同意する必要があることをユーザーに伝えることができます。 サポートされる値は、`UiRequiredExceptionClassification` 列挙型の一部です。

| 分類    | 意味           | 推奨される処理 |
|-------------------|-------------------|----------------------|
| BasicAction | 対話型認証フロー中にユーザーとの対話によって条件を解決できます。 | AcquireTokenInteractively() を呼び出します。 |
| AdditionalAction | 対話型認証フローの外部で、システムとの追加の修復対話によって条件を解決できます。 | AcquireTokenInteractively() を呼び出して、修復アクションについて説明するメッセージを表示します。 呼び出し元のアプリケーションでは、ユーザーが修復アクションを完了する可能性が低い場合に、additional_action を必要とするフローを非表示にすることを選択できます。 |
| MessageOnly      | 現時点で、条件は解決できません。 対話型認証フローを起動すると、条件を説明するメッセージが表示されます。 | AcquireTokenInteractively() を呼び出して、条件を説明するメッセージを表示します。 ユーザーがメッセージを読み取ってウィンドウを閉じると、AcquireTokenInteractively() によって UserCanceled エラーが返されます。 呼び出し元のアプリケーションでは、ユーザーがメッセージの恩恵を受ける可能性が低い場合に、message_only になるフローを非表示にすることを選択できます。|
| ConsentRequired  | ユーザーの同意がないか、または取り消されています。 | ユーザーの同意を得るために AcquireTokenInteractively() を呼び出します。 |
| UserPasswordExpired | ユーザーのパスワードの有効期限が切れています。 | AcquireTokenInteractively() を呼び出して、ユーザーが自分のパスワードをリセットできるようにします。 |
| PromptNeverFailed| パラメーター prompt=never を使用して対話型認証が呼び出されました。これにより、MSAL はブラウザーの Cookie に依存し、ブラウザーを表示しないように強制されます。 これは失敗しました。 | Prompt.None なしで、AcquireTokenInteractively() を呼び出します。 |
| AcquireTokenSilentFailed | MSAL SDK には、キャッシュからトークンを取り込むための十分な情報がありません。 これは、キャッシュ内にトークンがないこと、またはアカウントが見つからなかったことが原因である可能性があります。 エラー メッセージを見れば、詳細がわかります。  | AcquireTokenInteractively() を呼び出します。 |
| なし    | 詳細は提供されていません。 対話型認証フロー中にユーザーとの対話によって条件を解決できる場合があります。 | AcquireTokenInteractively() を呼び出します。 |

## <a name="net-code-example"></a>.NET コード例

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

MSAL.js には、さまざまな種類の一般的エラーを抽象化して分類するエラー オブジェクトが用意されています。 エラーを適切に処理するため、エラー メッセージなど、エラーの特定の詳細にアクセスするためのインターフェイスも提供されています。

### <a name="error-object"></a>Error オブジェクト

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

## <a name="pythontabpython"></a>[Python](#tab/python)

MSAL for Python では、ほとんどのエラーは API 呼び出しからの戻り値として伝達されます。 このエラーは、Microsoft ID プラットフォームからの JSON 応答を含むディクショナリとして表されます。

* 成功時の応答には `"access_token"` が含まれています。 応答の形式は OAuth2 プロトコルで定義されています。 詳細については、「[5.1 Successful Response](https://tools.ietf.org/html/rfc6749#section-5.1)」(5.1 成功応答) を参照してください。
* エラー応答には `"error"` が含まれ、通常は `"error_description"` も含まれています。 応答の形式は OAuth2 プロトコルで定義されています。 詳細については、「[5.2 Error Response](https://tools.ietf.org/html/rfc6749#section-5.2)」(5.2 エラー応答) を参照してください。

エラーが返されると、`"error_description"` キーには人間が読めるメッセージが含まれています。このメッセージには通常、Microsoft ID プラットフォーム エラー コードが含まれています。 さまざまなエラー コードの詳細については、「[認証と承認エラー コード](https://docs.microsoft.com/azure/active-directory/develop/reference-aadsts-error-codes)」をご覧ください。

MSAL for Python では、ほとんどのエラーはエラー値を返すことで処理されるため、例外はまれです。 `ValueError` 例外は、API パラメーターの形式が正しくない場合など、ライブラリの使用方法に問題がある場合にのみスローされます。

## <a name="javatabjava"></a>[Java](#tab/java)

MSAL for Java には、`MsalClientException`、`MsalServiceException`、`MsalInteractionRequiredException` という 3 種類の例外があります。すべて `MsalException` から継承されます。

- `MsalClientException` は、ライブラリまたはデバイスに対してローカルとなるエラーの発生時にスローされます。
- `MsalServiceException` は、セキュア トークン サービス (STS) からエラー応答が返されたときか、別のネットワーク エラーが発生したときにスローされます。
- `MsalInteractionRequiredException` は、認証を成功させるためには UI 対話が必要になるときにスローされます。

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` により、要求で返された HTTP ヘッダーが STS に公開されます。 `MsalServiceException.headers()` 経由でアクセスする

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

`AcquireTokenSilently()` を呼び出したときに MSAL for Java から返される一般的なステータスコードの 1 つが `InvalidGrantError` です。 つまり、認証トークンを発行するには、ユーザーによる追加の対話が必要になります。 アプリケーションでは認証ライブラリをもう一度呼び出す必要があります。ただし、パブリック クライアント アプリケーションに対して `AuthorizationCodeParameters` または `DeviceCodeParameters` を送信し、対話モードで行います。

ほとんどの場合、`AcquireTokenSilently` が失敗するのは、要求に一致するトークンがトークン キャッシュにないことが原因です。 アクセス トークンの有効期限は 1 時間です。`AcquireTokenSilently` では、更新トークンに基づいて新しいアクセス トークンの入手が試行されます。 OAuth2 用語では、これは更新トークン フローです。 このフローは、テナント管理者がより厳しいログイン ポリシーを構成している場合など、さまざまな理由により失敗する可能性があります。

このエラーを起こす条件の中にはユーザーが簡単に解決できるものもあります。 たとえば、利用規約に同意する必要があるかもしれません。 あるいは、コンピューターを特定の企業ネットワークに接続する必要があるため、現在の構成では要求を満たせないかもしれません。

MSAL からは `reason` フィールドが公開されます。このフィールドを使用し、ユーザー エクスペリエンスを改善できます。 たとえば、`reason` フィールドからは、パスワードの有効期限が切れたことや、一部のリソースの使用に同意する必要があることをユーザーに伝えることができます。 サポートされる値は、`InteractionRequiredExceptionReason` 列挙型の一部です。

| 理由 | 意味 | 推奨される処理 |
|---------|-----------|-----------------------------|
| `BasicAction` | 対話型認証フロー中にユーザーとの対話によって条件を解決できます。 | 対話型パラメーターで `acquireToken` を呼び出す |
| `AdditionalAction` | 対話型認証フローの外部で、システムとの追加の修復対話によって条件を解決できます。 | 対話型パラメーターで `acquireToken` を呼び出し、修復アクションについて説明するメッセージを表示します。 呼び出し元のアプリでは、ユーザーが修復アクションを完了する可能性が低い場合に、追加アクションを必要とするフローを非表示にすることを選択できます。 |
| `MessageOnly` | 現時点で、条件は解決できません。 対話型認証フローを起動すると、条件を説明するメッセージが表示されます。 | 対話型パラメーターで `acquireToken` を呼び出し、条件について説明するメッセージを表示します。 ユーザーがメッセージを読み取ってウィンドウを閉じると、`acquireToken` によって `UserCanceled` エラーが返されます。 アプリでは、ユーザーがメッセージの恩恵を受ける可能性が低い場合に、メッセージになるフローを非表示にすることを選択できます。 |
| `ConsentRequired`| ユーザーの同意がないか、または取り消されています。 |ユーザーが同意できるように、対話型パラメーターで `acquireToken` を呼び出します。 |
| `UserPasswordExpired` | ユーザーのパスワードの有効期限が切れています。 | ユーザーが自分のパスワードをリセットできるように、対話型パラメーターで `acquireToken` を呼び出します。 |
| `None` |  詳細が提供されます。 対話型認証フロー中にユーザーとの対話によって条件を解決できる場合があります。 | 対話型パラメーターで `acquireToken` を呼び出す |

### <a name="code-example"></a>コード例

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacostabiosmacos"></a>[iOS/macOS](#tab/iosmacos)

iOS 向けと macOS 向けの MSAL のエラーの完全リストについては、「[MSALError 列挙型](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128)」に一覧されています。

MSAL で生成されたエラーはすべて、`MSALErrorDomain` ドメインと共に返されます。

システム エラーの場合、MSAL によってシステム API から元の `NSError` が返されます。 たとえば、ネットワーク接続がないことが原因でトークンの取得に失敗した場合、MSAL によって `NSURLErrorDomain` ドメインと `NSURLErrorNotConnectedToInternet` コードを含むエラーが返されます。

クライアント側で少なくとも次の 2 つの MSAL エラーを処理することをお勧めします。

- `MSALErrorInteractionRequired`:ユーザーは対話型の要求を行う必要があります。 有効期限が切れた認証セッションや追加の認証要件のニーズなど、このエラーの原因となる可能性のある条件が多数あります。 MSAL 対話型トークンの取得 API を呼び出して、復旧します。 

- `MSALErrorServerDeclinedScopes`:一部またはすべてのスコープが拒否されました。 許可されたスコープのみを続行するか、サインイン プロセスを停止するかを決定します。

> [!NOTE]
> `MSALInternalError` 列挙型は、参照およびデバッグにのみ使用する必要があります。 これらのエラーを実行時に自動的に処理しないようにしてください。 `MSALInternalError` で失敗するエラーがアプリで発生した場合は、発生した内容を説明する一般的なユーザー向けメッセージを表示することができます。

たとえば、`MSALInternalErrorBrokerResponseNotReceived` は、ユーザーが認証を完了せず、アプリに手動で戻ったことを意味します。 この場合、アプリには認証が完了していないことを示す一般的なエラー メッセージが表示され、認証を再試行することが提案されます。

次の Objective-C サンプル コードは、一般的なエラー条件を処理するためのベスト プラクティスを示しています。

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>条件付きアクセスとクレーム チャレンジ

トークンをサイレントで取得するとき、アクセスしようとしている API で MFA ポリシーなどの[条件付きアクセス クレーム チャレンジ](conditional-access-dev-guide.md)が必要な場合、ご利用のアプリケーションはエラーを受け取ることがあります。

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
    if (error instanceof InteractionRequiredAuthError) {
        // Extract claims from error message
        accessTokenRequest.claimsRequest = extractClaims(error.errorMessage);
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function (accessTokenResponse) {
            // call API
        }).catch(function (error) {
            console.log(error);
        });
    }
});
```

対話形式でトークンを取得すると、ユーザーにメッセージが表示され、ユーザーは必要な条件付きアクセス ポリシーを満たす機会を与えられます。

条件付きアクセスを必要とする API を呼び出すと、API からのエラーでクレーム チャレンジを受け取ることがあります。 この場合、エラーで返されたクレームを `AuthenticationParameters.ts` クラスの `claimsRequest` フィールドに渡すと、該当するポリシーを満たすことができます。 

詳細については、「[方法:Azure AD アプリに省略可能な要求を提供する](active-directory-optional-claims.md)」を参照してください。

### <a name="msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL

iOS および macOS 用の MSAL を使用すると、対話型とサイレントの両方のトークン取得シナリオで特定の要求をリクエストすることができます。

カスタム要求をリクエストするには、`MSALSilentTokenParameters` または `MSALInteractiveTokenParameters` で `claimsRequest` を指定します。

詳細については、「[iOS と macOS 用の MSAL を使用してカスタム要求をリクエストする](request-custom-claims.md)」を参照してください。

## <a name="retrying-after-errors-and-exceptions"></a>エラーおよび例外の後の再試行

MSAL を呼び出すときには、独自の再試行ポリシーを実装することが求められます。 MSAL では AAD サービスに対して HTTP 呼び出しが行われます。ネットワークがダウンしたり、サーバーが過負荷になったりするなど、障害が時折発生する可能性があります。  

### <a name="http-error-codes-500-600"></a>HTTP エラー コード 500 - 600

MSAL.NET では、HTTP エラー コード 500 - 600 のエラーに対して、簡単な 1 回再試行のメカニズムが実装されています。

### <a name="http-429"></a>HTTP 429

サービス トークン サーバー (STS) が過剰な要求で過負荷になると、HTTP エラー 429 が返され、`Retry-After` 応答フィールドで再試行できるまでの期間に関するヒントが示されます。

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception?view=azure-dotnet) では、`namedHeaders` プロパティとして `System.Net.Http.Headers.HttpResponseHeaders` が示されます。 エラー コードからの追加情報を使用して、アプリケーションの信頼性を向上させることができます。 ここで説明したケースでは、`RetryAfterproperty` (`RetryConditionHeaderValue` 型) を使用して、再試行するタイミングを計算できます。

クライアント資格情報フローを使用するデーモン アプリケーションの例を次に示します。 これはトークンを取得するための任意のメソッドに適用することができます。

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
