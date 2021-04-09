---
title: MSAL.NET におけるエラーと例外の処理
titleSuffix: Microsoft identity platform
description: MSAL.NET で、エラーと例外、条件付きアクセス、クレーム チャレンジ、再試行を処理する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 565acd745ba5d7fdec71f306d3851e599838f7d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584046"
---
# <a name="handle-errors-and-exceptions-in-msalnet"></a>MSAL.NET におけるエラーと例外の処理

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msalnet"></a>MSAL.NET でのエラー処理

.NET 例外を処理するときは、例外の種類自体と `ErrorCode` メンバーを使用して例外を区別できます。 `ErrorCode` の値は、[MsalError](/dotnet/api/microsoft.identity.client.msalerror) 型の定数です。

[MsalClientException](/dotnet/api/microsoft.identity.client.msalexception)、[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)、[MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) のフィールドを確認することもできます。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) がスローされた場合は、「[認証と承認エラー コード](reference-aadsts-error-codes.md)」を試して、コードがそこに一覧表示されているかどうかを確認します。

### <a name="common-net-exceptions"></a>一般的な .NET の例外

スローされる可能性がある一般的な例外と、可能な軽減策を次に示します。  

| 例外 | エラー コード | 対応策|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: ユーザーまたは管理者は、ID が '{appId}' で名前が '{appName}' であるアプリケーションを使用することに同意していません。 このユーザーとリソースに、対話形式の承認要求を送信してください。| 先にユーザーの同意を得ます。 .NET Core (Web UI はありません) を使用していない場合は、`AcquireTokeninteractive` を (1 回だけ) 呼び出します。 .NET Core を使用している場合、または `AcquireTokenInteractive` を行わない場合は、ユーザーは次の URL に移動して同意できます: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read`。 `AcquireTokenInteractive` を呼び出すには: `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: ユーザーは、[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) を使用する必要があります。| 軽減策はありません。 テナントに MFA が構成されていて、Azure Active Directory (AAD) でそれを適用することを決定した場合、`AcquireTokenInteractive` や `AcquireTokenByDeviceCode` などの対話型フローにフォールバックします。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: 付与タイプが */common* または */consumers* エンドポイントでサポートされていません。 */organizations* またはテナント固有のエンドポイントを使用してください。 */common* を使用しました。| Azure AD からのメッセージで説明されているように、機関はテナントを持っている必要があり、それ以外の場合は */organizations* です。|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: 要求本文には、次のパラメーターが含まれる必要があります: `client_secret or client_assertion`。| この例外は、アプリケーションが Azure AD でパブリック クライアント アプリケーションとして登録されていなかった場合にスローされる可能性があります。 Azure portal でアプリケーションのマニフェストを編集し、`allowPublicClient` を `true` に設定します。 |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`:ログインしているユーザーを識別できませんでした| ライブラリで現在 Windows にログインしているユーザーのクエリを実行できなかったか、またはこのユーザーは AD または Azure AD に参加していません (ワークプレースに参加しているユーザーはサポートされません)。 軽減策 1: UWP で、アプリケーションに次の機能があることを確認します: エンタープライズ認証、プライベート ネットワーク (クライアントとサーバー)、ユーザー アカウント情報。 軽減策 2: 独自のロジックを実装してユーザー名をフェッチし (たとえば、john@contoso.com)、ユーザー名を受け取る `AcquireTokenByIntegratedWindowsAuth` フォームを使用します。|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| このメソッドは、Active Directory (AD) で公開されているプロトコルに依存します。 ユーザーが AD サポートなしに Azure AD で作成された場合 ("マネージド" ユーザー)、このメソッドは失敗します。 AD で作成されて Azure AD によってサポートされているユーザーは ("フェデレーション" ユーザー)、この非対話型メソッドの認証を利用できます。 軽減策:対話型認証を使用します。|

### `MsalUiRequiredException`

`AcquireTokenSilent()` を呼び出したときに MSAL.NET から返される一般的なステータスコードの 1 つが `MsalError.InvalidGrantError` です。 この状態コードは、アプリケーションから認証ライブラリが、対話モードで再度呼び出される必要があることを意味しています (パブリック クライアント アプリケーションの場合は AcquireTokenInteractive または AcquireTokenByDeviceCodeFlow。Web アプリではチャレンジを使用します)。 この理由としては、認証トークンを発行するには、ユーザーによる追加の対話が必要になるためです。

ほとんどの場合、`AcquireTokenSilent` が失敗するのは、要求に一致するトークンがトークン キャッシュにないことが原因です。 アクセス トークンは 1 時間で有効期限切れになり、`AcquireTokenSilent` では更新トークンに基づいて新しいトークンの取得が試みられます (OAuth2 の用語では、これは "更新トークン" フローです)。 このフローは、さまざまな理由 (テナント管理者がより厳しいログイン ポリシーを構成している場合など) により失敗する可能性があります。 

対話の目的は、ユーザーにアクションを実行させることです。 これらの条件には、ユーザーが簡単に解決できるものもあれば (たとえば、シングル クリックで使用条件に同意する)、現在の構成では解決できないものもあります (たとえば、問題のマシンが特定の企業ネットワークに接続する必要がある)。 ユーザーが多要素認証を設定したり、自分のデバイスに Microsoft Authenticator をインストールしたりする際に役立つものがあります。

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` 分類列挙型

MSAL からは `Classification` フィールドが公開されます。このフィールドを読み取り、ユーザー エクスペリエンスを改善できます。 たとえば、パスワードの有効期限が切れたことや、一部のリソースの使用に同意する必要があることをユーザーに伝えることができます。 サポートされる値は、`UiRequiredExceptionClassification` 列挙型の一部です。

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
[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL.NET の条件付きアクセスを必要とする API を呼び出すとき、アプリケーションではクレーム チャレンジの例外を処理する必要があります。 これは、[Claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) プロパティが空ではない [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) として示されます。

クレーム チャレンジを処理するには、`PublicClientApplicationBuilder` クラスの `.WithClaim()` メソッドを使用する必要があります。

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

### <a name="http-error-codes-500-600"></a>HTTP エラー コード 500 - 600

MSAL.NET では、HTTP エラー コード 500 - 600 のエラーに対して、簡単な 1 回再試行のメカニズムが実装されています。

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) では、`namedHeaders` プロパティとして `System.Net.Http.Headers.HttpResponseHeaders` が示されます。 エラー コードからの追加情報を使用して、アプリケーションの信頼性を向上させることができます。 ここで説明したケースでは、`RetryAfterproperty` (`RetryConditionHeaderValue` 型) を使用して、再試行するタイミングを計算できます。

クライアント資格情報フローを使用するデーモン アプリケーションの例を次に示します。 これはトークンを取得するための任意のメソッドに適用することができます。

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
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
    // . . .
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```

## <a name="next-steps"></a>次のステップ

問題の診断とデバッグに役立つように、[MSAL.NET でログ記録](msal-logging-dotnet.md)を有効にすることを検討してください。
