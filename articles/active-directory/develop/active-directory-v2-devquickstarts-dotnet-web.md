---
title: "Azure AD v2.0 .NET Web アプリのサインインの概要 | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用する .NET MVC Web アプリを構築する方法を説明します。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: c8b97ac6-0a06-4367-81b6-7d1d98152b14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 43b77cabdb2d8832bda8fd0b726ba27edb0a0602
ms.openlocfilehash: 2992b074986a7b7f3244ce996f2b41269bff8bbd


---
# <a name="add-sign-in-to-an-net-mvc-web-app"></a>.NET MVC Web アプリへのサインインの追加
v2.0 エンドポイントを使用すると、Microsoft の個人および職場/学校アカウントの両方に対応した Web アプリに認証をすばやく追加できます。  ASP.NET Web アプリでは、.NET Framework 4.5 に含まれる Microsoft の OWIN ミドルウェアを使用することにより、これを達成できます。

> [!NOTE]
> Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。  v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
>
>

 ここで、ユーザーをサインインするために OWIN を使用する Web アプリをビルドし、ユーザーに関する情報を表示し、アプリからユーザーをサインアウトします。

## <a name="download"></a>ダウンロード
このチュートリアルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet)で管理されています。  追加の参考資料として、 [アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) したり、スケルトンを複製したりすることができます:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

完成したアプリは、このチュートリアルの終わりにも示しています。

## <a name="register-an-app"></a>アプリを登録します
[apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で新しいアプリを作成するか、この[詳細な手順](active-directory-v2-app-registration.md)に従います。  次のことを確認します。

* アプリに割り当てられた**アプリケーション ID** をメモしておきます。これは後で必要になります。
* アプリの **Web** プラットフォームを追加します。
* 適切な **リダイレクト URI**を入力します。 リダイレクト URI は、認証の応答が送られる Azure AD を示します。このチュートリアルの既定値は `https://localhost:44326/` です。

## <a name="install--configure-owin-authentication"></a>OWIN 認証のインストールと構成
ここでは、OpenID Connect 認証プロトコルを使用するように、OWIN ミドルウェアを構成します。  OWIN は、サインイン要求またはサインアウト要求の発行、ユーザー セッションの管理、ユーザーに関する情報の取得などを行うために使用されます。

1. 最初に、プロジェクトのルートにある `web.config` ファイルを開いて、`<appSettings>` セクションでアプリの構成値を入力します。

  * `ida:ClientId` は、登録ポータルでアプリに割り当てられた **アプリケーション ID** です。
  * `ida:RedirectUri` は、ポータルで入力した **リダイレクト URI** です。

2. 次に、パッケージ マネージャー コンソールを使用して、プロジェクトに OWIN ミドルウェア NuGet パッケージを追加します。

        ```
        PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
        PM> Install-Package Microsoft.Owin.Security.Cookies
        PM> Install-Package Microsoft.Owin.Host.SystemWeb
        ```  

3. OWIN Startup クラスを `Startup.cs` という名前のプロジェクトに追加します。プロジェクトを右クリックし、**[追加]** --> **[新しい項目]** の順にクリックして、"OWIN" を検索します。  アプリが起動すると、OWIN ミドルウェアは `Configuration(...)` メソッドを呼び出します。
4. クラス宣言を `public partial class Startup` に変更します。このクラスの一部は別ファイルで実装済みです。  `Configuration(...)` メソッドで、ConfigureAuth(...) を呼び出して、Web アプリ用の認証をセットアップします。  

        ```C#
        [assembly: OwinStartup(typeof(Startup))]
        
        namespace TodoList_WebApp
        {
            public partial class Startup
            {
                public void Configuration(IAppBuilder app)
                {
                    ConfigureAuth(app);
                }
            }
        }
        ```

5. ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(...)` メソッドを実装します。  `OpenIdConnectAuthenticationOptions` で提供されたパラメーターは、アプリが Azure AD と通信するための調整役として機能します。  OpenID Connect ミドルウェアは、ユーザーに意識されない Cookie を使用するため、Cookie 認証もセットアップする必要があります。

        ```C#
        public void ConfigureAuth(IAppBuilder app)
                     {
                             app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);
        
                             app.UseCookieAuthentication(new CookieAuthenticationOptions());
        
                             app.UseOpenIdConnectAuthentication(
                                     new OpenIdConnectAuthenticationOptions
                                     {
                                             // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
                                             // The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
                                             // In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.
        
                                             ClientId = clientId,
                                             Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0 "),
                                             RedirectUri = redirectUri,
                                             Scope = "openid email profile",
                                             ResponseType = "id_token",
                                             PostLogoutRedirectUri = redirectUri,
                                             TokenValidationParameters = new TokenValidationParameters
                                             {
                                                     ValidateIssuer = false,
                                             },
                                             Notifications = new OpenIdConnectAuthenticationNotifications
                                             {
                                                     AuthenticationFailed = OnAuthenticationFailed,
                                             }
                                     });
                     }
        ```

## <a name="send-authentication-requests"></a>認証要求を送信する
これまでに、アプリは、OpenID Connect 認証プロトコルを使用して v2.0 エンドポイントと適切に通信するように構成されています。  OWIN は、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持を行うためのすべての煩わしい処理を実行します。  OWIN により処理されないのは、ユーザーにサインインおよびサインアウトの方法を提供する処理のみです。

- コントローラーで承認タグを使用することにより、特定のページでは、サインインしてからでないとアクセスできないようにすることができます。  `Controllers\HomeController.cs` を開いて、`[Authorize]` タグを About コントローラーに追加します。
        
        ```C#
        [Authorize]
        public ActionResult About()
        {
          ...
        ```

- OWIN を使用することにより、コード内から認証要求を直接発行することもできます。  `Controllers\AccountController.cs`を開きます。  SignIn() アクションおよび SignOut() アクションで、それぞれ OpenID Connect チャレンジおよびサインアウト要求を発行します。

        ```C#
        public void SignIn()
        {
            // Send an OpenID Connect sign-in request.
            if (!Request.IsAuthenticated)
            {
                HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
            }
        }
        
        // BUGBUG: Ending a session with the v2.0 endpoint is not yet supported.  Here, we just end the session with the web app.  
        public void SignOut()
        {
            // Send an OpenID Connect sign-out request.
            HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
            Response.Redirect("/");
        }
        ```

- ここで、 `Views\Shared\_LoginPartial.cshtml`を開きます。  ここは、ユーザーに、アプリのサインイン リンクおよびサインアウト リンクを表示し、ユーザーの名前をビュー内に表示する場所です。

        ```HTML
        @if (Request.IsAuthenticated)
        {
            <text>
                <ul class="nav navbar-nav navbar-right">
                    <li class="navbar-text">
        
                        @*The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves.*@
        
                        Hello, @(System.Security.Claims.ClaimsPrincipal.Current.FindFirst("preferred_username").Value)!
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

## <a name="display-user-information"></a>ユーザー情報を表示する
OpenID Connect を使用してユーザーの認証処理を実行すると、v2.0 エンドポイントは id_token をアプリに返します。id_token には、"要求"、つまりユーザーに関する "アサーション" が含まれます。  これらのクレームを使用して、アプリを個人向けにカスタマイズすることができます。

- `Controllers\HomeController.cs` ファイルを開きます。  `ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー クレームにアクセスできます。

        ```C#
        [Authorize]
        public ActionResult About()
        {
            ViewBag.Name = ClaimsPrincipal.Current.FindFirst("name").Value;
        
            // The object ID claim will only be emitted for work or school accounts at this time.
            Claim oid = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier");
            ViewBag.ObjectId = oid == null ? string.Empty : oid.Value;
        
            // The 'preferred_username' claim can be used for showing the user's primary way of identifying themselves
            ViewBag.Username = ClaimsPrincipal.Current.FindFirst("preferred_username").Value;
        
            // The subject or nameidentifier claim can be used to uniquely identify the user
            ViewBag.Subject = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
        
            return View();
        }
        ```

## <a name="run"></a>実行
最後に、アプリを構築して実行します。   Microsoft の個人または職場/学校アカウントのいずれかでサインインすると、ユーザーの ID が上部のナビゲーション バーにどのように反映されるかがわかります。  これで、Web アプリが業界標準のプロトコルで保護され、個人および職場/学校アカウントの両方でユーザーを認証できるようになりました。

参照用に、完成したサンプル (構成値を除く) が[ここに .zip として提供されています](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet/archive/complete.zip)。または、GitHub から複製することもできます。

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIdConnect-DotNet.git```

## <a name="next-steps"></a>次のステップ
これ以降は、さらに高度なトピックに進むことができます。  次のチュートリアルを試してみてください。

[v2.0 エンドポイントでの Web API の保護 >>](active-directory-devquickstarts-webapi-dotnet.md)

その他のリソースについては、以下を参照してください。

* [v2.0 開発者向けガイド >>](active-directory-appmodel-v2-overview.md)
* [StackOverflow "azure-Active Directory" タグ >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

## <a name="get-security-updates-for-our-products"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題が発生したときに通知を受け取ることをお勧めします。そのためには、[このページ](https://technet.microsoft.com/security/dd252948)にアクセスし、セキュリティ アドバイザリ通知を受信登録してください。



<!--HONumber=Feb17_HO1-->


