---
title: "Azure AD .NET Web API の概要 | Microsoft Docs"
description: "認証と承認のために Azure AD と連携する .NET MVC Web API を構築する方法"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: b028a75836f7c762431bfb9e3fc30822b7dee885
ms.lasthandoff: 03/18/2017


---
# <a name="help-protect-a-web-api-by-using-bearer-tokens-from-azure-ad"></a>Azure AD からのベアラー トークンを使用して Web API の保護を支援する
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

保護されているリソースにアクセスするアプリケーションを構築する場合、不正なアクセスからこれらのリソースを保護する方法を習得する必要があります。
Azure Active Directory (Azure AD) を使用すると、OAuth 2.0 ベアラー アクセス トークンを使用し、わずか数行のコードを追加するだけで、Web API の保護を簡単に支援することができます。

ASP.NET Web アプリでは、こうした保護を、.NET Framework 4.5 に含まれるコミュニティ駆動の OWIN ミドルウェアの Microsoft 実装を使用することにより実現できます。 ここでは、OWIN を使用して、次の処理を実行する "To Do List" Web API を構築します。

* 保護対象の API を指定する。
* Web API 呼び出しに有効なアクセス トークンが含まれているかどうかを検証する。

To Do List API を構築するには、まず次の手順を実行する必要があります。

1. アプリケーションを Azure AD に登録する。
2. OWIN 認証パイプラインを使用するようにアプリを設定する。
3. Web API を呼び出すようにクライアント アプリケーションを構成する。

最初に、[アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)します。 両方とも、Visual Studio 2013 ソリューションです。 アプリケーションの登録先となる Azure AD テナントも必要です。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

## <a name="step-1-register-an-application-with-azure-ad"></a>手順 1: アプリケーションを Azure AD に登録する
アプリケーションのセキュリティ保護を支援するには、まず、テナントでアプリケーションを作成し、Azure AD にいくつかの重要な情報を提供する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Azure AD テナントを選択します。

3. 左側のウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。

4. **[アプリの登録]** をクリックし、**[追加]** を選択します。

5. 画面の指示に従い、新しい **Web アプリケーションか Web API (またはその両方)** を作成します。
  * **[名前]** には、ユーザーがアプリケーションの機能を把握できる名前を入力します。 「**To Do List Service**」と入力します。
  * **[リダイレクト URI]** には、Azure AD が、アプリから要求されたトークンを返すために使用するスキームと文字列の組み合わせを設定します。 この値として、「 `https://localhost:44321/` 」を入力します。

6. アプリケーションの **[設定]**  ->  **[プロパティ]** ページで、アプリ ID URI を更新します。 テナント固有の識別子を入力します。 たとえば、「 `https://contoso.onmicrosoft.com/TodoListService`」のように入力します。

7. 構成を保存します。 今後の手順でクライアント アプリケーションも登録する必要があるため、ポータルは開いたままにします。

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>手順 2: OWIN 認証パイプラインを使用するようにアプリを設定する
受信した要求およびトークンを検証するために、アプリケーションを Azure AD と通信するように設定する必要があります。

1. まず、ソリューションを開き、パッケージ マネージャー コンソールを使用して、OWIN ミドルウェア NuGet パッケージを TodoListService プロジェクトに追加します。

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. OWIN Startup クラスを TodoListService プロジェクト ( `Startup.cs`) に追加します。  プロジェクトを右クリックし、**[追加]** > **[新しい項目]** の順に選択して、**OWIN** を検索します。 アプリが起動すると、OWIN ミドルウェアは `Configuration(…)` メソッドを呼び出します。

3. クラスの宣言を `public partial class Startup` に変更します。 このクラスの部分は、別のファイルに実装済みです。 `Configuration(…)` メソッドで、Web アプリ用の認証を設定する `ConfgureAuth(…)` への呼び出しを作成します。

    ```C#
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. ファイル `App_Start\Startup.Auth.cs` を開いて、`ConfigureAuth(…)` メソッドを実装します。 `WindowsAzureActiveDirectoryBearerAuthenticationOptions` で指定するパラメーターは、アプリが Azure AD と通信するための調整役として機能します。

    ```C#
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. 次に、`[Authorize]` 属性を使用して、JSON Web トークン (JWT) ベアラー認証によりコントローラーとアクションの保護を支援します。 認証タグを使用して、 `Controllers\TodoListController.cs` クラスを修飾します。 これにより、ユーザーはそのページにアクセスする前に、サインインが必要になります。

    ```C#
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    承認呼び出し元が `TodoListController` API の 1 つを正常に呼び出すときに、呼び出し元についての情報にアクセスするアクションが必要な場合があります。 OWIN は `ClaimsPrincpal` オブジェクトを通して、ベアラー トークン内のクレームにアクセスできるようにします。  

6. Web API の一般的な要件に、トークンに存在する "スコープ" の検証があります。 この検証により、To Do List Service へのアクセスに必要なアクセス許可に対してユーザーが同意したことを確認できます。

    ```C#
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. TodoListService プロジェクトのルートにある `web.config` ファイルを開いて、`<appSettings>` セクションに構成値を入力します。
  * `ida:Tenant` には、Azure AD テナントの名前 (contoso.onmicrosoft.com など) を指定します。
  * `ida:Audience` には、Azure Portal で入力したアプリケーションのアプリ ID URI を指定します。

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>手順 3: クライアント アプリケーションを構成してサービスを実行する
To Do List Service の動作を確認できるようにするには、AAD からトークンを取得してサービスを呼び出せるように To Do List クライアントを構成する必要があります。

1. [Azure Portal](https://portal.azure.com) に戻ります。

2. Azure AD テナントで新しいアプリケーションを作成し、表示されるプロンプトで **[ネイティブ クライアント アプリケーション]** を選択します。
  * **[名前]** には、ユーザーがアプリケーションの機能を把握できる名前を入力します。
  * **[リダイレクト URI]** 値には、「`http://TodoListClient/`」を入力します。

3. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 この値は次の手順で必要になるので、アプリケーション ページからコピーします。

4. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。 To Do List Service を見つけて選択し、**Access TodoListService \(TodoListService へのアクセス)** アクセス許可を **[デリゲートされたアクセス許可]** の下に追加し、**[完了]** をクリックします。

5. Visual Studio で、TodoListClient プロジェクトの `App.config` を開いて、`<appSettings>` セクションに構成値を入力します。

  * `ida:Tenant` には、Azure AD テナントの名前 (contoso.onmicrosoft.com など) を指定します。
  * `ida:ClientId` には、Azure Portal からコピーしたアプリ ID を指定します。
  * `todo:TodoListResourceId` には、Azure Portal で入力した To Do List Service のアプリ ID URI を指定します。

## <a name="next-steps"></a>次のステップ
最後に、各プロジェクトをクリーンアップし、ビルドして実行します。 *.onmicrosoft.com ドメインを使用して、テナントに新しいユーザーを作成します (作成していない場合)。 そのユーザーとして To Do List クライアントにサインインし、いくつかのタスクを To-Do リストに追加します。

参考用に、完全なサンプル (環境に応じた構成値を除く) が [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip) で提供されています。 ここからは、さらに詳細な ID シナリオに進むことができます。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

