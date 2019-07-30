---
title: Azure Active Directory を使用するようにアプリケーションを登録する | Microsoft Docs
description: IT プロフェッショナル向けに記述されたこの記事では、Azure アプリケーションを Active Directory を統合するためのガイドラインを示します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 10/30/2018
ms.author: mimart
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba54f8042c20a00f8d559ddce28e007a93afaace
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108283"
---
# <a name="develop-line-of-business-apps-for-azure-active-directory"></a>Azure Active Directory 用の基幹業務アプリをの開発する
このガイドでは、Azure Active Directory (AD) 用の基幹業務 (LoB) アプリケーションの開発の概要について説明します。Active Directory/Office 365 のグローバル管理者向けに記述されています。

## <a name="overview"></a>概要
Azure AD と統合されるアプリケーションを構築すると、組織内のユーザーは、Office 365 でシングル サインオンできます。 アプリケーションを Azure AD と統合すると、アプリケーションの認証ポリシーを管理できます。 条件付きアクセスと多要素認証 (MFA) によってアプリを保護する方法については、「[アクセス ルールの構成](../conditional-access/app-based-mfa.md)」を参照してください。

Azure Active Directory を使用するようにアプリケーションを登録します。 アプリケーションを登録することで、開発者が Azure AD を使用してユーザーを認証し、電子メール、カレンダー、ドキュメントなどのユーザー リソースへのアクセスを要求できます。

ディレクトリ内の (ゲスト以外の) すべてのメンバーがアプリケーションを登録できます。これは*アプリケーション オブジェクトの作成*とも呼ばれます。

アプリケーションを登録すると、ユーザーは次の操作を実行できます。

* Azure AD が認識するアプリケーションの ID を取得する
* アプリケーションが自身を AD に対して認証するために使用する 1 つまたは複数のシークレット/キーを取得する
* カスタム名やロゴなどを使用して Azure Portal でアプリケーションをブランド化する
* アプリ用に次の Azure AD 承認機能を適用する

  * ロール ベースのアクセス制御 (RBAC)
  * OAuth 承認サーバーとしての Azure Active Directory (アプリケーションによって公開される API をセキュリティで保護する)
* アプリケーションが期待どおりに機能するために必要な次のアクセス許可を宣言する

     - アプリのアクセス許可 (グローバル管理者のみ)。 例: 別の Azure AD アプリケーションのロール メンバーシップ、または Azure リソース、リソースグループ、またはサブスクリプションに関係するロール メンバーシップ
     - 委任されたアクセス許可 (任意のユーザー) 例: Azure AD、サインイン、プロファイルの読み取り

> [!NOTE]
> 既定では、すべてのメンバーがアプリケーションを登録できます。 アプリケーションを登録するためのアクセス許可を特定のメンバーに制限する方法については、 [アプリケーションを Azure AD に追加する方法に関する記事](../develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance)を参照してください。
>
>

開発者によるアプリケーションの作成をサポートするために、グローバル管理者として次の操作を行う必要があります。

* アクセス規則を構成する (アクセス ポリシー/MFA)
* ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
* 既定のユーザー同意エクスペリエンスを抑制する

## <a name="configure-access-rules"></a>アクセス規則を構成する
SaaS アプリに対してアプリケーションごとのアクセス規則を構成します。 たとえば、MFA を必須とするか、信頼されたネットワークでのみユーザーのアクセスを許可することができます。 この詳細については、 [アクセス規則の構成](../conditional-access/app-based-mfa.md)に関するドキュメントをご覧ください。

## <a name="configure-the-app-to-require-user-assignment-and-assign-users"></a>ユーザー割り当てを必要とするようにアプリを構成し、ユーザーを割り当てる
既定では、ユーザーは割り当てなしにアプリケーションにアクセスできます。 ただし、アプリケーションがロールを公開している場合、またはアプリケーションをユーザーのアクセス パネルに表示する場合は、ユーザー割り当てが必要です。

Azure AD Premium または Enterprise Mobility Suite (EMS) のサブスクライバーの場合は、グループを使用することを強くお勧めします。 グループをアプリケーションに割り当てると、グループの所有者に継続的にアクセスの管理を委任できます。 自分でグループを作成するか、グループ管理機能を使用して組織の責任者にグループの作成を依頼できます。

[ユーザーとグループをアプリに割り当てる](methods-for-assigning-users-and-groups.md)  


## <a name="suppress-user-consent"></a>ユーザーの同意を抑制する
既定では、各ユーザーはサインインするために同意エクスペリエンスを経由します。 アプリケーションへのアクセス許可を付与することを求める同意エクスペリエンスは、そのような決定に慣れていないユーザーの混乱を招く可能性があります。

信頼できるアプリケーションの場合は、組織の代理としてアプリケーションに同意することでユーザー エクスペリエンスを簡略化できます。

ユーザーの同意と Azure での同意エクスペリエンスの詳細については、「 [Azure Active Directory とアプリケーションの統合](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)」を参照してください。

## <a name="related-articles"></a>関連記事
* [Azure AD アプリケーション プロキシを使用してオンプレミス アプリケーションへの安全なリモート アクセスを有効にする](application-proxy.md)
* [Azure AD を使用したアプリへのアクセスの管理](what-is-access-management.md)

