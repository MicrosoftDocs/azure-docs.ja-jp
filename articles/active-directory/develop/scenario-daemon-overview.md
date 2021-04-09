---
title: Web API を呼び出すデーモン アプリのビルド | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すデーモン アプリをビルドする方法について学習します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/31/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 00a70b585ddf522a25e81703fe5bdf55efbcb7e1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582776"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>シナリオ:Web API を呼び出すデーモン アプリケーション

Web API を呼び出すデーモン アプリをビルドするために必要なすべてのことについて学習します。

## <a name="overview"></a>概要

アプリケーションは、(ユーザーの代わりではなく) 自身の代わりに、Web API を呼び出すトークンを取得できます。 このシナリオでは、デーモン アプリケーションに役立ちます。 これには標準の OAuth 2.0 [クライアント資格情報](v2-oauth2-client-creds-grant-flow.md)の付与が使用されます。

![デーモン アプリ](./media/scenario-daemon-app/daemon-app.svg)

デーモン アプリのユース ケースの例をいくつか次に示します。

- プロビジョニングまたはユーザーの管理に使用される、またはディレクトリ内でプロセスのバッチ処理を行う Web アプリケーション
- バッチ ジョブ (バックグラウンドで実行されているオペレーティング システム サービス) を実行するデスクトップ アプリケーション (Windows 上の Windows サービスや Linux 上のデーモン プロセスなど)
- 特定のユーザーではなく、ディレクトリを操作する必要がある Web API

デーモン以外のアプリケーションがクライアントの資格情報を使用する一般的なケースがもう 1 つあります。これらのアプリケーションがユーザーの代わりに動作している場合でも、技術的な理由から、アプリケーションは自身の ID を使用して Web API またはリソースにアクセスする必要があります。 Azure Key Vault 内のシークレットまたはキャッシュのための Azure SQL Database へのアクセスはその一例です。

自身の ID 用にトークンを取得するアプリケーションは:

- 機密性の高いクライアント アプリケーションです。 これらのアプリがユーザーとは無関係にリソースにアクセスする場合には、自身の ID を証明する必要があります。 また、これらは機密性の高いアプリでもあります。 Azure Active Directory (Azure AD) テナント管理者によって承認される必要があります。
- Azure AD に登録されたシークレット (アプリケーション パスワードまたは証明書) を持っています。 このシークレットは、トークンを取得するために Azure AD への呼び出し中に渡されます。

## <a name="specifics"></a>詳細

> [!IMPORTANT]
>
> - ユーザーは、デーモン アプリケーションと対話できません。 デーモン アプリケーションには独自の ID が必要です。 この種のアプリケーションは、アプリケーション ID を使用し、アプリケーション ID、資格情報 (パスワードまたは証明書)、アプリケーション ID の URI を Azure AD に提示して、アクセス トークンを要求します。 認証が成功すると、デーモンは Microsoft ID プラットフォームからアクセス トークン (および更新トークン) を受け取ります。 このトークンは、Web API の呼び出しに使用されます (必要に応じて更新されます)。
> - ユーザーはデーモン アプリケーションと対話できないため、増分の同意を実行できません。 必要なすべての API アクセス許可は、アプリケーションの登録時に構成する必要があります。 このアプリケーションのコードでは、静的に定義されたアクセス許可のみが要求されます。 これは、デーモン アプリケーションが増分同意をサポートしないことも意味します。

開発者にとって、このシナリオのエンドツーエンドのエクスペリエンスには、次の側面があります。

- デーモン アプリケーションは Azure AD テナントでのみ機能します。 Microsoft の個人アカウントを操作しようとするデーモン アプリケーションをビルドする意味はありません。 基幹業務 (LOB) アプリの開発者の場合は、自分のテナント内でデーモン アプリを作成します。 ISV の場合は、マルチテナントのデーモン アプリケーションを作成した方がよいでしょう。 各テナント管理者は、同意を示す必要があります。
- [アプリケーションの登録](./scenario-daemon-app-registration.md)時に、応答 URI は必要ありません。 Azure AD でシークレット、証明書、または署名付きアサーションを共有します。 また、アプリケーションのアクセス許可を要求し、それらのアプリのアクセス許可を使用できるように管理者の同意を与える必要があります。
- [アプリケーションの構成](./scenario-daemon-app-configuration.md)で、アプリケーションの登録時に Azure AD と共有されるようにクライアントの資格情報を提供する必要があります。
- クライアント資格情報フローでトークンを取得するために使用される[スコープ](scenario-daemon-acquire-token.md#scopes-to-request)は、静的スコープである必要があります。

## <a name="recommended-reading"></a>推奨資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事「[アプリの登録](./scenario-daemon-app-registration.md)」に進みます。
