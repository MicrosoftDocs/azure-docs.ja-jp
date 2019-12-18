---
title: チュートリアル:シングルページ アプリから ASP.NET Core Web API へのアクセスを許可する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Active Directory B2C を使用して .NET Core Web API を保護し、その API をシングルページの Node.js アプリケーションから呼び出す方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e5ced0c3e4ad05c33866548acb09189e03ed22f4
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949706"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してシングルページ アプリケーションから ASP.NET Core Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) で保護された ASP.NET Core Web API リソースをシングルページ アプリケーションから呼び出す方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

## <a name="prerequisites"></a>前提条件

* 「[チュートリアル: Azure Active Directory B2C を使用してシングルページ アプリケーションで認証を有効にする](active-directory-b2c-tutorials-spa.md)」の手順を完了し、前提条件を満たしていること。
* Visual Studio 2019 以降または Visual Studio Code
* .NET Core 2.2 以降
* Node.js

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りと書き込み両方のアクセス許可を定義します。

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

`demo.read` スコープの **[スコープ]** に示されている値を記録しておきます。これは、後の手順でシングルページ アプリケーションを構成するときに使用します。 完全なスコープ値は `https://contosob2c.onmicrosoft.com/api/demo.read` のようになります。

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API を別のアプリケーションから呼び出すには、その Web API へのアクセス許可をそのアプリケーションに付与する必要があります。

前提条件のチュートリアルでは、*webapp1* という名前の Web アプリケーションを作成しました。 このチュートリアルでは、前のセクションで作成した Web API である *webapi1* を呼び出すようにそのアプリケーションを構成します。

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

シングルページ Web アプリケーションが登録されて、保護された Web API を呼び出すことができるようになります。 ユーザーは、シングルページ アプリケーションを使用するために Azure AD B2C で認証を行います。 シングルページ アプリは、保護された Web API にアクセスするために、Azure AD B2C から承認付与を取得します。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントを使用するように Web API コードを構成します。 このチュートリアルでは、GitHub からダウンロードしたサンプル .NET Core Web アプリケーションを構成します。

[\*.zip アーカイブをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)するか、GitHub からサンプルの Web API プロジェクトを複製します。

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API を構成する

1. Visual Studio または Visual Studio Code で <em>B2C-WebApi/**appsettings.json**</em> ファイルを開きます。
1. 実際のテナント名、Web API アプリケーションのアプリケーション ID、サインアップ ポリシーまたはサインイン ポリシーの名前、前に定義したスコープを反映するように `AzureAdB2C` ブロックを変更します。 ブロックは次の例のようになります (`Tenant` と `ClientId` には適切な値が入力されます)。

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "demo.read",
      "ScopeWrite": "demo.write"
    },
    ```

#### <a name="enable-cors"></a>CORS を有効にする

自分のシングルページ アプリケーションに ASP.NET Core Web API の呼び出しを許可するには、Web API で [CORS](https://docs.microsoft.com/aspnet/core/security/cors) を有効にする必要があります。

1. *Startup.cs* の `ConfigureServices()` メソッドに CORS を追加します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. また、`ConfigureServices()` メソッド内で、`jwtOptions.Authority` の値を次のトークン発行者 URI に設定します。

    `<your-tenant-name>` は実際の B2C テナントの名前に置き換えます。

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` メソッド内で、CORS を構成します。

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Visual Studio のみ) ソリューション エクスプローラー内の **[プロパティ]** で、*launchSettings.json* ファイルを開き、次に `iisExpress` ブロックを見つけます。
1. (Visual Studio のみ) 前の手順で *webapi1* アプリケーションを登録したときに指定したポート番号で `applicationURL` の値を更新します。 例:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>シングルページ アプリケーションを構成する

このシリーズの[前のチュートリアル](active-directory-b2c-tutorials-spa.md)のシングルページ アプリケーション (SPA) では、ユーザーのサインアップとサインインに Azure AD B2C を使用し、デモ テナントである *frabrikamb2c* によって保護されている ASP.NET Core Web API を呼び出しています。

このセクションでは、"*自分の*" Azure AD B2C テナントによって保護され、自分のローカル コンピューターで実行する ASP.NET Core Web API を呼び出すようにシングルページ アプリケーションを更新します。

SPA 内の設定を変更するには:

1. 前のチュートリアルでダウンロードまたは複製した [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] プロジェクト内にある *index.html* ファイルを開きます。
1. 先ほど作成した *demo.read* スコープの URI と Web API の URL を使用してサンプルを構成します。
    1. `appConfig` 定義内で、`b2cScopes` 値を、スコープの完全な URI (前に記録した**スコープ**値) に置き換えます。
    1. `webApi` 値をリダイレクト URI に変更します。この URI は、前の手順で Web API アプリケーションを登録したときに追加しました。

    `appConfig` の定義は次のコード ブロックのようになります (`<your-tenant-name>` にはテナント名が入力されます)。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/demo.read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA と Web API を実行する

最後に、ローカル コンピューターで ASP.NET Core Web API と Node.js シングルページ アプリケーションの両方を実行します。 次に、シングルページ アプリケーションにサインインし、ボタンを押して、保護されている API に対する要求を開始します。

このチュートリアルでは両方のアプリケーションをローカルで実行しますが、サインアップとサインインをセキュリティで保護し、保護された Web API へのアクセスを許可するために、Azure AD B2C を使用します。

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core Web API を実行する

Visual Studio で、**F5** キーを押して *B2C-WebAPI.sln* ソリューションを構築およびデバッグします。 プロジェクトが起動すると、既定のブラウザーに、Web API を要求に使用できることを示す Web ページが表示されます。

Visual Studio ではなく `dotnet` CLI を使用する場合:

1. コンソール ウィンドウを開き、 *\*.csproj* ファイルを含むディレクトリに変更します。 例:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. `dotnet run` を実行することで、Web API をビルドおよび実行します。

    API が稼働しているときは、次のような出力が表示されます (このチュートリアルでは、`NETSDK1059` の警告は無視しても問題ありません)。

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>シングルページ アプリを実行する

1. コンソール ウィンドウを開き、Node.js サンプルを含むディレクトリに変更します。 例:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. 次のコマンドを実行します。

    ```console
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。

    ```console
    Listening on port 6420...
    ```

1. アプリケーションを表示するには、ブラウザーで `http://localhost:6420` に移動します。
1. [前のチュートリアル](active-directory-b2c-tutorials-spa.md)で使用したメール アドレスとパスワードを使用してサインインします。 ログインが成功すると、`User 'Your Username' logged-in` というメッセージが表示されます。
1. **[Call Web API]\(Web API の呼び出し\)** ボタンを選択します。 SPA は Azure AD B2C から承認付与を取得し、保護された Web API にアクセスして、そのインデックス ページの内容を表示します。

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

これで、SPA が保護された Web API に対してリソースを要求したことを確認できました。次は、これらの種類のアプリケーションが相互にまたは Azure AD B2C との間でどのように作用するかについて、理解を深めます。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C で使用できるアプリケーションの種類 >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
