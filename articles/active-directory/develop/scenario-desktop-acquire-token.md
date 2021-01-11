---
title: Web API を呼び出すためのトークンを取得する (デスクトップ アプリ) | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデスクトップ アプリを構築して、そのアプリのトークンを取得する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 300bc6acbe7821841b578dcc2166ecfc498ad750
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141297"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Web API を呼び出すデスクトップ アプリ:トークンを取得する

パブリック クライアント アプリケーションのインスタンスを構築したら、それを使用して、トークンを取得します。これは、Web API を呼び出すために後で使用します。

## <a name="recommended-pattern"></a>推奨パターン

Web API はその `scopes` によって定義されます。 どのようなエクスペリエンスをアプリケーション内で提供する場合でも、使用するパターンは次のようになります。

- `AcquireTokenSilent` を呼び出すことで、トークン キャッシュからのトークンの取得を体系的に試行します。
- この呼び出しが失敗した場合は、使用したい `AcquireToken` フローを使用します (ここでは `AcquireTokenXX` で表されています)。

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>MSAL.NET の場合

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account  
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL の場合

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

ここでは、デスクトップ アプリケーション内でトークンを取得するさまざまな方法を示します。

## <a name="acquire-a-token-interactively"></a>トークンを対話形式で取得する

次の例は、Microsoft Graph を使用してユーザーのプロファイルを読み取るためにトークンを対話形式で取得する最小限のコードを示しています。

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>MSAL.NET の場合

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

### <a name="mandatory-parameters"></a>必須のパラメーター

`AcquireTokenInteractive` の必須のパラメーターは 1 つだけです (``scopes``)。これには、トークンが必要なスコープを定義する文字列のリストが含まれています。 Microsoft Graph 用のトークンの場合、必要なスコープは各 Microsoft Graph API の API リファレンスの「アクセス許可」というセクションにあります。 たとえば、[ユーザーの連絡先を一覧表示する](/graph/api/user-list-contacts)には、"User.Read"、"Contacts.Read" スコープを使用する必要があります。 詳細については、「[Microsoft Graph のアクセス許可のリファレンス](/graph/permissions-reference)」を参照してください。

Android では、示されているように `.WithParentActivityOrWindow` を使用して親アクティビティを指定する必要もあります。これにより、操作後にトークンが親アクティビティに戻ります。 そのように指定しないと、`.ExecuteAsync()` の呼び出し時に例外がスローされます。

### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET の特定の省略可能なパラメーター

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

UI は対話型であるため、重要です。 `AcquireTokenInteractive` には、特定の省略可能なパラメーターが 1 つあり、それをサポートするプラットフォームに対して親 UI を指定できます。 デスクトップ アプリケーションで使用する場合、`.WithParentActivityOrWindow` の型はプラットフォームによって異なります。

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

解説:

- .NET Standard では、想定される `object` は `Activity` (Android の場合)、`UIViewController` (iOS の場合)、`NSWindow` (MAC の場合)、`IWin32Window` または `IntPr` (Windows の場合) です。
- Windows では、埋め込みブラウザーが適切な UI 同期コンテキストを取得するように、UI スレッドから `AcquireTokenInteractive` を呼び出す必要があります。 UI スレッドから呼び出さない場合は、メッセージが適切にポンプされなかったり、UI によるデッドロックが発生したりする可能性があります。 UI スレッドでない場所で UI スレッドから Microsoft 認証ライブラリ (MSAL) を呼び出す方法の 1 つとしては、WPF で `Dispatcher` を使用します。
- WPF を使用している場合に WPF コントロールからウィンドウを取得するには、`WindowInteropHelper.Handle` クラスを使用できます。 そして、呼び出しは WPF コントロール (`this`) から次のように行われます。

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` は、プロンプトを指定してユーザーとのインタラクティビティを制御するために使用します。

![プロンプト構造内のフィールドを示す画像。 これらの定数値を使用することで、WithPrompt() メソッドによって表示されるプロンプトの種類が定義され、ユーザーとのインタラクティビティが制御されます。](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

このクラスでは次の定数を定義します。

- ``SelectAccount`` により、ユーザーがセッションを確立しているアカウントを含む、アカウントの選択ダイアログ ボックスが STS で強制的に表示されます。 このオプションは、アプリケーション開発者が各種の ID の中からユーザーに選択させる場合に役立ちます。 このオプションを使用すると、MSAL から ID プロバイダーに ``prompt=select_account`` が送信されます。 これは既定のオプションです。 これにより、使用可能な情報 (アカウント、ユーザーのセッションの有無など) に基づいて最適なエクスペリエンスが提供されます。 正当な理由がない限り、このオプションを変更しないでください。
- ``Consent`` により、以前に同意が得られている場合であっても、アプリケーション開発者がユーザーの同意を求めることができます。 この場合、MSAL から ID プロバイダーに `prompt=consent` が送信されます。 このオプションは、アプリケーションを使用するたびに同意ダイアログ ボックスをユーザーに表示することが組織のガバナンスで求められる、セキュリティを重視する一部のアプリケーションで使用できます。
- ``ForceLogin`` により、このユーザー プロンプトが不要な場合でも、アプリケーション開発者がサービスによってユーザーに資格情報の入力を求めることができます。 このオプションは、トークンの取得に失敗した場合に、ユーザーが再度サインインできるようにするのに役立ちます。 この場合、MSAL から ID プロバイダーに `prompt=login` が送信されます。 これは、アプリケーションの特定の部分にアクセスするたびにユーザーが再度サインインすることが組織のガバナンスで求められる、セキュリティを重視するアプリケーションで使用されることがあります。
- ``Never`` (.NET 4.5 および WinRT の場合のみ) はユーザーに入力を求めませんが、代わりに非表示の埋め込み Web ビューに格納された Cookie の使用を試行します。 詳細については、MSAL.NET の Web ビューを参照してください。 このオプションを使用すると、失敗する場合があります。 その場合、`AcquireTokenInteractive` は、UI 操作が必要であることを通知するために例外をスローします。 別の `Prompt` パラメーターを使用することが必要になります。
- ``NoPrompt`` は、ID プロバイダーにプロンプトを送信しません。 このオプションは、Azure Active Directory (Azure AD) B2C のプロファイルの編集ポリシーに対してのみ有効です。 詳細については、[Azure AD B2C での詳細](https://aka.ms/msal-net-b2c-specificities)に関するページを参照してください。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

この修飾子は、複数のリソースに対するユーザーの事前の同意を求め、かつ (通常は MSAL.NET または Microsoft ID プラットフォームで使用される) 増分同意を使用しない高度なシナリオで使用されます。 詳細については、「[複数のリソースでユーザーの同意を事前に取得する](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources)」を参照してください。

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Web UI は、ブラウザーを起動するメカニズムです。 このメカニズムは、専用の UI WebBrowser コントロール、またはブラウザーを開くことを委任する方法の場合があります。
MSAL によりほとんどのプラットフォームに Web UI の実装が提供されますが、ブラウザーを自分でホストしたい場合もあります。

- プラットフォームがデスクトップ上の MSAL (Blazor、Unity、Mono など) によって明示的にカバーされていない場合。
- UI でアプリケーションのテストを行い、Selenium で使用できる自動化されたブラウザーを使用する必要がある場合。
- ブラウザーと MSAL を実行するアプリが別々のプロセスにある場合。

##### <a name="at-a-glance"></a>概略

これを実現するには、MSAL に `start Url` を指定します。これは、エンド ユーザーが自分のユーザー名などの項目を入力できるように、選択したブラウザーに表示される必要があります。
認証が完了したら、アプリから MSAL に `end Url` を返す必要があります。これには、Azure AD によって提供されるコードが含まれます。
`end Url` のホストは常に `redirectUri` です。 `end Url` をインターセプトするには、次のいずれかを行います。

- `redirect Url` にヒットするまでブラウザーのリダイレクトを監視します。
- ブラウザーを URL にリダイレクトさせ、これを監視します。

##### <a name="withcustomwebui-is-an-extensibility-point"></a>拡張ポイントとしての WithCustomWebUi

`WithCustomWebUi` は、パブリック クライアント アプリケーションで独自の UI を提供するために使用できる拡張ポイントです。 また、ユーザーが ID プロバイダーの /Authorize エンドポイントを経由し、サインインして同意できるようにすることもできます。 MSAL.NET では、その後で認証コードを引き換えて、トークンを取得できます。 たとえば、電子アプリケーション (Visual Studio フィードバックなど) に Web 操作を提供させて、ほとんどの処理を MSAL.NET に任せるために Visual Studio で使用されます。 また、UI オートメーションを提供する場合に使用することもできます。 パブリック クライアント アプリケーションの場合、MSAL.NET では Proof Key for Code Exchange (PKCE) 標準を使用してセキュリティが確保されるようにします。 コードを引き換えることができるのは MSAL.NET のみです。 詳細については、「[RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)」を参照してください。

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi を使用する

`.WithCustomWebUI` を使用するには、次の手順に従います。

  1. `ICustomWebUi` インターフェイスを実装します。 詳細については、[こちらの Web サイト](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)を参照してください。 1 つの `AcquireAuthorizationCodeAsync` メソッドを実装し、MSAL.NET によって計算された認証コード URL を受け入れます。 次に、ユーザーに ID プロバイダーとの対話を実行させ、ID プロバイダーが実装のコールバックに使用した URL (認証コードを含む) を返します。 問題がある場合は、MSAL と適切に連携する `MsalExtensionException` 例外が実装によってスローされます。
  2. `AcquireTokenInteractive` の呼び出しで、カスタム Web UI のインスタンスを渡す `.WithCustomUI()` 修飾子を使用します。

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>テスト自動化における ICustomWebUi の実装例:SeleniumWebUI

MSAL.NET チームでは、この拡張メカニズムを使用するように UI テストを書き換えました。 関心がある場合は、MSAL.NET のソース コードで [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) クラスをご確認ください。

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>SystemWebViewOptions を使用して優れたエクスペリエンスを提供する

MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions?view=azure-dotnet) から、以下を指定できます。

- システム Web ブラウザーでのサインインあるいは同意エラーの場合に、(`BrowserRedirectError`) に移動する URI または (`HtmlMessageError`) を表示する HTML フラグメント。
- サインインあるいは同意が成功した場合に、(`BrowserRedirectSuccess`) に移動する URI または (`HtmlMessageSuccess`) を表示する HTML フラグメント。
- システム ブラウザーを起動するために実行するアクション。 `OpenBrowserAsync` デリゲートを設定して、独自の実装を提供できます。 このクラスでは、2 つのブラウザー (Microsoft Edge 用の `OpenWithEdgeBrowserAsync` と [Chromium に基づく Microsoft Edge](https://www.windowscentral.com/faq-edge-chromium) 用 の `OpenWithChromeEdgeBrowserAsync`) の既定の実装も用意されています。

この構造体を使用するには、次の例のように記述します。

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>その他の省略可能なパラメーター

`AcquireTokenInteractive` のその他すべての省略可能なパラメーターについては、[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) に関する記事を参照してください。

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python では、対話型のトークン取得メソッドは直接的には提供されません。 代わりに、アプリケーションにユーザー対話フローの実装内で承認要求を送信することを要求して、認証コードを取得します。 このコードは、その後、トークンを取得するために `acquire_token_by_authorization_code` メソッドに渡すことができます。

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])    

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>iOS および macOS 用の MSAL の場合

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>統合 Windows 認証

ドメインまたは Azure AD 参加済みのマシンでドメイン ユーザーをサインインさせるには、統合 Windows 認証を使用します。

### <a name="constraints"></a>制約

- 統合 Windows 認証は、"*フェデレーション+* " ユーザー (Active Directory で作成され、Azure AD によってサポートされているユーザー) に対してのみ使用できます。 Azure AD で直接作成され、Active Directory のサポートのないユーザー ("*マネージド*" ユーザーと呼ばれます) はこの認証フローを使用できません。 この制限は、ユーザー名とパスワードのフローには影響しません。
- IWA は、.NET Framework、.NET Core、およびユニバーサル Windows プラットフォーム (UWP) の各プラットフォーム用に作成されたアプリを対象としています。
- IWA では[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) はバイパスされません。 MFA が構成されている状況では、MFA チャレンジが必要な場合に IWA が失敗する可能性があります。これは、MFA でユーザーの操作が必要になるためです。
  > [!NOTE]
  > これには注意が必要です。 IWA は非対話型ですが、MFA にはユーザーの操作が必要です。 ID プロバイダーが MFA の実行を要求するタイミングの制御は、ユーザーではなくテナント管理者が行います。 弊社の観測によると、MFA が必要なのは、他の国/地域からサインインする場合と VPN 経由で企業ネットワークに接続されていない場合です。ただし、VPN 経由で接続されている場合であっても MFA が必要になる可能性があります。 確定的なルール セットを想定しないでください。 Azure AD では、AI を使用して、MFA が必要かどうかを継続的に学習します。 IWA が失敗した場合は、対話型認証やデバイス コード フローなどのユーザー プロンプトにフォールバックしてください。

- `PublicClientApplicationBuilder` で渡される機関の要件は次のとおりです。
  - `https://login.microsoftonline.com/{tenant}/` の形式でテナント化されている。ここで、`tenant` は、テナント ID を表す GUID またはテナントに関連付けられているドメインです。
  - 任意の職場および学校アカウント用である: `https://login.microsoftonline.com/organizations/`。
  - Microsoft 個人アカウントはサポートされていません。 /common および /consumers テナントを使用することはできません。

- 統合 Windows 認証はサイレント フローであるため、次の要件が適用されます。
  - アプリケーションのユーザーが、アプリケーションの使用に事前に同意しておく必要があります。
  - または、テナント管理者が、テナント内のすべてのユーザーによるアプリケーションの使用に事前に同意しておく必要があります。
  - つまり、以下の要件が適用されます。
    - 開発者が自分で Azure portal 上の **[許可]** ボタンを選択しておきます。
    - または、テナント管理者がアプリケーションの登録の **[API のアクセス許可]** タブにある **[{テナント ドメイン} の管理者の同意を付与/取り消す]** ボタンを選択しておきます。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](./quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。
    - または、ユーザーがアプリケーションに同意する方法を指定しておきます。 詳細については、「[個々のユーザーの同意を要求する](./v2-permissions-and-consent.md#requesting-individual-user-consent)」を参照してください。
    - または、テナント管理者がアプリケーションに同意する方法を指定しておきます。 詳細については、[管理者の同意](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)に関する記事を参照してください。

- このフローは、.NET デスクトップ、.NET Core、および UWP の各アプリに対して有効です。

同意の詳細については、[Microsoft ID プラットフォームでのアクセス許可と同意](./v2-permissions-and-consent.md)に関する記事を参照してください。

### <a name="learn-how-to-use-it"></a>使用方法の確認

# <a name="net"></a>[.NET](#tab/dotnet)

MSAL.NET では、次を使用する必要があります。

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

通常、必要なパラメーターは 1 つだけです (`scopes`)。 Windows 管理者によるポリシーの設定方法に応じて、Windows マシン上のアプリケーションではサインインしているユーザーの検索が許可されない場合があります。 その場合は、2 番目のメソッドである `.WithUsername()` を使用して、サインインしているユーザーのユーザー名を UPN 形式で渡します (例: `joe@contoso.com`)。 .NET Core では、ユーザー名を受け取るオーバーロードだけを使用できます。これは、.NET Core プラットフォームで OS に対してユーザー名を問い合わせることができないためです。

次のサンプルは、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

AcquireTokenByIntegratedWindowsAuthentication で使用可能な修飾子の一覧については、[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods) に関する記事を参照してください。

# <a name="java"></a>[Java](#tab/java)

この抜粋は、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)から取得したものです。

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

このフローは、MSAL Python ではまだサポートされていません。

# <a name="macos"></a>[macOS](#tab/macOS)

このフローは macOS には適用されません。

---

## <a name="username-and-password"></a>ユーザー名とパスワード

ユーザー名とパスワードを指定してトークンを取得することもできます。 このフローは制限があり推奨されませんが、このフローが必要となるユース ケースが存在します。

### <a name="this-flow-isnt-recommended"></a>推奨されないフロー

アプリケーションでユーザーにパスワードを求めることは安全でないため、このフローは "*推奨されません*"。 詳細については、[深刻化するパスワードの問題の解決策](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)に関する記事を参照してください。 Windows ドメイン参加済みマシン上でトークンを自動的に取得するために推奨されるフローは、[統合 Windows 認証](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)です。 [デバイス コード フロー](https://aka.ms/msal-net-device-code-flow)を使用することもできます。

> [!NOTE]
> DevOps シナリオなど、場合によっては、ユーザー名とパスワードを使用すると便利です。 ただし、独自の UI を提供する対話型のシナリオでユーザー名とパスワードを使用したい場合は、別の方法について検討してください。 ユーザー名とパスワードを使用すると、いくつかのことを放棄することになります。
>
> - 最新の ID の核となる概念。 共有シークレットが傍受される可能性があるため、パスワードはフィッシングされて再生される可能性があります。 これは、パスワードレスとは互換性がありません。
> - 対話式操作がないため、MFA を実行する必要がユーザーがサインインできない。
> - ユーザーがシングル サインオン (SSO) できない。

### <a name="constraints"></a>制約

次の制約も適用されます。

- ユーザー名とパスワードのフローは、条件付きアクセスと多要素認証との互換性がありません。 その結果、テナント管理者が多要素認証を必要とする Azure AD テナントでアプリを実行する場合は、このフローを使用できません。 これは多くの組織に該当します。
- (MSA ではなく) 職場および学校アカウントにのみ有効です。
- このフローは、.NET デスクトップと .NET Core で使用できますが、UWP では使用できません。

### <a name="b2c-specifics"></a>B2C の詳細

詳細については、「[B2C でのリソース所有者のパスワード資格情報 (ROPC)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)」を参照してください。

### <a name="use-it"></a>使用方法

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` にはメソッド `AcquireTokenByUsernamePassword` が含まれています。

次のサンプルは、シンプルなケースを示しています。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

次のサンプルは、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

`AcquireTokenByUsernamePassword` に適用できるすべての修飾子の詳細については、[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods) に関する記事を参照してください。

# <a name="java"></a>[Java](#tab/java)

次の抜粋は、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)から取得したものです。

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

この抜粋は、[MSAL Python dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)から取得したものです。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

このフローは、macOS 用の MSAL ではサポートされていません。

---

## <a name="command-line-tool-without-a-web-browser"></a>Web ブラウザーを使用しないコマンドライン ツール

### <a name="device-code-flow"></a>デバイス コード フロー

Web コントロールのないコマンドライン ツールを記述する際に前述のフローを使用できない (しない) 場合は、デバイス コード フローを使用する必要があります。

Azure AD による対話型認証には Web ブラウザーが必要です。 詳細については、[Web ブラウザーの使用](https://aka.ms/msal-net-uses-web-browser)に関する記事を参照してください。 Web ブラウザーを提供しないデバイスまたはオペレーティング システム上でユーザーを認証する場合、ユーザーはデバイス コード フローによって別のデバイス (コンピューターや携帯電話など) を使用して対話形式でサインインできます。 デバイス コード フローを使用すると、アプリケーションでは、これらのデバイスまたはOS 用に設計された 2 ステップ プロセスを通じてトークンを取得します。 このようなアプリケーションには、IoT やコマンドライン ツール (CLI) で実行されるアプリケーションがあります。 考え方は次のとおりです。

1. ユーザー認証が必要になるたびに、アプリではユーザーに対してコードを提供します。 ユーザーは、インターネットに接続されたスマートフォンなどの別のデバイスを使用して URL (例: `https://microsoft.com/devicelogin`) にアクセスするように求められます。 次に、ユーザーはそのコードを入力するように要求されます。 完了すると、Web ページにおいて通常の認証エクスペリエンス (必要に応じて、同意のプロンプトと多要素認証が含まれます) が実行されます。

2. 認証が成功すると、コマンドライン アプリはバック チャネル経由で必要なトークンを受信し、それらを使用して必要な Web API の呼び出しを実行します。

### <a name="use-it"></a>使用方法

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` には `AcquireTokenWithDeviceCode` というメソッドが含まれています。

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

このメソッドは、パラメーターとして次のものを受け取ります。

- アクセス トークンを要求する対象の `scopes`。
- `DeviceCodeResult` を受信するコールバック。

  ![DeviceCodeResult のプロパティ](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

次のサンプル コードは、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] Scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(Scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }         
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

この抜粋は、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)から取得したものです。

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

この抜粋は、[MSAL Python dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)から取得したものです。

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

このフローは macOS には適用されません。

---

## <a name="file-based-token-cache"></a>ファイルベースのトークン キャッシュ

MSAL.NET では、既定でメモリ内のトークン キャッシュが提供されています。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Windows デスクトップ アプリと Web アプリまたは Web API でカスタマイズ可能なシリアル化

.NET Framework と .NET Core では、追加の処理を行わなければ、メモリ内のトークン キャッシュがアプリケーションの実行時間の間持続します。 シリアル化がすぐに使用できる状態で提供されない理由については、MSAL .NET デスクトップまたは .NET Core アプリケーションが、コンソールまたは Windows アプリケーション (ファイル システムへのアクセスが可能) の場合もあれば、Web アプリケーションまたはWeb API の "*場合もある*" ことを思い出してください。 このような Web アプリと Web API では、ある特定のキャッシュ メカニズム (データベース、分散キャッシュ、Redis Cache など) を使用する可能性があります。 .NET デスクトップまたは .NET Core に永続的なトークン キャッシュ アプリケーションを用意するには、シリアル化をカスタマイズする必要があります。

トークン キャッシュのシリアル化に関与するクラスとインターフェイスの型は次のとおりです。

- ``ITokenCache`` では、トークン キャッシュのシリアル化要求をサブスクライブするイベントと、キャッシュをさまざまな形式 (ADAL v3.0、MSAL 2.x、および MSAL 3.x = ADAL v5.0) でシリアル化または逆シリアル化するメソッドを定義します。
- ``TokenCacheCallback`` は、シリアル化を処理できるようにイベントに渡されるコールバックです。 ``TokenCacheNotificationArgs`` の型の引数で呼び出されます。
- ``TokenCacheNotificationArgs`` では、アプリケーションの ``ClientId`` と、トークンを使用できるユーザーへの参照のみを提供します。

  ![トークン キャッシュのシリアル化の図](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> アプリケーションの `UserTokenCache` プロパティと `AppTokenCache` プロパティを呼び出すと、MSAL.NET によりトークン キャッシュが自動的に作成され、`IToken` キャッシュが提供されます。 インターフェイスを自分で実装することはできません。 カスタムのトークン キャッシュのシリアル化を実装する際には、お客様の責任で次の処理を行います。
>
> - `BeforeAccess` および `AfterAccess` イベント (または同等の "*非同期*" イベント) に対応する。 `BeforeAccess` デリゲートは、キャッシュの逆シリアル化を担当します。 `AfterAccess` デリゲートは、キャッシュのシリアル化を担当します。
> - これらのイベントの一部では、BLOB が格納されるか、または読み込まれ、イベント引数を介して任意のストレージに渡されることを理解する。

パブリック クライアント アプリケーション (デスクトップなど) と機密クライアント アプリケーション (Web アプリまたは Web API や、デーモン アプリなど) のどちらに対してトークン キャッシュのシリアル化を作成しているかに応じて、戦略は異なります。

MSAL v2.x 以降では、オプションがいくつかあります。 どれを選択するかは、 MSAL.NET 形式のみにキャッシュをシリアル化するかどうかによって異なります。これは、MSAL と共通であり、複数のプラットフォーム間でも共通である統一形式キャッシュです。 また、ADAL v3 の[レガシ](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) トークン キャッシュのシリアル化をサポートすることもできます。

ADAL.NET 3.x、ADAL.NET 5.x、および MSAL.NET の間で SSO 状態を共有するようトークン キャッシュのシリアル化をカスタマイズする方法については、サンプル「[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)」の一部で説明されています。

### <a name="simple-token-cache-serialization-msal-only"></a>トークン キャッシュの単純なシリアル化 (MSAL のみ)

次の例では、デスクトップ アプリケーション用のトークン キャッシュについて、カスタムのシリアル化の単純な実装を示します。 ここでは、ユーザーのトークン キャッシュは、アプリケーションと同じフォルダー内のファイルにあります。

アプリケーションをビルドした後、``TokenCacheHelper.EnableSerialization()`` を呼び出し、アプリケーションに `UserTokenCache` を渡すことで、シリアル化を有効にします。

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

このヘルパー クラスは次のコード スニペットのようになります。

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Windows、Mac、Linux で実行されているデスクトップ アプリケーションのパブリック クライアント アプリケーション用の製品品質トークン キャッシュ ファイルベースのシリアライザーのプレビューは、[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) オープン ソース ライブラリで入手できます。 これを、次の NuGet パッケージからアプリケーションに追加することができます。[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> [!NOTE]
> 免責事項:Microsoft.Identity.Client.Extensions.Msal ライブラリは MSAL.NET の拡張機能です。 これらのライブラリ内のクラスは、今後 MSAL.NET に現状のまま、または破壊的変更が適用された状態で追加される可能性があります。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>トークン キャッシュのデュアル シリアル化 (MSAL 統一キャッシュと ADAL v3)

トークン キャッシュのシリアル化を統一キャッシュ形式で実装することもできます。 この形式は、同じプラットフォーム上の ADAL.NET 4.x と MSAL.NET 2.x に共通であり、同じ世代以前のその他の MSAL でよく使用されます。 次のコードを参考にしてください。

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

今回のヘルパー クラスは次のコードのようになります。

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [デスクトップ アプリから Web API を呼び出す](scenario-desktop-call-api.md)