---
title: 保護された Web API - アプリ コードの構成 | Azure Active Directory
description: 保護された Web API をビルドして、アプリケーションのコードを構成する方法について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ff03316a1f9409d4d6e4b7ddf52d0c8cc7a909
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551540"
---
# <a name="protected-web-api-adding-authorization-to-your-api"></a>保護された Web API: API への承認の追加

この記事では、Web API に承認を追加する方法について説明します。 この保護により、API を呼び出せるのは以下のものだけになります。

- 適切なスコープを持つユーザーに代わるアプリケーション。
- 適切なアプリケーション ロールを持つデーモン アプリ。

ASP.NET および ASP.NET Core Web API を保護するには、以下のいずれかに `[Authorize]` 属性を追加する必要があります。

- コントローラーのすべてのアクションを保護したい場合は、コントローラー自体
- API の個々のコントローラー アクション

```CSharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

ただし、この保護は不十分です。 ASP.NET および ASP.NET Core によってトークンが検証されることしか保証されません。 API は、Web API の呼び出しに使用されるトークンが、想定されている要求によって求められたことを確かめる必要があります、特に、以下の点を確認してください。

- API がユーザーに代わって呼び出された場合は、"*スコープ*"。
- API をデーモン アプリから呼び出すことができる場合は、"*アプリ ロール*"。

## <a name="verifying-scopes-in-apis-called-on-behalf-of-users"></a>ユーザーに代わって呼び出される API のスコープの確認

API がユーザーに代わってクライアント アプリによって呼び出される場合は、API 用の特定のスコープを持つベアラー トークンを要求する必要があります ([コードの構成のベアラー トークン](scenario-protected-web-api-app-configuration.md#bearer-token)に関するページを参照してください)。

```CSharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    const string scopeRequiredByAPI = "access_as_user";

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
        VerifyUserHasAnyAcceptedScope(scopeRequiredByAPI);
        // Do the work and return the result.
        ...
    }
...
}
```

`VerifyUserHasAnyAcceptedScope` メソッドは、次のような処理を行います。

- `http://schemas.microsoft.com/identity/claims/scope` または `scp` という名前の要求があることを確認してください。
- 要求が、API で想定されているスコープを含む値を持っていることを確認します。

```CSharp
    /// <summary>
    /// When applied to a <see cref="HttpContext"/>, verifies that the user authenticated in the 
    /// web API has any of the accepted scopes.
    /// If the authenticated user doesn't have any of these <paramref name="acceptedScopes"/>, the
    /// method throws an HTTP Unauthorized error with a message noting which scopes are expected in the token.
    /// </summary>
    /// <param name="acceptedScopes">Scopes accepted by this API</param>
    /// <exception cref="HttpRequestException"/> with a <see cref="HttpResponse.StatusCode"/> set to 
    /// <see cref="HttpStatusCode.Unauthorized"/>
    public static void VerifyUserHasAnyAcceptedScope(this HttpContext context,
                                                     params string[] acceptedScopes)
    {
        if (acceptedScopes == null)
        {
            throw new ArgumentNullException(nameof(acceptedScopes));
        }
        Claim scopeClaim = HttpContext?.User
                                      ?.FindFirst("http://schemas.microsoft.com/identity/claims/scope");
        if (scopeClaim == null || !scopeClaim.Value.Split(' ').Intersect(acceptedScopes).Any())
        {
            context.Response.StatusCode = (int)HttpStatusCode.Unauthorized;
            string message = $"The 'scope' claim does not contain scopes '{string.Join(",", acceptedScopes)}' or was not found";
            throw new HttpRequestException(message);
        }
    }
```

このサンプル コードは、ASP.NET Core 用です。 ASP.NET の場合は、`HttpContext.User` を `ClaimsPrincipal.Current` に置き換え、要求の種類 `"http://schemas.microsoft.com/identity/claims/scope"` を `"scp"` に置き換えるだけです (この記事の後の方のコード スニペットも参照してください)。

## <a name="verifying-app-roles-in-apis-called-by-daemon-apps"></a>デーモン アプリによって呼び出される API のアプリ ロールの確認

Web API が [デーモン アプリ](scenario-daemon-overview.md)によって呼び出された場合、そのアプリには Web API に対するアプリケーションのアクセス許可が必要です。 「[アプリケーションのアクセス許可 (アプリ ロール) の公開](https://docs.microsoft.com/azure/active-directory/develop/scenario-protected-web-api-app-registration#exposing-application-permissions-app-roles)」で、API がそのようなアクセス許可 (たとえば `access_as_application` アプリ ロール) を公開していることを確認しました。
ここでは、API に、受け取ったトークンに `roles` 要求が含まれていることと、この要求に想定されている値が含まれていることを検証させる必要があります。 この検証を行うコードは、委任されたアクセス許可を検証するコードと似ています。異なるのは、`scopes` をテストする代わりに、コントローラーのアクションが `roles` をテストすることです。

```CSharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        ValidateAppRole("access_as_application");
        ...
    }
```

`ValidateAppRole()` メソッドは、次のようなものになります。

```CSharp
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

このサンプル コードは、ASP.NET 用です。 ASP.NET Core の場合は、`ClaimsPrincipal.Current` を `HttpContext.User` に置き換え、要求の名前 `"roles"` を `"http://schemas.microsoft.com/identity/claims/roles"` に置き換えるだけです (この記事の前の方のコード スニペットも参照してください)。

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Web API がデーモン アプリのみによって呼び出される必要がある場合のアプリ専用トークンの受け入れ

`roles` 要求は、ユーザー割り当てパターンのユーザーに対しても使用されます (「[方法: アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください)。そのため、ロールが両方に割り当てられている場合は、ロールを確認するだけでアプリがユーザーとして (またはその逆として) サインインできるようになります。 この混乱を避けるために、ユーザー用とアプリ用に異なるロールを宣言することをお勧めします。

デーモン アプリのみに Web API の呼び出しを許可する場合は、アプリ ロールを検証するときに、トークンがアプリ専用トークンであるという条件を追加します。

```CSharp
string oid = ClaimsPrincipal.Current.FindFirst("oid");
string sub = ClaimsPrincipal.Current.FindFirst("sub");
bool isAppOnlyToken = oid == sub;
```

逆の条件を確認するようにすると、ユーザーがサインインするアプリのみに API の呼び出しが許可されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [運用環境への移行](scenario-protected-web-api-production.md)
