---
title: クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する | Azure
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、承認に Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する方法を示すコード サンプルをダウンロードして変更します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 7d38ee1782987afce703fbd8b4203186bbcbb505
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754483"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>クイック スタート:Microsoft ID プラットフォームを使用して ASP.NET Core Web API を保護する

このクイックスタートでは、ASP.NET Core Web API コード サンプルをダウンロードし、リソースへのアクセスを承認されたアカウントだけに制限するコードを確認します。 このサンプルでは、個人用 Microsoft アカウントと Azure Active Directory (Azure AD) 組織のアカウントの承認がサポートされています。

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>前提条件
>
> - アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> - [Azure Active Directory テナント](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1 以降](https://dotnet.microsoft.com/)
> - [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) または [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>手順 1:アプリケーションを登録する
>
> まず、次の手順に従って、Azure AD テナントに Web API を登録し、スコープを追加します。
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> にサインインします。
> 1. 複数のテナントにアクセスできる場合は、トップ メニューの **[ディレクトリとサブスクリプション]** フィルター:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::を使用して、アプリケーションを登録するテナントを選択します。
> 1. **Azure Active Directory** を検索して選択します。
> 1. **[管理]** で **[アプリの登録]**  >  **[新規登録]** の順に選択します。
> 1. アプリケーションの **名前** を入力します (`AspNetCoreWebApi-Quickstart` など)。 この名前は、アプリのユーザーに表示される場合があります。また、後で変更することができます。
> 1. **[登録]** を選択します。
> 1. **[管理]** で、 **[API の公開]**  >  **[スコープの追加]** の順に選択します。 **[保存して続行]** を選択して、既定の **アプリケーション ID URI** を受け入れ、次の詳細を入力します。
>    - **スコープ名**: `access_as_user`
>    - **同意できるのはだれですか?** : **管理者とユーザー**
>    - **管理者の同意の表示名**: `Access AspNetCoreWebApi-Quickstart`
>    - **管理者の同意の説明**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **ユーザーの同意の表示名**: `Access AspNetCoreWebApi-Quickstart`
>    - **ユーザーの同意の説明**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **[状態]** :**有効**
> 1. **[スコープの追加]** を選択してスコープの追加を完了します。

## <a name="step-2-download-the-aspnet-core-project"></a>手順 2:ASP.NET Core プロジェクトをダウンロードする

> [!div renderon="docs"]
> GitHub から [ASP.NET Core ソリューションをダウンロード](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip)します。

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>手順 3:ASP.NET Core プロジェクトを構成する
>
> この手順では、前に作成したアプリの登録を操作するようにサンプル コードを構成します。
>
> 1. .zip アーカイブを、ドライブのルート付近にあるフォルダーに抽出します。 たとえば、*C:\Azure-Samples* にします。
> 1. コード エディターで *webapi* フォルダーにあるソリューションを開きます。
> 1. *appsettings.json* ファイルを開き、以下を変更します。
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - `Enter_the_Application_Id_here` を、Azure portal で登録したアプリケーションの **アプリケーション (クライアント) ID** に置き換えます。 "**アプリケーション (クライアント) ID**" は、アプリの **[概要]** ページで確認できます。
>    - `Enter_the_Tenant_Info_Here` を、次のいずれかに置き換えます。
>       - アプリケーションで **[この組織のディレクトリ内のアカウントのみ]** がサポートされている場合は、この値を **ディレクトリ (テナント) ID** (GUID) または **テナント名** (例: `contoso.onmicrosoft.com`) に置き換えます。 **ディレクトリ (テナント) ID** は、アプリの **[概要]** ページで確認できます。
>       - アプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を `organizations` に置き換えます。
>       - アプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされている場合は、この値を `common` のままにします。
>
> このクイックスタートでは、*appsettings.json* ファイル内のその他の値は変更しないでください。

## <a name="how-the-sample-works"></a>このサンプルのしくみ

Web API はクライアント アプリケーションからトークンを受け取り、Web API のコードでそのトークンを検証します。 このシナリオの詳細については、「[シナリオ: 保護された Web API](scenario-protected-web-api-overview.md)」を参照してください。

### <a name="startup-class"></a>スタートアップ クラス

*Microsoft.AspNetCore.Authentication* ミドルウェアは、ホスティング プロセスの初期化時に実行される `Startup` クラスを使用します。 その `ConfigureServices` メソッドでは、*Microsoft.Identity.Web* によって提供される `AddMicrosoftIdentityWebApi` 拡張メソッドが呼び出されます。

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

`AddAuthentication()` メソッドは、JwtBearer ベースの認証を追加するようサービスを構成します。

`.AddMicrosoftIdentityWebApi` を含む行によって、Microsoft ID プラットフォームの承認が Web API に追加されます。 その後、*appsettings.json* 構成ファイルの `AzureAD` セクションの情報に基づいて、Microsoft ID プラットフォームによって発行されたアクセス トークンを検証するよう構成されます。

| *appsettings.json* のキー | 説明                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | Azure portal に登録されているアプリケーションの **アプリケーション (クライアント) ID**。                                                                                       |
| `Instance`             | ユーザーが認証するためのセキュリティ トークン サービス (STS) エンドポイント。 通常、この値は、Azure パブリック クラウドを示す `https://login.microsoftonline.com/` です。 |
| `TenantId`             | テナントの名前またはテナント ID (GUID)。職場または学校アカウントあるいは Microsoft 個人アカウントを使用してユーザーのサインインを行う場合は *common*。                             |

`Configure()` メソッドには、`app.UseAuthentication()` と `app.UseAuthorization()` という 2 つの重要なメソッドが含まれており、それらの名前付き機能を有効にします。

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>コントローラー、コントローラーのメソッド、Razor ページを保護する

`[Authorize]` 属性を使用して、コントローラーまたはコントローラーのメソッドを保護できます。 この属性は、認証済みのユーザーのみを許可することによって、コントローラーまたはメソッドへのアクセスを制限します。つまり、ユーザーが認証されていない場合は、コントローラーにアクセスするための認証チャレンジを開始できます。

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>コントローラーでスコープを検証する

次に、API のコードでは、`HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);` を使用して、必要なスコープがトークンに含まれていることを確認します。

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次の手順

この ASP.NET Core Web API コード サンプルを含む GitHub リポジトリには、次の操作方法を示す手順とその他のコード サンプルが含まれています。

- 新しい ASP.NET Core Web API に認証を追加する
- デスクトップ アプリケーションから Web API を呼び出す
- Microsoft Graph やその他の Microsoft API のようなダウンストリーム API を呼び出す

> [!div class="nextstepaction"]
> [GitHub の ASP.NET Core Web API のチュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
