---
title: 'クイックスタート: ASP.NET Core Web アプリに "Microsoft アカウントでサインイン" を追加する | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、OpenID Connect を使用して、ASP.NET Core Web アプリに Microsoft サインインを実装する方法について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: b2e42dcaeb72a053f20afa47439d1825445b0ad8
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225835"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>クイック スタート:ASP.NET Core Web アプリに Microsoft サインインを追加する

このクイックスタートでは、ASP.NET Core Web アプリで Azure Active Directory (Azure AD) 組織のユーザーをサインインする方法を示すコード サンプルをダウンロードして実行します。  

図については、「[このサンプルのしくみ](#how-the-sample-works)」を参照してください。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>前提条件
>
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) または [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 以降](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>オプション 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
>
> 1. <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure portal のアプリの登録<span class="docon docon-navigate-external x-hidden-focus"></span></a>クイックスタート エクスペリエンスに移動します。
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
> 1. アプリケーションの **名前** を入力します (例: `AspNetCore-Quickstart`)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **リダイレクト URI** として「`https://localhost:44321/`」と入力します。
> 1. **[登録]** を選択します。
> 1. **[管理]** で、 **[認証]** を選択します。
> 1. **[リダイレクト URI]** で **[URI の追加]** を選択し、「`https://localhost:44321/signin-oidc`」と入力します。
> 1. **[Front-channel logout URL]\(フロントチャネル ログアウト URL\)** に「`https://localhost:44321/signout-oidc`」を入力します。
> 1. **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** で、 **[ID トークン]** を選択します。
> 1. **[保存]** を選択します。

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>手順 1:Azure portal でのアプリケーションの構成
> このクイックスタートのサンプル コードには、**リダイレクト URI** (`https://localhost:44321/` および `https://localhost:44321/signin-oidc`) と **フロントチャネルのログアウト URL** (`https://localhost:44321/signout-oidc`) が必要です。 要求の ID トークンは、承認エンドポイントによって発行されます。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-aspnet-webapp/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-your-aspnet-core-project"></a>手順 2:ASP.NET Core プロジェクトのダウンロード

> [!div renderon="docs"]
> [ASP.NET Core ソリューションをダウンロード](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)します。

> [!div renderon="portal" class="sxs-lookup"]
> プロジェクトを実行します。

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>手順 3:アプリが構成され、実行準備ができる
> アプリのプロパティの値を使用してプロジェクトを構成したら、実行する準備は完了です。
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>手順 3:ASP.NET Core プロジェクトの構成
> 1. .zip アーカイブを、ドライブのルート付近にあるローカル フォルダーに抽出します。 たとえば、*C:\Azure-Samples* に抽出します。
> 1. Visual Studio 2019 でソリューションを開きます。
> 1. *appsettings.json* ファイルを開き、次のように変更します。
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - `Enter_the_Application_Id_here` を、Azure portal で登録したアプリケーションの **アプリケーション (クライアント) ID** に置き換えます。 "**アプリケーション (クライアント) ID**" は、アプリの **[概要]** ページで確認できます。
>    - `common` を、次のいずれかに置き換えます。
>       - アプリケーションで **[この組織のディレクトリ内のアカウントのみ]** がサポートされている場合は、この値を **ディレクトリ (テナント) ID** (GUID) または **テナント名** (例: `contoso.onmicrosoft.com`) に置き換えます。 **ディレクトリ (テナント) ID** は、アプリの **[概要]** ページで確認できます。
>       - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
>       - アプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされている場合は、この値を `common` のままにします。
>
> このクイックスタートでは、*appsettings.json* ファイル内のその他の値は変更しないでください。
>
> #### <a name="step-4-build-and-run-the-application"></a>手順 4: アプリケーションをビルドして実行する
>
> アプリをビルドして実行するには、Visual Studio で **[デバッグ]** メニュー、 **[デバッグの開始]** の順に選択するか、`F5` キーを押します。
>
> 資格情報の入力を求められ、アプリに必要なアクセス許可に同意するよう求められます。 同意プロンプトで **[同意する]** を選択します。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="アプリが > ユーザーから要求しているアクセス許可を示す同意ダイアログ":::
>
> 要求されたアクセス許可に同意すると、Azure Active Directory の資格情報を使用して正常にログインしたことがアプリに表示されます。
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="実行中の Web アプリとサインインしたユーザーが表示されている Web ブラウザー":::

## <a name="more-information"></a>詳細情報

このセクションでは、ユーザーをサインインさせるために必要なコードの概要を示します。 この概要、コードの機能や主な引数について理解するために、また、既存の ASP.NET Core アプリケーションにサインインを追加する場合にも役立ちます。

### <a name="how-the-sample-works"></a>このサンプルのしくみ
![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>スタートアップ クラス

*Microsoft.AspNetCore.Authentication* ミドルウェアは、ホスティング プロセスの初期化時に実行される `Startup` クラスを使用します。

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

`AddAuthentication()` メソッドは、ブラウザーのシナリオで使用される Cookie ベースの認証を追加し、OpenID Connect へのチャレンジを設定するようにサービスを構成します。

`.AddMicrosoftIdentityWebApp` を含む行によって、Microsoft ID プラットフォーム認証がアプリケーションに追加されます。 その後、*appsettings.json* 構成ファイルの `AzureAD` セクションの情報に基づいて、Microsoft ID プラットフォームを使用してサインインするように構成されます。

| *appsettings.json* のキー | 説明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure portal に登録されているアプリケーションの **アプリケーション (クライアント) ID**。                                                                                       |
| `Instance`             | ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、この値は、Azure パブリック クラウドを示す `https://login.microsoftonline.com/` です。 |
| `TenantId`             | テナントの名前またはテナント ID (GUID)。職場または学校アカウントあるいは Microsoft 個人アカウントを使用してユーザーをサインインする場合は *common*。                             |

`Configure()` メソッドには、`app.UseAuthentication()` と `app.UseAuthorization()` という 2 つの重要なメソッドが含まれており、それらの名前付き機能を有効にします。 また、`Configure()` メソッドで、少なくとも 1 つの `endpoints.MapControllerRoute()` 呼び出し (または `endpoints.MapControllers()` 呼び出し) に、Microsoft Identity Web のルートを登録する必要があります。

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>コントローラーまたはコントローラーのメソッドを保護する

`[Authorize]` 属性を使用して、コントローラーまたはコントローラーのメソッドを保護できます。 この属性は、認証済みのユーザーのみを許可することによって、コントローラーまたはメソッドへのアクセスを制限します。つまり、ユーザーが認証されていない場合は、コントローラーにアクセスするための認証チャレンジを開始できます。

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

この ASP.NET Core チュートリアルを含む GitHub リポジトリには、次の方法を示す手順とその他のコード サンプルが含まれています。

- 新しい ASP.NET Core Web アプリに認証を追加する
- Microsoft Graph、他の Microsoft API、またはユーザー独自の Web API を呼び出す
- 承認を追加する
- 国内クラウドで、またはソーシャル ID を使用してユーザーをサインインする

> [!div class="nextstepaction"]
> [GitHub の ASP.NET Core Web アプリのチュートリアル](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
