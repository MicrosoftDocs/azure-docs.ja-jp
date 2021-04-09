---
title: Microsoft ID プラットフォームの概要 - Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームのコンポーネントの概要と、それらを使用してアプリケーションに ID およびアクセス管理 (IAM) のサポートを組み込む方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: a4ce8242bd3110fee038ac826973e6a134413344
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426843"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Microsoft ID プラットフォームとは

Microsoft ID プラットフォームにより、開発者は、ユーザーや顧客が各自の Microsoft ID やソーシャル アカウントを使用してサインインできるアプリケーションを構築できます。また、独自の API または Microsoft Graph などの Microsoft API への承認されたアクセスを提供できます。

Microsoft ID プラットフォームは、次のようないくつかのコンポーネントで構成されています。

- 開発者が次のような種類の ID を認証できるようにする **OAuth 2.0 および OpenID Connect 標準準拠の認証サービス**
  - 職場または学校のアカウント (Azure AD を通じてプロビジョニングされます)
  - 個人用 Microsoft アカウント (Skype、Xbox、Outlook.com など)
  - ソーシャル アカウントまたはローカル アカウント (Azure AD B2C を使用)
- **オープンソース ライブラリ**:Microsoft Authentication Libraries (MSAL) およびその他の標準準拠ライブラリのサポート
- **アプリケーション管理ポータル**:Microsoft Azure portal 上での登録および構成エクスペリエンスと、その他の Azure 管理機能。
- **アプリケーション構成 API および PowerShell**:Microsoft Graph API および PowerShell を使用した、プログラムによるアプリケーションの構成。これにより、DevOps タスクを自動化できます。
- **開発者向けコンテンツ**:クイックスタート、チュートリアル、攻略ガイド、コード サンプルなどの技術ドキュメント。

開発者は Microsoft ID プラットフォームにより、パスワードレス認証、ステップアップ認証、条件付きアクセスなど、ID およびセキュリティ領域における最新の革新的技術を統合できます。 このような機能を自分で実装する必要はありません。Microsoft ID プラットフォームと統合されたアプリケーションは、このような革新的技術をネイティブに利用します。

Microsoft ID プラットフォームでは、一度コードを記述すればすべてのユーザーに対応できます。 アプリを一度ビルドすれば、多数のプラットフォームで動作させたり、リソース アプリケーション (API) と同様にクライアントとして機能するアプリを構築できます。

プラットフォームの概要ビデオと認証エクスペリエンスのデモについては、「[開発者向けの Microsoft ID プラットフォームとは](https://youtu.be/uDU1QTSw7Ps)」をご覧ください。

## <a name="getting-started"></a>作業の開始

ビルドする[アプリケーション シナリオ](authentication-flows-app-scenarios.md)を選択します。 これらの各シナリオのパスは、概要と、すばやく作業を開始するのに役立つクイックスタートへのリンクから始まります。

- [シングルページ アプリ (SPA)](scenario-spa-overview.md)
- [ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)
- [Web API を呼び出す Web アプリ](scenario-web-app-call-api-overview.md)
- [保護された Web API](scenario-protected-web-api-overview.md)
- [Web API を呼び出す Web API](scenario-web-api-call-api-overview.md)
- [デスクトップ アプリ](scenario-desktop-overview.md)
- [デーモン アプリ](scenario-daemon-overview.md)
- [モバイル アプリ](scenario-mobile-overview.md)

Microsoft IDプラットフォーム を使用してアプリに認証と承認を組み込む際に、ほとんどの一般的なアプリ シナリオとその ID コンポーネントの概要を示した次の図を参考にすることができます。 図を選択すると、フルサイズで表示されます。

[![Microsoft ID プラットフォームでのアプリケーション シナリオを示したメトロ マップ](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>認証の概念について学習する

以下の一連の推奨される記事で、認証および Azure AD の主要な概念が Microsoft ID プラットフォームにどのように適用されるかについて学習します。

- [認証の基本](./authentication-vs-authorization.md)
- [アプリケーションとサービス プリンシパル](app-objects-and-service-principals.md)
- [対象ユーザー](v2-supported-account-types.md)
- [アクセス許可と同意](v2-permissions-and-consent.md)
- [ID トークン](id-tokens.md)
- [アクセス トークン](access-tokens.md)
- [認証フローとアプリケーションのシナリオ](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>その他の ID およびアクセス管理のオプション

[Azure AD B2C](../../active-directory-b2c/overview.md) - ユーザーが自分のソーシャル アカウント (Facebook や Google など) を使用するかまたは電子メール アドレスとパスワードを使用してサインインできる、顧客向けアプリケーションをビルドします。

[Azure AD B2B](../external-identities/what-is-b2b.md) - 外部ユーザーを自分の Azure AD テナントに "ゲスト" ユーザーとして招待し、認可のためにアクセス許可を割り当てますが、認証にはユーザー自身の既存の資格情報を使用します。

[開発者向け Azure Active Directory (v1.0)](../azuread-dev/v1-overview.md) - 以前の v1.0 エンドポイントを使用する既存のアプリがある開発者向けに、ここに表示されます。 新しいプロジェクトには、v1.0 を使用 **しないでください**。

## <a name="next-steps"></a>次のステップ

Azure アカウントをお持ちの場合は既に Azure Active Directory テナントへのアクセス権がありますが、ほとんどの Microsoft ID プラットフォーム開発者の方は、アプリケーションの開発時に使用する自分用の Azure AD テナント、"dev テナント" を用意する必要があります。

アプリケーションのビルド時に使用する自分用のテナントを作成する方法を学習します。

[クイック スタート: Azure AD テナントを設定する](quickstart-create-new-tenant.md)
