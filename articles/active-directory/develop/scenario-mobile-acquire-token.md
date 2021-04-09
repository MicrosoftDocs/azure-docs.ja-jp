---
title: Web API を呼び出すトークンを取得する (モバイル アプリ) | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法について説明します。 (アプリのトークンを取得します。)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582555"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Web API を呼び出すモバイル アプリ トークンを取得する

保護された Web API をアプリから呼び出すには、アクセス トークンが必要です。 この記事では、Microsoft Authentication Library (MSAL) を使用してトークンを取得するプロセスについて説明します。

## <a name="define-a-scope"></a>スコープの定義

トークンを要求するときは、スコープを定義します。 スコープでは、お使いのアプリでアクセスできるデータを決定します。

スコープを定義する最も簡単な方法は、必要な Web API `App ID URI` をスコープ `.default` と組み合わせることです。 この定義により、アプリで、ポータルに設定されているすべてのスコープが必要であることを Microsoft ID プラットフォームに通知できます。

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>トークンを取得する

### <a name="acquire-tokens-via-msal"></a>MSAL によるトークンの取得

MSAL により、アプリはサイレントかつ対話的にトークンを取得できます。 `AcquireTokenSilent()` または `AcquireTokenInteractive()` を呼び出すと、要求されたスコープのアクセス トークンが MSAL から返されます。 正しいパターンは、サイレント要求を行ってから、対話型の要求にフォールバックすることです。

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

まず、トークンをサイレント モードで取得します。

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

MSAL から `MSALErrorInteractionRequired` が返された場合は、トークンを対話形式で取得します。

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL for iOS と macOS では、トークンを対話形式またはサイレント モードで取得するためのさまざまな修飾子がサポートされています。
* [トークンを取得するための共通パラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [対話型トークンを取得するためのパラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [サイレント トークンを取得するための共通パラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

次の例は、トークンを対話形式で取得するための最小限のコードを示しています。 この例では、Microsoft Graph を使用してユーザーのプロファイルを読み取ります。

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>MSAL.NET の必須のパラメーター

`AcquireTokenInteractive` の必須パラメーターは `scopes`の 1 つだけです。 `scopes` パラメーターで、トークンが必要なスコープを定義する文字列を列挙します。 Microsoft Graph 用のトークンの場合、必要なスコープは各 Microsoft Graph API の API リファレンスで見つけることができます。 リファレンスの「アクセス許可」セクションにアクセスします。

たとえば、[ユーザーの連絡先を一覧表示する](/graph/api/user-list-contacts)には、"User.Read"、"Contacts.Read" スコープを使用します。 詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」を参照してください。

Android では、`PublicClientApplicationBuilder` を使用してアプリの作成時に親アクティビティを指定できます。 この時点で親アクティビティを指定しない場合は、次のセクションのように、`.WithParentActivityOrWindow` を使用して後で指定できます。 親アクティビティを指定した場合は、操作後にトークンがその親アクティビティに通知されます。 指定しない場合は、`.ExecuteAsync()` を呼び出して例外をスローします。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET の特定の省略可能なパラメーター

以下のセクションでは、MSAL.NET の省略可能なパラメーターについて説明します。

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` パラメーターでは、プロンプトを指定してユーザーとのインタラクティビティを制御します。

![プロンプト構造内のフィールドを示す画像。 これらの定数値で、WithPrompt () パラメーターによって表示されるプロンプトの種類を定義して、ユーザーとのインタラクティビティを制御します。](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

このクラスでは次の定数を定義します。

- `SelectAccount` は、セキュリティ トークン サービス (STS) で、アカウントの選択ダイアログ ボックスを強制的に表示します。 ダイアログ ボックスには、ユーザーがセッションを持っているアカウントが表示されます。 このオプションは、ユーザーが異なる ID を選択できるようにする場合に使用できます。 このオプションを使用すると、MSAL から ID プロバイダーに `prompt=select_account` が送信されます。

    `SelectAccount` 定数は既定値であり、使用可能な情報に基づいて、考えられる最善のエクスペリエンスが効果的に提供されます。 使用可能な情報には、アカウントや、ユーザーのセッションの有無などが含まれます。 適切な理由がない限り、この既定値を変更しないでください。
- `Consent` を使用すると、事前に同意が得られていた場合でも、ユーザーに同意を求めることができます。 この場合、MSAL から ID プロバイダーに `prompt=consent` が送信されます。

    `Consent` 定数は、組織のガバナンスにより、ユーザーがアプリケーションを使用するたびに同意ダイアログ ボックスを表示することが求められるセキュリティ重視のアプリケーションで使用できます。
- `ForceLogin` を使用すると、プロンプトが必須ではない場合でも、ユーザーに資格情報の入力を求めるサービスを実現できます。

    このオプションは、トークンの取得に失敗し、ユーザーが再度サインインできるようにする場合に役立ちます。 この場合、MSAL から ID プロバイダーに `prompt=login` が送信されます。 このオプションは、組織のガバナンスにより、ユーザーがアプリケーションの特定の部分にアクセスするたびにサインインすることが求められるセキュリティ重視のアプリケーションで使用できます。
- `Never` は、.NET 4.5 と Windows ランタイム (WinRT) のみに使用できます。 この定数を使用すると、ユーザーの操作を求めず、非表示の埋め込み Web ビューに格納された Cookie を使用しようとします。 詳細については、「[Web ブラウザーを使用する (MSAL.NET)](./msal-net-web-browsers.md)」を参照してください。

    このオプションが失敗した場合は、`AcquireTokenInteractive` によって、UI 操作が必要であることを通知する例外がスローされます。 次に、別の `Prompt` パラメーターが使用されます。
- `NoPrompt` を使用すると、ID プロバイダーにプロンプトが送信されません。

    このオプションは、Azure Active Directory (Azure AD) B2C のプロファイルの編集ポリシーに対してのみ有効です。 詳細については、[B2C の詳細](https://aka.ms/msal-net-b2c-specificities)に関するページを参照してください。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

複数のリソースに対してユーザーの事前の同意を求める高度なシナリオでは、`WithExtraScopeToConsent` 修飾子を使用します。 この修飾子は、MSAL.NET または Microsoft ID プラットフォームで通常使用される増分同意を使用しない場合に使用できます。 詳細については、「[複数のリソースでユーザーの同意を事前に取得する](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)」を参照してください。

次にコード例を示します。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>その他の省略可能なパラメーター

`AcquireTokenInteractive` のその他の省略可能なパラメーターの詳細については、[AcquireTokenInteractiveParameterBuilder の参照ドキュメント](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)をご覧ください。

### <a name="acquire-tokens-via-the-protocol"></a>プロトコルを使用してトークンを取得する

プロトコルを直接使用してトークンを取得することはお勧めしません。 これを行うと、シングルサインオン (SSO)、デバイス管理、条件付きアクセスが含まれるいくつかのシナリオがアプリでサポートされなくなります。

プロトコルを使用してモバイル アプリのトークンを取得する場合は、次の 2 つの要求を行います。

* 承認コードを取得します。
* トークンのコードを交換します。

#### <a name="get-an-authorization-code"></a>承認コードを取得する

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>アクセスを取得してトークンを更新する

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>次の手順

このシナリオの次の記事である [Web API の呼び出し](scenario-mobile-call-api.md)に関する記事に進みます。