---
title: デーモン アプリで Web API を呼び出すトークンを取得する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出すデーモン アプリ (トークンを取得する) をビルドする方法について学習します。
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0588e20467701512da6542da0d87fca786dcc793
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920295"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Web API を呼び出すデーモン アプリ - トークンを取得する

機密クライアント アプリケーションが構築されたら、``AcquireTokenForClient`` を呼び出し、スコープを渡し、トークンを強制するかトークンを更新しないことでアプリのトークンを取得できます。

## <a name="scopes-to-request"></a>要求するスコープ

クライアントの資格情報フローのために要求するスコープは、後に `/.default` が続くリソースの名前です。 この表記は、アプリケーションの登録時に静的に宣言された**アプリケーション レベルの権限**を使用するように Azure AD に指示します。 また、前述したように、これらの API アクセス許可は、テナント管理者によって付与される必要があります。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

MSAL Python では、構成ファイルは次のコード スニペットのようになります。

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Azure AD (v1.0) リソースの場合

クライアント資格情報に使用するスコープは常に resourceId+"/.default" にする必要があります。

> [!IMPORTANT]
> v1.0 アクセス トークンを受け入れるリソースのためにアクセス トークンを要求する MSAL の場合、Azure AD では、最後のスラッシュの前のすべてを取得し、それをリソース ID として使用することで、要求されたスコープから目的の対象ユーザーを解析します。
> そのため、Azure SQL ( **https://database.windows.net** ) のようにリソースによってスラッシュで終わる対象ユーザー (Azure SQL の場合: `https://database.windows.net/` ) が要求されている場合は、`https://database.windows.net//.default` のスコープを要求する必要があります(二重スラッシュに注意してください)。 次も参照してください。MSAL.NET の問題 [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747):リソース URL の末尾のスラッシュが省略されたため、SQL 認証エラーが発生した。

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

アプリのトークンを取得するには、プラットフォームに応じて `AcquireTokenForClient` または同等のものを使用します。

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

これは、[MSAL Java dev サンプル](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)から抜粋したものです。

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol

選択した言語のライブラリがまだない場合は、プロトコルを直接使用することもできます。

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>最初のケース:共有シークレットを使ったアクセス トークン要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>2 番目のケース:証明書を使ったアクセス トークン要求

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

詳細については、プロトコルのドキュメント:[Microsoft ID プラットフォームと OAuth 2.0 クライアント資格情報フロー](v2-oauth2-client-creds-grant-flow.md)。

## <a name="application-token-cache"></a>アプリケーション トークン キャッシュ

MSAL.NET では、`AcquireTokenForClient` は**アプリケーション トークン キャッシュ**を使用します (他の AcquireTokenXX メソッドはすべてユーザー トークン キャッシュを使用します)。`AcquireTokenSilent` は**ユーザー** トークン キャッシュを使用するため、`AcquireTokenForClient` を呼び出す前に `AcquireTokenSilent` を呼び出さないでください。 `AcquireTokenForClient` は**アプリケーション** トークン キャッシュそのものをチェックして、更新します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="did-you-use-the-resourcedefault-scope"></a>リソース/.default スコープを使用しましたか?

無効なスコープを使用したことを示すエラー メッセージが表示された場合は、`resource/.default` スコープを使用しなかった可能性があります。

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>管理者の同意を提供するのを忘れましたか? デーモン アプリにはそれが必要です。

API を呼び出すときに、**この操作を完了するのに十分な特権がありません**というエラーが表示された場合は、テナント管理者がアプリケーションにアクセス許可を付与する必要があります。 上記のクライアント アプリの登録の手順 6 を参照してください。
一般的に、次のようなエラーの説明が表示されます。

```JSon
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>次の手順

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [デーモン アプリ - Web API の呼び出し](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [デーモン アプリ - Web API の呼び出し](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [デーモン アプリ - Web API の呼び出し](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
