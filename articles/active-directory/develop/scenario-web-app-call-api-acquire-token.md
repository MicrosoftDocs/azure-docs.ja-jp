---
title: Web API を呼び出す Web アプリのトークンを取得する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web アプリのトークンを取得する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfd479382cb69e7355b033312e165699223fdbf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756298"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Web API を呼び出す Web アプリ:アプリのトークンを取得する

クライアント アプリケーション オブジェクトは構築済みです。 次はこれを使って Web API を呼び出すトークンを取得します。 ASP.NET または ASP.NET Core では、Web API の呼び出しはコントローラーで実行されます。

- トークン キャッシュを使用して Web API のトークンを取得します。 このトークンを取得するには、MSAL `AcquireTokenSilent` メソッド (あるいは Microsoft.Identity.Web における同等のメソッド) を呼び出します。
- 保護された API を呼び出して、アクセス トークンをパラメーターとして渡します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

*Microsoft.Identity.Web* では、Microsoft Graph またはダウンストリーム Web API を呼び出す便利なサービスを提供する拡張メソッドが追加されます。 これらのメソッドの詳細については、「[Web API を呼び出す Web アプリ: API を呼び出す](scenario-web-app-call-api-call-api.md)」を参照してください。 これらのヘルパー メソッドを使用すれば、トークンを手動で取得する必要はありません。

ただし、トークンを手動で取得する場合は、ホーム コントローラーで取得することを目的とした *Microsoft.Identity.Web* の使用の例が、次のコードによって示されています。 これにより、REST API (Microsoft Graph SDK ではなく) を使用した、Microsoft Graph の呼び出しが行われます。 ダウンストリーム API を呼び出すためのトークンを取得するには、コントローラーのコンストラクター (Blazor を使用する場合はページ コンストラクター) に依存関係を挿入して、`ITokenAcquisition` サービスを挿入し、コントローラー アクションで使用します。これにより、ユーザーのトークン (`GetAccessTokenForUserAsync`)、またはデーモン シナリオでのアプリケーション自体 (`GetAccessTokenForAppAsync`) のトークンが取得されます。

コントローラー メソッドは、認証されたユーザーのみが Web アプリを使用できるようにする `[Authorize]` 属性によって保護されます。

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

`ITokenAcquisition` サービスは、依存関係の挿入を使用して ASP.NET によって挿入されます。

Microsoft Graph を呼び出すトークンを取得する `HomeController` のアクションの簡略化されたコードを次に示します。

```csharp
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

このシナリオに必要なコードをさらに理解したい場合は、[ms-identity-aspnetcore-Webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) チュートリアルのフェーズ 2 ([2-1-Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) の手順を参照してください。

コントローラー アクションの上 (または Razor テンプレートを使用する場合は Razor ページ) の `AuthorizeForScopes` 属性は、Microsoft.Identity.Web によって提供されます。 これにより、必要に応じて、かつ段階的にユーザーに同意が求められます。

次のような複雑なバリエーションもあります。

- 複数の API の呼び出し。
- 増分同意と条件付きアクセスの処理。

このような高度な手順については、[3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) チュートリアルの第 3 章を参照してください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET のコードは、ASP.NET Core 用に示したコードと似ています。

- [Authorize] 属性によって保護されたコントローラー アクションは、コントローラーの `ClaimsPrincipal` メンバーのテナント ID とユーザー ID を抽出します。 (ASP.NET は `HttpContext.User` を使用します。)
- そこから、MSAL.NET `IConfidentialClientApplication` オブジェクトを構築します。
- 最後に、機密クライアント アプリケーションの `AcquireTokenSilent` メソッドを呼び出します。
- 対話型操作が必要な場合は、Web アプリでユーザーをチャレンジ (再サインイン) し、追加の要求を求める必要があります。

次のコード スニペットは、[ms-identity-aspnet-webapp-openidconnect](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) ASP.NET MVC コード サンプルの [HomeController.cs#L157-L192](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Controllers/HomeController.cs#L157-L192) から抜粋したものです。

```C#
public async Task<ActionResult> ReadMail()
{
    IConfidentialClientApplication app = MsalAppBuilder.BuildConfidentialClientApplication();
    AuthenticationResult result = null;
    var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());
    string[] scopes = { "Mail.Read" };

    try
    {
        // try to get token silently
        result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync().ConfigureAwait(false);
    }
    catch (MsalUiRequiredException)
    {
        ViewBag.Relogin = "true";
        return View();
    }

    // More code here
    return View();
}
```

詳細については、コード サンプルの [BuildConfidentialClientApplication()](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Utils/MsalAppBuilder.cs) と [GetMsalAccountId](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/257c8f96ec3ff875c351d1377b36403eed942a18/WebApp/Utils/ClaimPrincipalExtension.cs#L38) のコードを参照してください


# <a name="java"></a>[Java](#tab/java)

Java のサンプルでは、API を呼び出すコードは getUsersFromGraph メソッド ([AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)) にあります。

メソッドから `getAuthResultBySilentFlow` の呼び出しが試行されます。 ユーザーがより多くのスコープに同意する必要がある場合、コードでは `MsalInteractionRequiredException` オブジェクトが処理され、ユーザーがチャレンジされます。

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

Python のサンプルでは、Microsoft Graph を呼び出すコードは [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62) にあります。

このコードでは、トークン キャッシュからのトークンの取得が試行されます。 次に、authorization ヘッダーが設定された後、Web API が呼び出されます。 トークンを取得できない場合は、ユーザーのサインインが再実行されます。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である [Web API の呼び出し](scenario-web-app-call-api-call-api.md)に関する記事に進みます。
