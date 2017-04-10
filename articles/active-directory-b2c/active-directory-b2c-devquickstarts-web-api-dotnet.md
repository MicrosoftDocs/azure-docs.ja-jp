---
title: Azure Active Directory B2C | Microsoft Docs
description: "Azure Active Directory B2C と OAuth 2.0 アクセス トークンを使用して、.NET Web アプリを構築し、Web API を呼び出す方法。"
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 02ec1b7a58aff6ae0788e341e8987b9d32cb5a7b
ms.lasthandoff: 03/27/2017


---
# <a name="azure-ad-b2c-call-a-net-web-api-from-a-net-web-app"></a>Azure AD B2C: .NET Web アプリから .NET Web API を呼び出す

Azure AD B2C を使用すると、Web アプリと Web API に強力な ID 管理機能を追加できます。 この記事では、アクセス トークンを要求し、"To Do リスト" Web アプリから .NET Web API を呼び出す方法について説明します。

この記事では、Azure AD B2C でサインイン、サインアップ、プロファイルの管理を実装する方法については説明しません。 ユーザーが既に認証された後での Web API の呼び出しに焦点を合わせています。 まだ準備ができていない場合は、次の記事を参照して準備してください。

* [.NET Web アプリ](active-directory-b2c-devquickstarts-web-dotnet-susi.md)の使用
* [.NET API](active-directory-b2c-devquickstarts-api-dotnet.md) の使用

## <a name="prerequisite"></a>前提条件

Web API を呼び出す Web アプリケーションを構築するには、次の作業を行う必要があります。

1. [Azure AD B2C テナントを作成](active-directory-b2c-get-started.md)します。
2. [Web API を登録](active-directory-b2c-app-registration.md#register-a-web-api)します。
3. [Web アプリを登録](active-directory-b2c-app-registration.md#register-a-web-application)します。
4. [ポリシーを設定](active-directory-b2c-reference-policies.md)します。
5. [Web API を使用するためのアクセス許可を Web アプリに付与](active-directory-b2c-access-tokens.md#granting-permissions-to-a-web-api)します。

> [!IMPORTANT]
> クライアント アプリケーションと Web API は、同じ Azure AD B2C ディレクトリを使用する必要があります。
>

## <a name="download-the-code"></a>コードのダウンロード

このチュートリアルのコードは、[GitHub](https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi) で管理されています。 次のコマンドを実行するとサンプルを複製できます。

```console
git clone https://github.com/Azure-Samples/b2c-dotnet-webapp-and-webapi.git
```

サンプル コードをダウンロードした後、Visual Studio の .sln ファイルを開いて作業を開始します。 ソリューション ファイルには、`TaskWebApp` と `TaskService` の 2 つのプロジェクトが含まれています。 `TaskWebApp` は、ユーザーが操作する MVC Web アプリケーションです。 `TaskService` は、各ユーザーの To-Do List を格納する、アプリのバックエンド Web API です。 この記事では、`TaskWebApp` Web アプリと `TaskService` Web API の構築については取り上げていません。 Azure AD B2C を使用して .NET Web アプリを構築する方法については、[.NET Web アプリのチュートリアル](active-directory-b2c-devquickstarts-web-dotnet-susi.md)をご覧ください。 Azure AD B2C を使用して .NET Web API を構築する方法については、[.NET Web API のチュートリアル](active-directory-b2c-devquickstarts-api-dotnet.md)をご覧ください。

### <a name="update-the-azure-ad-b2c-configuration"></a>Azure AD B2C の構成を更新する

サンプルは、デモ テナントのポリシーとクライアント ID を使用するように構成されています。 独自のテナントを使用する場合は、次の手順に従います。

1. `TaskService` プロジェクトの `web.config` を開き、次のように値を置き換えます。

    * `ida:Tenant` を実際のテナント名に置き換えます。
    * `ida:ClientId` を実際の Web API のアプリケーション ID に置き換えます。
    * `ida:SignUpSignInPolicyId` を実際の "サインアップまたはサインイン" ポリシーの名前に置き換えます。

2. `TaskWebApp` プロジェクトの `web.config` を開き、次のように値を置き換えます。

    * `ida:Tenant` を実際のテナント名に置き換えます。
    * `ida:ClientId` を実際の Web アプリのアプリケーション ID に置き換えます。
    * `ida:ClientSecret` を実際の Web アプリのシークレット キーに置き換えます。
    * `ida:SignUpSignInPolicyId` を実際の "サインアップまたはサインイン" ポリシーの名前に置き換えます。
    * `ida:EditProfilePolicyId` を実際の "プロファイルの編集" ポリシーの名前に置き換えます。
    * `ida:ResetPasswordPolicyId` を実際の "パスワードのリセット" ポリシーの名前に置き換えます。



## <a name="requesting-and-saving-an-access-token"></a>アクセス トークンの要求と保存

### <a name="specify-the-permissions"></a>アクセス許可を指定する

Web API を呼び出すには、(サインアップ/サインイン ポリシーを使用して) ユーザーを認証し、Azure AD B2C から[アクセス トークンを受け取る](active-directory-b2c-access-tokens.md)必要があります。 アクセス トークンを受け取るには、アクセス トークンによって付与するアクセス許可を指定しておく必要があります。 `/authorize` エンドポイントに対して要求を行うときに、`scope` パラメーターにアクセス許可が指定されます。 たとえば、アプリケーション ID URI が `https://contoso.onmicrosoft.com/tasks` であるリソース アプリケーションに対する "読み取り" アクセス許可を付与するアクセス トークンを取得する場合、スコープは `https://contoso.onmicrosoft.com/tasks/read` になります。

サンプルでスコープを指定するには、`App_Start\Startup.Auth.cs` ファイルを開き、OpenIdConnectAuthenticationOptions で `Scope` 変数を定義します。

```CSharp
// App_Start\Startup.Auth.cs

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ...

            // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
            Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
        }
    );
}
```

### <a name="exchange-the-authorization-code-for-an-access-token"></a>承認コードをアクセス トークンと交換する

ユーザーがサインアップまたはサインイン エクスペリエンスを完了すると、アプリは Azure AD B2C から承認コードを受け取ります。 OWIN OpenID Connect ミドルウェアはこのコードを保存しますが、アクセス トークンとの交換は行いません。 交換は [MSAL ライブラリ](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)を使用して行うことができます。 サンプルでは、承認コードを受け取ったときの OpenID Connect ミドルウェアへの通知コールバックを構成しています。 コールバックでは、MSAL を使用してコードをトークンと交換し、トークンをキャッシュに保存します。

```CSharp
/*
* Callback function when an authorization code is received
*/
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
    // Extract the code from the response notification
    var code = notification.Code;

    var userObjectId = notification.AuthenticationTicket.Identity.FindFirst(ObjectIdElement).Value;
    var authority = String.Format(AadInstance, Tenant, DefaultPolicy);
    var httpContext = notification.OwinContext.Environment["System.Web.HttpContextBase"] as HttpContextBase;

    // Exchange the code for a token. Make sure to specify the necessary scopes
    ClientCredential cred = new ClientCredential(ClientSecret);
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                            RedirectUri, cred, new NaiveSessionCache(userObjectId, httpContext));
    var authResult = await app.AcquireTokenByAuthorizationCodeAsync(new string[] { ReadTasksScope, WriteTasksScope }, code, DefaultPolicy);
}
```

## <a name="calling-the-web-api"></a>Web API の呼び出し

このセクションでは、Web API にアクセスするために、Azure AD B2C でサインアップ/サインイン時に受け取ったトークンを使用する方法について説明します。

### <a name="retrieve-the-saved-token-in-the-controllers"></a>コントローラーで保存済みのトークンを取得する

`TasksController` は Web API と通信し、タスクの読み取り、作成、削除を行うための HTTP 要求を API に送信する役割を担います。 API は Azure AD B2C によってセキュリティ保護されているため、まず、前の手順で保存したトークンを取得する必要があります。

```CSharp
// Controllers\TasksController.cs

/*
* Uses MSAL to retrieve the token from the cache
*/
private async void acquireToken(String[] scope)
{
    string userObjectID = ClaimsPrincipal.Current.FindFirst(Startup.ObjectIdElement).Value;
    string authority = String.Format(Startup.AadInstance, Startup.Tenant, Startup.DefaultPolicy);

    ClientCredential credential = new ClientCredential(Startup.ClientSecret);

    // Retrieve the token using the provided scopes
    ConfidentialClientApplication app = new ConfidentialClientApplication(authority, Startup.ClientId,
                                        Startup.RedirectUri, credential,
                                        new NaiveSessionCache(userObjectID, this.HttpContext));
    AuthenticationResult result = await app.AcquireTokenSilentAsync(scope);

    accessToken = result.Token;
}
```

### <a name="read-tasks-from-the-web-api"></a>Web API からのタスク読み取り

トークンを取得したら、`Authorization` ヘッダーの HTTP `GET` 要求にトークンをアタッチして、`TaskService` を安全に呼び出すことができます。

```CSharp
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        // Retrieve the token with the specified scopes
        acquireToken(new string[] { Startup.ReadTasksScope });

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, apiEndpoint);

        // Add token to the Authorization header and make the request
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
        HttpResponseMessage response = await client.SendAsync(request);

        // Handle response ...
}

```

### <a name="create-and-delete-tasks-on-the-web-api"></a>Web API でのタスクの作成および削除

`POST` 要求と `DELETE` 要求を Web API に送信する場合と同じパターンに従い、MSAL を使用してキャッシュからアクセス トークンを取得します。

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

最後に、アプリをビルドして実行します。 サインアップおよびサインインを行い、サインインしているユーザーのタスクを作成します。 サインアウトし、別のユーザーとしてサインインします。 そのユーザーのタスクを作成します。 API でタスクがユーザーごとに保存されたことを確認します。これは、API が、受信したトークンからユーザー ID を抽出したためです。 スコープも操作してみてください。 "書き込み" アクセス許可を削除し、タスクを追加してみます。 スコープを変更するたびに、必ずサインアウトしてください。


