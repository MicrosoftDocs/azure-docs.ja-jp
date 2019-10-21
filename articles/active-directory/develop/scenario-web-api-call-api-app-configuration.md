---
title: ダウンストリーム Web API を呼び出す Web API (アプリのコード構成) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web API を構築する方法について説明します (アプリのコード構成)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 529665a03d2203dcb501b59d7647f4390bdaeb78
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936738"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Web API を呼び出す Web API - コードの構成

Web API を登録した後は、アプリケーションのコードを構成することができます。

ダウンストリーム Web API を呼び出すように Web API を構成するコードは、Web API を保護するために使用されるコードの上に構築されます。 詳細については、「[保護された Web API - アプリ構成](scenario-protected-web-api-app-configuration.md)」をご覧ください。

## <a name="code-subscribed-to-ontokenvalidated"></a>OnTokenValidated をサブスクライブするコード

次のように、保護されている Web API のコード構成に加え、API が呼び出されたときに受け取るベアラー トークンの検証をサブスクライブする必要があります。

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>On-Behalf-Of フロー

AddAccountToCacheFromJwt() メソッドは次を実行する必要があります。

- MSAL 機密クライアント アプリケーションをインスタンス化します。
- `AcquireTokenOnBehalf` を呼び出して、Web API のクライアントによって取得されたベアラー トークンを、同じユーザーのベアラー トークンに照らして交換します (ただし、API がダウンストリーム API を呼び出すため)。

### <a name="instantiate-a-confidential-client-application"></a>機密クライアントをインスタンス化する

このフローは機密クライアント フローでのみ使用可能なため、保護された Web API は、クライアントの資格情報 (クライアント シークレットまたは証明書) を、それぞれ `WithClientSecret` メソッドまたは `WithCertificate` メソッドを介して [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) に提供します。

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

最後に、機密クライアント アプリケーションでは、クライアント シークレットや証明書ではなく、クライアント アサーションを使用して ID を証明することもできます。
この高度なシナリオの詳細については、[クライアント アサーション](msal-net-client-assertions.md)に関する記事を参照してください。

### <a name="how-to-call-on-behalf-of"></a>On-behalf-of を呼び出す方法

on-behalf-of (OBO) 呼び出しは、`IConfidentialClientApplication` インターフェイスで [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) メソッドを呼び出すことによって行われます。

`UserAssertion` は、独自のクライアントから Web API で受信されたベアラー トークンから構築されます。 [2 つのコンストラクター](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet)、すなわち JWT ベアラー トークンを取るものと、任意の種類のユーザー アサーション (別の種類のセキュリティ トークン。その型は、`assertionType` という名前の追加パラメーターに指定される) を取るものがあります。

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

実際には、OBO フローは、ダウンストリーム API のトークンを取得して、それを MSAL.NET ユーザー トークン キャッシュに格納するためによく使用されます。それにより、Web API の他の部分は後で ``AcquireTokenOnSilent`` の[オーバーライド](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet)で呼び出してダウンストリーム API を呼び出すことができます。 この呼び出しには、必要な場合、トークンを最新の情報に更新する効果があります。

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

## <a name="protocol"></a>Protocol

on-behalf-of プロトコルの詳細情報については、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)」をご覧ください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのトークンの取得](scenario-web-api-call-api-acquire-token.md)
