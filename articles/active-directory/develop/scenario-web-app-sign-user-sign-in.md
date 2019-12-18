---
title: ユーザーにサインインする Web アプリを作成する - Microsoft ID プラットフォーム | Azure
description: ユーザーをサインインさせる Web アプリをビルドする方法について学習します (サインイン)
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
ms.openlocfilehash: c8d7d5737a8332416a225154709ab7d66e447764
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961983"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>ユーザーをサインインさせる Web アプリ:サインインとサインアウト

ユーザーをサインインさせる Web アプリのコードにサインインを追加する方法について学習します。 次に、ユーザーをサインアウトさせる方法について学習します。

## <a name="sign-in"></a>サインイン

サインインは、次の 2 つの部分から構成されます。

- HTML ページのサインイン ボタン
- コントローラーの分離コードでのサインイン アクション

### <a name="sign-in-button"></a>サインイン ボタン

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、サインイン ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されます。 認証済みのアカウントがない場合にのみ表示されます。 つまり、ユーザーがまだサインインしていない場合やサインアウト済みの場合に表示されます。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC では、サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されます。 認証済みのアカウントがある場合にのみ表示されます。 つまり、ユーザーが既にサインインしている場合に表示されます。

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java のクイックスタートでは、サインイン ボタンは [main/resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/resources/templates/index.html) ファイルに配置されています。

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のクイックスタートでは、サインイン ボタンはありません。 分離コードは、Web アプリのルートに到達すると、ユーザーにサインインを求めるメッセージを自動的に表示します。 [app.py#L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18) を参照してください。

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>コントローラーの `SignIn` アクション

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET では、Web アプリの **[サインイン]** ボタンを選択すると、`AccountController` コントローラーの `SignIn` アクションがトリガーされます。 以前のバージョンの ASP.NET Core テンプレートでは、`Account` コントローラーは Web アプリに埋め込まれていました。 コントローラーは ASP.NET Core フレームワークの一部になったため、これは当てはまらなくなりました。

`AccountController` 用のコードは、ASP.NET Core リポジトリから [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) で入手できます。 アカウント制御では、Microsoft ID プラットフォーム エンドポイントへのリダイレクトによって、ユーザーにチャレンジを行います。 詳細については、ASP.NET Core の一部として提供されている [SignIn](https://github.com/aspnet/AspNetCore/blob/f3e6b74623d42d5164fd5f97a288792c8ad877b6/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs#L23-L31) メソッドを参照してください。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET では、サインアウトは、コントローラー (例: [AccountController.cs#L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)) の `SignOut()` メソッドからトリガーされます。 このメソッドは、(ASP.NET Core での動作とは対照的に) ASP.NET フレームワークの一部ではありません。 リダイレクト URI を提案した後に OpenID サインイン チャレンジを送信します。

```CSharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java では、サインアウトは、Microsoft ID プラットフォームの `logout` エンドポイントを直接呼び出し、`post_logout_redirect_uri` 値を提供することによって処理されます。 詳細については、[AuthPageController.java#L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48) を参照してください。

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="pythontabpython"></a>[Python](#tab/python)

他のプラットフォームとは異なり、MSAL.Python では、ユーザーのサインインをログイン ページから実行できるようにしています。 [app.py#L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28) を参照してください。

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

`_build_msal_app()` メソッドは、[app.py#L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) で次のように定義されています。

```Python
def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

ユーザーがアプリにサインインした後、ユーザーがサインアウトできるようにすることが必要です。

## <a name="sign-out"></a>サインアウト

Web アプリからのサインアウトに必要なのは、サインインしたアカウントに関する情報を Web アプリの状態から削除することだけではありません。
サインアウトするには、Web アプリによってユーザーが Microsoft ID プラットフォーム `logout` エンドポイントにリダイレクトされる必要もあります。 

Web アプリによってユーザーが `logout` エンドポイントにリダイレクトされると、このエンドポイントでは、ユーザーのセッションがブラウザーから消去されます。 アプリが `logout` エンドポイントに移動しなかった場合、ユーザーは資格情報を再入力しなくてもアプリに再認証されます。 理由は、Microsoft ID プラットフォーム エンドポイントとの有効なシングル サインイン セッションがあるからです。

詳細については、「[Microsoft ID プラットフォームと OpenID Connect プロトコル](v2-protocols-oidc.md)」ドキュメントの「[サインアウト要求を送信する](v2-protocols-oidc.md#send-a-sign-out-request)」セクションを参照してください。

### <a name="application-registration"></a>アプリケーションの登録

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

アプリケーションの登録中に、ログアウト後の URI を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `https://localhost:44321/signout-oidc` と登録しました。 詳細については、「[webApp アプリを登録する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)」を参照してください。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

アプリケーションの登録中に、ログアウト後の URI を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `https://localhost:44308/Account/EndSession` と登録しました。 詳細については、「[webApp アプリを登録する](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)」を参照してください。

# <a name="javatabjava"></a>[Java](#tab/java)

アプリケーションの登録中に、ログアウト後の URI を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `http://localhost:8080/msal4jsample/sign_out` と登録しました。

# <a name="pythontabpython"></a>[Python](#tab/python)

アプリケーションの登録中に、追加のログアウト URL を登録する必要はありません。 アプリはそのメイン URL でコールバックされます。

---

### <a name="sign-out-button"></a>サインアウト ボタン

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されます。 認証済みのアカウントがある場合にのみ表示されます。 つまり、ユーザーが既にサインインしている場合に表示されます。

```html
@using Microsoft.Identity.Web
@if (User.Identity.IsAuthenticated)
{
    <ul class="nav navbar-nav navbar-right">
        <li class="navbar-text">Hello @User.GetDisplayName()!</li>
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignOut">Sign out</a></li>
    </ul>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="AzureAD" asp-controller="Account" asp-action="SignIn">Sign in</a></li>
    </ul>
}
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET MVC では、サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されます。 認証済みのアカウントがある場合にのみ表示されます。 つまり、ユーザーが既にサインインしている場合に表示されます。

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java のクイックスタートでは、サインアウト ボタンは main/resources/templates/auth_page.html ファイルにあります。

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のクイックスタートでは、サインアウト ボタンは [templates/index.html#L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) ファイルに配置されています。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python Web App</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>コントローラーの `SignOut` アクション

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET では、Web アプリの **[サインアウト]** ボタンを選択すると、`AccountController` コントローラーの `SignOut` アクションがトリガーされます。 以前のバージョンの ASP.NET Core テンプレートでは、`Account` コントローラーは Web アプリに埋め込まれていました。 コントローラーは ASP.NET Core フレームワークの一部になったため、これは当てはまらなくなりました。

`AccountController` 用のコードは、ASP.NET Core リポジトリから [AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) で入手できます。 アカウント制御では次のことを行います。

- Azure AD でサインアウトが完了したときにコントローラーがコールバックされるように、OpenID リダイレクト URI を `/Account/SignedOut` に設定します。
- OpenID Connect ミドルウェアで Microsoft ID プラットフォームの `logout` エンドポイントに連絡できるようにする `Signout()` を呼び出します。 その後、エンドポイントは次のことを行います。

  - ブラウザーからセッション Cookie を消去します。
  - ログアウト URL をコールバックします。 既定では、ログアウト URL は、サインアウト済みビューのページ [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) を表示します。 このページは ASP.NET Core の一部としても提供されています。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET では、サインアウトは、コントローラー (例: [AccountController.cs#L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)) の `SignOut()` メソッドからトリガーされます。 このメソッドは、ASP.NET Core での動作とは対照的に、ASP.NET フレームワークの一部ではありません。 それでは次のことが行われます。

- OpenID サインアウト チャレンジを送信します。
- キャッシュをクリアします。
- 目的のページにリダイレクトします。

```CSharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java では、サインアウトは、Microsoft ID プラットフォームの `logout` エンドポイントを直接呼び出し、`post_logout_redirect_uri` 値を提供することによって処理されます。 詳細については、[AuthPageController.java#L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60) を参照してください。

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="pythontabpython"></a>[Python](#tab/python)

ユーザーをサインアウトさせるコードは [app.py#L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48) に含まれています。

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` エンドポイントへの呼び出しをインターセプトする

ログアウト後の URI により、アプリケーションはグローバル サインアウトに参加できるようになります。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenID Connect ミドルウェアでは、`OnRedirectToIdentityProviderForSignOut` という名前の OpenID Connect イベントを提供することで、お客様のアプリで Microsoft ID プラットフォーム `logout` エンドポイントへの呼び出しをインターセプトすることができます。 このイベントをサブスクライブする (そしてトークン キャッシュをクリアする) 方法の例については、[Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156) を参照してください

```CSharp
    // Handling the global sign-out
    options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
    {
        // Forget about the signed-in user
    };
```

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET では、ミドルウェアに委任してサインアウトを実行し、セッション Cookie をクリアします。

```CSharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

Java のクイックスタートでは、ログアウト後のリダイレクト URI は index.html ページを表示するだけです。

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のクイックスタートでは、ログアウト後のリダイレクト URI は index.html ページを表示するだけです。

---

## <a name="protocol"></a>Protocol

サインアウトの詳細については、[Open ID Connect](./v2-protocols-oidc.md) から入手できるプロトコルのドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-sign-user-production.md)
