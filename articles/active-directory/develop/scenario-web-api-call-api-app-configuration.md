---
title: Web API を呼び出す Web API を構成する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web API を構築する方法について説明します (アプリのコード構成)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e9faea3462ae953e474b5053b651808b03f07c23
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855451"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Web API を呼び出す Web API:コード構成

Web API を登録した後は、アプリケーションのコードを構成することができます。

ダウンストリーム Web API を呼び出すように Web API を構成するために使用するコードは、Web API を保護するために使用されるコードの上に構築されます。 詳細については、[保護された Web API: アプリ構成](scenario-protected-web-api-app-configuration.md)に関するページを参照してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>クライアント シークレットまたはクライアント証明書

Web API でダウンストリーム Web API を呼び出すことができるようになったので、クライアント シークレットまたはクライアント証明書を *appsettings.json* ファイルに指定する必要があります。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

クライアント シークレットの代わりに、クライアント証明書を指定することができます。 次のコード スニペットは、Azure Key Vault に格納されている証明書の使用を示しています。

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft.Identity.Web では、構成またはコードの両方で証明書を記述するいくつかの方法を提供しています。 詳細については、GitHub 上の「[Microsoft.Identity.Web wiki - 証明書の使用](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)」を参照してください。

## <a name="startupcs"></a>Startup.cs

Microsoft.Identity.Web を使用して、Web API でダウンストリーム Web API を呼び出したい場合、*Startup.cs* 内で `.AddMicrosoftIdentityWebApi(Configuration)` の後に `.EnableTokenAcquisitionToCallDownstreamApi()` 行を追加して、トークン キャッシュの実装 (例: `.AddInMemoryTokenCaches()`) を選択します。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
                .EnableTokenAcquisitionToCallDownstreamApi()
                .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Web アプリと同様に、さまざまなトークン キャッシュの実装を選択できます。 詳細については、GitHub 上の「[Microsoft identity web wiki - トークン キャッシュのシリアル化](https://aka.ms/ms-id-web/token-cache-serialization)」を参照してください。

# <a name="java"></a>[Java](#tab/java)

On-Behalf-Of (OBO) フローは、ダウンストリーム Web API を呼び出すトークンを取得するために使用されます。 このフローでは、Web API は、ユーザーが委任したアクセス許可を持つベアラー トークンをクライアント アプリケーションから受信し、このトークンを別のアクセス トークンと交換してダウンストリーム Web API を呼び出します。

次のコードでは、Web API で Spring Security フレームワークの `SecurityContextHolder` を使用して、検証済みのベアラー トークンを取得します。 次に、MSAL Java ライブラリを使用して、`OnBehalfOfParameters` を設定した `acquireToken` を使ってダウンストリーム API のトークンを取得します。 MSAL はトークンをキャッシュし、API への後続の呼び出しで `acquireTokenSilently` を使用してキャッシュされたトークンを取得できるようにします。

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

On-Behalf-Of (OBO) フローは、ダウンストリーム Web API を呼び出すトークンを取得するために使用されます。 このフローでは、Web API は、ユーザーが委任したアクセス許可を持つベアラー トークンをクライアント アプリケーションから受信し、このトークンを別のアクセス トークンと交換してダウンストリーム Web API を呼び出します。

Python Web API は、クライアントから受信したベアラー トークンを検証するために何らかのミドルウェアを使用する必要があります。 Web API は、[`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) メソッドを呼び出すことにより、MSAL Python ライブラリを使用してダウンストリーム API のアクセス トークンを取得できます。 この API の使用例については、[GitHub の microsoft-authentication-library-for-python のテスト コード](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472)を参照してください。 また、中間層アプリケーションの必要性をなくす方法については、同じリポジトリ内の[問題 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) の説明も参照してください。

---

また、[Node.js と Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61) への OBO フロー実装の例を確認することもできます。

## <a name="protocol"></a>Protocol

OBO プロトコルの詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](./v2-oauth2-on-behalf-of-flow.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web API を呼び出す Web API:アプリのトークンの取得](scenario-web-api-call-api-acquire-token.md)