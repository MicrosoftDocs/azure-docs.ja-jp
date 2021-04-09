---
title: Azure AD アプリケーション ギャラリーにマルチテナント アプリを追加する
description: 独自に開発したマルチテナント アプリケーションを Azure AD アプリケーション ギャラリーで表示する方法について説明します。
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ROBOTS: NOINDEX
ms.openlocfilehash: de0231a49c4f806660ea0cb305fdc1a70e2b36d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98063129"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

Azure Active Directory (Azure AD) は、クラウド ベースの ID サービスです。 [Azure AD アプリケーション ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)は、Azure Marketplace アプリ ストア内にあり、シングル サインオンおよびユーザー プロビジョニング用のすべてのアプリケーション コネクタが公開されています。 ID プロバイダーとして Azure AD を使うお客様は、ここで発行されているさまざまな SaaS アプリケーション コネクタを検索します。 IT 管理者は、アプリ ギャラリーからコネクタを追加した後、シングル サインオンおよびプロビジョニング用にコネクタを構成して使います。 Azure AD は、シングル サインオン用の SAML 2.0、OpenID Connect、OAuth、WS-Fed など、主要なフェデレーション プロトコルをすべてサポートします。 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされている場合
Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションの一覧を表示したい場合は、次のいずれかのシングル サインオン テクノロジがそのアプリケーションでサポートされていることを最初に確認する必要があります。

- **OpenID Connect**:アプリの一覧を表示するには、Azure AD でマルチテナント アプリケーションを作成し、アプリケーションに [Azure AD 同意フレームワーク](./consent-framework.md)を実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](./v2-howto-app-gallery-listing.md)」で説明するプロセスを使用して、アプリケーションを送信します。

- **SAML**: アプリケーションが SAML 2.0 をサポートしている場合は、ギャラリーにアプリの一覧を表示できます。 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](./v2-howto-app-gallery-listing.md)」の手順に従ってください。

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされていない場合
SAML または OpenIDConnect をサポートしていないアプリケーションでも、パスワード シングル サインオン テクノロジによって、アプリ ギャラリーに統合することができます。

パスワード シングル サインオン (パスワード保管とも呼ばれます) を使用すると、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

このテクノロジを使用してアプリケーションの一覧を表示するには:
1. HTML サインイン ページがある Web アプリケーションを作成して、[パスワード シングル サインオン](../manage-apps/what-is-single-sign-on.md)を構成します。 
2. 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](./v2-howto-app-gallery-listing.md)」の説明に従って、要求を送信します。

## <a name="escalations"></a>エスカレーション

すべてのエスカレーションについて、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にメールでご連絡いただけば、できるだけ早く折り返しご連絡いたします。

## <a name="next-steps"></a>次のステップ
[Azure Active Directory アプリケーション ギャラリーにアプリケーションを表示する](./v2-howto-app-gallery-listing.md)方法を確認します。