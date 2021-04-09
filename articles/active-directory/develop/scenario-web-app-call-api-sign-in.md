---
title: サインアウト時にトークン キャッシュからアカウントを削除する | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 9fc271dfa9edbedac8527009dd2b2180b7c5e7cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756248"
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

このシナリオの次の記事である [Web アプリのトークンの取得](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore)に関する記事に進みます。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

このシナリオの次の記事である [Web アプリのトークンの取得](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet)に関する記事に進みます。

# <a name="java"></a>[Java](#tab/java)

このシナリオの次の記事である [Web アプリのトークンの取得](./scenario-web-app-call-api-acquire-token.md?tabs=java)に関する記事に進みます。

# <a name="python"></a>[Python](#tab/python)

このシナリオの次の記事である [Web アプリのトークンの取得](./scenario-web-app-call-api-acquire-token.md?tabs=python)に関する記事に進みます。

---