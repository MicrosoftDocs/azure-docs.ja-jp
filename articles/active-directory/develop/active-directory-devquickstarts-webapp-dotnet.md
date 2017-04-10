---
title: "Azure AD .NET Web アプリの概要 | Microsoft Docs"
description: "サインインのために Azure AD と連携する .NET MVC Web アプリを構築します。"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: e15a41a4-dc5d-4c90-b3fe-5dc33b9a1e96
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 43ba592b6294a9a75a20dacd81953a77c241b89f
ms.lasthandoff: 03/18/2017


---
# <a name="aspnet-web-app-sign-in-and-sign-out-with-azure-ad"></a>Azure AD を使用した ASP.NET Web アプリへのサインインおよびサインアウト
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure Active Directory (Azure AD) では、数行のコードを追加するだけでシングル サインイン/サインアウトを搭載できるため、Web アプリの ID 管理の外部委託を簡単に実現できます。 ユーザーは、Microsoft が実装する Open Web Interface for .NET (OWIN) ミドルウェアを使用して ASP.NET Web アプリへのサインインおよびサインアウトを行うことができます。 コミュニティ主導型の OWIN ミドルウェアは、.NET Framework 4.5 に含まれています。 この記事では、OWIN を使用して次を行う方法について説明します。

* Azure AD を ID プロバイダーとして使用して Web アプリにサインインする。
* いくつかのユーザー情報を表示する。
* アプリからサインアウトする。

## <a name="before-you-get-started"></a>開始する前に
* [アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip)します。
* アプリの登録先となる Azure AD テナントも必要です。 Azure AD テナントがない場合は、[テナントの取得方法](active-directory-howto-tenant.md)に関するページを参照して取得してください。

準備ができたら、次の 4 つのセクションの手順を実行します。

## <a name="step-1-register-the-new-app-with-azure-ad"></a>手順 1: Azure AD に新しいアプリを登録する
ユーザー認証を行うようにアプリを設定するには、まず次の手順でテナントにアプリを登録する必要があります。

1. [Azure Portal](https://portal.azure.com)にサインインします。
2. 上部のバーで、自分のアカウント名をクリックします。 **[ディレクトリ]** リストから、アプリを登録する Active Directory テナントを選択します。
3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従い、新しい **Web アプリケーションまたは WebAPI** (またはその両方) を作成します。
  * **[名前]** は、ユーザーに対して表示されるアプリ名です。
  * **[サインオン URL]** は、アプリのベース URL です。 スケルトンの既定の URL は https://localhost:44320/ です。
6. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 次のセクションで使用するために、アプリのページからこの値をコピーします。
7. アプリケーションの **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新します。 **[アプリケーション ID/URI]** は、アプリケーションの一意識別子です。 名前は、`https://<tenant-domain>/<app-name>` のように付けます (例: `https://contoso.onmicrosoft.com/my-first-aad-app`)。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>手順 2: OWIN 認証パイプラインを使用するようにアプリを設定する
この手順では、OpenID Connect 認証プロトコルを使用するように、OWIN ミドルウェアを構成します。 OWIN は、サインイン要求およびサインアウト要求の発行、ユーザー セッションの管理、ユーザー情報の取得などに使用します。

1. まず、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

     ```
     PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
     PM> Install-Package Microsoft.Owin.Security.Cookies
     PM> Install-Package Microsoft.Owin.Host.SystemWeb
     ```

2. `Startup.cs` という名前のプロジェクトを右クリックし、**[追加]**、**[新しいアイテム]** の順に選択して**OWIN** を検索し、OWIN Startup クラスをプロジェクトに追加します。 アプリの起動時に、OWIN ミドルウェアによって **Configuration(...)** メソッドが呼び出されます。
3. クラスの宣言を `public partial class Startup` に変更します。 このクラスの部分は、別のファイルに実装済みです。 **Configuration(...)** メソッドで **ConfgureAuth(...)** を呼び出して、アプリの認証をセットアップします。  

     ```C#
     public partial class Startup
     {
         public void Configuration(IAppBuilder app)
         {
             ConfigureAuth(app);
         }
     }
     ```

4. App_Start\Startup.Auth.cs ファイルを開いて、**ConfigureAuth(...)** メソッドを実装します。 *OpenIDConnectAuthenticationOptions* で指定したパラメーターは、アプリが Azure AD と通信するための調整役として機能します。 OpenID Connect ミドルウェアはバックグラウンドで Cookie を使用するため、Cookie 認証も設定する必要があります。

     ```C#
     public void ConfigureAuth(IAppBuilder app)
     {
         app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

         app.UseCookieAuthentication(new CookieAuthenticationOptions());

         app.UseOpenIdConnectAuthentication(
             new OpenIdConnectAuthenticationOptions
             {
                 ClientId = clientId,
                 Authority = authority,
                 PostLogoutRedirectUri = postLogoutRedirectUri,
             });
     }
     ```

5. プロジェクトのルートにある web.config ファイルを開いて、`<appSettings>` セクションに構成値を入力します。
  * `ida:ClientId`: 「手順 1: Azure AD に新しいアプリを登録する」で Azure Portal からコピーした GUID。
  * `ida:Tenant`: Azure AD テナントの名前 (contoso.onmicrosoft.com など)。
  * `ida:PostLogoutRedirectUri`: Azure AD に、サインアウト要求が正常に完了した後のユーザーのリダイレクト先を指示するインジケーター。

## <a name="step-3-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad"></a>手順 3: OWIN を使用して Azure AD にサインインおよびサインアウト要求を発行する
これで、OpenID Connect 認証プロトコルを使用して Azure AD と通信するように、アプリを適切に構成できました。 OWIN により、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持のすべてが処理されます。 OWIN により処理されないのは、ユーザーにサインインおよびサインアウトの方法を提供する処理のみです。

1. コントローラーで承認タグを使用することにより、特定のページには、サインインしてからでないとアクセスできないようにすることができます。 これを行うには、Controllers\HomeController.cs を開いて、About コントローラーに `[Authorize]` タグを追加します。

     ```C#
     [Authorize]
     public ActionResult About()
     {
       ...
     ```

2. OWIN を使用することにより、コード内から認証要求を直接発行することもできます。 これを行うには、 Controllers\AccountController.cs を開きます。 次に、SignIn() アクションおよび SignOut() アクションで、OpenID Connect チャレンジとサインアウト要求を発行します。

     ```C#
     public void SignIn()
     {
         // Send an OpenID Connect sign-in request.
         if (!Request.IsAuthenticated)
         {
             HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
         }
     }
     public void SignOut()
     {
         // Send an OpenID Connect sign-out request.
         HttpContext.GetOwinContext().Authentication.SignOut(
              OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
     }
     ```

3. Views\Shared\_LoginPartial.cshtml を開き、アプリのサインインとサインアウトのリンクをユーザーに表示するとともに、ビューにユーザー名を出力します。

    ```HTML
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

## <a name="step-4-display-user-information"></a>手順 4: ユーザー情報を表示する
Azure AD で OpenID Connect を使用してユーザーを認証すると、"要求" (ユーザーに関するアサーション) を含む id_token がアプリに返されます。 以下の手順により、これらの要求を使用してアプリをカスタマイズできます。

1. Controllers\HomeController.cs ファイルを開きます。 `ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー クレームにアクセスできます。

 ```C#
 public ActionResult About()
 {
     ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
     ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
     ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
     ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
     ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

     return View();
 }
 ```

2. アプリケーションをビルドし、実行します。 テナントでドメインが onmicrosoft.com である新規ユーザーを作成していない場合は、この時点で作成します。 その方法は次のとおりです。

  a. そのユーザーとしてサインインし、ユーザーの ID が上部のバーにどのように表示されるかを確認します。

  b. サインアウトしてから、テナント内の別のユーザーとして再びサインインします。

  c. 状況が許す場合は、このアプリケーションの別のインスタンス (独自のクライアント ID を使用) を登録して実行し、シングル サインインの動作を確認します。

## <a name="next-steps"></a>次のステップ
リファレンスについては、[完全なサンプル](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip) (環境に応じた構成値を除く) をご覧ください。

これで、さらに高度なトピックに進む準備ができました。 たとえば、[Azure AD を使用して Web API を保護](active-directory-devquickstarts-webapi-dotnet.md)してみましょう。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

