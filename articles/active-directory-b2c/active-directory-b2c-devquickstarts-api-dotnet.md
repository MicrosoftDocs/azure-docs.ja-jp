---
title: Azure AD B2C | Microsoft Docs
description: "Azure Active Directory B2C を使用し、認証に OAuth 2.0 アクセス トークンを使用してセキュリティ保護された .NET Web API を構築する方法。"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: 3dd207805c1a8f53c6cc74da08cc9378609581c5
ms.lasthandoff: 03/23/2017


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: .NET Web API を構築する

Azure Active Directory (Azure AD) B2C では、OAuth 2.0 アクセス トークンを使用して Web API をセキュリティ保護できます。 これらのトークンにより、クライアント アプリは API に対して認証を行うことができます。 この記事では、.NET MVC の "To-Do List" API を作成し、クライアント アプリケーションのユーザーが CRUD タスクを実行できるようにする方法について説明します。 この Web API は Azure AD B2C を使用してセキュリティで保護されるため、認証済みのユーザーのみが To-Do List を管理できます。

## <a name="create-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの作成

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、先に進む前に [B2C ディレクトリを作成](active-directory-b2c-get-started.md) してください。

> [!NOTE]
> クライアント アプリケーションと Web API は、同じ Azure AD B2C ディレクトリを使用する必要があります。
>

## <a name="create-a-web-api"></a>Web API の作成

次に、B2C ディレクトリに Web API アプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 アプリを作成するには、 [こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* アプリケーションに **Web アプリ**または **Web API** を含めます。
* Web アプリの**リダイレクト URI** として `https://localhost:44332/` を使用します。 これは、このコード サンプルでの Web アプリ クライアントの既定の場所です。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報は後で必要になります。
* アプリ ID を **[アプリケーション ID/URI]** に入力します。
* **[Published scopes (公開スコープ)]** メニューからアクセス許可を追加します。

  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>ポリシーの作成

Azure AD B2C では、すべてのユーザー エクスペリエンスが [ポリシー](active-directory-b2c-reference-policies.md)によって定義されます。 Azure AD B2C と通信を行うためにはポリシーを作成する必要があります。 [ポリシーについてのリファレンス記事](active-directory-b2c-reference-policies.md)で説明されている、サインアップとサインインを組み合わせたポリシーの使用をお勧めします。 ポリシーを作成するときは、以下の操作を必ず実行してください。

* ポリシーで **[表示名]** と他のサインアップ属性を選択します。
* すべてのポリシーで、アプリケーション要求として **[表示名]** 要求と **[オブジェクト ID]** 要求を選択します。 その他のクレームも選択できます。
* ポリシーの作成後、各ポリシーの **名前** をコピーしておきます。 このポリシー名は後で必要になります。

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

ポリシーの作成が正常に完了したら、いつでもアプリをビルドできます。

## <a name="download-the-code"></a>コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi) で管理されています。 次のコマンドを実行するとサンプルを複製できます。

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。 ソリューション ファイルには、`TaskWebApp` と `TaskService` の 2 つのプロジェクトが含まれています。 `TaskWebApp` は、ユーザーが操作する MVC Web アプリケーションです。 `TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。 この記事では、`TaskService` アプリケーションについてのみ取り上げます。 Azure AD B2C を使用して `TaskWebApp` を構築する方法については、[.NET Web アプリのチュートリアル](active-directory-b2c-devquickstarts-web-dotnet-susi.md)を参照してください。

### <a name="update-the-azure-ad-b2c-configuration"></a>Azure AD B2C の構成を更新する

サンプルは、デモ テナントのクライアント ID とポリシーを使うように構成されています。 独自のテナントを使う場合は、次の作業が必要となります。

1. `TaskService` プロジェクトの `web.config` を開き、次のように値を置き換えます。
    * `ida:Tenant`。実際のテナント名を指定します。
    * `ida:ClientId`。実際の Web API のアプリケーション ID を指定します。
    * `ida:SignUpSignInPolicyId`。実際の "サインアップまたはサインイン" ポリシー名を指定します。

2. `TaskWebApp` プロジェクトの `web.config` を開き、次のように値を置き換えます。
    * `ida:Tenant`。実際のテナント名を指定します。
    * `ida:ClientId`。実際の Web アプリのアプリケーション ID を指定します。
    * `ida:ClientSecret`。実際の Web アプリのシークレット キーを指定します。
    * `ida:SignUpSignInPolicyId`。実際の "サインアップまたはサインイン" ポリシー名を指定します。
    * `ida:EditProfilePolicyId`。実際の "プロファイルの編集" ポリシーの名前を指定します。
    * `ida:ResetPasswordPolicyId`。実際の "パスワードのリセット" ポリシーの名前を指定します。


## <a name="secure-the-api"></a>API のセキュリティ保護

作成した API を呼び出すクライアントがあれば、OAuth 2.0 のベアラー トークンを使って API (`TaskService` など) のセキュリティを確保することができます。 この場合、API に対する各要求は、ベアラー トークンを持っている場合に限り有効となります。 API では、Microsoft の Open Web Interface for .NET (OWIN) ライブラリを使用してベアラー トークンを受け入れて検証できます。

### <a name="install-owin"></a>OWIN をインストールする

まず、Visual Studio のパッケージ マネージャー コンソールを使って OWIN OAuth 認証パイプラインをインストールします。

```Console
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

これにより、ベアラー トークンを受け入れて検証する OWIN ミドルウェアがインストールされます。

### <a name="add-an-owin-startup-class"></a>OWIN Startup クラスを追加する

OWIN スタートアップ クラス (`Startup.cs`) を API に追加します。  プロジェクトを右クリックし、**[追加]**、**[新しい項目]** の順に選択して、"OWIN" を検索します。 アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。

サンプルでは、クラスの宣言を `public partial class Startup` に変更し、その残りの部分を `App_Start\Startup.Auth.cs` に実装しています。 `Configuration` メソッドには、`ConfigureAuth` への呼び出しを追加していますが、その定義は `Startup.Auth.cs` に存在します。 変更後の `Startup.cs` は、次のようになります。

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

### <a name="configure-oauth-20-authentication"></a>OAuth 2.0 認証を構成する

ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(...)` メソッドを実装します。 その例を次に示します。

```CSharp
// App_Start\Startup.Auth.cs

 public partial class Startup
    {
        // These values are pulled from web.config
        public static string AadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
        public static string Tenant = ConfigurationManager.AppSettings["ida:Tenant"];
        public static string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
        public static string SignUpSignInPolicy = ConfigurationManager.AppSettings["ida:SignUpSignInPolicyId"];
        public static string DefaultPolicy = SignUpSignInPolicy;

        /*
         * Configure the authorization OWIN middleware.
         */
        public void ConfigureAuth(IAppBuilder app)
        {
            TokenValidationParameters tvps = new TokenValidationParameters
            {
                // Accept only those tokens where the audience of the token is equal to the client ID of this app
                ValidAudience = ClientId,
                AuthenticationType = Startup.DefaultPolicy
            };

            app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
            {
                // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
                AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(AadInstance, Tenant, DefaultPolicy)))
            });
        }
    }
```

### <a name="secure-the-task-controller"></a>タスク コントローラーをセキュリティで保護する

OAuth 2.0 認証を使用するようにアプリを構成した後は、タスク コントローラーに `[Authorize]` タグを追加することによって、Web API をセキュリティ保護できます。 このコントローラーでは To-Do List のすべての操作が実行されるため、コントローラー全体をクラス レベルでセキュリティ保護する必要があります。 よりきめ細かく制御するために、 `[Authorize]` タグを個々のアクションに追加することもできます。

```CSharp
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>トークンからユーザー情報を取得する

`TasksController` はデータベースにタスクを格納します。 所有者は、ユーザーの**オブジェクト ID** によって識別されます  (すべてのポリシーにアプリケーション要求としてオブジェクト ID を追加する必要があったのは、このためです)。

```CSharp
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

### <a name="validate-the-permissions-in-the-token"></a>トークン内のアクセス許可を検証する

Web API の一般的な要件に、トークンに存在する "スコープ" の検証があります。 この検証により、To-Do List サービスへのアクセスに必要なアクセス許可に対してユーザーが同意したことを確認できます。

```CSharp
public IEnumerable<Models.Task> Get()
{
    if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "read")
    {
        throw new HttpResponseException(new HttpResponseMessage {
            StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = "The Scope claim does not contain 'read' or scope claim not found"
        });
    }
    ...
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

最後に、`TaskWebApp` と `TaskService` を両方ともビルドして実行します。 ユーザーの To-Do List にいくつかのタスクを作成すると、クライアントを停止して再起動した後でも、作成したタスクが API に保持されていることを確認できます。

## <a name="edit-your-policies"></a>ポリシーの編集

Azure AD B2C を使用して API のセキュリティを確保した後は、サインイン/サインアップ ポリシーでさまざまな設定を試し、その効果 (または不足部分) を API で確認することができます。 ポリシー内のアプリケーション要求を操作し、Web API で利用できるユーザー情報を変更することができます。 追加した要求は、既に説明したとおり .NET MVC Web API の `ClaimsPrincipal` オブジェクトで利用できます。

