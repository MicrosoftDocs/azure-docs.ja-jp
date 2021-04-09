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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5072ae58d3a9412237e70a9bc98970296ce1e1fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686582"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Web API を呼び出す Web API:コード構成

Web API を登録した後は、アプリケーションのコードを構成することができます。

ダウンストリーム Web API を呼び出すように Web API を構成するために使用するコードは、Web API を保護するために使用されるコードの上に構築されます。 詳細については、[保護された Web API: アプリ構成](scenario-protected-web-api-app-configuration.md)に関するページを参照してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft.Identity.Web

ダウンストリーム Web API を呼び出す ASP.NET Core で保護された API を開発する場合は、[Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet パッケージを使用することをお勧めします。 「[保護された Web API: Web API のコンテキストでそのライブラリをすばやく表示するには、コード構成の「Microsoft.Identity.Web」](scenario-protected-web-api-app-configuration.md#microsoftidentityweb)を参照してください。

## <a name="client-secrets-or-client-certificates"></a>クライアント シークレットまたはクライアント証明書

ご利用の Web API でダウンストリーム Web API を呼び出すことができるようになったので、クライアント シークレットまたはクライアント証明書を *appsettings.json* ファイルに指定してください。 次を指定するセクションを追加することもできます。

- ダウンストリーム Web API の URL
- API の呼び出しに必要なスコープ

次の例では、`GraphBeta` セクションでこれらの設定を指定しています。

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
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
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
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft.Identity.Web では、構成またはコードの両方で証明書を記述するいくつかの方法を提供しています。 詳細については、GitHub 上の「[Microsoft.Identity.Web wiki - 証明書の使用](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates)」を参照してください。

## <a name="startupcs"></a>Startup.cs

Web API では、ダウンストリーム API のトークンを取得する必要があります。 これを指定するには、`.AddMicrosoftIdentityWebApi(Configuration)` の後に `.EnableTokenAcquisitionToCallDownstreamApi()` 行を追加します。 この行により、コントローラーまたはページのアクションで使用できる `ITokenAcquisition` サービスが公開されます。 ただし、次の 2 つの箇条書きでわかるように、さらに簡単に行うことができます。 また、*Startup.cs* で、`.AddInMemoryTokenCaches()` などのトークン キャッシュの実装を選択する必要もあります。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

トークンを自分で取得しない場合は、*Microsoft.Identity.Web* には、別の API からダウンストリーム Web API を呼び出すための 2 つのメカニズムが用意されています。 選択するオプションは、Microsoft Graph または別の API のどちらを呼び出すかによって異なります。

### <a name="option-1-call-microsoft-graph"></a>オプション 1: Microsoft Graph の呼び出し

Microsoft Graph を呼び出す場合は、Microsoft.Identity.Web を使用すると、API アクションで (Microsoft Graph SDK によって公開されている) `GraphServiceClient` を直接使用することができます。 Microsoft Graph を公開するには、次の手順を実行します。

1. プロジェクトに、[Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet パッケージを追加します。
1. *Startup.cs* ファイルで `.EnableTokenAcquisitionToCallDownstreamApi()` の後に `.AddMicrosoftGraph()` を追加します。 `.AddMicrosoftGraph()` にはいくつかのオーバーライドがあります。 構成セクションをパラメーターとして受け取るオーバーライドを使用すると、コードは次のようになります。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>オプション 2:Microsoft Graph 以外のダウンストリーム Web API を呼び出す

Microsoft Graph 以外のダウンストリーム API を呼び出すために、*Microsoft.Identity.Web* には、トークンを要求し、ダウンストリーム Web API を呼び出す `.AddDownstreamWebApi()` が用意されています。

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Web アプリと同様に、さまざまなトークン キャッシュの実装を選択できます。 詳細については、GitHub の [Microsoft identity web - Token cache serialization](https://aka.ms/ms-id-web/token-cache-serialization) (トークン キャッシュのシリアル化) を参照してください。

次の図は、*Microsoft.Identity.Web* のさまざまな可能性と、*Startup.cs* ファイルへの影響を示しています。

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Web API を呼び出し、トークン キャッシュの実装を指定するための Startup.cs のサービス構成オプションを示すブロック図":::

> [!NOTE]
> これらのコード例を完全に理解するには、[ASP.NET Core の基礎](/aspnet/core/fundamentals)、特に[依存関係の挿入](/aspnet/core/fundamentals/dependency-injection)と[オプション](/aspnet/core/fundamentals/configuration/options)について熟知しておいてください。

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

[ms-identity-python-on-behalf-of](https://github.com/Azure-Samples/ms-identity-python-on-behalf-of) サンプルで、OBO フローの実装の例を参照することもできます。

---

また、[Node.js と Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61) への OBO フロー実装の例を確認することもできます。

## <a name="protocol"></a>Protocol

OBO プロトコルの詳細については、「[Microsoft ID プラットフォームと OAuth 2.0 On-Behalf-Of フロー](./v2-oauth2-on-behalf-of-flow.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリのトークンの取得](scenario-web-api-call-api-acquire-token.md)に関する記事に進みます。
