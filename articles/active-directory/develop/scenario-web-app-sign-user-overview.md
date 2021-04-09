---
title: Web アプリからユーザーをサインインさせる | Azure
titleSuffix: Microsoft identity platform
description: ユーザーをサインインさせる Web アプリを構築する方法 (概要) について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dbd4923dd2843ab2c043da69f1b72efd8e1eb701
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573049"
---
# <a name="scenario-web-app-that-signs-in-users"></a>シナリオ:ユーザーをサインインさせる Web アプリ

Microsoft ID プラットフォームを使用してユーザーをサインインさせる Web アプリを構築するために必要なすべてのことについて説明します。

## <a name="getting-started"></a>作業の開始

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ユーザーをサインインさせる最初のポータブル (ASP.NET Core) Web アプリを作成する場合は、このクイックスタートに従います。

[クイック スタート: ユーザーをサインインさせる ASP.NET Core Web アプリ](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

既存の ASP.NET Web アプリケーションにサインインを追加する方法を理解したい場合は、次のクイックスタートをお試しください。

[クイック スタート: ユーザーをサインインさせる ASP.NET Web アプリ](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Java 開発者の方は、次のクイックスタートをお試しください。

[クイック スタート:Java Web アプリに "Microsoft でサインイン" を追加する](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Python を使用して開発する場合は、次のクイックスタートをお試しください。

[クイック スタート: Python Web アプリに Microsoft でサインインを追加する](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>概要

Web アプリに認証を追加して、ユーザーがサインインできるようにします。 認証を追加すると、ユーザーのエクスペリエンスをカスタマイズするために、限られたプロファイル情報に Web アプリがアクセスできるようになります。

Web アプリでは、Web ブラウザーでユーザーを認証します。 このシナリオでは、Web アプリが、ユーザーを Azure Active Directory (Azure AD) にサインインさせるよう、ユーザーのブラウザーに指示します。 Azure AD は、ユーザーのブラウザーを介してサインイン応答を返します。この応答のセキュリティ トークンには、ユーザーに関する要求が含まれています。 ユーザーのサインインでは、ミドルウェアの[ライブラリ](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)を使用して簡素化された [Open ID Connect](./v2-protocols-oidc.md) の標準プロトコルを利用します。

![Web アプリによるユーザーのサインイン](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

第 2 フェーズとして、アプリケーションで、サインインしたユーザーの代わりに Web API を呼び出すことができます。 この第 2 フェーズは別のシナリオであり、[Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)に関するページでご確認いただけます。

> [!NOTE]
> Web アプリにサインインを追加することは、Web アプリの保護と、**ミドルウェア** のライブラリが行うユーザー トークンの検証に関連します。 .NET の場合、このシナリオでは、保護された API を呼び出すためのトークンを取得することに関連する Microsoft 認証ライブラリ (MSAL) はまだ必要ありません。 認証ライブラリは、Web アプリで Web API を呼び出す必要がある場合に、フォローアップ シナリオで導入されます。

## <a name="specifics"></a>詳細

- アプリケーションの登録時、1 つまたは複数 (アプリを複数の場所にデプロイする場合) の応答 URI を提供する必要があります。 一部のケース (ASP.NET および ASP.NET Core) では、ID トークンを有効にする必要があります。 最終的に、アプリケーションがユーザーのサインアウトに反応できるように、サインアウト URI を設定する必要が生じることがあります。
- アプリケーションのコードでは、Web アプリがサインインを委任する機関を指定する必要があります。 トークンの検証をカスタマイズする必要が生じる場合があります (パートナー シナリオでは特に)。
- Web アプリケーションでは、さまざまなアカウントの種類をサポートしています。 詳細については、「[サポートされているアカウントの種類](v2-supported-account-types.md)」を参照してください。

## <a name="recommended-reading"></a>推奨資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>次のステップ

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

このシナリオの次の記事「[アプリの登録](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore)」に進みます。

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

このシナリオの次の記事「[アプリの登録](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet)」に進みます。

# <a name="java"></a>[Java](#tab/java)

このシナリオの次の記事「[アプリの登録](./scenario-web-app-sign-user-app-registration.md?tabs=java)」に進みます。

# <a name="python"></a>[Python](#tab/python)

このシナリオの次の記事「[アプリの登録](./scenario-web-app-sign-user-app-registration.md?tabs=python)」に進みます。

---
