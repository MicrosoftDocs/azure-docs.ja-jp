---
title: Web API を呼び出す Web アプリ (コードの構成) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリをビルドする方法について学習します (アプリのコード構成)
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
ms.openlocfilehash: 6c78a951258e3c279f96f44ceac469e4c38cf22c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785572"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Web API を呼び出す Web アプリ - コードの構成

[ユーザーをサインインさせる Web アプリのシナリオ](scenario-web-app-sign-user-overview.md)で示されているように、サインインしているユーザーを Open ID Connect (OIDC) ミドルウェアに委任する場合、OIDC プロセスで接続する必要があります。 これを行う方法は使用したフレームワーク (ここでは ASP.NET と ASP.NET Core) に応じて異なりますが、最後にミドルウェア OIDC イベントをサブスクライブします。 原則は以下のとおりです。

- ASP.NET または ASP.NET Core で認証コードを要求できるようにします。 これを行うことで、ASP.NET/ASP.NET Core によってユーザーをサインインおよび同意させることができます。
- Web アプリによって認証コードの受信をサブスクライブします。
- 認証コードを受信すると、MSAL ライブラリを使用して、コードと結果のアクセス トークンを引き換え、トークン キャッシュに格納されたトークンを更新します。 そこから、アプリケーションの他の部分でキャッシュを使用し、その他のトークンを通知せずに取得することができます。

## <a name="libraries-supporting-web-app-scenarios"></a>Web アプリをサポートするライブラリのシナリオ

Web アプリの認証コード フローをサポートするライブラリは以下のとおりです。

| MSAL ライブラリ | 説明 |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | サポートされるプラットフォームは、.NET Framework と .NET Core プラットフォームです (UWP、Xamarin.iOS、Xamarin.Android のプラットフォームはパブリック クライアント アプリケーションのビルドに使用されるため、これら以外) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | 進行中の開発 - パブリック プレビュー中 |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | 進行中の開発 - パブリック プレビュー中 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 構成

ASP.NET Core では、`Startup.cs` ファイルで物事が行われます。 `OnAuthorizationCodeReceived` Open ID Connect イベントをサブスクライブし、このイベントから、MSAL.NET のメソッド `AcquireTokenFromAuthorizationCode` を呼び出します。これには、トークン キャッシュに格納する影響と、要求されたスコープに対するアクセス トークン、期限に近づいたときにアクセスを更新したり、別のリソースがない場合に同じユーザーの代理でトークンを取得したりするために使用される更新トークンがあります。

以下のコードのコメントは、MSAL.NET と ASP.NET Core を組み込む難しい側面を理解するのに役立ちます。 完全な詳細は、[ASP.NET Core Web アプリ増分チュートリアルの第 2 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)で提供されています

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

ASP.NET Core では、Confidential クライアント アプリケーションのビルドで、HttpContext にある情報を使用します。 この HttpContext では、Web アプリの URL とサインインしているユーザー (`ClaimsPrincipal` 内) について把握します。 ASP.NET Core 構成も使用します。これには、"AzureAD" セクションがあり、`_applicationOptions` データ構造にバインドされます。 最後に、アプリケーションではトークン キャッシュを保持する必要があります。

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode` では、実際に ASP.NET によって要求される認証コードを引き換え、MSAL.NET ユーザー トークン キャッシュに追加されるトークンを取得します。 そこから、これらが ASP.NET Core コントローラーで使用されます。

## <a name="aspnet-configuration"></a>ASP.NET 構成

OpenIdConnect の構成と `App_Start\Startup.Auth.cs` ファイルで発生する `OnAuthorizationCodeReceived` イベントへのサブスクリプションを除き、ASP.NET で処理される方法は同様です。 ここでは、構成ファイルで RedirectUri を指定する (少し堅牢さが低減する) 必要があることを除き、同様の概念であることがわかります。

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>ASP.NET (Core) Web アプリ用の MSAL.NET トークン キャッシュ

Web アプリ (実際のところは Web API) で、トークン キャッシュの実装は、デスクトップ アプリケーション トークン キャッシュの実装 ([ファイル ベース](scenario-desktop-acquire-token.md#file-based-token-cache)であることが多い) とは異なります。 ASP.NET/ASP.NET Core セッション、Redis キャッシュ、データベース、または Azure Blob Storage も使用できます。 上記のコード スニペットでは、これはキャッシュ サービスにバインドされる `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` メソッドの呼び出しのオブジェクトです。 ここで行われる内容の詳細は、このシナリオ ガイドの範囲を超えていますが、リンクを下記に示します。

> [!IMPORTANT]
> Web アプリと Web API では、ユーザーごと (アカウントごと) に 1 つのトークン キャッシュが必要となることを認識していることが非常に重要です。 アカウントごとにトークン キャッシュをシリアル化する必要があります。

Web アプリと Web API 用のトークン キャッシュの使用方法の例については、フェーズ「[2-2 Token Cache](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)」にある [ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)に関するページを参照してください。 実装については、[microsoft-authentication-extensions-for-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) ライブラリにある [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) フォルダー ([Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) フォルダー内) を参照してください。

## <a name="next-steps"></a>次の手順

この時点で、ユーザーがサインインすると、トークンはトークン キャッシュに格納されます。 これが Web アプリの他の部分でどのように使用されるかを見てみましょう。

> [!div class="nextstepaction"]
> [Web アプリにサインインする](scenario-web-app-call-api-sign-in.md)
