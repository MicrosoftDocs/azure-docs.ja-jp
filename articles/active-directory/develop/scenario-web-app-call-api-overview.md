---
title: Web API を呼び出す Web アプリの構築 | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出す Web アプリ を構築する方法 (概要) について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1fdbdada54320ef28f6a4b04a7f415c835acc9dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756288"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>シナリオ:Web API を呼び出す Web アプリ

Microsoft ID プラットフォーム上でユーザーがサインインし、サインインしたユーザーに代わって Web API を呼び出す Web アプリを構築する方法について説明します。

## <a name="prerequisites"></a>前提条件

このシナリオでは、次を既に完了していることを前提としています。「[シナリオ:ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)」を参照してください。

## <a name="overview"></a>概要

お使いの Web アプリに認証を追加することで、サインインしているユーザーに代わってユーザーにサインインし、Web API を呼び出すことができるようにします。

![Web API を呼び出す Web アプリ](./media/scenario-webapp/web-app.svg)

Web API を呼び出す Web アプリは、機密クライアント アプリケーションです。
そのため、Azure Active Directory (Azure AD) を使用してシークレット (アプリケーション パスワードまたは証明書) を登録することになります。 このシークレットは、トークンを取得するために Azure AD への呼び出し中に渡されます。

## <a name="specifics"></a>詳細

> [!NOTE]
> Web アプリにサインインを追加するということは、Web アプリ自体を保護することです。 この保護は、Microsoft Authentication Library (MSAL) ではなく *ミドルウェア* ライブラリを使用して実現されます。 前述のシナリオでは、[ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)でこれを説明していました。
>
> このシナリオでは、Web アプリから Web API を呼び出す方法について説明します。 これらの Web API のアクセス トークンを取得する必要があります。 MSAL ライブラリを使用してこれらのトークンを取得します。

このシナリオの開発には、次の特定のタスクが含まれます。

- [アプリケーションの登録](scenario-web-app-call-api-app-registration.md)中に、Azure AD と共有する応答 URI、シークレット、または証明書を指定する必要があります。 複数の場所にアプリをデプロイする場合は、場所ごとに応答 URI を入力します。
- [アプリケーションの構成](scenario-web-app-call-api-app-configuration.md)で、アプリケーションの登録時に Azure AD と共有されたクライアント資格情報を提供する必要があります。

## <a name="recommended-reading"></a>推奨資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](scenario-web-app-call-api-app-registration.md)」に進みます。