---
title: Azure AD ASP.NET Web アプリにサインインを追加する
titleSuffix: Microsoft identity platform
description: 従来の Web ブラウザーベースのアプリケーションと OpenID Connect 標準を使用して、ASP.NET ソリューション上で Microsoft のサインインを実装します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff89d3c11ca88db14d2efd772be44aef7165a8a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964737"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>ASP.NET Web アプリに Microsoft へのサインインを追加する

このガイドでは、従来の Web ブラウザーベースのアプリケーションと OpenID Connect を使用して、ASP.NET MVC ソリューションを通じて Microsoft へのサインインを実装する方法を示します。

このガイドを完了すると、ご自分のアプリケーションが outlook.com や live.com などの個人用アカウントのサインインを受け入れることができるようになります。 また、Azure Active Directory (Azure AD) と統合されている会社や組織の職場または学校アカウントでも、アプリにサインインできるようになります。

> このガイドでは、Microsoft Visual Studio 2019 が必要です。  お持ちでない場合は、  [Visual Studio 2019 を無料でダウンロードできます](https://www.visualstudio.com/downloads/)。

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>このガイドで生成されたサンプル アプリの動作

![このチュートリアルで生成されたサンプル アプリの動作の紹介](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

作成するサンプル アプリケーションは、サインイン ボタンを通じてユーザーに認証を求める ASP.NET Web サイトにブラウザーを使用してアクセスするシナリオに基づいています。 このシナリオでは、Web ページを表示する処理の大半がサーバー側で発生します。

## <a name="libraries"></a>ライブラリ

このガイドでは、次のライブラリを使用します。

|ライブラリ|説明|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|アプリケーションで認証に OpenIDConnect を使用できるようにするためのミドルウェア|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|アプリケーションで Cookie を使用してユーザー セッションを維持できるようにするためのミドルウェア|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|ASP.NET 要求パイプラインを使用して、OWIN ベースのアプリケーションをインターネット インフォメーション サービス (IIS) 上で実行できるようにするミドルウェア|

## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、ASP.NET プロジェクトで OpenID Connect を使用して、OWIN ミドルウェアから認証パイプラインをインストールおよび構成する方法について説明します。

> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)したら「[アプリケーションの登録](#register-your-application)」に進み、実行前にコード サンプルを構成します。

### <a name="create-your-aspnet-project"></a>ASP.NET プロジェクトを作成する

1. Visual Studio で次の操作を行います。 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に移動します。
2. **[Visual C#\Web]** で **[ASP.NET Web アプリケーション (.NET Framework)]** を選択します。
3. アプリケーションに名前を付けて、 **[OK]** を選択します。
4. **[空]** を選択して、**MVC** 参照を追加するチェック ボックスをオンにします。

## <a name="add-authentication-components"></a>認証コンポーネントの追加

1. Visual Studio で次の操作を行います。 **[ツール]**  >  **[NuGet パッケージ マネージャー]**  >  **[パッケージ マネージャー コンソール]** の順に移動します。
2. パッケージ マネージャー コンソールのウィンドウで以下を入力し、*OWIN ミドルウェア NuGet パッケージ*を追加します。

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>これらのライブラリについて
> これらのライブラリでは、Cookie ベースの認証を通じて OpenID Connect を使用して、シングル サインオン (SSO) を有効にします。 認証が完了し、ユーザーを表すトークンがアプリケーションに送信されたら、OWIN ミドルウェアはセッションの Cookie を作成します。 その後、ブラウザーはこの Cookie を後の要求に使用するため、ユーザーはパスワードを再入力する必要がなく、追加の認証は必要ありません。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>認証パイプラインを構成する

OWIN ミドルウェアの Startup クラスを作成し、OpenID Connect の認証を構成するには、次の手順を使用します。 このクラスは、IIS プロセスの開始時に自動的に実行されます。

> [!TIP]
> プロジェクトのルート フォルダー内に `Startup.cs` ファイルがない場合:
> 1. プロジェクトのルート フォルダーを右クリックして、 **[追加]**  >  **[新しい項目]**  >  **[OWIN Startup クラス]** の順に選択します。<br/>
> 2. **Startup.cs** という名前を付けます。
>
>> 選択したクラスが標準的な C# クラスではなく、OWIN Startup クラスであることを確認します。 これを確認するには、[assembly: OwinStartup(typeof({NameSpace}.Startup))] が名前空間の上に表示されているかどうかを調べます。

1. *OWIN* と *Microsoft.IdentityModel* の参照を Startup.cs に追加します。

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. スタートアップ クラスを次のコードに置き換えます。

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> `ValidateIssuer = false` の設定は、このクイック スタートを単純にするためのものです。 実際のアプリケーションでは、発行者を検証する必要があります。
> その方法については、サンプルを参照してください。

<!--start-collapse-->
> ### <a name="more-information"></a>詳細情報
> *OpenIDConnectAuthenticationOptions* で指定したパラメーターは、アプリケーションが Azure AD と通信するための調整役として機能します。 OpenID Connect のミドルウェアはバックグラウンドで Cookie を使用するため、前のコードで示したように、Cookie 認証も設定する必要があります。 *ValidateIssuer* 値によって、OpenIdConnect はアクセスを 1 つの特定の組織に制限しないように設定されます。
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>サインインとサインアウト要求を処理するコントローラーを追加する

サインインとサインアウトの方法を公開する新しいコントローラーを作成するには、次の手順に従います。

1.  **Controllers** フォルダーを右クリックし、 **[追加]**  >  **[コントローラー]** の順に選択します。
2.  **[MVC (.NET version) Controller – Empty] (MVC (.NET バージョン) コントローラー – 空)** を選択します。
3.  **[追加]** を選択します。
4.  **HomeController** という名前を付け、 **[追加]** を選択します。
5.  クラスに OWIN の参照を追加します。

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. 認証チャレンジを開始することで、サインインとサインアウトを処理する次の 2 つのメソッドをコントローラーに追加します。

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>ユーザー サインイン用にアプリのホーム ページを作成する

Visual Studio で、サインイン ボタンを追加し、認証後にユーザー情報を表示するための、新しいビューを作成します。

1.  **Views\Home** フォルダーを右クリックし、 **[ビューの追加]** を選択します。
2.  新しいビューに **Index** という名前を付けます。
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
> このページは、SVG 形式で黒の背景の [サインイン] ボタンを追加します。<br/>![Microsoft アカウントでのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> その他のサインイン ボタンについては、[ブランド化ガイドライン](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Bブランド化ガイドライン)をご覧ください。
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>ユーザー要求を表示するコントローラーを追加する
このコントローラーでは、コントローラーを保護する `[Authorize]` 属性の使用例を示します。 この属性は、認証されたユーザーのみを許可することで、コントローラーへのアクセスを制限します。 次のコードではこの属性を利用して、サインインの一部として取得されたユーザー要求を表示します。

1.  **Controllers** フォルダーを右クリックし、 **[追加]**  >  **[コントローラー]** の順に選択します。
2.  **[MVC (.NET version) Controller – Empty] (MVC (.NET バージョン) コントローラー – 空)** を選択します。
3.  **[追加]** を選択します。
4.  これに **ClaimsController** という名前を付けます。
5.  controller クラスのコードを次のコードに置き換えます。 これによって、クラスに `[Authorize]` 属性が追加されます。

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
> `[Authorize]` 属性を使用しているため、このコントローラーのすべてのメソッドは、ユーザーが認証されている場合にのみ実行できます。 認証されていないユーザーがコントローラーにアクセスしようとすると、OWIN は認証チャレンジを開始し、ユーザーに認証を強制します。 前のコードは、ユーザーの ID トークンに含まれている特定のユーザー属性に対する要求の一覧を参照します。 これらの属性には、ユーザーのフルネームとユーザー名、さらにグローバル ユーザー識別子のサブジェクトが含まれます。 また、ユーザーの組織の ID を表す*テナント ID* も含まれています。 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>ユーザー要求を表示するビューを作成する

Visual Studio で、Web ページでユーザー要求を表示するための新しいビューを作成します。

1.  **Views\Claims** フォルダーを右クリックし、 **[ビューの追加]** を選択します。
2.  新しいビューに **Index** という名前を付けます。
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

## <a name="register-your-application"></a>アプリケーションの登録

アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加する場合、2 つのオプションがあります。

### <a name="option-1-express-mode"></a>オプション 1:簡易モード

アプリケーションをすばやく登録するには、次の手順に従います。

1. 新しい [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ウィンドウに移動します。
1. アプリケーションの名前を入力し、 **[登録]** を選択します。
1. 指示に従ってダウンロードすると、1 回のクリックで、新しいアプリケーションが自動的に構成されます。

### <a name="option-2-advanced-mode"></a>オプション 2:詳細設定モード

アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。

1. Visual Studio を開き、次の操作を行います。
   1. ソリューション エクスプローラーで、プロジェクトを選択してプロパティ ウィンドウを確認します (プロパティ ウィンドウが表示されない場合は F4 キーを押します)。
   1. [SSL 有効] を `True` に変更します。
   1. Visual Studio でプロジェクトを右クリックし、 **[プロパティ]** を選択してから **[Web]** タブを選択します。 **[サーバー]** セクションで、 **[プロジェクト URL]** の設定を **SSL URL** に変更します。
   1. SSL URL をコピーします。 この後の手順で、登録ポータルのリダイレクト URL の一覧に、この URL を追加します。<br/><br/>![プロジェクトのプロパティ](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 職場または学校アカウントを使用するか、個人用 Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 自分のアカウントで複数のテナントにアクセスできる場合は、右上隅で自分のアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://go.microsoft.com/fwlink/?linkid=2083908) ページに移動します。
1. **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: **ASPNET-Tutorial**)。
   1. 手順 1. で Visual Studio からコピーした SSL URL (たとえば `https://localhost:44368/`) を **[応答 URL]** に追加し、 **[登録]** を選択します。
1. **[認証]** メニューを選択し、 **[暗黙の付与]** の **[ID トークン]** を選択して、 **[保存]** を選択します。
1. `configuration\appSettings` セクションのルート フォルダーにある web.config ファイルに、次のコードを追加します。

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId` を、先ほど登録したアプリケーション ID に置き換えます。
1. `redirectUri` を、プロジェクトの SSL URL に置き換えます。

## <a name="test-your-code"></a>コードのテスト

Visual Studio でアプリケーションをテストするには、F5 キーを押してプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{ポート} の場所が開き、 **[Microsoft アカウントでサインイン]** ボタンが表示されます。 ボタンを選択して、サインイン プロセスを開始します。

テストを実行する準備が整ったら、Azure AD アカウント (職場または学校のアカウント) または個人用の Microsoft アカウント (<span>live.</span>com または <span>outlook.</span>com) を使用してサインインします。

![Microsoft アカウントでのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft アカウントへのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意
>  Microsoft ID プラットフォームと統合するアプリケーションは、データにアクセスする方法をユーザーと管理者が制御できるようにする承認モデルに従います。 このアプリケーションにアクセスするために Azure AD で認証されたユーザーは、アプリケーションによって要求されるアクセス許可 ("基本プロファイルの表示" と "自分がアクセス権を付与したデータへのアクセスの管理") に同意するように求められます。 これらのアクセス許可を受け入れると、ユーザーはアプリケーションの結果に進むこととなります。 ただし、次のいずれかの場合は、ユーザーに **[Need admin consent]\(管理者の同意が必要\)** ページが表示され、対応を求められることがあります。
>  > - アプリケーション開発者が、**管理者の同意**を必要とするその他の任意のアクセス許可を追加している。
>  > - または、( **[エンタープライズ アプリケーション] -> [ユーザー設定]** で) テナントが構成されていて、ユーザーが、自分の代わりにアプリが会社のデータにアクセスすることに同意できなくなっている。
>
> 詳細については、「[Microsoft ID プラットフォーム エンドポイントでのアクセス許可と同意](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)」を参照してください。
<!--end-collapse-->

#### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインした後、ユーザーは Web サイトのホーム ページにリダイレクトされます。 ホーム ページは、Microsoft アプリケーション登録ポータルのアプリケーション登録情報で指定されている HTTPS の URL です。 ホーム ページには、 *"Hello \<ユーザー>"* というウェルカム メッセージ、サインアウトのためのリンク、ユーザーの要求を表示するリンクが表示されます。 ユーザーの要求へのリンクは、前に作成した Claims コントローラーに接続しています。

### <a name="view-the-users-claims"></a>ユーザーの要求を表示する

ユーザーの要求を表示するには、認証されたユーザーのみが使用できるコントローラー ビューを参照するリンクを選択します。

#### <a name="view-the-claims-results"></a>要求の結果を表示する

コントローラー ビューを参照した後は、ユーザーの基本プロパティを示す次の表を確認する必要があります。

|プロパティ |値 |説明 |
|---|---|---|
|**Name** |ユーザーのフルネーム | ユーザーの姓と名
|**ユーザー名** |user<span>@domain.com</span> | ユーザーの識別に使用されるユーザー名|
|**[件名]** |サブジェクト |Web 全体でユーザーを一意に識別する文字列|
|**テナント ID** |Guid | ユーザーの Azure AD 組織を一意に表す **guid**|

さらに、認証要求内にあるすべての要求を示した表を確認する必要があります。 詳細については、[Azure AD ID トークン内にある要求の一覧](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)を参照してください。

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 属性を持つメソッドへのアクセスをテストする (省略可能)

`Authorize` 属性で保護されているコントローラーに対する匿名ユーザーとしてのアクセスをテストするには、次の手順に従います。

1. ユーザーのサインアウトのリンクを選択し、サインアウトのプロセスを完了します。
2. お使いのブラウザーで「 http://<span></span>localhost:{ポート}/claims」と入力し、`Authorize` 属性で保護されているコントローラーにアクセスします。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>保護されているコントローラーへアクセスした後に期待される結果

保護されているコントローラー ビューを使用するために、認証を求めるメッセージが表示されます。

## <a name="advanced-options"></a>[詳細オプション]

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Web サイト全体を保護する
Web サイト全体を保護するには、**Global.asax** ファイルで、`AuthorizeAttribute` 属性を `Application_Start` メソッドの `GlobalFilters` フィルターに追加します。

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>アプリケーションにサインインできるユーザーを制限する

このガイドに従ってアプリケーションを構築すると、既定では、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure AD と統合されたすべての会社や組織の職場と学校アカウントのサインインを受け入れることになります。 これは、SaaS アプリケーションに推奨されるオプションです。

アプリケーションへのユーザーのサインイン アクセスを制限するために、複数のオプションを使用できます。

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>オプション 1:アプリケーションへのサインインを 1 つの組織の Active Directory インスタンスのユーザーのみに制限する (シングルテナント)

このオプションは、"*LOB アプリケーション*" でよく使用されます。アプリケーションが特定の Azure AD インスタンスに属するアカウント (そのインスタンスの *ゲスト アカウント* を含む) からのサインインのみを受け入れるようにする場合は、以下の手順を実行します。

1. web.config ファイルで、`Tenant` パラメーターの値を `Common` から `contoso.onmicrosoft.com` などの組織のテナント名に変更します。
2. [OWIN Startup クラス](#configure-the-authentication-pipeline)の `ValidateIssuer` 引数を `true` に設定します。

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>オプション 2:アクセスを特定の組織の一覧内のユーザーに制限する

許可されている組織の一覧にある Azure AD 組織に属するユーザー アカウントのみが許可されるように、サインイン アクセスを制限できます。
1. [OWIN Startup クラス](#configure-the-authentication-pipeline)の `ValidateIssuer` 引数を `true` に設定します。
2. `ValidIssuers` パラメーターの値を、許可される組織の一覧に設定します。

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>オプション 3: カスタム メソッドを使用して発行者を検証する

**IssuerValidator** パラメーターを使用して、カスタム メソッドを実装して発行者を検証できます。 このパラメーターの使用方法の詳細については、[TokenValidationParameters クラス](/previous-versions/visualstudio/dn464192(v=vs.114))に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Web アプリが Web API を呼び出す方法について学習します。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>このクイックスタート ガイドで使用されているアプリケーションの作成方法について学習します

Microsoft ID プラットフォームで Web API を呼び出す Web アプリについてさらに学習します。

> [!div class="nextstepaction"]
> [Web API を呼び出す Web アプリ](scenario-web-app-sign-user-overview.md)

Microsoft Graph を呼び出す Web アプリを構築する方法について学習します。

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET チュートリアル](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID プラットフォームの改善にご協力ください。 2 つの質問から成るアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
