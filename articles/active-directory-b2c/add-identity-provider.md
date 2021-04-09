---
title: ID プロバイダーの追加 - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C テナントに ID プロバイダーを追加する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 03/03/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: dbb2aeefc22fae4a3c83956dd7681b49fc435500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033998"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントへの ID プロバイダーの追加

ユーザーが外部のソーシャル ID プロバイダー (IDP) またはエンタープライズ ID プロバイダーの資格情報を使用してアプリケーションにサインインできるように、Azure AD B2C を構成できます。 Azure AD B2C では、Facebook、Microsoft アカウント、Google、Twitter などの外部 ID プロバイダー、および OAuth 1.0、OAuth 2.0、OpenID Connect、SAML の各プロトコルに対応するすべての ID プロバイダーがサポートされています。

外部 ID プロバイダーのフェデレーションを使用すると、アプリケーション用だけに新しいアカウントを作成する必要なしに、既存のソーシャルまたはエンタープライズ アカウントを使用してサインインする機能を、コンシューマーに提供できます。

サインアップまたはサインイン ページには、Azure AD B2C によって、ユーザーがサインインのために選択できる外部 ID プロバイダーの一覧が表示されます。 外部 ID プロバイダーの 1 つを選択すると、選択したプロバイダーの Web サイトに誘導 (リダイレクト) されるので、そこでサインイン プロセスを完了します。 正常にサインインしたユーザーは、お使いのアプリケーションでアカウントを認証するために、Azure AD B2C に戻されます。

![ソーシャル アカウント (Facebook) でのモバイル サインインの例](media/add-identity-provider/external-idp.png)

Azure portal を使用して、Azure Active Directory B2C (Azure AD B2C) によってサポートされる ID プロバイダーを[ユーザー フロー](user-flow-overview.md)に追加できます。 [カスタム ポリシー](custom-policy-get-started.md)に ID プロバイダーを追加することもできます。

## <a name="select-an-identity-provider"></a>ID プロバイダーの選択

通常、アプリケーションでは ID プロバイダーを 1 つだけ使用しますが、さらに追加することもできます。 以下の操作方法に関する記事では、ID プロバイダー アプリケーションを作成し、その ID プロバイダーをテナントに追加し、ユーザー フローまたはカスタム ポリシーに ID プロバイダーを追加する方法について説明します。

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (シングルテナント)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (マルチテナント)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [Facebook](identity-provider-facebook.md)
* [汎用の ID プロバイダー](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft アカウント](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (SAML プロトコル)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
