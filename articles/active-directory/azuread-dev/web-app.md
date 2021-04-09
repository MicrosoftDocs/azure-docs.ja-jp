---
title: Azure Active Directory の Web アプリ
description: Web アプリについて、およびこの種のアプリのプロトコル フロー、登録、およびトークンの有効期限の基本について説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154408"
---
# <a name="web-apps"></a>Web Apps

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Web アプリとは、Web ブラウザーで Web アプリケーションに対してユーザーを認証するアプリケーションです。 このシナリオでは、ユーザーが Azure AD にサインインするよう、Web アプリケーションがユーザーのブラウザーに指示します。 Azure AD は、ユーザーのブラウザーを介してサインイン応答を返します。この応答のセキュリティ トークンには、ユーザーに関する要求が含まれています。 このシナリオは、OpenID Connect、SAML 2.0、WS-Federation の各プロトコルを使用したサインオンをサポートします。

## <a name="diagram"></a>ダイアグラム

![ブラウザー対 Web アプリケーションの認証フロー](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>プロトコル フロー

1. ユーザーがアプリケーションにアクセスしたときに、サインインする必要がある場合、サインイン要求によって Azure AD の認証エンドポイントにリダイレクトされます。
1. ユーザーがサインイン ページでサインインします。
1. 認証が成功すると、Azure AD は認証トークンを作成し、Microsoft Azure Portal で構成されたアプリケーションの応答 URL にサインイン応答を返します。 実稼働アプリケーションでは、この応答 URL は HTTPS である必要があります。 返されたトークンには、トークンを検証するためにアプリケーションが必要とする、ユーザーと Azure AD に関する要求が含まれています。
1. アプリケーションは、Azure AD のフェデレーション メタデータ ドキュメントで入手できる公開署名キーと発行者情報を使用してトークンを検証します。 アプリケーションでのトークンの検証後に、ユーザーとの新しいセッションが開始されます。 このセッションにより、ユーザーはセッションの有効期限が切れるまでアプリケーションにアクセスできます。

## <a name="code-samples"></a>コード サンプル

Web ブラウザー対 Web アプリケーションのシナリオのコード サンプルをご覧ください。 新しいサンプルが頻繁に追加されているので、頻繁に確認してください。

## <a name="app-registration"></a>アプリの登録

Web アプリを登録するには、[アプリの登録](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)に関するページをご覧ください。

* シングル テナント: 自分の組織だけが使用するアプリケーションを構築している場合は、Azure portal を使用して、アプリケーションを会社のディレクトリに登録する必要があります。
* マルチテナント - 自分の組織の外部のユーザーが使用できるアプリケーションを構築している場合は、アプリケーションを会社のディレクトリに登録するだけでなく、そのアプリケーションを使用する各組織のディレクトリにも登録する必要があります。 ディレクトリ内でアプリケーションを使用できるようにするには、ユーザーがアプリケーションに同意できるようにするためのサインアップ プロセスを含めます。 ユーザーがアプリケーションにサインアップするときに、アプリケーションが必要とするアクセス許可と同意オプションを示すダイアログが表示されます。 必要なアクセス許可によっては、他の組織の管理者が同意することが必要な場合があります。 ユーザーまたは管理者が同意すると、アプリケーションがディレクトリに登録されます。

## <a name="token-expiration"></a>トークンの有効期限

Azure AD によって発行されたトークンの有効期間が終了すると、ユーザーのセッションが期限切れになります。 アプリケーションは、アイドル状態の時間に基づくユーザーのサインアウトなど、必要に応じてこの期間を短縮できます。 セッションの有効期限が切れると、ユーザーは再度サインインするよう求められます。

## <a name="next-steps"></a>次のステップ

* その他の[アプリケーションの種類とシナリオ](app-types.md)について学習する
* Azure AD [認証の基本](v1-authentication-scenarios.md)について学習する
