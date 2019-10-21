---
title: Web API を呼び出すデーモン アプリ (概要) - Microsoft ID プラットフォーム
description: Web API を呼び出すデーモン アプリをビルドする方法について学習します
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae0912203f2427694d2a9b8611966a55e1e6889e
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056394"
---
# <a name="scenario-daemon-application-that-calls-web-apis"></a>シナリオ: Web API を呼び出すデーモン アプリケーション

Web API を呼び出すデーモン アプリをビルドするために必要なすべてのことについて学習します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="overview"></a>概要

アプリケーションは、(ユーザーの代わりではなく) 自身の代わりに、Web API を呼び出すトークンを取得できます。 このシナリオでは、デーモン アプリケーションに役立ちます。 標準の OAuth 2.0 [クライアント資格情報](v2-oauth2-client-creds-grant-flow.md)の付与を使用しています。

![デーモン アプリ](./media/scenario-daemon-app/daemon-app.svg)

デーモン アプリのユース ケースの例をいくつか次に示します。

- プロビジョニングまたはユーザーの管理に使用される、またはディレクトリ内でプロセスのバッチ処理を行う Web アプリケーション
- バッチ ジョブ (バックグラウンドで実行されているオペレーティング システム サービス) を実行するデスクトップ アプリケーション (Windows 上の Windows サービスや Linux 上のデーモン プロセスなど)
- 特定のユーザーではなく、ディレクトリを操作する必要がある Web API

デーモン以外のアプリケーションがクライアントの資格情報を使用する一般的なケースがもう 1 つあります。これらのアプリケーションがユーザーの代わりに動作している場合でも、技術的な理由から、アプリケーションは自身の ID を使用して Web API またはリソースにアクセスする必要があります。 KeyVault 内のシークレットまたはキャッシュのための Azure SQL データベースへのアクセスはその一例です。

自身の ID 用にトークンを取得するアプリケーションは:

- 機密性の高いクライアント アプリケーションです。 これらのアプリがユーザーとは無関係にリソースにアクセスする場合には、自身の ID を証明する必要があります。 これらはかなり機密性の高いアプリでもあるため、Azure Active Directory (Azure AD) テナントの管理者によって承認される必要があります。
- Azure AD に登録されたシークレット (アプリケーション パスワードまたは証明書) を持っています。 このシークレットは、トークンを取得するために Azure AD への呼び出し中に渡されます。

## <a name="specifics"></a>詳細

> [!IMPORTANT]
>
> - ユーザーの操作は、デーモン アプリケーションでは不可能です。 デーモン アプリケーションには独自の ID が必要です。 この種のアプリケーションは、そのアプリケーション ID を使用し、そのアプリケーション ID、資格情報 (パスワードまたは証明書)、アプリケーション ID の URI を Azure AD に提示して、アクセス トークンを要求します。 認証に成功すると、デーモンは Microsoft ID プラットフォーム エンドポイントからアクセス トークン (および更新トークン) を受信します。このトークンはその後、Web API を呼び出すために使用されます (および必要に応じて更新されます)。
> - ユーザーの操作ができないため、増分同意はできません。 必要なすべての API のアクセス許可は、アプリケーションの登録時に構成する必要があります。アプリケーションのコードは、静的に定義されたアクセス許可のみを要求します。 これは、デーモン アプリケーションが増分同意をサポートしないことも意味します。

開発者にとって、このシナリオのエンドツーエンドのエクスペリエンスには、次の側面があります。

- デーモン アプリケーションは、Azure AD テナントでのみ機能します。 Microsoft の個人アカウントを操作しようとするデーモン アプリケーションをビルドする意味はありません。 基幹業務 (LOB) アプリの開発者の場合は、自分のテナント内でデーモン アプリを作成します。 ISV の場合は、マルチ テナントのデーモン アプリケーションを作成した方がよいでしょう。 各テナントの管理者が同意する必要があります。
- [アプリケーションの登録](./scenario-daemon-app-registration.md)時に、**応答 URI** は必要ありません。 シークレット、証明書、または署名付きアサーションを Azure AD と共有する必要があり、アプリケーションのアクセス許可を要求し、それらのアプリのアクセス許可を使用するように管理者の同意を付与する必要があります。
- [アプリケーションの構成](./scenario-daemon-app-configuration.md)で、アプリケーションの登録時に Azure AD と共有されるようにクライアントの資格情報を提供する必要があります。
- クライアント資格情報フローでトークンを取得するために使用される[スコープ](scenario-daemon-acquire-token.md#scopes-to-request)は、静的スコープである必要があります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [デーモン アプリ - アプリの登録](./scenario-daemon-app-registration.md)
