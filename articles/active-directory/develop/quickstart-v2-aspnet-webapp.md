---
title: 'クイックスタート: ASP.NET Web アプリへの "Microsoft でサインイン" の追加 | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して、ASP.NET Web アプリに Microsoft サインインを実装する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 420415cc3bc2228a104ccf054098543bf04847b0
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225767"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>クイック スタート:ASP.NET Web アプリに Microsoft ID プラットフォーム サインインを追加する

このクイックスタートでは、ASP.NET Web アプリで Azure Active Directory (Azure AD) 組織のユーザーをサインインする方法を示すコード サンプルをダウンロードして実行します。 

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。
> [!div renderon="docs"]
> ## <a name="prerequisites"></a>前提条件
>
> * アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET Framework 4.7.2 以降](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure portal のアプリの登録<span class="docon docon-navigate-external x-hidden-focus"></span></a>クイックスタート エクスペリエンスに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **名前** を入力します (例: `ASPNET-Quickstart`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[リダイレクト URI]** に `https://localhost:44368/` を追加し、 **[登録]** を選択します。
> 1. **[管理]** で、 **[認証]** を選択します。
> 1. **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** セクションで、 **[ID トークン]** を選択します。
> 1. **[保存]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのサンプル コードには、`https://localhost:44368/` という **リダイレクト URI** が必要です。

> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこの属性で構成されています

#### <a name="step-2-download-your-project"></a>手順 2:プロジェクトのダウンロード

> [!div renderon="docs"]
> [Visual Studio 2019 ソリューションのダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Visual Studio 2019 を使用してプロジェクトを実行します。
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成しました。

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>手順 3:Visual Studio プロジェクトを実行する

1. ルート フォルダーに近いローカル フォルダー (例: **C:\Azure-Samples**) に zip ファイルを展開します。
1. Visual Studio でソリューションを開きます (AppModelv2-WebApp-OpenIDConnect-DotNet.sln)
1. Visual Studio のバージョンによっては、プロジェクト `AppModelv2-WebApp-OpenIDConnect-DotNet` を右クリックして **[NuGet パッケージの復元]** を選択することが必要になる場合があります。
1. パッケージ マネージャー コンソール ([表示]、[その他のウィンドウ]、[パッケージ マネージャー コンソール] の順に選択) を開き、以下を実行します。`Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`

> [!div renderon="docs"]
> 5. **[Web.config]** を編集し、`ClientId` パラメーターと `Tenant` パラメーターを次のように置き換えます。
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    ```
>    各値の説明:
> - `Enter_the_Application_Id_here` - 登録したアプリケーションのアプリケーション ID。
> - `Enter_the_Tenant_Info_Here` - 以下のいずれかのオプション。
>   - アプリケーションでサポートされるのが **[所属する組織のみ]** である場合、この値を **テナント ID** または **テナント名** (例: contoso.onmicrosoft.com) に置き換えます
>   - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
>   - アプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされる場合は、この値を `common` に置き換えます。
>
> > [!TIP]
> > - *[アプリケーション ID]* 、 *[ディレクトリ (テナント) ID]* 、 *[サポートされているアカウントの種類]* の値を見つけるには、 **[概要]** ページに移動します。
> > - **Web.config** 内の `redirectUri` の値と、Azure AD でアプリ登録用に定義された **リダイレクト URI** が確実に対応するようにします (対応していない場合、アプリ登録の **[認証]** メニューに移動し、**リダイレクト URI** を一致するように更新します)。

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>詳細情報

このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要、コードの機能や主な引数について理解するために、また、既存の ASP.NET アプリケーションにサインインを追加する場合にも役立ちます。

### <a name="how-the-sample-works"></a>このサンプルのしくみ
![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN ミドルウェア NuGet パッケージ

ASP.NET で OpenID Connect を使用して Cookie ベースの認証を行うために、OWIN Middleware パッケージを使用して認証パイプラインをセットアップできます。 これらのパッケージは、Visual Studio の **パッケージ マネージャー コンソール** で次のコマンドを実行してインストールできます。

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN Startup クラス

OWIN ミドルウェアでは、ホスト プロセスの初期化時に実行される "*スタートアップ クラス*" が使用されます。 このクイック スタートでは、ルート フォルダーにある *startup.cs* ファイルを使用します。 次のコードは、このクイック スタートで使用されるパラメーターを示しています。

```csharp
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
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  | 説明 |
> |---------|---------|
> | `ClientId`     | Azure portal に登録されているアプリケーションのアプリケーション ID |
> | `Authority`    | ユーザーが認証するための STS エンドポイント。 パブリック クラウドでは、通常は `https://login.microsoftonline.com/{tenant}/v2.0`。{tenant} はテナントの名前、テナント ID、または共通エンドポイントへの参照を表す *common* (マルチテナント アプリケーションで使用) です |
> | `RedirectUri`  | Microsoft ID プラットフォームに対する認証後にユーザーが送られる URL |
> | `PostLogoutRedirectUri`     | サインオフ後にユーザーが送られる URL |
> | `Scope`     | 要求されているスコープのスペース区切りリスト |
> | `ResponseType`     | 認証からの応答に ID トークンが含まれていることを要求します |
> | `TokenValidationParameters`     | トークン検証のためのパラメーター リスト。 この場合、`ValidateIssuer` は `false` に設定され、任意の個人、あるいは職場または学校のアカウント タイプからのサインインを受け付け可能であることを示します |
> | `Notifications`     | さまざまな *OpenIdConnect* メッセージで実行可能なデリゲートの一覧 |


> [!NOTE]
> `ValidateIssuer = false` の設定は、このクイック スタートを単純にするためのものです。 実際のアプリケーションでは、発行者を検証する必要があります。
> その方法については、サンプルを参照してください。

### <a name="initiate-an-authentication-challenge"></a>認証チャレンジを開始する

コントローラーで認証チャレンジを要求することによって、ユーザーにサインインを強制することができます。

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> 上記の方法を使用して認証チャレンジを要求することはオプションであり、通常は、認証されたユーザーと未認証ユーザーの両方からビューにアクセスできるようにする場合に使用します。 代わりに、次のセクションで説明する方法を使ってコントローラーを保護することもできます。

### <a name="protect-a-controller-or-a-controllers-method"></a>コントローラーまたはコントローラーのメソッドを保護する

`[Authorize]` 属性を使用して、コントローラーまたはコントローラー アクションを保護できます。 この属性は、認証されたユーザーにしかコントローラー内のアクションへのアクセスを許可しない、つまり、`[Authorize]` 属性によって装飾されたアクションまたはコントローラーのいずれかに *未認証* ユーザーがアクセスしようとしたときに認証チャレンジを自動的に発生させることによって、コントローラーまたはアクションへのアクセスを制限します。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドは、ASP.NET チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

> [!div class="nextstepaction"]
> [ASP.NET Web アプリにサインインを追加する](tutorial-v2-asp-webapp.md)
