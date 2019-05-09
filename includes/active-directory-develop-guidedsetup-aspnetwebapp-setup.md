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
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: dcfc341b89a3cfebcb5538f88481fd2fbb2936a7
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505819"
---
## <a name="set-up-your-project"></a>プロジェクトの設定

このセクションでは、ASP.NET プロジェクトで OpenID 接続を使用して、OWIN ミドルウェアから認証パイプラインをインストールおよび構成する手順について説明します。

> 代わりにこのサンプルの Visual Studio プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)したら[構成手順](#register-your-application)に進み、実行前にコード サンプルを構成します。

### <a name="create-your-aspnet-project"></a>ASP.NET プロジェクトを作成する

1. Visual Studio での操作: [`File`] > [`New`] > `Project`
2. *Visual C#\Web* で、[`ASP.NET Web Application (.NET Framework)`] \(ASP.NET Web アプリケーション (.NET Framework)) を選択します。
3. アプリケーションに名前を付けて、*[OK]* をクリックします。
4. [`Empty`] を選択し、`MVC` 参照を追加するチェック ボックスを選択します。

## <a name="add-authentication-components"></a>認証コンポーネントの追加

1. Visual Studio での操作: [`Tools`] > [`Nuget Package Manager`] > `Package Manager Console`
2. パッケージ マネージャー コンソールのウィンドウで以下を入力し、*OWIN ミドルウェア NuGet パッケージ*を追加します。

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>これらのライブラリについて
> 上記のライブラリでは、Cookie ベースの認証によって、OpenID 接続を使用したシングル サインオン (SSO) が有効になります。 認証が完了し、ユーザーを表すトークンがアプリケーションに送信されたら、OWIN ミドルウェアはセッションの Cookie を作成します。 そしてブラウザーはこの Cookie を後の要求に使用するため、ユーザーはパスワードを再入力する必要がなく、追加の認証は必要ありません。
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>認証パイプラインを構成する

次の手順を使用して、OWIN ミドルウェアのスタートアップ クラスを作成し、OpenID 接続の認証を構成できます。 このクラスは、IIS プロセスの開始時に自動的に実行されます。

> [!TIP]
> プロジェクトのルート フォルダー内に `Startup.cs` ファイルがない場合:
> 1. プロジェクトのルート フォルダーを右クリックし、次を選択します: [`Add`] > [`New Item...`] > `OWIN Startup class`<br/>
> 2. これに次のように名前を付けます:  `Startup.cs`
>
>> 選択したクラスが標準的な C# クラスではなく、OWIN Startup クラスであることを確認します。 これを確認するには、名前空間の上に `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` があるかチェックします。

1. *OWIN* と *Microsoft.IdentityModel* への参照を `Startup.cs` に追加します。

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
> `ValidateIssuer = false` の設定は、このクイック スタートを単純にするためのものです。 実際のアプリケーションでは、発行者を検証する必要があります。その方法については、サンプルを参照してください。

<!--start-collapse-->
> ### <a name="more-information"></a>詳細情報
> *OpenIDConnectAuthenticationOptions* で指定したパラメーターは、アプリケーションが Azure AD と通信するための調整役として機能します。 OpenID 接続のミドルウェアはバックグラウンドで Cookie を使用するため、上記のコードで示したように、Cookie 認証も設定する必要があります。 *ValidateIssuer* 値によって、OpenIdConnect はアクセスを 1 つの特定の組織に制限しないように設定されます。
<!--end-collapse-->
