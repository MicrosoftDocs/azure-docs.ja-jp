---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2b9d696ca896d0c8f0801f055000b9763d65d7ff
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181787"
---
## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>サインインとサインアウト要求を処理するコントローラーを追加する

この手順では、サインインとサインアウトの方法を公開する新しいコントローラーを作成する方法について説明します。

1.  `Controllers` フォルダーを右クリックし、[`Add`] > [`Controller`] を選択します。
2.  [`MVC (.NET version) Controller – Empty`] を選択します。
3.  *[追加]* をクリックします。
4.  それに`HomeController` と名前を付けて、 *[追加]* をクリックします。
5.  クラスに *OWIN* 参照を追加します。

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. コードを使用して認証チャレンジを開始し、サインインとサインアウトを処理する以下の 2 つのメソッドをコントローラーに追加します。
    
    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>アプリのホーム ページを作成し、サインイン ボタンでユーザーをサインインする

Visual Studio で新しいビューを作成してサインイン ボタンを追加し、認証後にユーザー情報を表示します。

1.  `Views\Home` フォルダーを右クリックし、[`Add View`] を選択します。
2.  これに `Index` という名前を付けます。
3.  サインイン ボタンが含まれいる次の HTML をファイルに追加します。

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>詳細情報
> このページは、SVG 形式で黒の背景の [サインイン] ボタンを追加します。<br/>![Microsoft アカウントでのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> その他のサインイン ボタンについては、[このページ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "ブランド化ガイドライン")をご覧ください。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>ユーザー要求を表示するコントローラーを追加する
このコントローラーでは、コントローラーを保護する `[Authorize]` 属性の使用例を示します。 この属性は、認証されたユーザーのみを許可して、コントローラーへのアクセスを制限します。 次のコードではこの属性を利用して、サインインの一部として取得されたユーザー要求を表示します。

1.  `Controllers` フォルダーを右クリックし、`Add` > `Controller` と選択します。
2.  [`MVC {version} Controller – Empty`] を選択します。
3.  *[追加]* をクリックします。
4.  これに `ClaimsController` という名前を付けます。
5.  コントローラー クラスのコードを、以下のコードに置き換えます。これによって、クラスに `[Authorize]` 属性が追加されます。

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>詳細情報
> `[Authorize]` 属性を使用しているため、このコントローラーのすべてのメソッドは、ユーザーが認証されている場合にのみ実行できます。 認証されていないユーザーがコントローラーにアクセスしようとすると、OWIN は認証チャレンジを開始し、ユーザーに認証を強制します。 上記のコードは、ユーザーの ID トークンに含まれる特定のユーザー属性の要求の一覧を参照します。 これらの属性には、ユーザーのフルネームとユーザー名、さらにグローバル ユーザー識別子のサブジェクトが含まれます。 また、ユーザーの組織の ID を表す*テナント ID* も含まれています。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>ユーザー要求を表示するビューを作成する

Visual Studio で、Web ページでユーザー要求を表示するための新しいビューを作成します。

1.  `Views\Claims` フォルダーを右クリックし、[`Add View`] を選択します。
2.  これに `Index` という名前を付けます。
3.  次の HTML をファイルに追加します。

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```
