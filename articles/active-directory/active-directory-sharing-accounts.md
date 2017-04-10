---
title: "Azure AD を使用したアカウントの共有 | Microsoft Docs"
description: "Azure Active Directory を使用してオンプレミスのアプリケーションおよびコンシューマー クラウド サービス用のアカウントを組織で安全に共有できるようにする方法について説明します。"
services: active-directory
documentationcenter: 
author: msStevenPo
manager: femila
editor: 
ms.assetid: e2d77104-d978-46a3-bfea-03ffdf3b61e6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2016
ms.author: stevenpo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b689a0033103231d560c9ba4b349e675f304e35c


---
# <a name="sharing-accounts-with-azure-ad"></a>Azure AD とのアカウントの共有
## <a name="overview"></a>Overview
組織では、複数のユーザーに対して 1 組のユーザー名とパスワードを使用することが必要な場合があります。 このような状況は、通常、次の 2 つのケースで発生します。

* オンプレミスのアプリケーションであろうと、コンシューマー クラウド サービス (企業のソーシャル メディア アカウントなど) であろうと、ユーザーごとに一意のログインおよびパスワードを必要とするアプリケーションにアクセスする場合。
* マルチユーザー環境を作成する場合。 昇格された特権を有し中心となるセットアップ、管理、および回復アクティビティに使用できる単一のローカル アカウントを持つことができます (たとえば、Office 365 のローカル「グローバル管理者」アカウントまたは Salesforce のルート アカウント)。

これまで、このようなアカウントを共有するためには、権限のあるユーザーに資格情報 (ユーザー名/パスワード) を配布するか、または信頼済みの複数のエージェントがアクセスできる共有の場所に当該アカウントを格納するということを行ってきました。

従来の共有のモデルには、以下に示すようないくつかの欠点があります。

* 新しいアプリケーションへのアクセスを有効にするには、アクセスを必要としているすべてのユーザーに資格情報を配布する必要があります。
* 各共有アプリケーションでそれぞれ固有の共有資格情報セットを必要とすることがあります。 このような場合、ユーザーは複数の資格情報セットを覚えておく必要があります。 多くの資格情報を覚えておかなければならない場合、ユーザーはリスクを伴う手段に (たとえば、パスワードを書き留める) 頼るので、リスクは高まります。
* どのユーザーがアプリケーションにアクセスできるのかはっきりしません。
* アプリケーションに *アクセスした* ユーザーをはっきりさせることができません。
* アプリケーションへのアクセス権を削除する必要がある場合は、資格情報を更新して、アプリケーションへのアクセスを必要としているすべてのユーザーに再配布する必要があります。

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory アカウントの共有
Azure AD では、共有のアカウントを使用する上で、上記の欠点を排除した新しい方法を提供します。

Azure AD 管理者は、アクセス パネルを使用して、ユーザーがどのアプリケーションにアクセスできるかを構成し、そのアプリケーションに最適なシングル サインオンの種類を選択します。 その中の 1 つである *パスワードベースのシングル サインオン*を選択した場合、Azure AD はアプリケーションに対するサインオン プロセス中に一種のブローカーとしての役割を果たします。

ユーザーは組織アカウントを使用して 1 度、ログインします。 このアカウントは、ユーザーがデスクトップまたは電子メールにアクセスするためによく使用するのと同じものです。 ユーザーは自分が割り当てられているアプリケーションだけを検出し、アクセスすることができます。 共有アカウントの場合は、このアプリケーション リストに任意の数の共有資格情報を含めることができます。 エンド ユーザーは、使用する可能性があるさまざまなアカウントを覚えることも、書き留めておくことも必要ありません。

共有アカウントは、管理作業の強化、使いやすさの向上だけでなく、セキュリティも強化します。 資格情報の使用権限を持つユーザーには、共有パスワードが表示されるのではなく、パスワードを調整された認証フローの一部として使用する権限が与えられます。 さらに、一部のパスワード SSO アプリケーションの場合、大規模で複雑なパスワードを使用して Azure AD に定期的にパスワードをロールオーバー (更新) させて、アカウントのセキュリティを強化するオプションもあります。 管理者は、アプリケーションへのアクセス権の付与または取り消しを簡単に行うことができ、また、アカウントへのアクセス権を有するユーザーおよび過去にアプリケーションにアクセスしたユーザーを把握できます。

Azure AD では、アプリケーションに対するあらゆる種類のパスワード シングル サインオンで、エンタープライズ モビリティ スイート (EMS)、Premium、または Basic ライセンス ユーザーを対象にした共有アカウントをサポートします。 アプリケーション ギャラリーに事前に統合された何千ものアプリケーションのいずれについてもアカウントを共有することができると共に、 [カスタム SSO アプリケーション](active-directory-sso-integrate-saas-apps.md)を使用して独自のパスワード認証アプリケーションを追加することができます。

アカウントの共有を有効にする Azure AD の機能は、次のとおりです。

* [パスワード シングル サインオン](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* パスワード シングル サインオン エージェント
* [グループの割り当て](active-directory-accessmanagement-self-service-group-management.md)
* カスタム パスワード アプリケーション
* [アプリケーションの使用状況に関するダッシュ ボード/レポート](active-directory-passwords-get-insights.md)
* エンド ユーザー アクセス ポータル
* [アプリケーション プロキシ](active-directory-application-proxy-get-started.md)
* [Active Directory マーケットプレース](https://azure.microsoft.com/marketplace/active-directory/all/)

## <a name="sharing-an-account"></a>アカウントの共有
Azure AD を使用してアカウントを共有するには、次の操作が必要です。

* アプリケーションを[アプリケーション ギャラリー](https://azure.microsoft.com/marketplace/active-directory/)または[カスタム アプリケーション](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)に追加する
* パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する
* [グループ ベースの割り当て](active-directory-accessmanagement-group-saasapps.md) を使用し、共有資格情報を入力するオプションを選択する
* オプション: Facebook、Twitter、LinkedIn などの一部のアプリケーションでは、 [Azure AD 自動パスワード ロール オーバー](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx)

Multi-Factor Authentication (MFA) で共有アカウントの安全性を強化 (詳細については「[Azure AD によるアプリケーションのセキュリティ保護](../multi-factor-authentication/multi-factor-authentication-get-started.md)」を参照) すると共に、[Azure AD のセルフ サービス](active-directory-accessmanagement-self-service-group-management.md)のグループ管理を使用してアプリケーションへのアクセス権限を有するユーザーを管理する機能を委任することもできます。

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [条件付きアクセスを使用したアプリケーションの保護](active-directory-conditional-access.md)
* [セルフサービス グループの管理/SSAA](active-directory-accessmanagement-self-service-group-management.md)




<!--HONumber=Nov16_HO3-->


