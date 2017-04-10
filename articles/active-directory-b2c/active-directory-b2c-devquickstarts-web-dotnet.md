---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C を使用してサインイン、サインアップ、プロファイル管理を行う Web アプリケーションを構築する方法。"
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 0b1c6ede-de0a-4c32-92b3-5c813dc26804
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: a0e03bda81d26dbc28706b45ee974359337573f0


---
# <a name="azure-ad-b2c-build-a-net-web-app"></a>Azure AD B2C: .NET Web アプリを構築する
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Azure Active Directory (Azure AD) B2C を使用すると、強力なセルフサービス方式の ID 管理機能を、わずかな手順で Web アプリに追加できます。 この記事では、ユーザーのサインアップ、サインイン、プロファイル管理などの処理を含む .NET Model-View-Controller (MVC) web アプリの作成方法について説明します。 このアプリには、ユーザー名または電子メールと、Facebook や Google などのソーシャル アカウントを使用した、サインアップとサインインのサポートが含まれます。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得
Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。  まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

## <a name="create-an-application"></a>アプリケーションの作成
次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。  次を行ってください。

* アプリケーションに **Web アプリまたは Web API** を含めます。
* **[リダイレクト URI]** として「`https://localhost:44316/`」と入力します。 これはこのサンプル コードで使用する既定の URL です。
* アプリケーションに割り当てられた **アプリケーション ID** をメモしておきます。  この情報は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成
Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このコード サンプルには、3 つの ID エクスペリエンス (サインアップ、サインイン、プロファイル編集) が含まれています。 [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md#create-a-sign-up-policy)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。

> [!NOTE]
> このチュートリアルでは取り上げていませんが、Azure AD B2C では、サインアップまたはサインイン複合ポリシーもサポートしています。  サインアップまたはサインイン ポリシーについては、 [こちらの対応するチュートリアル](active-directory-b2c-devquickstarts-web-dotnet-susi.md)に記載されています。
>
>

3 つのポリシーを作成するときは、以下の点に注意してください。

* ID プロバイダーのブレードで、**[User ID sign-up (ユーザー ID サインアップ)]** または **[Email sign-up (電子メール サインアップ)]** を選択します。
* サインアップ ポリシーで、 **[表示名]** と他のサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 これらのポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

3 つのポリシーを作成した後は、いつでもアプリをビルドできます。  

## <a name="download-the-code-and-configure-authentication"></a>コードのダウンロードと認証の構成
このサンプルのコードは、 [GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet)で管理されています。 手順に従ってサンプルを構築するには、 [スケルトン プロジェクトを .zip ファイルとしてダウンロード](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip)します。 スケルトンを複製することもできます。

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

また、完成済みのサンプルも、[.zip ファイルとして入手する](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)か、同じリポジトリの `complete` ブランチで入手できます。

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。

アプリは、認証メッセージを送信することで、Azure AD B2C と通信します。このとき、HTTP 要求の一環として実行するポリシーを指定します。 .NET Web アプリケーションの場合、Microsoft の OWIN ミドルウェアを使用して OpenID Connect 認証要求の送信や、ポリシーの実行、ユーザーのセッションの管理などができます。

まず、Visual Studio のパッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

次に、プロジェクトのルートにある `web.config` ファイルを開き、アプリの構成値を `<appSettings>` セクションに入力して元の値を置き換えます。

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

次に、OWIN スタートアップ クラスを `Startup.cs` プロジェクトに追加します。 プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順に選択して、"OWIN" を検索します。 **クラスの宣言は `public partial class Startup` に変更してください**。 このクラスの一部は別のファイルに実装してあります。 アプリが起動すると、OWIN ミドルウェアは `Configuration(...)` メソッドを呼び出します。 このメソッドでは、`ConfigureAuth(...)` を呼び出します。ここでアプリの認証をセットアップします。

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(...)` メソッドを実装します。  `OpenIdConnectAuthenticationOptions` に指定するパラメーターは、アプリが Azure AD と通信するための調整役として機能します。 Cookie 認証を設定する必要もあります。 特に、OpenID Connect ミドルウェアは、ユーザー セッションを維持するために Cookie を使用します。

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignUpPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(ProfilePolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SignInPolicyId));
    }

    // Used for avoiding yellow-screen-of-death
    private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        notification.HandleResponse();
        if (notification.Exception.Message == "access_denied")
        {
            notification.Response.Redirect("/");
        }
        else
        {
            notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
        }

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Azure AD への認証要求の送信
ここまでの手順で、アプリは OpenID Connect 認証プロトコルを使用して Azure AD B2C と通信するよう適切に構成されています。  OWIN は、認証メッセージの構築、Azure AD からのトークンの検証、およびユーザー セッションの維持を行うためのすべての処理を実行します。  残りは、各ユーザーのフローを開始する処理です。

ユーザーが Web アプリの **[Sign up (サインアップ)]**、**[Sign in (サインイン)]**、または **[Edit profile (プロファイルの編集)]** を選択すると、関連付けられているアクションが `Controllers\AccountController.cs` で呼び出されます。 いずれの場合にも、組み込みの OWIN メソッドを使用して適切なポリシーをトリガーできます。

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties () { RedirectUri = "/" }, Startup.SignInPolicyId);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SignUpPolicyId);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.ProfilePolicyId);
    }
}
```

また、コントローラーで `[Authorize]` タグを使用して、ユーザーがサインイン済みでない場合に特定のポリシーの実行を要求することもできます。 `Controllers\HomeController.cs` を開いて、`[Authorize]` タグを要求コントローラーに追加します。  OWIN は、Authorize タグが呼び出されたときに、最後に構成されたポリシーを選択して実行します。

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a policy if the user is not already signed in the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

アプリからのユーザーのサインアウトにも OWIN を使用できます。 `Controllers\AccountController.cs`で、次のように記述します。  

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>ユーザー情報を表示する
OpenID Connect を使用してユーザーを認証すると、Azure AD は、 **要求**が含まれる ID トークンをアプリに返します。 これらは、ユーザーに関するアサーションです。 要求を使用して、アプリを個人向けにカスタマイズすることができます。  

`Controllers\HomeController.cs` ファイルを開きます。 `ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー要求にアクセスできます。

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

同じ方法で、アプリケーションが受け取るすべての要求にアクセスできます。  アプリが受信するすべての要求の一覧が、 **[Claims (要求)]** ページに表示されます。

## <a name="run-the-sample-app"></a>サンプル アプリを実行する
最後に、アプリを構築して実行できます。 電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。 サインアウトし、同じユーザーとしてもう一度サインインします。 そのユーザーのプロファイルを編集します。 サインアウトし、別のユーザーとしてサインアップします。 **[Claims (要求)]** タブに表示される情報が、ポリシーで構成した情報に対応していることを確認してください。

## <a name="add-social-idps"></a>ソーシャル IDP を追加する
現時点では、このアプリは **ローカル アカウント**を使用したユーザーのサインアップとサインインのみをサポートしています。 これらは、ユーザー名とパスワードを使用する B2C ディレクトリに格納されているアカウントです。 Azure AD B2C を使用すると、コードを変更せずに、その他の **ID プロバイダー** (IDP) のサポートを追加することができます。

ソーシャル IDP をアプリに追加するには、まず、以下の記事に記載されている詳細な手順に従います。 サポートする IDP ごとに、そのシステムでアプリケーションを登録してクライアント ID を取得する必要があります。

* [Facebook を IDP として設定する](active-directory-b2c-setup-fb-app.md)
* [Google を IDP として設定する](active-directory-b2c-setup-goog-app.md)
* [Amazon を IDP として設定する](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn を IDP として設定する](active-directory-b2c-setup-li-app.md)

ID プロバイダーを B2C ディレクトリに追加した後、3 つのポリシーをそれぞれ編集し、 [ポリシーに関するリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、新しい IDP を加える必要があります。 ポリシーを保存した後、再度アプリを実行します。  各 ID エクスペリエンスに、サインインおよびサインアップの選択肢として新しい IDP が追加されていることがわかります。

いろいろなポリシーを試して、サンプル アプリへの影響を確認できます。 ID を追加または削除したり、アプリケーションの要求を操作したり、サインアップ属性を変更してみます。 ポリシー、認証要求、および OWIN すべてが互いにどのように結び付いているかを理解できるまで、いろいろ試してみてください。

参考のため、完成済みサンプル (構成値を除く) が [.zip ファイルとして提供されています](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip)。 GitHub から複製することもできます。

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO5-->


