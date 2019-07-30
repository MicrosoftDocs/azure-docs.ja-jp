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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853063"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>ユーザーをサインインさせる Web アプリ - コードの構成

ユーザーをサインインさせる Web アプリのコードを構成する方法について説明します。

## <a name="libraries-used-to-protect-web-apps"></a>Web アプリを保護するために使用されるライブラリ

<!-- This section can be in an include for Web App and Web APIs -->
Web アプリ (および Web API) を保護するために使用されるライブラリは次のとおりです。

| プラットフォーム | ライブラリ | 説明 |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identity Model Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | ASP.NET および ASP.NET Core によって直接使用される Microsoft Identity Extensions for .NET は、.NET Framework と .NET Core の両方で実行される DLL のセットを提案します。 ASP.NET/ASP.NET Core Web アプリから、**TokenValidationParameters** クラスを使用してトークン検証を制御できます (特に、一部の ISV シナリオで)。 |

## <a name="aspnet-core-configuration"></a>ASP.NET Core 構成

この記事および以下のコード スニペットは、[ASP.NET Core Web アプリ増分チュートリアルの第 1 章](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)から抽出されています。 完全な実装の詳細については、そのチュートリアルをご覧ください。

### <a name="application-configuration-files"></a>アプリケーション構成ファイル

ASP.NET Core では、Microsoft ID プラットフォームを使用してユーザーをサインインさせる Web アプリケーションは `appsettings.json` ファイルを使って構成されます。 入力する必要がある設定は次のとおりです。

- アプリを国内のクラウドで実行する場合は、クラウド `Instance`
- `tenantId` の対象ユーザー
- Azure portal からコピーした、アプリケーションの `clientId`

```JSon
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

ASP.NET Core には、アプリケーションの URL (`applicationUrl`) と SSL ポート (`sslPort`)、およびさまざまなプロファイルを含む別のファイルがあります。

```JSon
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

### <a name="initialization-code"></a>初期化コード

ASP.NET Core Web アプリ (および Web API) では、アプリケーションの初期化を実行するコードは `Startup.cs` ファイルにあり、Microsoft ID プラットフォーム (以前の Azure AD) v2.0 を使用して認証を追加するには、次のコードを追加する必要があります。 コード内のコメントは、見てすぐにわかるものにする必要があります。

  > [!NOTE]
  > Visual Studio 内または `dotnet new mvc` を使用して、既定の ASP.NET Core Web プロジェクトから、プロジェクトを開始する場合、関連パッケージが自動的に読み込まれるため、メソッド `AddAzureAD` を既定で使用できます。 しかし、最初からプロジェクトをビルドし、下記のコードを使用しようとする場合は、NuGet パッケージ **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** をプロジェクトに追加して、`AddAzureAD` メソッドを使用できるようにすることをお勧めします。
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET 構成

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

ASP.NET Web アプリ / Web API での認証に関連したコードは `App_Start/Startup.Auth.cs` ファイルにあります。

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [サインインとサインアウト](scenario-web-app-sign-user-sign-in.md)
