---
title: ユーザーをサインインさせる Web アプリ (サインイン) - Microsoft ID プラットフォーム
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ed61a09ffc76b4813dcb97330d3a1a436aa16eb
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086467"
---
# <a name="web-app-that-signs-in-users---sign-in"></a>ユーザーをサインインさせる Web アプリ - サインイン

ユーザーをサインインさせる Web アプリのコードにサインインを追加する方法について学習します。

## <a name="sign-in"></a>サインイン

サインインの実装に必要なのは、前の記事の[アプリのコード構成](scenario-web-app-sign-user-app-configuration.md)で確認したコードだけです。
ユーザーがお客様のアプリにサインインしたら、ユーザーをサインアウトできるようにしたい場合があります。サインアウトは、ASP.NET Core によって自動的に処理されます。

## <a name="what-sign-out-involves"></a>サインアウトの関連事項

Web アプリからのサインアウトでは、サインインしたアカウントに関する情報が Web アプリの状態から削除されるだけではありません。
サインアウトするには、Web アプリによってユーザーが Microsoft ID プラットフォーム `logout` エンドポイントにリダイレクトされる必要もあります。Web アプリによってユーザーが `logout` エンドポイントにリダイレクトされると、このエンドポイントでは、ユーザーのセッションがブラウザーから消去されます。 アプリで `logout` エンドポイントに移動しない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。Microsoft ID プラットフォーム エンドポイントのシングル サインイン セッションが有効であるためです。

詳細については、概念に関するドキュメント「[Microsoft ID プラットフォーム と OpenID Connect プロトコル](v2-protocols-oidc.md)」の「[サインアウト要求を送信する](v2-protocols-oidc.md#send-a-sign-out-request)」セクションを参照してください。

### <a name="application-registration"></a>アプリケーションの登録

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

アプリケーションの登録中に、**ログアウト後の URI** を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `https://localhost:44321/signout-oidc` と登録しました。 詳細については、「[Register the webApp app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg#register-the-webapp-app-webapp)」 (webApp アプリを登録する) を参照してください。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

アプリケーションの登録中に、**ログアウト後の URI** を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `https://localhost:44308/Account/EndSession` と登録しました。 詳細については、「[webApp アプリを登録する](https://github.com/Azure-Samples/active-directory-dotnet-web-single-sign-out#register-the-service-app-webapp-distributedsignout-dotnet)」を参照してください

# <a name="javatabjava"></a>[Java](#tab/java)

アプリケーションの登録中に、**ログアウト後の URI** を登録します。 このチュートリアルでは、 **[認証]** ページの **[詳細設定]** セクションの **[ログアウト URL]** フィールドに `http://localhost:8080/msal4jsample/` と登録しました。

# <a name="pythontabpython"></a>[Python](#tab/python)

アプリケーションの登録中に、ログアウト URL を登録する必要はありません。 このサンプルでは、グローバル サインアウトは実装しません

---

### <a name="sign-out-button"></a>サインアウト ボタン

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core では、サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されており、認証済みのアカウントがあるとき (つまり、ユーザーが以前にサインインしたとき) にのみ表示されます。

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

ASP.NET MVC では、サインアウト ボタンは `Views\Shared\_LoginPartial.cshtml` で公開されており、認証済みのアカウントがあるとき (つまり、ユーザーが以前にサインインしたとき) にのみ表示されます。

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

Java のクイックスタートでは、サインアウト ボタンは main/resources/templates/auth_page.html ファイルにあります

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

Python のクイックスタートでは、サインアウト ボタンは [templates/display.html](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e1199b4c3cdcb637cf0d8306832efbd85492e123/templates/display.html#L18-L20) ファイルにあります

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Acquire Token Result </title>
</head>
<body>
    {% if cond  %}
        <p1><b>Your information</b> </p1>
        <table>
        {% for key, value in auth_result.items() %}
           <tr>
                <th> {{ key }} </th>
                <td> {{ value }} </td>
           </tr>
        {% endfor %}
        </table>
        <form action="/logout" >
            <input type="submit" value=" Logout"/>
        </form>
    {% else %}
        <p1><b> {{auth_result}} </b> </p1>
        <form action="/authenticate" >
            <input type="submit" value=" Sign-in"/>
        </form>
    {% endif %}
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>コントローラーの `Signout()` アクション

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET では、Web アプリ上の **[サインアウト]** ボタンを押すと、`AccountController` コントローラーの `SignOut` アクションがトリガーされます。 以前のバージョンの ASP.NET Core テンプレートでは、`Account` コントローラーは Web アプリに埋め込まれていましたが、現在は ASP.NET Core フレームワーク自体の一部になっているため、それはもう当てはまりません。

`AccountController` 用のコードは、[AccountController.cs](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Controllers/AccountController.cs) からの ASP.NET Core リポジトリから利用できます。 アカウント制御では次のことを行います。

- Azure AD でサインアウトを実行したときにコントローラーがコールバックされるように、OpenID リダイレクト URI を `/Account/SignedOut` に設定します
- OpenIdConnect ミドルウェアで Microsoft ID プラットフォーム `logout` エンドポイントに連絡できるようにする `Signout()` を呼び出します。エンドポイントでは次のことが行われます。

  - ブラウザーからセッション Cookie が消去される
  - 最後に**ログアウト URL** のコール バックを呼び出す。既定では、ASP.NET Core の一部としても提供される、サインアウトされた表示ページ [SignedOut.html](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/Areas/AzureAD/Pages/Account/SignedOut.cshtml) が表示されます。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET で、サインアウトは、コントローラー (例: AccountController) の `SignOut()` メソッドからトリガーされます。 このメソッドは、(ASP.NET Core での動作とは対照的に) ASP.NET フレームワークの一部ではありません。 `async` も使用されません。そのため、次のことが行われます。

- OpenId サインアウト チャレンジの送信
- キャッシュのクリア
- 目的のページへのリダイレクト

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

Java では、サインアウトは、Microsoft ID プラットフォームのログアウト エンドポイントを直接呼び出し、post_logout_redirect_uri を提供することによって処理されます。

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

```Python
@app.route("/logout")
def logout():
    return flask.redirect(flask.url_for('index'))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>`logout` エンドポイントへの呼び出しをインターセプトする

ログアウト後の URI により、アプリケーションはグローバル サインアウトに参加できるようになります。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core OpenIdConnect ミドルウェアでは、`OnRedirectToIdentityProviderForSignOut` という名前の OpenIdConnect イベントを提供することで、お客様のアプリで Microsoft ID プラットフォーム `logout` エンドポイントへの呼び出しをインターセプトすることができます。 このイベントをサブスクライブする (そしてトークン キャッシュをクリアする) 方法の例については、「[Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L156)」を参照してください

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

この Java のクイックスタートの場合は、サインアウト後のリダイレクト URI では index. html ページが表示されるだけです 

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のクイックスタートの場合は、サインアウト後のリダイレクト URI では index. html ページが表示されるだけです。

---

## <a name="protocol"></a>Protocol

サインアウトの詳細については、[Open ID Connect](./v2-protocols-oidc.md) から入手できるプロトコルのドキュメントを参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-web-app-sign-user-production.md)
