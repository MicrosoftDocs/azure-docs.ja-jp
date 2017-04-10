---
title: "SaaS アプリと Azure Active Directory シングル サインオンを統合する | Microsoft Docs"
description: "Azure Active Directory での SaaS アプリのシングル サインオン認証およびユーザー プロビジョニングによるアクセス管理の一元化を有効にします。 Azure Active Directory を SaaS アプリに統合する方法の概要について説明します。"
services: active-directory
keywords: "Azure AD と SaaS アプリの統合"
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 53b9d341-d1fc-4bbb-ac7c-3f4c68fcf00a
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: e4896c1a3adf76477899336f1ff1ee215cc7c9aa
ms.openlocfilehash: a451ab54144222676e8063b764a6d22f44a690d7


---
# <a name="integrate-azure-active-directory-single-sign-on-with-saas-apps"></a>SaaS アプリと Azure Active Directory シングル サインオンを統合する
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-enterprise-apps-manage-sso.md)
> * [Azure クラシック ポータル](active-directory-sso-integrate-saas-apps.md)
>
>

[!INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

組織に展開するアプリのためのシングル サインオンのセットアップ作業では、Azure Active Directory (Azure AD) 内にある既存のディレクトリを使用します。 Microsoft Azure、Office 365、または Windows Intune を通じて取得した Azure AD ディレクトリを使用できます。 これらのうち&2; つ以上を所有している場合は、「 [Azure AD ディレクトリの管理](active-directory-administer.md) 」を参照して、どれを使用するかを判断してください。

## <a name="authentication"></a>認証
プロトコル SAML 2.0、Ws-federation、または OpenID Connect をサポートしているアプリケーションは、Azure Active Directory を使用して署名証明書を信頼関係を確立できます。 詳細については、「 [フェデレーション シングル サインオンのための証明書の管理](active-directory-sso-certs.md)」を参照してください。

HTML フォーム ベースのサインインのみをサポートするアプリケーションの場合、Azure Active Directory では "パスワード保管" を使用して信頼関係を確立します。 これにより、Azure AD は SaaS アプリケーションから取得したユーザー アカウント情報を使用して、組織のユーザーを SaaS アプリケーションに自動的にサインインします。 ユーザー アカウント情報および関連するパスワードは Azure AD によって収集され、安全に格納されます。 詳細については、「 [パスワードベースのシングル サインオン](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)」を参照してください。

## <a name="authorization"></a>承認
プロビジョニングされたアカウントを使用すると、ユーザーがシングル サインオンを介して認証された後にアプリケーションの使用を許可できます。 ユーザーのプロビジョニングは手動で実行できます。また、Azure Active Directory で行われた変更に基づいて、SaaS アプリケーションに対してユーザー情報の追加や削除を実行できる場合もあります。 既存の Azure AD コネクタを使用して自動プロビジョニングを実現する方法の詳細については、[SaaS アプリケーションへのユーザーのプロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)に関するページを参照してください。

それ以外の場合は、アプリにユーザー情報を手動で追加するか、市販されている他のプロビジョニング ソリューションを使用できます。

## <a name="access"></a>アクセス
Azure AD には、組織内のエンド ユーザーにアプリケーションをデプロイするためのカスタマイズ可能な方法が複数用意されています。 特定のデプロイ ソリューションやアクセス ソリューションに限定されているわけではありません。 [ニーズに合わせて最適なソリューション](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)を使用できます。

## <a name="additional-considerations-for-applications-already-in-use"></a>使用中のアプリケーションに関する追加の考慮事項
組織が既に使用しているアプリケーションにシングル サインオンをセットアップするプロセスは、新しいアプリケーションに新しいアカウントを作成するプロセスとは異なります。 いくつかの準備手順: アプリケーション内のユーザー ID を Azure AD の ID にマップする、統合後にユーザーがアプリケーションへのログインを経験する方法を把握する、など。

> [!NOTE]
> 既存のアプリケーションの SSO を設定するには、Azure AD と SaaS アプリケーションの両方でグローバル管理者権限が必要です。
>
>

### <a name="mapping-user-accounts"></a>ユーザー アカウントのマップ
ユーザー ID には、通常、一意の識別子が含まれます。それには電子メール アドレスまたはユーザー プリンシパル名 (UPN) が使用可能です。 ユーザーの各アプリケーション ID は、ユーザーの各 Azure AD ID にリンク (マップ) する必要があります。 アプリケーション認証の要件に応じて、これを実現する方法はいくつかあります。

アプリケーション ID と Azure AD ID のマッピングの詳細については、[SAML トークンで発行される要求のカスタマイズに関するページ](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx)と、「[プロビジョニングにおける属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)」をご覧ください。

### <a name="understanding-the-users-log-in-experience"></a>ユーザーのログイン エクスペリエンスの理解
既に使用しているアプリケーションの SSO を統合するときは、ユーザー エクスペリエンスが影響を受けることを認識しておくことが重要です。 すべてのアプリケーションで、ユーザーは Azure AD の資格情報を使用してサインインすることになります。 また、アプリケーションにアクセスする際に別のポータルを使用することが必要な場合もあります。

アプリケーションのサインイン インターフェイスで SSO を実行できるアプリケーションもあれば、ユーザーが中央ポータル ([マイ アプリ](http://myapps.microsoft.com)や [Office365](http://portal.office.com/myapps) など) からサインインする必要があるアプリケーションもあります。 さまざまな種類の SSO とそのユーザー エクスペリエンスの詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

「 *開発者ガイド* 」の「 [ユーザーの同意の抑制](active-directory-applications-guiding-developers-for-lob-applications.md) 」も参照してください。

## <a name="next-steps"></a>次のステップ
アプリケーション ギャラリーにある SaaS アプリについては、 [SaaS アプリを統合する方法に関するチュートリアル](active-directory-saas-tutorial-list.md)が Azure AD 内に多数用意されています。

アプリケーション ギャラリーにないアプリは、 [カスタム アプリケーションとして Azure AD アプリケーション ギャラリーに追加](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)できます。

Azure.com ライブラリには、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をはじめとして、ここで紹介した問題に関する詳細情報があります。

## <a name="next-steps"></a>次のステップ
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


