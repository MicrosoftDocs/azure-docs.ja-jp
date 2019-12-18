---
title: Web アプリで Web API を呼び出すトークンを取得する - Microsoft ID プラットフォーム | Azure
description: Web API を呼び出す Web アプリを構築する方法について説明します (アプリのトークンの取得)
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 398b68221681f0d14dbcc20ac7c0cb603313eaee
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919465"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API を呼び出す Web アプリ - アプリのトークンの取得

クライアント アプリケーション オブジェクトを構築したので、次にそれを使用して、Web API を呼び出すためのトークンを取得します。 ASP.NET または ASP.NET Core では、Web API の呼び出しはコントローラーで実行されます。 詳細は次のとおりです。

- トークン キャッシュを使用して Web API のトークンを取得します。 このトークンを取得するには、`AcquireTokenSilent` を呼び出します。
- アクセス トークンを使用して、保護された API を呼び出します。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

コントローラー メソッドは、ユーザーが Web アプリを使用するために認証されるようにする `[Authorize]` 属性によって保護されます。 以下に、Microsoft Graph を呼び出すコードを示します。

```CSharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions(see code below)

}
```

`ITokenAcquisition` サービスは、ASP.NET によって依存関係の挿入を通して挿入されます。


以下に、Microsoft Graph を呼び出すトークンを取得する、HomeController のアクションの簡略化されたコードを示します。

```CSharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenOnBehalfOfUserAsync(scopes);

 // use the access token to call a protected web API
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

このシナリオに必要なコードをさらに詳しく理解したい場合は、[ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) チュートリアルのフェ ーズ 2 の「[2-1 Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)」の手順を参照してください。

次のように、さらに複雑な操作があります。

- 複数の API の呼び出し
- 増分同意と条件付きアクセスの処理。

これらの高度な手順は、チュートリアル [3-WebApp-multi-APIs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) の第 3 章で処理されます

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET でも同様です。

- [Authorize] 属性によって保護されたコントローラー アクションは、コントローラーの `ClaimsPrincipal` メンバーのテナント ID とユーザー ID を抽出します。 (ASP.NET は `HttpContext.User` を使用します。)
- そこから、MSAL.NET `IConfidentialClientApplication` を構築します。
- 最後に、機密クライアント アプリケーションの `AcquireTokenSilent` メソッドを呼び出します。

コードは ASP.NET Core で示されたコードと同様です。

# <a name="javatabjava"></a>[Java](#tab/java)

Java のサンプルでは、API を呼び出すコードは getUsersFromGraph メソッド ([AuthPageController.java#L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)) にあります。

`getAuthResultBySilentFlow` の呼び出しが試みられています。 ユーザーがより多くのスコープに同意する必要がある場合、コードでは `MsalInteractionRequiredException` を処理してユーザーをチャレンジします。

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

            // If silent call returns MsalInteractionRequired, then redirect to Authorization endpoint
            // so user can consent to new scopes
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
// Code omitted here.
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のサンプルでは、Microsoft Graph を呼び出すコードは [app.py#L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62) にあります。

トークン キャッシュからのトークンの取得が試みられた後、承認ヘッダーを設定した後で eb API が呼び出されます。 できない場合は、ユーザーを再度サインインさせます。

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Web API を呼び出す](scenario-web-app-call-api-call-api.md)
