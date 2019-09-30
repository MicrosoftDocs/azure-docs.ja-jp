---
title: Web API を呼び出す Web アプリ (アプリのトークンの取得) | Microsoft ID プラットフォーム
description: Web API を呼び出す Web アプリを構築する方法について説明します (アプリのトークンの取得)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860627"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Web API を呼び出す Web アプリ - アプリのトークンの取得

クライアント アプリケーション オブジェクトを構築したので、次にそれを使用して、Web API を呼び出すためのトークンを取得します。 ASP.NET または ASP.NET Core では、Web API の呼び出しはコントローラーで実行されます。 詳細は次のとおりです。

- トークン キャッシュを使用して Web API のトークンを取得します。 このトークンを取得するには、`AcquireTokenSilent` を呼び出します。
- アクセス トークンを使用して、保護された API を呼び出します。

## <a name="aspnet-core"></a>ASP.NET Core

コントローラー メソッドは、ユーザーが Web アプリを使用するために認証されるようにする `[Authorize]` 属性によって保護されます。 以下に、Microsoft Graph を呼び出すコードを示します。

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

以下に、Microsoft Graph を呼び出すトークンを取得する、HomeController のアクションの簡略化されたコードを示します。

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

このシナリオに必要なコードをさらに詳しく理解したい場合は、[ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) チュートリアルのフェ ーズ 2 の「[2-1 Web App Calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)」の手順を参照してください。

次のように、さらに複雑な操作があります。

- Web アプリのトークン キャッシュを実装する (チュートリアルではいくつかの実装を提示しています)
- ユーザーがサインアウトするときにキャッシュからアカウントを削除する
- 増分同意を持つことを含め複数の API を呼び出す

## <a name="aspnet"></a>ASP.NET

ASP.NET でも同様です。

- [Authorize] 属性によって保護されたコントローラー アクションは、コントローラーの `ClaimsPrincipal` メンバーのテナント ID とユーザー ID を抽出します。 (ASP.NET は `HttpContext.User` を使用します。)
- そこから、MSAL.NET `IConfidentialClientApplication` を構築します。
- 最後に、機密クライアント アプリケーションの `AcquireTokenSilent` メソッドを呼び出します。

コードは ASP.NET Core で示されたコードと同様です。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Web API を呼び出す](scenario-web-app-call-api-call-api.md)
