---
title: Web API を呼び出すデスクトップ アプリ (アプリのトークンの取得) - Microsoft ID プラットフォーム
description: Web API を呼び出すデスクトップ アプリをビルドする方法について説明します (アプリのトークンの取得)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecf5b874345a94e8fd3d3a0783f8e48c7484377d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111267"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Web API を呼び出すデスクトップ アプリ - トークンの取得

ビルドした `IPublicClientApplication` を使用してトークンを取得し、Web API の呼び出しに使用します。

## <a name="recommended-pattern"></a>推奨パターン

Web API はその `scopes` によって定義されます。 どのようなエクスペリエンスをアプリケーション内で提供する場合であっても、使用するパターンは次のようになります。

- `AcquireTokenSilent` を呼び出すことによって、トークン キャッシュからのトークンの取得を体系的に試行します。
- この呼び出しが失敗した場合は、`AcquireToken` フローを使用します (ここでは `AcquireTokenXX` で表されています)。

```CSharp
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

ここでは、デスクトップ アプリケーション内でトークンを取得するさまざまな方法の詳細を示します。

## <a name="acquiring-a-token-interactively"></a>トークンを対話形式で取得する

次の例は、Microsoft Graph を使用してユーザーのプロファイルを読み取るためにトークンを対話形式で取得する最小限のコードを示しています。

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` の必須のパラメーターは 1 つだけです (``scopes``)。このパラメーターには、トークンが必要なスコープを定義する文字列のリストが含まれています。 Microsoft Graph 用のトークンの場合、必要なスコープは各 Microsoft Graph API の API リファレンスの "アクセス許可" というセクションにあります。 たとえば、[ユーザーの連絡先を一覧表示する](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)には、"User.Read", "Contacts.Read" スコープを使用する必要があります。 「[Microsoft Graph のアクセス許可のリファレンス](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)」も参照してください。

Android では、親アクティビティを指定して (`.WithParentActivityOrWindow` を使用。以下を参照)、操作後にその親アクティビティにトークンが戻るようにする必要もあります。 そのように指定しないと、`.ExecuteAsync()` の呼び出し時に例外がスローされます。

### <a name="specific-optional-parameters"></a>特定の省略可能なパラメーター

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

対話型の操作には UI が重要です。 `AcquireTokenInteractive` には、特定の省略可能なパラメーターが 1 つあり、それをサポートするプラットフォームに対して親 UI を指定できます。 デスクトップ アプリケーションで使用する場合、`.WithParentActivityOrWindow` の型はプラットフォームによって異なります。

```CSharp
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
- Windows では、埋め込みブラウザーが適切な UI 同期コンテキストを取得するように、UI スレッドから `AcquireTokenInteractive` を呼び出す必要があります。  UI スレッドから呼び出さない場合は、メッセージが適切にポンプされなかったり、UI によるデッドロックが発生したりする可能性があります。 UI スレッドでない場所で UI スレッドから MSAL を呼び出す方法の 1 つとしては、WPF で `Dispatcher` を使用します。
- WPF を使用している場合に WPF コントロールからウィンドウを取得するには、`WindowInteropHelper.Handle` クラスを使用できます。 呼び出しは WPF コントロール (`this`) から次のように行われます。
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` は、Prompt を指定してユーザーとのインタラクティビティを制御するために使用します。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

このクラスでは次の定数を定義します。

- ``SelectAccount``: ユーザーがセッションを確立しているアカウントを含む、アカウントの選択ダイアログを STS で強制的に表示します。 このオプションは、アプリケーション開発者が複数の ID の中からユーザーに選択させる場合に役立ちます。 このオプションを使用すると、MSAL から ID プロバイダーに ``prompt=select_account`` が送信されます。 これは既定のオプションであり、使用可能な情報 (アカウント、ユーザーのセッションの有無など) に基づいて最適なエクスペリエンスが提供されます 。正当な理由がない限り、このオプションを変更しないでください。
- ``Consent``: 以前に同意が得られている場合であっても、アプリケーション開発者がユーザーの同意を求めることができます。 この場合、MSAL から ID プロバイダーに `prompt=consent` が送信されます。 このオプションは、組織のガバナンスによって、アプリケーションを使用するたびに同意ダイアログをユーザーに対して表示することを求めるような、セキュリティを重視する一部のアプリケーションで使用できます。
- ``ForceLogin``: このユーザー プロンプトが不要な場合であっても、アプリケーション開発者がサービスによってユーザーに資格情報の入力を求めるように指定することができます。 このオプションは、トークンの取得に失敗し、ユーザーに再度のサインインを求める場合に役立ちます。 この場合、MSAL から ID プロバイダーに `prompt=login` が送信されます。 前述のとおり、このオプションは、組織のガバナンスによって、アプリケーションの特定の部分にアクセスするたびにユーザーによる再ログインを求めるような、セキュリティを重視する一部のアプリケーションで使用されます。
- ``Never`` (.NET 4.5 および WinRT の場合のみ): ユーザーの操作を求めませんが、代わりに非表示の埋め込み Web ビューに格納された Cookie の使用を試行します (以下に示すMSAL.NET の Web ビューを参照)。 このオプションの使用は失敗する可能性があります。その場合、UI 操作が必要であることを通知する例外が `AcquireTokenInteractive` によってスローされ、別の `Prompt` パラメーターを使用する必要があります。
- ``NoPrompt``:ID プロバイダーにプロンプトが送信されません。 このオプションは、Azure AD B2C のプロファイルの編集ポリシーに対してのみ有効です (「[B2C の詳細](https://aka.ms/msal-net-b2c-specificities)」を参照)。

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

この修飾子は、複数のリソースに対するユーザーの事前の同意を求める (通常は MSAL.NET/Microsoft ID プラットフォーム v2.0 で使用されるインクリメンタルな同意を使用しない) 高度なシナリオで使用されます。 詳細については、「[複数のリソースでユーザーの同意を事前に取得する方法](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)」を参照してください。

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>拡張ポイントとしての WithCustomWebUi

`WithCustomWebUi` は、パブリック クライアント アプリケーション内で独自の UI を提供し、ユーザーが ID プロバイダーの /Authorize エンドポイントを経由し、サインインして同意できるようにするための拡張ポイントです。 MSAL.NET では、その後で認証コードを引き換えて、トークンを取得できます。 たとえば、電子アプリケーション (VS フィードバックなど) に Web 操作を提供させて、ほとんどの処理を MSAL.NET に任せるために Visual Studio で使用されます。 また、UI オートメーションを提供する場合に使用することもできます。 パブリック クライアント アプリケーションの場合、MSAL.NET では PKCE 標準 ([RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)) を使用してセキュリティを確保します。コードを引き換えることができるのは MSAL.NET のみです。

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>WithCustomWebUi を使用する方法

`.WithCustomWebUI` を使用するには、次の手順を実行する必要があります。
  
  1. `ICustomWebUi` インターフェイスを実装します ([こちら](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)を参照)。 基本的には、1 つのメソッド `AcquireAuthorizationCodeAsync` を実装する必要があります。このメソッドでは、(MSAL.NET によって計算される) 認証コード URL を受け入れ、ユーザーに ID プロバイダーを操作させて、ID プロバイダーが実装のコールバックに使用した URL (認証コードを含む) を返します。 問題がある場合は、MSAL と適切に連携する `MsalExtensionException` 例外が実装によってスローされます。
  2. `AcquireTokenInteractive` の呼び出しでは、カスタム Web UI のインスタンスを渡す `.WithCustomUI()` 修飾子を使用できます。

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>テスト オートメーションにおける ICustomWebUi の実装例 - SeleniumWebUI

MSAL.NET チームでは、この拡張メカニズムを活用するために UI テストを書き換えました。 MSAL.NET のソース コードで [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) クラスを確認することができます。

#### <a name="other-optional-parameters"></a>その他の省略可能なパラメーター

`AcquireTokenInteractive` のその他すべての省略可能なパラメーターについては、[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) の参照ドキュメントに記載されています。

## <a name="integrated-windows-authentication"></a>統合 Windows 認証

ドメインまたは Azure AD 参加済みのマシンでドメイン ユーザーにサインインする場合は、次のコードを使用する必要があります。

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>制約

- AcquireTokenByIntegratedWindowsAuth (IWA) は、**フェデレーション** ユーザー (Active Directory で作成され、Azure Active Directory によって支援されているユーザー) に対してのみ使用できます。 AAD で直接作成され、AD による支援のないユーザー (**マネージド** ユーザー) はこの認証フローを使用できません。 この制限は、ユーザー名/パスワードのフローには影響しません。
- IWA は、.NET Framework、.NET Core、および UWP プラットフォーム用に記述されたアプリを対象としています。
- IWA では MFA (多要素認証) をバイパスしません。 MFA が構成されている状況では、MFA チャレンジが必要な場合に IWA が失敗する可能性があります。これは、MFA でユーザーの操作が必要になるためです。
  > [!NOTE]
  > これには注意が必要です。 IWA は非対話型ですが、2FA にはユーザーの操作が必要です。 ID プロバイダーが 2FA の実行を要求するタイミングの制御は、ユーザーではなくテナント管理者が行います。 当社の観測によると、2FA が必要なのは、他の国からログインする場合と VPN 経由で企業ネットワークに接続されていない場合です。ただし、VPN 経由で接続されている場合であっても 2FA が必要になる可能性があります。 確定的なルール セットを想定しないでください。Azure Active Directory では AI を使用して、2FA が必要かどうかを継続的に学習します。 IWA が失敗した場合は、ユーザー プロンプト (対話型認証またはデバイス コード フロー) にフォールバックする必要があります。

- `PublicClientApplicationBuilder` で渡される機関の要件は次のとおりです。
  - tenant-ed (`https://login.microsoftonline.com/{tenant}/` の形式。`tenant` は、テナント ID を表す GUID またはテナントに関連付けられているドメイン)
  - 任意の職場および学校アカウント用 (`https://login.microsoftonline.com/organizations/`)

  > Microsoft の個人用アカウントはサポートされていません (/common テナントまたは /consumers テナントを使用することはできません)。

- 統合 Windows 認証はサイレント フローであるため、次の要件が適用されます。
  - アプリケーションのユーザーが、アプリケーションの使用に事前に同意しておく必要があります。
  - または、テナント管理者が、テナント内のすべてのユーザーによるアプリケーションの使用に事前に同意しておく必要があります。
  - つまり、以下の要件が適用されます。
    - 開発者が自分用に Azure portal 上の **[許可]** ボタンをクリックしておきます。
    - または、テナント管理者がアプリケーションの登録ページの **[API のアクセス許可]** タブにある **[{テナント ドメイン} の管理者の同意を付与/取り消す]** ボタンをクリックしておきます (「[Web API にアクセスするためのアクセス許可を追加する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)」を参照)。
    - または、ユーザーがアプリケーションに同意する方法を指定しておきます (「[個々のユーザーの同意を要求する](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)」を参照)。
    - または、テナント管理者がアプリケーションに同意する方法を指定しておきます ([管理者の同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)に関するセクションを参照)。

- このフローは、.NET デスクトップ、.NET Core、および Windows ユニバーサル (UWP) の各アプリに対して有効です。 .NET Core では、ユーザー名を受け取るオーバーロードだけを使用できます。これは、.NET Core プラットフォームで OS に対してユーザー名を問い合わせることができないためです。
  
同意の詳細については、[v2.0 のアクセス許可と同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)に関するページを参照してください。

### <a name="how-to-use-it"></a>使用方法

通常、必要なパラメーターは 1 つだけです (`scopes`)。 ただし、Windows 管理者によるポリシーの設定方法に応じて、Windows マシン上のアプリケーションではログインしているユーザーの検索が許可されない場合があります。 その場合は、2 番目のメソッドである `.WithUsername()` を使用して、ログインしているユーザーのユーザー名を UPN 形式 (`joe@contoso.com`) で渡します。

次の例は、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```CSharp
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

AcquireTokenByIntegratedWindowsAuthentication で使用可能な修飾子の一覧については、[AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods) を参照してください。

## <a name="username--password"></a>ユーザー名/パスワード

ユーザー名とパスワードを指定してトークンを取得することもできます。 このフローは制限があり推奨されませんが、このフローが必要となるユース ケースが存在します。

### <a name="this-flow-isnt-recommended"></a>推奨されないフロー

ユーザーにパスワードをたずねるアプリケーションは安全でないため、このフローは**推奨されません**。 この問題の詳細については、[この記事](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)を参照してください。 Windows ドメイン参加済みマシン上でトークンを自動的に取得するために推奨されるフローは、[統合 Windows 認証](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication)です。 それ以外の場合は、[デバイス コード フロー](https://aka.ms/msal-net-device-code-flow)を使用することもできます。

> これが役立つケース (DevOps のシナリオ) もありますが、独自の UI を提供する対話形式のシナリオでユーザー名/パスワードを使用する場合は、別の方法について検討してください。 ユーザー名/パスワードを使用すると、いくつかのことを利用できなくなります。

> - 最新の ID のコア テナント: パスワードがフィッシングされて再生されます。 インターセプトできる共有シークレットのこの概念を使用しているためです。
> これは、パスワードなしとは互換性がありません。
> - MFA を実行する必要のあるユーザーがサインインできない (対話式操作がないため)
> - ユーザーがシングル サインオンできない

### <a name="constraints"></a>制約

次の制約も適用されます。

- ユーザー名/パスワードのフローは、条件付きアクセスと多要素認証との互換性がありません。その結果、テナント管理者が多要素認証を必要とする Azure AD テナントでアプリを実行する場合は、このフローを使用できません。 これは多くの組織に該当します。
- 職場および学校アカウント (MSA ではありません) にのみ有効です。
- このフローは、.NET デスクトップと .NET Core で使用できますが、UWP では使用できません。

### <a name="b2c-specifics"></a>B2C の詳細

B2C で ROPC を使用する方法の詳細については、[こちら](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c)を参照してください。

### <a name="how-to-use-it"></a>使用方法

`IPublicClientApplication` にはメソッド `AcquireTokenByUsernamePassword` が含まれています。

次の例は、シンプルなケースを示しています。

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

次の例は、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

`AcquireTokenByUsernamePassword` に適用できるすべての修飾子の詳細については、[AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods) を参照してください。

## <a name="command-line-tool-without-web-browser"></a>コマンドライン ツール (Web ブラウザーを使用しない)

### <a name="device-code-flow-why-and-how"></a>デバイス コード フローを使用する理由と方法

(Web コントロールのない) コマンドライン ツールを記述する際に前述のフローを使用できない (しない) 場合は、`AcquireTokenWithDeviceCode` を使用する必要があります。

Azure AD による対話型認証には Web ブラウザーが必要です (詳細については、[Web ブラウザーの使用方法](https://aka.ms/msal-net-uses-web-browser)に関するページを参照)。 ただし、Web ブラウザーを提供しないデバイスまたはオペレーティング システム上でユーザーを認証する場合、ユーザーはデバイス コード フローによって別のデバイス (たとえば、別のコンピューターや携帯電話) を使用して対話形式でサインインできます。 デバイス コード フローを使用すると、アプリケーションでは、これらのデバイス/OS 用に特別に設計された 2 ステップ プロセスを通じてトークンを取得します。 このようなアプリケーションには、iOT で実行されているアプリケーションやコマンドライン ツール (CLI) などがあります。 考え方は次のとおりです。

1. ユーザー認証が必要になるたびに、アプリがコードを提供し、ユーザーに別のデバイス (インターネットに接続されたスマートフォンなど) を使用して特定の URL (たとえば、`https://microsoft.com/devicelogin`) に移動するよう求めます。ここで、ユーザーはコードの入力を求められます。 完了すると、Web ページにおいて通常の認証エクスペリエンス (必要に応じて、同意のプロンプトや多要素認証など) が実行されます。

2. 認証が成功すると、コマンド ライン アプリはバック チャネル経由で必要なトークンを受信し、そのトークンを使用して必要な Web API の呼び出しを実行します。

### <a name="code"></a>コード

`IPublicClientApplication` には `AcquireTokenWithDeviceCode` というメソッドが含まれています。

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

このメソッドは、パラメーターとして次のものを受け取ります。

- アクセス トークンを要求する `scopes`
- `DeviceCodeResult` を受信するコールバック

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

次のサンプル コードは、最新のケース (および取得可能な例外の種類の説明とその軽減策) を示しています。

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>ファイル ベースのトークン キャッシュ

MSAL.NET では、既定でメモリ内のトークン キャッシュが提供されています。

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Windows デスクトップ アプリと Web アプリ/Web API でカスタマイズ可能なシリアル化

.NET Framework と .NET Core では、追加の処理を行わなければ、メモリ内のトークン キャッシュがアプリケーションの実行時間の間持続します。 シリアル化がすぐに使用できる状態で提供されない理由については、MSAL .NET デスクトップ/コア アプリケーションの形式がコンソール/Windows アプリケーション (ファイル システムへのアクセスが可能) である一方、Web アプリケーション/Web API の**場合もある**ことを思い出してください。 このような Web アプリと Web API では、ある特定のキャッシュ メカニズム (データベース、分散キャッシュ、Redis Cache など) を使用する可能性があります。 .NET デスクトップまたは .NET Core で永続的なトークン キャッシュ アプリケーションを入手するには、シリアル化をカスタマイズする必要があります。

トークン キャッシュのシリアル化に関与するクラスとインターフェイスの型は次のとおりです。

- ``ITokenCache`` では、トークン キャッシュのシリアル化要求をサブスクライブするイベントと、キャッシュをさまざまな形式 (ADAL v3.0、MSAL 2.x、および MSAL 3.x = ADAL v5.0) でシリアル化または逆シリアル化するメソッドを定義します。
- ``TokenCacheCallback`` は、シリアル化を処理できるようにイベントに渡されるコールバックです。 ``TokenCacheNotificationArgs`` の型の引数で呼び出されます。
- ``TokenCacheNotificationArgs`` では、アプリケーションの ``ClientId`` と、トークンを使用できるユーザーへの参照のみを提供します。

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET により、トークン キャッシュが自動的に作成され、アプリケーションの `GetUserTokenCache` メソッドと `GetAppTokenCache` メソッドを呼び出すと `IToken` キャッシュが提供されます。 インターフェイスを自分で実装することはできません。 カスタムのトークン キャッシュのシリアル化を実装する際には、お客様の責任で次の処理を行います。
>
> - `BeforeAccess` と `AfterAccess` の各 "イベント" に対応します。 `BeforeAccess` デリゲートではキャッシュの逆シリアル化を行い、`AfterAccess` デリゲートではキャッシュのシリアル化を行います。
> - これらのイベントの一部では、BLOB が格納されるか、または読み込まれ、イベント引数を介して任意のストレージに渡されます。

パブリック クライアント アプリケーション (デスクトップ) と機密クライアント アプリケーション (Web アプリ/Web API、デーモン アプリ) のどちらに対してトークン キャッシュのシリアル化を作成しているかに応じて、戦略は異なります。

MSAL V2.x 以降では、キャッシュを MSAL.NET 形式にシリアル化する場合 (MSAL と共通で、プラットフォーム全体の統一形式キャッシュ)、または ADAL V3 の[従来の](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)トークン キャッシュのシリアル化のサポートも必要な場合に応じて、複数のオプションが用意されています。

ADAL.NET 3.x、ADAL.NET 5.x、および MSAL.NET の間で SSO 状態を共有するようトークン キャッシュのシリアル化をカスタマイズする方法については、サンプル「[active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)」を参照してください。

### <a name="simple-token-cache-serialization-msal-only"></a>トークン キャッシュの単純なシリアル化 (MSAL のみ)

デスクトップ アプリケーション用のトークン キャッシュについて、カスタムのシリアル化の単純な実装の例を次に示します。 ここでは、ユーザーのトークン キャッシュは、アプリケーションと同じフォルダー内のファイルです。

アプリケーションをビルドした後、アプリケーションに `UserTokenCache` を渡す ``TokenCacheHelper.EnableSerialization()`` を呼び出すことで、シリアル化を有効にします。

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

このヘルパー クラスは次のコード スニペットのようになります。

```CSharp
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

パブリック クライアント アプリケーション (Windows、Mac、Linux で実行されているデスクトップ アプリケーション) 用の製品品質トークン キャッシュ ファイル ベースのシリアライザーのプレビューは、[Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) オープン ソース ライブラリで入手できます。 これを、次の NuGet パッケージからアプリケーションに追加することができます。[Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/)。

> 免責事項: Microsoft.Identity.Client.Extensions.Msal ライブラリは MSAL.NET の拡張機能です。 これらのライブラリ内のクラスは、今後 MSAL.NET に現状のまま、または破壊的変更が適用された状態で追加される可能性があります。

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>トークン キャッシュのデュアル シリアル化 (MSAL 統一キャッシュと ADAL v3)

トークン キャッシュのシリアル化を統一キャッシュ形式の両方 (同じプラットフォーム上の ADAL.NET 4.x と MSAL.NET 2.x に共通、および同じ世代以前のその他の MSAL を使用) で実装する場合は、次のコードを参考にすることができます。

```CSharp
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

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デスクトップ アプリから Web API を呼び出す](scenario-desktop-call-api.md)
