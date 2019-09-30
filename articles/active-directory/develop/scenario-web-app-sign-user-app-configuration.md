---
title: ユーザーをサインインさせる Web アプリ (コードの構成) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリを構築する方法について説明します (コードの構成)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a746b0f6d85e3f012cdd2e78fff8cd10a586950
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086749"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>ユーザーをサインインさせる Web アプリ - コードの構成

ユーザーをサインインさせる Web アプリのコードを構成する方法について説明します。

## <a name="libraries-used-to-protect-web-apps"></a>Web アプリを保護するために使用されるライブラリ

<!-- This section can be in an include for Web App and Web APIs -->
Web アプリ (および Web API) を保護するために使用されるライブラリは次のとおりです。

| プラットフォーム | ライブラリ | 説明 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identity Model Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET および ASP.NET Core によって直接使用される Microsoft Identity Extensions for .NET は、.NET Framework と .NET Core の両方で実行される DLL のセットを提案します。 ASP.NET/ASP.NET Core Web アプリから、**TokenValidationParameters** クラスを使用してトークン検証を制御できます (特に、一部の ISV シナリオで)。 |
| ![Java](media/sample-v2-code/logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | MSAL for Java - 現在、パブリック プレビュー段階です |
| ![Python](media/sample-v2-code/logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL for Python - 現在、パブリック プレビュー段階です |

この記事以降のコード スニペットは以下から抽出されたものです。

- [ASP.NET Core Web アプリの増分チュートリアル、第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)。
- [ASP.NET Web アプリのサンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)
- [Microsoft Graph を呼び出す Java Web アプリケーション](https://github.com/Azure-Samples/ms-identity-java-webapp)、msal4j Web アプリ サンプル
- [Microsoft Graph を呼び出す Python Web アプリケーション](https://github.com/Azure-Samples/ms-identity-python-webapp)、MSAL.Python Web アプリ サンプル

完全な実装の詳細については、これらのチュートリアルとサンプルをご覧ください。

## <a name="configuration-files"></a>構成ファイル

Microsoft ID プラットフォームを使用してユーザーをサインインさせる Web アプリケーションは、通常、構成ファイルを使用して構成します。 入力する必要がある設定は次のとおりです。

- アプリを実行する場合はクラウド `Instance` (国内のクラウドのインスタンスの場合)
- `tenantId` の対象ユーザー
- Azure portal からコピーした、アプリケーションの `clientId`

場合によっては、アプリケーションを `authority` によってパラメーター化できます。これは、`instance` と `tenantId` の連結です

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、これらの設定は `appsettings.json` ファイルの "AzureAD" セクションにあります。

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.NET Core には、アプリケーションの URL (`applicationUrl`) と SSL ポート (`sslPort`) およびさまざまなプロファイルが含まれる別のファイル (`properties\launchSettings.json`) があります。

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Azure portal では、アプリケーションの **[認証]** ページに登録する必要がある応答 URI は、これらの URL に一致する必要があります。つまり、上記の 2 つの構成ファイルでは、applicationUrl は `http://localhost:3110` ですが、`sslPort` が (44321) と指定されており、`appsettings.json` に定義されているように `CallbackPath` は `/signin-oidc` のため、それらは `https://localhost:44321/signin-oidc` になります。
  
同様に、サインアウト URI は `https://localhost:44321/signout-callback-oidc` に設定されます。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET では、アプリケーションは `Web.Config` ファイルを介して構成されます。

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Azure portal では、アプリケーションの **[認証]** ページで登録する必要がある応答 URI は、これらの URL (つまり `https://localhost:44326/`) と一致している必要があります。

# <a name="javatabjava"></a>[Java](#tab/java)

Java では、構成は `src/main/resources` の下の `application.properties` ファイルにあります

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

Azure portal では、アプリケーションの **[認証]** ページで登録する必要がある応答 URI は、アプリケーションによって定義されている redirectUri (つまり `http://localhost:8080/msal4jsample/secure/aad` と `http://localhost:8080/msal4jsample/graph/users`) と一致している必要があります

# <a name="pythontabpython"></a>[Python](#tab/python)

[app_config.py](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/web_app_sample/app_config.py) の Python 構成ファイルを次に示します

```Python
AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
CLIENT_ID = "Enter_the_Application_Id_here"
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
SCOPE = ["https://graph.microsoft.com/User.Read"]
REDIRECT_URI = "http://localhost:5000/getAToken"
```

---

## <a name="initialization-code"></a>初期化コード

初期化コードは、プラットフォームによって異なります。 ASP.NET Core と ASP.NET の場合、ユーザーのサインインは OpenIDConnect ミドルウェアに委任されます。 現在、ASP.NET/ASP.NET Core テンプレートでは、Azure AD v1.0 エンドポイント用の Web アプリケーションが生成されます。 そのため、Microsoft ID プラットフォーム (v2.0) エンドポイントに適合させるには若干の構成が必要です。 Java の場合は、アプリケーションとの連携で Spring によって処理されます。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (および Web API) では、コントローラーまたはコントローラー アクションでの `[Authorize]` 属性があるため、アプリケーションは保護されます。 この属性では、ユーザーが認証されていることが確認されます。 アプリケーションの初期化を実行するコードは `Startup.cs` ファイルにあり、Microsoft ID プラットフォーム (以前の Azure AD v2.0) を使用して認証を追加するには、次のコードを追加する必要があります。 コード内のコメントは、見てすぐにわかるものにする必要があります。

  > [!NOTE]
  > Visual Studio 内または `dotnet new mvc` を使用して、既定の ASP.NET Core Web プロジェクトから、プロジェクトを開始する場合、関連パッケージが自動的に読み込まれるため、メソッド `AddAzureAD` を既定で使用できます。
  > しかし、最初からプロジェクトをビルドし、下記のコードを使用しようとする場合は、NuGet パッケージ **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** をプロジェクトに追加して、`AddAzureAD` メソッドを使用できるようにすることをお勧めします。
  
次のコードは、[Startup.cs#L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34) から入手できます

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);
  
      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

`AddMicrosoftIdentityPlatformAuthentication` は、[Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23) で定義されている拡張メソッドです。 それでは次のことが行われます。

- 認証サービスが追加されます
- 構成ファイルを読み取るためのオプションが構成されます
- 機関として Microsoft ID プラットフォーム (旧称 Azure AD v2.0) エンドポイントが使用されるように、OpenID Connect のオプションが構成されます
- トークンの発行者が検証されます
- 名前に対応する要求が、ID トークンの "preferred_username" 要求からマップされます 

`AddMicrosoftIdentityPlatformAuthentication` を呼び出すときに、構成に加えて以下を指定できます。

- 構成セクションの名前 (既定では AzureAD)
- OpenIdConnect ミドルウェアのイベントをトレースするかどうか。これは、認証が機能しない場合に、Web アプリケーションをトラブルシューティングするのに役立ちます。`subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` を `true` に設定すると、HTTP 応答から `HttpContext.User` でのユーザーの ID に進むときに ASP.NET Core ミドルウェアのセットによって情報が詳しく説明される方法が示されます。

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

`AadIssuerValidator` クラスを使用すると、多くの場合に、トークンの発行者を検証できます (Azure パブリック クラウドまたは国内クラウドでの、v1.0 または v2.0 トークン、シングルテナントまたはマルチテナント アプリケーション、または個人の Microsoft アカウントを使用してユーザーをサインインさせるアプリケーション)。 それは、[Microsoft.Identity.Web/Resource/AadIssuerValidator.cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs) から入手できます

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET Web アプリ / Web API での認証に関連したコードは `App_Start/Startup.Auth.cs` ファイルにあります。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java サンプルでは、Spring フレームワークが使用されています。 `Filter` を実装するため、アプリケーションは保護されており、各 HTTP 応答が取得されます。 Java Web アプリのクイックスタートでは、これは `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` 内の `AuthFilter` です。 フィルターによって OAuth 2.0 認証コード フローが処理され、したがって次のようになります。

- ユーザーが認証されているかどうかが検証されます (`isAuthenticated()` メソッド)
- ユーザーが認証されていない場合は、Azure AD 承認エンドポイントの URL が計算されて、ブラウザーはこの URI にリダイレクトされます
- 認証コード フローを含む応答が到着すると、msal4j でトークンを取得できます。
- 最後にトークン エンドポイントからトークンを受け取ると (リダイレクト URI で)、ユーザーはサインインされます。

詳細については、[AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) の`doFilter()` メソッドを参照してください

> [!NOTE]
> `doFilter()` のコードは少し異なる順序で記述されていますが、フローは説明されているものです。

このメソッドによってトリガーされる認証コード フローの詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md)」を参照してください

# <a name="pythontabpython"></a>[Python](#tab/python)

Python サンプルでは Flask が使用されます。 Flask と MSAL.Python の初期化は、[app.py#L1-L17](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L1-L17) で行われます

```Python
import uuid
import flask
import requests
from flask import Flask, render_template, session, request
from flask_session import Session
import msal
import app_config

sess = Session()
app = Flask(__name__)
app.config.from_object('config.Config')
sess.init_app(app)
cache = msal.SerializableTokenCache()
application = msal.ConfidentialClientApplication(
    app_config.CLIENT_ID, authority=app_config.AUTHORITY,
    client_credential=app_config.CLIENT_SECRET,
    token_cache=cache)
```

これは、ユーザーのサインインを処理する MSAL.Python です。 [app.py#L74-84](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/app.py#L74-84) を参照してください

```Python
@app.route('/authenticate')
def authenticate():
    # Call to the authorize endpoint
    auth_state = str(uuid.uuid4())
    session[(request.cookies.get("session")+'state')] = auth_state
    authorization_url = application.get_authorization_request_url(app_config.SCOPE, state=auth_state,
                                                                  redirect_uri=app_config.REDIRECT_URI)
    resp = flask.Response(status=307)
    resp.headers['location'] = authorization_url
    return resp
```

---

## <a name="next-steps"></a>次の手順

次の記事では、サインインとサインアウトをトリガーする方法について説明します。

> [!div class="nextstepaction"]
> [サインインとサインアウト](scenario-web-app-sign-user-sign-in.md)
