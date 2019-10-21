---
title: ユーザーをサインインさせる Web アプリ (運用環境への移行) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリをビルドする方法について説明します (運用環境への移行)
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd6717c132d32d54c16e7678695a09e35181a057
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086546"
---
# <a name="web-app-that-signs-in-users---move-to-production"></a>ユーザーをサインインさせる Web アプリ - 運用環境への移行

Web API を呼び出すトークンの取得方法が分かったので、次にそれを運用環境に移行する方法について説明します。

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>次の手順

### <a name="calling-web-apis-scenario"></a>Web API を呼び出すシナリオ

Web アプリはユーザーをサインインさせると、サインインしているユーザーの代わりに Web API を呼び出すことができます。 Web アプリから Web API を呼び出すことは、次のシナリオの目標です。

> [!div class="nextstepaction"]
> [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)

### <a name="deep-dive---aspnet-core-web-app-tutorial"></a>詳細情報 - ASP.NET Core Web アプリのチュートリアル

ASP.NET Core チュートリアル: [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) を使用してユーザーをサインインさせる他の方法について説明します。 このサンプルは、以下のアカウントでのサインインを追加する方法など、Web アプリの運用環境対応コードを使用したプログレッシブ チュートリアルです。

- 自分の組織
- 複数の組織
- 職場または学校アカウント、または個人用 Microsoft アカウント
- [Azure AD B2C](https://aka.ms/aadb2c)
- 各国のクラウド

### <a name="sample-code---java-web-app"></a>サンプル コード - Java Web アプリ

GitHub のサンプルで Java Web アプリの詳細を学習してください。[Microsoft ID プラットフォームを使用してユーザーをサインインさせ、Microsoft Graph を呼び出す Java Web アプリケーション](https://github.com/Azure-Samples/ms-identity-java-webapp)
