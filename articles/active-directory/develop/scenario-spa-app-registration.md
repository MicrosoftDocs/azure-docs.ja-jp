---
title: シングル ページ アプリケーションを登録する - Microsoft ID プラットフォーム | Azure
description: シングルページ アプリケーションを構築する方法 (アプリの登録) について説明します
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03115db0ad286c7a5c24590906d8e3715e43bac7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962442"
---
# <a name="single-page-application-app-registration"></a>シングルページ アプリケーション:アプリの登録

このページでは、シングルページ アプリケーション (SPA) におけるアプリの登録の特性について説明します。

[Microsoft ID プラットフォームに新しいアプリケーションを登録する](quickstart-register-app.md)手順に従って、お使いのアプリケーションでサポートされているアカウントを選択します。 SPA のシナリオでは、組織のアカウント、または任意の組織もしくは個人の Microsoft アカウントでの認証をサポートできます。

次に、シングルページ アプリケーションに適用されるアプリケーションの登録の特定の側面について説明します。

## <a name="register-a-redirect-uri"></a>リダイレクト URI の登録

暗黙的フローでは、Web ブラウザーで実行されているシングルページ アプリケーションにリダイレクトでトークンを送信します。 そのため、アプリケーションがトークンを受け取ることができるリダイレクト URI を登録することが重要になります。 リダイレクト URI が、お使いのアプリケーションの URI と完全に一致することを確認してください。

[Azure portal](https://go.microsoft.com/fwlink/?linkid=2083908) で、登録したアプリケーションにアクセスします。 アプリケーションの **[認証]** ページで、**Web** プラットフォームを選択します。 **[リダイレクト URI]** フィールドに、アプリケーションのリダイレクト URI の値を入力します。

## <a name="enable-the-implicit-flow"></a>暗黙的フローの有効化

同じ **[認証]** ページ上の **[詳細設定]** で、 **[暗黙的な許可]** も有効にする必要があります。 アプリケーションがユーザーをサインインさせて、ID トークンを取得するだけの場合は、 **[ID トークン]** チェック ボックスを選択するだけで十分です。

お使いのアプリケーションで、API を呼び出すためにアクセス トークンも取得する必要がある場合は、必ず **[アクセス トークン]** チェック ボックスも選択してください。 詳細については、「[ID トークン](./id-tokens.md)」と「[アクセス トークン](./access-tokens.md)」を参照してください。

## <a name="api-permissions"></a>API のアクセス許可

シングルページ アプリケーションでは、サインインしたユーザーの代わりに API を呼び出すことができます。 委任されたアクセス許可を要求する必要があります。 詳細については、「[Web API にアクセスするためのアクセス許可を追加する](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)」を参照してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-spa-app-configuration.md)
