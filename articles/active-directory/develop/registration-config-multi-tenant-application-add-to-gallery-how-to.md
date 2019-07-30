---
title: Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する | Microsoft Docs
description: 独自に開発したマルチテナント アプリケーションを Azure AD アプリケーション ギャラリーで表示する方法について説明します。
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8024130b986d111abe3b470d6b06e86ed6419dc4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320916"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションを追加する

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーとは

Azure Active Directory (Azure AD) は、クラウド ベースの ID サービスです。 [Azure AD アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/all/)は、Azure Marketplace アプリ ストア内にあり、シングル サインオンおよびユーザー プロビジョニング用のすべてのアプリケーション コネクタが公開されています。 ID プロバイダーとして Azure AD を使うお客様は、ここで発行されているさまざまな SaaS アプリケーション コネクタを検索します。 IT 管理者は、アプリ ギャラリーからコネクタを追加した後、シングル サインオンおよびプロビジョニング用にコネクタを構成して使います。 Azure AD は、シングル サインオン用の SAML 2.0、OpenID Connect、OAuth、WS-Fed など、主要なフェデレーション プロトコルをすべてサポートします。 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされている場合
Azure AD アプリケーション ギャラリーにマルチテナント アプリケーションの一覧を表示したい場合は、次のいずれかのシングル サインオン テクノロジがそのアプリケーションでサポートされていることを最初に確認する必要があります。

- **OpenID Connect**:アプリの一覧を表示するには、Azure AD でマルチテナント アプリケーションを作成し、アプリケーションに [Azure AD 同意フレームワーク](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)を実装します。 すべての顧客がアプリケーションへの同意を提供できるように、共通エンドポイントにログイン要求を送信します。 トークンで受け取ったテナント ID とユーザーの UPN に基づいてユーザー アクセスを制御できます。 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」で説明するプロセスを使用して、アプリケーションを送信します。

- **SAML**: アプリケーションが SAML 2.0 をサポートしている場合は、ギャラリーにアプリの一覧を表示できます。 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」の手順に従ってください。

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>SAML または OpenIDConnect がアプリケーションでサポートされていない場合
SAML または OpenIDConnect をサポートしていないアプリケーションでも、パスワード シングル サインオン テクノロジによって、アプリ ギャラリーに統合することができます。

パスワード シングル サインオン (パスワード保管とも呼ばれます) を使用すると、ID フェデレーションをサポートしない Web アプリケーションに対するユーザーのアクセスおよびパスワードを管理できます。 これは、複数のユーザーが、たとえば、組織のソーシャル メディア アプリ アカウントなどに、1 つのアカウントを共有する必要があるシナリオにも便利です。 

このテクノロジを使用してアプリケーションの一覧を表示するには:
1. HTML サインイン ページがある Web アプリケーションを作成して、[パスワード シングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)を構成します。 
2. 「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」の説明に従って、要求を送信します。

## <a name="escalations"></a>エスカレーション

すべてのエスカレーションについて、[Azure AD SSO 統合チーム](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)にメールでご連絡いただけば、できるだけ早く折り返しご連絡いたします。

## <a name="next-steps"></a>次の手順
[Azure Active Directory アプリケーション ギャラリーにアプリケーションを表示する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)方法を確認します。
