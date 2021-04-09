---
title: 保護された Web API でスコープとアプリ ロールを検証する | Azure
titleSuffix: Microsoft identity platform
description: API が、適切なスコープを持つユーザーに代わるアプリケーションによって、また適切なアプリケーション ロールを持つデーモン アプリによってのみ呼び出されることを確認します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442907"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>保護された Web API: スコープとアプリのロールを検証する

この記事では、Web API に承認を追加する方法について説明します。 この保護により、API を呼び出せるのは以下のものだけになります。

- 適切なスコープを持つユーザーに代わるアプリケーション。
- 適切なアプリケーション ロールを持つデーモン アプリ。

> [!NOTE]
> この記事のコード スニペットは、GitHub の以下のコード サンプルから抜粋されたものです。
>
> - [ASP.NET Core Web API の増分チュートリアル](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET Web API のサンプル](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

ASP.NET または ASP.NET Core の Web API を保護するには、次のいずれかの項目に `[Authorize]` 属性を追加する必要があります。

- すべてのコントローラー アクションを保護する場合は、コントローラー自体
- API の個々のコントローラー アクション

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

ただし、この保護は不十分です。 ASP.NET および ASP.NET Core でトークンが検証されることしか保証されません。 API は、API の呼び出しに使用されるトークンが、予期された要求を使用して要求されていることを検証する必要があります。 特に次の要求では検証が必要です。

- API がユーザーの代わりに呼び出される場合は、"*スコープ*"。
- API をデーモン アプリから呼び出すことができる場合は、"*アプリ ロール*"。

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>ユーザーに代わって呼び出される API のスコープの確認

クライアント アプリがユーザーに代わって API を呼び出す場合、API は、API 用の特定のスコープを持つベアラー トークンを要求する必要があります。 詳細については、[「コード構成」の「ベアラー トークン」](scenario-protected-web-api-app-configuration.md#bearer-token)を参照してください。

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>各コントローラー アクションのスコープを確認する

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` メソッドは、次の手順のような処理を行います。

- `http://schemas.microsoft.com/identity/claims/scope` または `scp` という名前の要求があることを確認します。
- 要求が、API で想定されているスコープを含む値を持っていることを確認します。


#### <a name="verify-the-scopes-more-globally"></a>スコープをよりグローバルに検証する

Web API の詳細なスコープを定義し、各コントローラー アクションでスコープを確認することが、お勧めの方法です。 ただし、ASP.NET Core を使用して、アプリケーションまたはコントローラーのレベルでスコープを確認することもできます。 詳細については、ASP.NET のコア ドキュメントの[要求ベースの承認](/aspnet/core/security/authorization/claims)に関する記事を参照してください。

### <a name="net-mvc"></a>.NET MVC

ASP.NET の場合は、`HttpContext.User` を `ClaimsPrincipal.Current` に置き換え、要求の種類 `"http://schemas.microsoft.com/identity/claims/scope"` を `"scp"` に置き換えるだけです  この記事の後の方のコード スニペットも参照してください。

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>デーモン アプリによって呼び出される API のアプリ ロールの確認

Web API が [デーモン アプリ](scenario-daemon-overview.md)によって呼び出された場合、そのアプリには Web API に対するアプリケーションのアクセス許可が必要です。 「[アプリケーションのアクセス許可 (アプリ ロール) の公開](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)」に示されているように、API はそのようなアクセス許可を公開します。 1 つの例として、`access_as_application` アプリ ロールがあります。

ここでは、受け取ったトークンに `roles` 要求が含まれていることと、この要求に想定されている値が含まれていることを API に確認させる必要があります。 この確認コードは、委任されたアクセス許可を確認するコードと似ています。異なるのは、コントローラー アクションでテストされるのが、スコープではなくロールである点です。

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole` メソッドは、Microsoft.Identity.Web の [RolesRequiredHttpContextExtensions.cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28) で定義されています。

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Web API がデーモン アプリのみによって呼び出される必要がある場合のアプリ専用トークンの受け入れ

ユーザーは、ユーザー割り当てパターン内でロール要求を使用することもできます。使用方法は、「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください)。 ロールが両方に割り当て可能な場合は、ロールをチェックすると、アプリはユーザーとして、ユーザーはアプリとしてサインインできるようになります。 この混乱を避けるために、ユーザー用とアプリ用に異なるロールを宣言することをお勧めします。

デーモン アプリのみが Web API を呼び出せるようにしたい場合は、アプリ ロールを検証するときに、トークンがアプリ専用トークンであるという条件を追加します。

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

逆の条件をチェックすると、ユーザーとしてサインインするアプリのみが API を呼び出すことができます。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[運用環境に移行する](scenario-protected-web-api-production.md)」に進みます。
