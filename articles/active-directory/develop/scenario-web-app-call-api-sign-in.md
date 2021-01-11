---
title: サインアウト時にトークン キャッシュからアカウントを削除する - Microsoft ID プラットフォーム | Azure
description: サインアウト時にトークン キャッシュからアカウントを削除する方法について説明します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5246100815fde569c55027a555464c44a240d4b3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88119999"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Web API を呼び出す Web アプリ:グローバル サインアウト時にトークン キャッシュからアカウントを削除する

[ユーザーをサインインさせる Web アプリ:サインインとサインアウト](scenario-web-app-sign-user-sign-in.md) で、Web アプリにサインインを追加する方法を学びました。

サインアウトは、Web API を呼び出す Web アプリでは異なります。 ユーザーがアプリケーションまたは任意のアプリケーションからサインアウトするときに、トークン キャッシュからそのユーザーに関連付けられているトークンを削除する必要があります。

## <a name="intercept-the-callback-after-single-sign-out"></a>シングル サインアウト後にコールバックをインターセプトする

サインアウトしたアカウントに関連付けられているトークン キャッシュ エントリをクリアするには、アプリケーションで `logout` 後のイベントを受け取ることができます。 Web アプリは、各ユーザーのアクセス トークンをトークン キャッシュに格納します。 Web アプリケーションは、`logout` 後のコールバックをインターセプトすることにより、キャッシュからユーザーを削除できます。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft.Identity.Web では、サインアウトの実装が自動処理されます。 詳細については、[Microsoft.Identity.Web のソース コード](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)を参照してください。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

# <a name="java"></a>[Java](#tab/java)

Java のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

# <a name="python"></a>[Python](#tab/python)

Python のサンプルでは、グローバル サインアウト時にアカウントはキャッシュから削除されません。

---

## <a name="next-steps"></a>次のステップ

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](./scenario-web-app-call-api-acquire-token.md?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Web アプリのトークンを取得する](./scenario-web-app-call-api-acquire-token.md?tabs=python)

---