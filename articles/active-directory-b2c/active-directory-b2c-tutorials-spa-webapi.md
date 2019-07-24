---
title: チュートリアル - シングルページ アプリケーションから ASP.NET Core Web API へのアクセスを許可する - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C を使用して .NET Core Web API を保護し、シングルページ アプリからそれを呼び出す方法に関するチュートリアル。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.author: davidmu
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 13cbf1e81e0d203c181efb0881ec2a437cbaef24
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752179"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用してシングルページ アプリケーションから ASP.NET Core Web API へのアクセスを許可する

このチュートリアルでは、Azure Active Directory (Azure AD) B2C で保護された ASP.NET Core Web API リソースをシングルページ アプリケーションから呼び出す方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

「[チュートリアル: Azure Active Directory B2C を使用してアカウントによるシングルページ アプリの認証を有効にする](active-directory-b2c-tutorials-spa.md)」の手順と前提条件を完了してください。

## <a name="add-a-web-api-application"></a>Web API アプリケーションを追加する

アクセス トークンを提示するクライアント アプリケーションによる保護されたリソース要求を Web API リソースで受け取って処理できるためには、Web API リソースをテナントに登録しておく必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、**[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、**[追加]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
6. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、**[はい]** を選択します。
7. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 このチュートリアルでは、サンプルはローカルで実行され、`https://localhost:5000` をリッスンします。
8. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
9. **Create** をクリックしてください。
10. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば、読み取りアクセスと書き込みアクセス両方を持つユーザーもいれば、読み取り専用アクセス許可を持つユーザーもいます。 このチュートリアルでは、Web API の読み取りアクセス許可を定義します。

1. **[アプリケーション]** を選択し、*webapi1* を選択します。
2. **[公開済みスコープ]** を選択します。
3. **スコープ**に「`Hello.Read`」と入力し、説明に「`Read access to hello`」と入力します。
4. **スコープ**に「`Hello.Write`」と入力し、説明に「`Write access to hello`」と入力します。
5. **[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリに Web API へのアクセス許可を付与することができます。

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 前提条件のチュートリアルでは、Azure AD B2C に *webapp1* という名前の Web アプリケーションを作成しました。 このアプリケーションを使用して、Web API を呼び出します。

1. **[アプリケーション]** を選択し、*webapp1* を選択します。
2. **[API アクセス]** を選択し、**[追加]** を選択します。
3. **[API の選択]** ドロップダウンで、*webapi1* を選択します。
4. **[スコープの選択]** ドロップダウンで、前に定義した **Hello.Read** スコープと **Hello.Write** スコープを選択します。
5. Click **OK**.

**My sample single page app** は、保護された **Hello Core API** を呼び出すために登録されています。 ユーザーは、シングルページ アプリケーションを使用するために Azure AD B2C で認証を行います。 シングルページ アプリは、保護された Web API にアクセスするために、Azure AD B2C から承認付与を取得します。

## <a name="configure-the-sample"></a>サンプルの構成

Web API を登録し、スコープを定義したので、Azure AD B2C テナントを使用するように Web API コードを構成します。 このチュートリアルでは、GitHub からダウンロードできるサンプル .NET Core Web アプリケーションを構成します。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)するか、GitHub からサンプル Web アプリを複製します。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API を構成する

1. Visual Studio で **B2C-WebAPI.sln** ソリューションを開きます。
2. **appsettings.json** ファイルを開きます。 テナントを使用するように Web API を構成するには、次の値を更新します。

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>CORS を有効にする

シングルページ アプリケーションに ASP.NET Core Web API の呼び出しを許可するには、[CORS](https://docs.microsoft.com/aspnet/core/security/cors) を有効にする必要があります。

1. **Startup.cs** の `ConfigureServices()` メソッドに CORS を追加します。

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. **Startup.cs** の `Configure()` メソッドで CORS を構成します。

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. **[Properties]** の **launchSettings.json** を開き、**iisSettings** *applicationURL* 設定の場所を特定して、ポート番号を API 応答 URL `http://localhost:5000` に登録されている番号に設定します。

### <a name="configure-the-single-page-application"></a>シングルページ アプリを構成する

シングルページ アプリケーションでは、ユーザーのサインアップ、サインインに Azure AD B2C を使用し、保護された ASP.NET Core Web API を呼び出します。 シングルページ アプリケーションを、.NET Core Web API を呼び出すように更新します。

アプリの設定を変更するには、次の手順に従います。

1. `index.html` ファイルを開きます。
2. Azure AD B2C テナントの登録情報でサンプルを構成します。 次のコードで、テナント名を **b2cScopes** に追加し、**webApi** の値を、先ほどメモした *applicationURL* の値に変更します。

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>SPA アプリケーションと Web API を実行する

Node.js シングルページ アプリケーションと .NET Core Web API の両方を実行する必要があります。

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core Web API を実行する 

Visual Studio で **F5** キーを押して **B2C-WebAPI.sln** ソリューションをデバッグします。

プロジェクトが起動すると、既定のブラウザーには、Web API を要求に使用できることを示す Web ページが表示されます。

### <a name="run-the-single-page-app"></a>シングルページ アプリを実行する

1. Node.js コマンド プロンプトを起動します。
2. Node.js サンプルが含まれているディレクトリに変更します。 例: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`。
3. 次のコマンドを実行します。
    ```
    npm install && npm update
    node server.js
    ```

    コンソール ウィンドウには、アプリケーションがホストされている場所のポート番号が表示されます。
    
    ```
    Listening on port 6420...
    ```

4. ブラウザーを使用してアドレス `http://localhost:6420` に移動し、アプリケーションを表示します。
5. [シングルページ アプリケーション (JavaScript) における Azure Active Directory B2C を使用したユーザーの認証](active-directory-b2c-tutorials-spa.md) に関するページで使用したメール アドレスとパスワードでサインインします。
6. **[Call API]\(API の呼び出し\)** をクリックします。

ユーザー アカウントを使用してサインアップまたはサインインすると、このサンプルは、保護された Web API を呼び出して結果を返します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Web API アプリケーションを追加する
> * Web API のスコープを構成する
> * Web API に対するアクセス許可を付与する
> * アプリケーションを使用するようにサンプルを構成する

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C でアプリケーションに ID プロバイダーを追加する](tutorial-add-identity-providers.md)
