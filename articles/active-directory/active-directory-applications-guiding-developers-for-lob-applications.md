---
title: "Azure AD 用のアプリを開発する | Microsoft Docs"
description: "IT プロフェッショナル向けに記述されたこの記事では、Azure アプリケーションを Active Directory を統合するためのガイドラインを示します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: dd69f2bc-37c5-457c-857d-27acb84267fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 015cc28903bfd366c653a51b0f73512bf8b578ea
ms.openlocfilehash: aa3c83f82d1a60253f70350e88aa96fb285ef3d9
ms.lasthandoff: 02/28/2017

---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Azure Active Directory 用の基幹業務アプリをの開発する
このガイドでは、Azure Active Directory (AD) 用の基幹業務 (LoB) アプリケーションの開発の概要について説明します。Active Directory/Office 365 のグローバル管理者向けに記述されています。

## <a name="overview"></a>Overview
Azure AD と統合されるアプリケーションを構築すると、組織内のユーザーは、Office 365 でシングル サインオンできます。 アプリケーションを Azure AD と統合すると、アプリケーションの認証ポリシーを管理できます。 条件付きアクセスと多要素認証 (MFA) によってアプリを保護する方法については、「 [アクセス ルールの構成](active-directory-conditional-access-azuread-connected-apps.md)」を参照してください。

Azure Active Directory を使用するようにアプリケーションを登録します。 アプリケーションを登録することで、開発者が Azure AD を使用してユーザーを認証し、電子メール、カレンダー、ドキュメントなどのユーザー リソースへのアクセスを要求できます。

ディレクトリ内の (ゲスト以外の) すべてのメンバーがアプリケーションを登録できます。これは*アプリケーション オブジェクトの作成*とも呼ばれます。

アプリケーションを登録すると、ユーザーは次の操作を実行できます。

* Azure AD が認識するアプリケーションの ID を取得する
* アプリケーションが自身を AD に対して認証するために使用する&1; つまたは複数のシークレット/キーを取得する
* カスタム名やロゴなどを使用して Azure Portal でアプリケーションをブランド化する
* アプリ用に次の Azure AD 承認機能を適用する

  * ロール ベースのアクセス制御 (RBAC)
  * OAuth 承認サーバーとしての Azure Active Directory (アプリケーションによって公開される API をセキュリティで保護する)
* アプリケーションが期待どおりに機能するために必要な次のアクセス許可を宣言する

      - アプリのアクセス許可 (グローバル管理者のみ)。 例: 別の Azure AD アプリケーションのロール メンバーシップ、または Azure リソース、リソースグループ、またはサブスクリプションに関係するロール メンバーシップ
      - 委任されたアクセス許可 (任意のユーザー) 例: Azure AD、サインイン、プロファイルの読み取り

> [!NOTE]
> 既定では、すべてのメンバーがアプリケーションを登録できます。 アプリケーションを登録するためのアクセス許可を特定のメンバーに制限する方法については、 [アプリケーションを Azure AD に追加する方法に関する記事](develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)を参照してください。
>
>

開発者によるアプリケーションの作成をサポートするために、グローバル管理者として次の操作を行う必要があります。

* アクセス規則を構成する (アクセス ポリシー/MFA)
* ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
* 既定のユーザー同意エクスペリエンスを抑制する

## <a name="configure-access-rules"></a>アクセス規則を構成する
SaaS アプリに対してアプリケーションごとのアクセス規則を構成します。 たとえば、MFA を必須とするか、信頼されたネットワークでのみユーザーのアクセスを許可することができます。 この詳細については、 [アクセス規則の構成](active-directory-conditional-access-azuread-connected-apps.md)に関するドキュメントをご覧ください。

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
既定では、ユーザーは割り当てなしにアプリケーションにアクセスできます。 ただし、アプリケーションがロールを公開している場合、またはアプリケーションをユーザーのアクセス パネルに表示する場合は、ユーザー割り当てが必要です。

[ユーザー割り当ての要求](active-directory-applications-guiding-developers-requiring-user-assignment.md)

Azure AD Premium または Enterprise Mobility Suite (EMS) のサブスクライバーの場合は、グループを使用することを強くお勧めします。 グループをアプリケーションに割り当てると、グループの所有者に継続的にアクセスの管理を委任できます。 自分でグループを作成するか、グループ管理機能を使用して組織の責任者にグループの作成を依頼できます。

[アプリケーションへのユーザーの割り当て](active-directory-applications-guiding-developers-assigning-users.md)  
[アプリケーションへのグループの割り当て](active-directory-applications-guiding-developers-assigning-groups.md)

## <a name="suppress-user-consent"></a>ユーザーの同意を抑制する
既定では、各ユーザーはサインインするために同意エクスペリエンスを経由します。 アプリケーションへのアクセス許可を付与することを求める同意エクスペリエンスは、そのような決定に慣れていないユーザーの混乱を招く可能性があります。

信頼できるアプリケーションの場合は、組織の代理としてアプリケーションに同意することでユーザー エクスペリエンスを簡略化できます。

ユーザーの同意と Azure での同意エクスペリエンスの詳細については、「 [Azure Active Directory とアプリケーションの統合](active-directory-integrating-applications.md)」を参照してください。

## <a name="related-articles"></a>関連記事
* [Azure AD アプリケーション プロキシを使用してオンプレミス アプリケーションへの安全なリモート アクセスを有効にする](active-directory-application-proxy-get-started.md)
* [Azure Conditional Access Preview for SaaS Apps](active-directory-conditional-access-azuread-connected-apps.md)
* [Azure AD を使用したアプリへのアクセスの管理](active-directory-managing-access-to-apps.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

