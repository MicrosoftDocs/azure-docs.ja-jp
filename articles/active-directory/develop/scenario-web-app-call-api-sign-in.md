---
title: Web API を呼び出す Web アプリ (サインイン) - Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを構築する方法について説明します (サインイン)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720196"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Web API を呼び出す Web アプリ - サインイン

Web アプリにサインインを追加する方法については既に学習しました。 これについては、「[ユーザーをサインインさせる Web アプリ - サインインの追加](scenario-web-app-sign-user-sign-in.md)」でご説明しました。

ここで異なるのは、ユーザーがこのアプリケーションから、または任意のアプリケーションからサインアウトしたときに、トークン キャッシュ (ユーザーに関連付けられているトークン) から削除することです。

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>サインアウト後にコールバックをインターセプトする - シングル サインアウト

アプリケーションは、`logout` 後のイベントをインターセプトして、たとえば、サインアウトしたアカウントに関連付けられたトークン キャッシュのエントリをクリアすることができます。Web アプリでは、ユーザーのアクセス トークンがキャッシュに格納されます。 `logout` 後のコールバックをインターセプトすることにより、Web アプリケーションはトークン キャッシュからユーザーを削除することができます。

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

このメカニズムについては、[WebAppServiceCollectionExtensions.cs#L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) の `AddMsal()` メソッドで示されています

アプリケーションに対して登録した**ログアウト URL** を使用して、シングル サインアウトを実装することができます。Microsoft ID プラットフォームの `logout` エンドポイントは、アプリケーションに登録されている **Logout URL** を呼び出します。 この呼び出しは、サインアウトがユーザーの Web アプリから開始された場合、または別の Web アプリやブラウザーから開始された場合に行われます。 詳細については、「[シングル サインアウト](v2-protocols-oidc.md#single-sign-out)」をご覧ください。

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

RemoveAccountAsync のコードは、[Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288) から入手できます。

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません

# <a name="javatabjava"></a>[Java](#tab/java)

Java のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません

# <a name="pythontabpython"></a>[Python](#tab/python)

Python のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません

---

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Webアプリのトークンの取得](scenario-web-app-call-api-acquire-token.md)
