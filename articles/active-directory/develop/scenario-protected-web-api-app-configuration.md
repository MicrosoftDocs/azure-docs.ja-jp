---
title: 保護された Web API アプリを構成する |Microsoft
titleSuffix: Microsoft identity platform
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5206c2295ee7c01b4a2908e59da1cfdd8782bccd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517720"
---
# <a name="protected-web-api-code-configuration"></a>保護された Web API: コード構成

保護された Web API のコードを構成するには、次の点を理解しておいてください。

- API を保護対象として定義するもの
- ベアラー トークンの構成方法
- トークンの検証方法

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>ASP.NET と ASP.NET Core の API を保護対象として定義するものとは

Web アプリと同じように、ASP.NET と ASP.NET Core の Web API は、そのコントローラー アクションに **[Authorize]** 属性のプレフィックスがあるため、保護されています。 コントローラー アクションは、承認されている ID で API が呼び出された場合にのみ呼び出すことができます。

次の質問について考えてみましょう。

- Web API を呼び出せるのはアプリのみです。 API では、呼び出し元のアプリの ID をどのように認識しますか?
- ユーザーの代わりにアプリで API が呼び出される場合、ユーザーの ID は何ですか?

## <a name="bearer-token"></a>ベアラー トークン

アプリが呼び出されたときにヘッダーに設定されるベアラー トークンには、アプリ ID に関する情報が保持されます。 また、Web アプリがデーモン アプリからのサービス間の呼び出しを受け入れる場合を除き、ユーザーに関する情報も保持されます。

これは、Microsoft Authentication Library for .NET (MSAL.NET) を使用してトークンを取得した後、API を呼び出すクライアントを示す C# コードの例です。

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> クライアント アプリケーションにより、"*Web API の*" Microsoft ID プラットフォームにベアラー トークンが要求されます。 Web API は、トークンを検証し、含まれている要求を表示する必要がある唯一のアプリケーションです。 クライアント アプリで、トークンの要求を検査してみることはできません
>
> 将来、Web API で、トークンの暗号化が要求される可能性があります。 この要件により、アクセス トークンを表示できるクライアント アプリのアクセスが禁止されます。

## <a name="jwtbearer-configuration"></a>JwtBearer の構成

このセクションでは、ベアラー トークンの構成方法について説明します。

### <a name="config-file"></a>config ファイル

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Web API にカスタム アプリ ID URI を使用したケース

アプリ登録ポータルによって提案されたアプリ ID URI を受け入れた場合は、対象ユーザーを指定する必要はありません (「[アプリケーション ID の URI とスコープ](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)」を参照してください)。 それ以外の場合は、Web API のアプリ ID URI を値とする `Audience` プロパティを追加する必要があります。

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>コードの初期化

**[Authorize]** 属性を保持するコントローラー アクションでアプリが呼び出されると、ASP.NET と ASP.NET Core により、Authorization ヘッダーのベアラー トークンからアクセス トークンが抽出されます。 その後、アクセス トークンは JwtBearer ミドルウェアに転送され、Microsoft IdentityModel Extensions for .NET が呼び出されます。

#### <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

ASP.NET Core で Web API を開発する場合は、[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet パッケージを使用することをお勧めします。

_Microsoft.Identity.Web_ を使用すると、ASP.NET Core、認証ミドルウェア、および .NET 用の [Microsoft Authentication Library (MSAL)](msal-overview.md) 間を結び付けることができます。 これにより、より明確で堅牢な開発者エクスペリエンスが可能になり、Microsoft ID プラットフォームと Azure AD B2C の機能を活用できます。

#### <a name="using-microsoftidentityweb-templates"></a>Microsoft.Identity.Web テンプレートを使用する

Microsoft.Identity.Web プロジェクト テンプレートを使用して、Web API を最初から作成できます。 詳細については、「[Microsoft.Identity.Web - Web API プロジェクト テンプレート](https://aka.ms/ms-id-web/webapi-project-templates)」を参照してください。

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>既存の ASP.NET Core 3.1 アプリケーションから開始する

現在、ASP.NET Core 3.1 では、Microsoft.AspNetCore.AzureAD.UI ライブラリが使用されています。 このミドルウェアは Startup.cs ファイルで初期化されます。

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

この命令によって、ミドルウェアが Web API に追加されます。

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 現在、ASP.NET Core テンプレートでは、ご自分の組織または任意の組織内のユーザーのサインインを行う、Azure Active Directory (Azure AD) Web API が作成されます。 個人アカウントを使用してユーザーをサインインさせることはありません。 ただし、*Startup.cs* 内のコードを置き換える [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) を使用することで、Microsoft ID プラットフォームを使用するようにテンプレートを変更できます。

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

また、次のように記述することもできます (こちらも同等です)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Microsoft.Identity.Web を使用し、*appsettings.json* 内に `Audience` を設定しない場合、次が使用されます。
> -  `$"{ClientId}"`。[アクセス トークンで承認されたバージョン](scenario-protected-web-api-app-registration.md#accepted-token-version)を `2` に設定している場合、または Azure AD B2C Web API の場合。
> - `$"api://{ClientId}`。その他すべての場合 (v1.0 [アクセス トークン](access-tokens.md)の場合)。
> 詳細については、Microsoft.Identity.Web の[ソース コード](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83)を参照してください。

上記のコード スニペットは、[ASP.NET Core Web API の増分チュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28)から引用されています。 **AddMicrosoftIdentityWebApiAuthentication** に関する詳細については [Microsoft.Identity.Web](microsoft-identity-web.md) を参照してください。 このメソッドにより [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi) が呼び出されます。これ自体がトークンの検証方法をミドルウェアに指示します。

## <a name="token-validation"></a>トークンの検証

前のスニペットでは、Web アプリの OpenID Connect ミドルウェアと同様に、JwtBearer ミドルウェアによって `TokenValidationParameters` の値に基づいてトークンが検証されます。 トークンは必要に応じて暗号化が解除され、要求が抽出され、署名が検証されます。 その後、ミドルウェアでは、このデータを調べてトークンを確認します。

- Audience:トークンが Web API のターゲットとなっていること。
- サブ:Web API の呼び出しが許可されているアプリに対して発行されたこと。
- 発行者:信頼できるセキュリティ トークン サービス (STS) によって発行されたこと。
- 有効期限:有効期間が範囲内であること。
- 署名:改ざんされていないこと。

特別な検証もできます。 たとえば、署名キー (トークンに埋め込まれている場合) が信頼されていること、およびトークンが再生されていないことを確認することができます。 最後に、一部のプロトコルでは、特定の検証が必要です。

### <a name="validators"></a>検証コントロール

確認手順は、検証コントロールでキャプチャされます。これらは [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) のオープン ソース ライブラリで提供されています。 検証コントロールは、ライブラリ ソース ファイル [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs) で定義されています。

次の表では、検証コントロールについて説明します。

| 検証コントロール | 説明 |
|---------|---------|
| **ValidateAudience** | トークンが、自分のトークンを確認するアプリケーション用であることを保証します。 |
| **ValidateIssuer** | トークンが信頼できる STS、つまり自分が信頼する人から発行されたことを保証します。 |
| **ValidateIssuerSigningKey** | トークンを確認するアプリケーションで、トークンの署名に使用されたキーが信頼されていることを保証します キーがトークンに埋め込まれている特殊なケースがあります。 ただし、このケースは通常は発生しません。 |
| **ValidateLifetime** | トークンが引き続きまたは既に有効であることを保証します。 検証コントロールにより、トークンの有効期間が **notbefore** 要求と **expires** 要求で指定された範囲内にあるかどうかが確認されます。 |
| **ValidateSignature** | トークンが改ざんされていないことを保証します。 |
| **ValidateTokenReplay** | トークンが再生されていないことを保証します 一部の 1 回限りの使用のプロトコルには特殊なケースがあります。 |

#### <a name="customizing-token-validation"></a>トークンの検証のカスタマイズ

検証コントロールは、**TokenValidationParameters** クラスのプロパティに関連付けられています。 このプロパティは、ASP.NET と ASP.NET Core の構成から初期化されます。

ほとんどの場合、パラメーターを変更する必要はありません。 シングル テナントではないアプリは例外です。 これらの Web アプリでは、任意の組織から、または個人用 Microsoft アカウントからのユーザーを受け入れます。 この場合、発行者を検証する必要があります。 Microsoft.Identity.Web では発行者の検証も行われます。 詳細については、Microsoft.Identity.Web の [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs) を参照してください。

ASP.NET Core で、トークン検証パラメーターをカスタマイズする場合は、*Startup.cs* で次のスニペットを使用します。

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  };
});
```

ASP.NET MVC の場合、次のコード サンプルにカスタム トークンの検証を行う方法が示されています。

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Azure Functions でのトークンの検証

Azure Functions では、受信アクセス トークンを検証することもできます。 このような検証の例については、GitHub の次のコード サンプルを参照してください。

- .NET:[Azure-Samples/ms-identity-dotnet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js:[Azure-Samples/ms-identity-nodejs-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/ms-identity-python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である「[コードでスコープとアプリのロールを検証する](scenario-protected-web-api-verification-scope-app-roles.md)」に進みます。
