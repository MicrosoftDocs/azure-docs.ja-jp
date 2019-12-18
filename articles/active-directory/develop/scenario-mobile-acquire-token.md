---
title: モバイル アプリで Web API を呼び出すトークンを取得する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法について説明します (アプリのトークンの取得)
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
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1101a1923e094c22e9f6a82853f4dec4deb089a1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919921"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Web API を呼び出すモバイル アプリ - トークンの取得

保護された Web API の呼び出しを開始する前に、お使いのアプリにアクセス トークンが必要です。 この記事では、Microsoft Authentication Library (MSAL) を使用してトークンを取得するためのプロセスについて説明します。

## <a name="scopes-to-request"></a>要求するスコープ

トークンを要求する場合、スコープを定義する必要があります。 スコープでは、お使いのアプリでアクセスできるデータを決定します。  

最も簡単な方法は、必要な Web API の `App ID URI` をスコープ `.default` と組み合わせることです。 これにより、お使いのアプリがポータル内で設定されたすべてのスコープを必要とすることが Microsoft ID プラットフォームに通知されます。

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>トークンを取得する

### <a name="acquire-tokens-via-msal"></a>MSAL によるトークンの取得

MSAL により、アプリはサイレントかつ対話的にトークンを取得できます。 これらのメソッドを呼び出すだけで、MSAL は、要求されたスコープのアクセス トークンを返します。 正しいパターンは、サイレント要求を実行し、対話型の要求にフォールバックすることです。

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
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

**まず、トークンをサイレントで取得します。**

Objective-C:

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
            
        // Access token to call the Web API
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
 
Swift:

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
            
    // Access token to call the Web API
    let accessToken = authResult.accessToken
}
```

**次に、MSAL から `MSALErrorInteractionRequired` が返された場合は、トークンを対話形式で取得します。**

Objective-C:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithParentViewController:viewController];
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

Swift:

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(parentViewController: viewController)
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

iOS と macOS の MSAL では、トークンを対話形式またはサイレントで取得するときに、さまざまな修飾子がサポートされます。
* [トークンを取得するときの共通パラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [対話型のトークン取得のパラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [サイレントのトークン取得のパラメーター](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

次の例は、Microsoft Graph を使用してユーザーのプロファイルを読み取るためにトークンを対話形式で取得する最小限のコードを示しています。

```CSharp
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

`AcquireTokenInteractive` の必須のパラメーターは 1 つだけです (``scopes``)。このパラメーターには、トークンが必要なスコープを定義する文字列のリストが含まれています。 Microsoft Graph 用のトークンの場合、必要なスコープは各 Microsoft Graph API の API リファレンスの "アクセス許可" というセクションにあります。 たとえば、[ユーザーの連絡先を一覧表示する](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts)には、"User.Read", "Contacts.Read" スコープを使用する必要があります。 「[Microsoft Graph のアクセス許可のリファレンス](https://developer.microsoft.com/graph/docs/concepts/permissions_reference)」も参照してください。

アプリの構築時に指定していない場合、Android では、親アクティビティを指定して (`.WithParentActivityOrWindow` を使用。以下を参照)、操作後にその親アクティビティにトークンが戻るようにする必要もあります。 そのように指定しないと、`.ExecuteAsync()` の呼び出し時に例外がスローされます。

#### <a name="specific-optional-parameters-in-msalnet"></a>MSAL.NET の特定の省略可能なパラメーター

##### <a name="withprompt"></a>WithPrompt

`WithPrompt()` は、Prompt を指定してユーザーとのインタラクティビティを制御するために使用します。

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

このクラスでは次の定数を定義します。

- ``SelectAccount``: ユーザーがセッションを確立しているアカウントを含む、アカウントの選択ダイアログを STS で強制的に表示します。 このオプションは、アプリケーション開発者が複数の ID の中からユーザーに選択させる場合に役立ちます。 このオプションを使用すると、MSAL から ID プロバイダーに ``prompt=select_account`` が送信されます。 これは既定のオプションであり、使用可能な情報 (アカウント、ユーザーのセッションの有無など) に基づいて最適なエクスペリエンスが提供されます 。正当な理由がない限り、このオプションを変更しないでください。
- ``Consent``: 以前に同意が得られている場合であっても、アプリケーション開発者がユーザーの同意を求めることができます。 この場合、MSAL から ID プロバイダーに `prompt=consent` が送信されます。 このオプションは、組織のガバナンスによって、アプリケーションを使用するたびに同意ダイアログをユーザーに対して表示することを求めるような、セキュリティを重視する一部のアプリケーションで使用できます。
- ``ForceLogin``: このユーザー プロンプトが不要な場合であっても、アプリケーション開発者がサービスによってユーザーに資格情報の入力を求めるように指定することができます。 このオプションは、トークンの取得に失敗し、ユーザーに再度のサインインを求める場合に役立ちます。 この場合、MSAL から ID プロバイダーに `prompt=login` が送信されます。 前述のとおり、このオプションは、組織のガバナンスによって、アプリケーションの特定の部分にアクセスするたびにユーザーによる再ログインを求めるような、セキュリティを重視する一部のアプリケーションで使用されます。
- ``Never`` (.NET 4.5 および WinRT の場合のみ): ユーザーの操作を求めませんが、代わりに非表示の埋め込み Web ビューに格納された Cookie の使用を試行します (以下に示すMSAL.NET の Web ビューを参照)。 このオプションの使用は失敗する可能性があります。その場合、UI 操作が必要であることを通知する例外が `AcquireTokenInteractive` によってスローされ、別の `Prompt` パラメーターを使用する必要があります。
- ``NoPrompt``:ID プロバイダーにプロンプトが送信されません。 このオプションは、Azure AD B2C のプロファイルの編集ポリシーに対してのみ有効です (「[B2C の詳細](https://aka.ms/msal-net-b2c-specificities)」を参照)。

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

この修飾子は、複数のリソースに対するユーザーの事前の同意を求める (通常は MSAL.NET/Microsoft ID プラットフォーム v2.0 で使用されるインクリメンタルな同意を使用しない) 高度なシナリオで使用されます。 詳細については、「[複数のリソースでユーザーの同意を事前に取得する方法](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources)」を参照してください。

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>その他の省略可能なパラメーター

`AcquireTokenInteractive` のその他すべての省略可能なパラメーターについては、[AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods) の参照ドキュメントに記載されています。

### <a name="acquire-tokens-via-the-protocol"></a>プロトコルを使用してトークンを取得する

プロトコルを直接使用することはお勧めできません。 これを行った場合、アプリは一部シングル サインオン (SSO)、デバイス管理、および条件付きアクセスのシナリオをサポートしなくなります。

プロトコルを使用してモバイル アプリのトークンを取得するときに、認証コードを取得してトークンと交換するという 2 つの要求を行う必要があります。

#### <a name="get-authorization-code"></a>承認コードを取得する

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>アクセスと更新トークンを取得する

```Text
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

> [!div class="nextstepaction"]
> [Web API の呼び出し](scenario-mobile-call-api.md)
