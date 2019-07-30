---
title: 保護された Web API - アプリ コードの構成 | Azure
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536902"
---
# <a name="protected-web-api-code-configuration"></a>保護された Web API:コード構成

保護された Web API 用にコードを構成するには、API を保護対象として定義するもの、ベアラー トークンを構成する方法、およびトークンを確認する方法を理解する必要があります。

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>ASP.NET/ASP.NET Core API を保護対象として定義するものとは

Web アプリと同じように、ASP.NET/ASP.NET Core Web API は、そのコントローラー アクションに `[Authorize]` 属性のプレフィックスがあるため、"保護" されています。 そのため、コントローラー アクションは、承認されている ID で API が呼び出された場合にのみ呼び出すことができます。

次の質問について考えてみましょう。

- Web API では、呼び出し元のアプリの ID をどのように認識しますか? (Web API を呼び出せるのはアプリのみです)。
- ユーザーの代わりにアプリによって Web API が呼び出された場合のユーザーの ID は何ですか?

## <a name="bearer-token"></a>ベアラー トークン

アプリの ID とユーザーに関する情報は (Web アプリで、デーモン アプリからのサービス間呼び出しを受け入れる場合を除く)、アプリが呼び出されるときにヘッダーで設定されるベアラー トークンに保持されます。

以下は、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用してトークンを取得した後、API を呼び出すクライアントを示す C# コードの例です。

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> *Web API の* Microsoft ID プラットフォーム エンドポイントへのベアラー トークンがクライアント アプリケーションによって要求されました。 Web API は、トークンを検証し、含まれている要求を表示する必要がある唯一のアプリケーションです。 クライアント アプリで、トークンの要求を検査してみることはできません (今後、Web API で、トークンの暗号化が要求されるようになる可能性があります。 この要件により、アクセス トークンを表示できるクライアント アプリのアクセスが禁止されます)。

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

### <a name="code-initialization"></a>コードの初期化

`[Authorize]` 属性を保持するコントローラー アクションでアプリが呼び出されると、ASP.NET/ASP.NET Core では呼び出し要求の Authorization ヘッダー内のベアラー トークンが確認され、アクセス トークンが抽出されます。 その後、トークンは JwtBearer ミドルウェアに転送され、Microsoft IdentityModel Extensions for .NET が呼び出されます。

ASP.NET Core では、このミドルウェアは Startup.cs ファイルで初期化されます。

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

この命令によって、ミドルウェアが Web API に追加されます。

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 現在、ASP.NET Core テンプレートでは、ご自分の組織または任意の組織内の (個人用アカウントを持っていない) ユーザーのサインインを行う、Azure Active Directory (Azure AD) Web API が作成されます。 しかし、これらは、このコードを Startup.cs ファイルに追加することで、Microsoft ID プラットフォーム エンドポイントを使用するように簡単に変更できます。

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>トークンの検証

JwtBearer ミドルウェアは、Web アプリの OpenID Connect ミドルウェアと同様に、トークンを確認するように `TokenValidationParameters` によって指示されます。 トークンの暗号化が解除され (必要に応じて)、要求が抽出され、署名が検証されます。 その後、ミドルウェアでは、このデータを調べてトークンを確認します。

- Web API の対象となっていること (対象ユーザー)。
- Web API の呼び出しが許可されているアプリに対して発行されたこと (サブ)。
- 信頼できるセキュリティ トークン サービス (STS) によって発行されたこと (発行者)。
- 有効期間が範囲内であること (有効期限)。
- 改ざんされていないこと (署名)。

特別な検証もできます。 たとえば、署名キー (トークンに埋め込まれている場合) が信頼されていること、およびトークンが再生されていないことを確認することができます。 最後に、一部のプロトコルでは、特定の検証が必要です。

### <a name="validators"></a>検証コントロール

確認手順は、検証コントロールでキャプチャされます。これらはすべて [Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) のオープン ソース ライブラリ内の 1 つのソース ファイル内にあります。[Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)。

検証コントロールについて、この表で説明します。

| 検証コントロール | 説明 |
|---------|---------|
| `ValidateAudience` | トークンが、(自分の) トークンを確認するアプリケーション用であることを保証します。 |
| `ValidateIssuer` | トークンが信頼できる STS によって (信頼する人から) 発行されたことを保証します。 |
| `ValidateIssuerSigningKey` | トークンを確認するアプリケーションで、トークンの署名に使用されたキーが信頼されていることを保証します (キーがトークンに埋め込まれている特殊なケース。 通常は必要ありません)。 |
| `ValidateLifetime` | トークンが引き続き (または既に) 有効であることを保証します。 検証コントロールでは、トークン (`notbefore` および `expires` 要求) の有効期間が範囲内であるかどうかを確認します。 |
| `ValidateSignature` | トークンが改ざんされていないことを保証します。 |
| `ValidateTokenReplay` | トークンが再生されていないことを保証します (一部の 1 回限りの使用のプロトコルの特殊なケース)。 |

検証コントロールはすべて `TokenValidationParameters` クラスのプロパティと関連付けられ、ASP.NET/ASP.NET Core 構成から初期化されます。 ほとんどの場合、パラメーターを変更する必要はありません。 単一テナントではないアプリの場合、1 つの例外があります (つまり、任意の組織から、または個人用 Microsoft アカウントからのユーザーを受け入れる Web アプリ)。この場合、発行者を確認する必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コードでスコープとアプリのロールを検証する](scenario-protected-web-api-verification-scope-app-roles.md)
