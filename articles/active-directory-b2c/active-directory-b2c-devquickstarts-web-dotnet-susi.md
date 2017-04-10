---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C を使用して、サインアップおよびサインイン、プロファイルの編集、パスワード リセットを含む Web アプリケーションを構築する方法。"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 4d7476156b51ca82b1f119becb1576a97d2bd457
ms.lasthandoff: 03/23/2017


---
# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C: ASP.NET Web アプリでのサインアップとサインイン

Azure AD B2C を使用すると、Web アプリに強力な ID 管理機能を追加できます。 この記事では、ユーザーのサインアップおよびサインイン、プロファイルの編集、パスワード リセットを含む ASP.NET Web アプリの作成方法について説明します。

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの作成

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

## <a name="create-an-application"></a>アプリケーションの作成

次に、B2C ディレクトリに Web アプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* アプリケーションに **Web アプリまたは Web API** を含めます。
* **[リダイレクト URI]** として「`https://localhost:44316/`」と入力します。 これはこのサンプル コードで使用する既定の URL です。
* アプリケーションに割り当てられた **アプリケーション ID** をメモしておきます。  この情報は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 このコード サンプルには、**サインアップおよびサインイン**、**プロファイルの編集**、**パスワード リセット**の 3 つの ID エクスペリエンスが含まれています。  [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、種類ごとに 1 つのポリシーを作成する必要があります。 ポリシーを作成するときは、次の操作を必ず実行してください。

* ID プロバイダーのブレードで、**[User ID sign-up (ユーザー ID サインアップ)]** または **[Email sign-up (電子メール サインアップ)]** を選択します。
* サインアップおよびサインイン ポリシーで、**[表示名]** と他のサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 これらのポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

ポリシーを作成したら、いつでもアプリをビルドできます。

## <a name="download-the-code"></a>コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi) で管理されています。 次のコマンドを実行するとサンプルを複製できます。

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。 ソリューション ファイルには、`TaskWebApp` と `TaskService` の 2 つのプロジェクトが含まれています。 `TaskWebApp` は、ユーザーが操作する MVC Web アプリケーションです。 `TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。 この記事では、`TaskWebApp` アプリケーションだけを取り上げます。 Azure AD B2C を使用して `TaskService` を構築する方法については、[.NET Web API のチュートリアル](active-directory-b2c-devquickstarts-api-dotnet.md)をご覧ください。

### <a name="update-the-azure-ad-b2c-configuration"></a>Azure AD B2C の構成を更新する

サンプルは、デモ テナントのポリシーとクライアント ID を使用するように構成されています。 独自のテナントを使用する場合は、`TaskWebApp` プロジェクトの `web.config` を開き、次のように値を置き換える必要があります。

* `ida:Tenant` を実際のテナント名に置き換えます。
* `ida:ClientId` を実際の Web アプリのアプリケーション ID に置き換えます。
* `ida:ClientSecret` を実際の Web アプリのシークレット キーに置き換えます。
* `ida:SignUpSignInPolicyId` を実際の "サインアップまたはサインイン" ポリシーの名前に置き換えます。
* `ida:EditProfilePolicyId` を実際の "プロファイルの編集" ポリシーの名前に置き換えます。
* `ida:ResetPasswordPolicyId` を実際の "パスワードのリセット" ポリシーの名前に置き換えます。

## <a name="add-authentication-support"></a>認証サポートの追加

Azure AD B2C を使用するようにアプリを構成できるようになりました。 アプリは、OpenID Connect 認証要求を送信することで Azure AD B2C と通信します。 この要求では、ポリシーを指定することによって、アプリが実行するユーザー エクスペリエンスを示します。 Microsoft の OWIN ライブラリを使用して、要求の送信、ポリシーの実行、ユーザー セッションの管理などを行うことができます。

### <a name="install-owin"></a>OWIN をインストールする

まず、Visual Studio のパッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="add-an-owin-startup-class"></a>OWIN Startup クラスを追加する

OWIN Startup クラス (`Startup.cs`) を API に追加します。  プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順に選択して、"OWIN" を検索します。 アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。

サンプルでは、クラスの宣言を `public partial class Startup` に変更し、クラスの残りの部分を `App_Start\Startup.Auth.cs` に実装しました。 `Configuration` メソッド内に、`ConfigureAuth` (`Startup.Auth.cs` で定義) の呼び出しを追加しました。 変更後の `Startup.cs` は、次のようになります。

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-the-authentication-middleware"></a>認証ミドルウェアを構成する

ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(...)` メソッドを実装します。 `OpenIdConnectAuthenticationOptions` に指定するパラメーターは、アプリが Azure AD B2C と通信するための調整役として機能します。 一部のパラメーターを指定していない場合、既定値が使用されます。 たとえば、サンプルでは `ResponseType` を指定していないので、Azure AD B2C への各送信要求で既定値の `code id_token` が使用されます。

Cookie 認証を設定する必要もあります。 特に、OpenID Connect ミドルウェアは、ユーザー セッションを維持するために Cookie を使用します。

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

上記の `OpenIdConnectAuthenticationOptions` では、OpenID Connect ミドルウェアが受け取る特定の通知の一連のコールバック関数を定義しています。 これらの動作は `OpenIdConnectAuthenticationNotifications` オブジェクトを使用して定義され、`Notifications` 変数に格納されます。 サンプルでは、イベントに応じて 3 つの異なるコールバックを定義しています。

#### <a name="using-different-policies"></a>さまざまなポリシーを使用する

Azure AD B2C に対して要求が行われるたびに、`RedirectToIdentityProvider` 通知がトリガーされます。 `OnRedirectToIdentityProvider` コールバック関数では、別のポリシーを使用する場合に送信呼び出しで確認しています。 パスワード リセットを実行したり、プロファイルを編集したりするには、既定の "サインアップまたはサインイン" ポリシーではなく、パスワード リセット ポリシーなどの対応するポリシーを使用する必要があります。

サンプルでは、ユーザーがパスワードのリセットやプロファイルの編集を行うときに、使用するポリシーを OWIN コンテキストに追加しています。 これを行うには、次のコードを実行します。

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

`OnRedirectToIdentityProvider` コールバック関数を実装するには、次のコードを実行します。

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

#### <a name="handling-authorization-codes"></a>承認コードを処理する

承認コードを受信すると、`AuthorizationCodeReceived` 通知がトリガーされます。 OpenID Connect ミドルウェアでは、コードとアクセス トークンの交換をサポートしていません。 コールバック関数内でコードをトークンと手動で交換できます。 詳細については、この方法を説明する[ドキュメント](active-directory-b2c-devquickstarts-web-api-dotnet.md)をご覧ください。

#### <a name="handling-errors"></a>エラーを処理する

認証に失敗すると、`AuthenticationFailed` 通知がトリガーされます。 コールバック メソッドで、必要に応じてエラーを処理できます。 ただし、エラー コード `AADB2C90118` のチェックを追加する必要があります。 "サインアップまたはサインイン" ポリシーの実行中に、ユーザーが **[パスワードを忘れた場合]** リンクをクリックする可能性があります。 この場合、Azure AD B2C は、アプリがパスワード リセット ポリシーを使用して要求を行う必要があることを示す前述のエラー コードをアプリに送信します。

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Azure AD への認証要求の送信

ここまでの手順で、アプリは OpenID Connect 認証プロトコルを使用して Azure AD B2C と通信するよう適切に構成されています。 OWIN により、認証メッセージの作成、Azure AD B2C から受け取ったトークンの検証、ユーザー セッションの維持のためのすべての処理が行われました。 残りは、各ユーザーのフローを開始する処理です。

ユーザーが Web アプリで **[サインアップ]/[サインイン]**、**[プロファイルの編集]**、または **[パスワードのリセット]** を選択すると、`Controllers\AccountController.cs` で関連付けられているアクションが呼び出されます。

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

アプリからのユーザーのサインアウトにも OWIN を使用できます。 `Controllers\AccountController.cs` の内容を次に示します。

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

ポリシーを明示的に呼び出すだけでなく、ユーザーがサインインしていない場合にポリシーを実行する `[Authorize]` タグを、コントローラー内で使用することもできます。 `Controllers\HomeController.cs` を開いて、`[Authorize]` タグを要求コントローラーに追加します。  `[Authorize]` タグに達した時点で、OWIN は最後に構成されたポリシーを選択します。

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

## <a name="display-user-information"></a>ユーザー情報を表示する

OpenID Connect を使用してユーザーを認証すると、Azure AD B2C は**要求**が含まれた ID トークンをアプリに返します。 これらは、ユーザーに関するアサーションです。 要求を使用して、アプリを個人向けにカスタマイズすることができます。

`Controllers\HomeController.cs` ファイルを開きます。 `ClaimsPrincipal.Current` セキュリティ プリンシパル オブジェクトを介して、コントローラー内のユーザー要求にアクセスできます。

```CSharp
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

最後に、アプリを構築して実行できます。 電子メール アドレスまたはユーザー名を使用して、アプリにサインアップします。 サインアウトし、同じユーザーとしてもう一度サインインします。 プロファイルを編集するか、パスワードをリセットします。 サインアウトし、別のユーザーとしてサインアップします。 **[Claims (要求)]** タブに表示される情報が、ポリシーで構成した情報に対応していることを確認してください。

## <a name="add-social-idps"></a>ソーシャル IDP を追加する

現時点では、このアプリは **ローカル アカウント**を使用したユーザーのサインアップとサインインのみをサポートしています。 これらは、ユーザー名とパスワードを使用する B2C ディレクトリに格納されているアカウントです。 Azure AD B2C を使用すると、コードを変更せずに、その他の **ID プロバイダー** (IDP) のサポートを追加することができます。

ソーシャル IDP をアプリに追加するには、まず、以下の記事に記載されている詳細な手順に従います。 サポートする IDP ごとに、そのシステムでアプリケーションを登録してクライアント ID を取得する必要があります。

* [Facebook を IDP として設定する](active-directory-b2c-setup-fb-app.md)
* [Google を IDP として設定する](active-directory-b2c-setup-goog-app.md)
* [Amazon を IDP として設定する](active-directory-b2c-setup-amzn-app.md)
* [LinkedIn を IDP として設定する](active-directory-b2c-setup-li-app.md)

ID プロバイダーを B2C ディレクトリに追加した後、3 つのポリシーをそれぞれ編集し、 [ポリシーに関するリファレンス記事](active-directory-b2c-reference-policies.md)で説明されているように、新しい IDP を加える必要があります。 ポリシーを保存した後、再度アプリを実行します。  各 ID エクスペリエンスに、サインインおよびサインアップの選択肢として新しい IDP が追加されていることがわかります。

いろいろなポリシーを試して、サンプル アプリへの影響を確認できます。 ID を追加または削除したり、アプリケーションの要求を操作したり、サインアップ属性を変更してみます。 ポリシー、認証要求、および OWIN すべてが互いにどのように結び付いているかを理解できるまで、いろいろ試してみてください。

