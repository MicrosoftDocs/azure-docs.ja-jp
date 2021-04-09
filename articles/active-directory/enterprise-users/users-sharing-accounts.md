---
title: アカウントと資格情報の共有 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用してオンプレミスのアプリケーションおよびコンシューマー クラウド サービス用のアカウントを組織で安全に共有できるようにする方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576249"
---
# <a name="sharing-accounts-with-azure-ad"></a>Azure AD とのアカウントの共有

## <a name="overview"></a>概要

組織では、1 組のユーザー名とパスワードを複数のユーザーに使うことが必要な場合があります。一般に次の 2 つのケースです。

* オンプレミスのアプリケーションであろうと、コンシューマー クラウド サービス (企業のソーシャル メディア アカウントなど) であろうと、ユーザーごとに一意のサインインおよびパスワードを必要とするアプリケーションにアクセスする場合。
* マルチユーザー環境を作成する場合。 昇格された特権を持ち、中心となるセットアップ、管理、および回復アクティビティに使われる、単一のローカルなアカウントが用意されている場合があります。 たとえば、Microsoft 365 のローカル "グローバル管理者" アカウントや、Salesforce のルート アカウントなどです。

これまで、このようなアカウントを共有するには、権限のあるユーザーに資格情報 (ユーザー名とパスワード) を配布するか、または信頼済みの複数のエージェントがアクセスできる共有の場所に当該アカウントを格納するということを行ってきました。

従来の共有のモデルには、以下に示すようないくつかの欠点があります。

* 新しいアプリケーションへのアクセスを有効にするには、アクセスを必要としているすべてのユーザーに資格情報を配布する必要があります。
* 各共有アプリケーションでそれぞれ固有の共有資格情報セットを必要とすることがあります。 このような場合、ユーザーは複数の資格情報セットを覚えておく必要があります (たとえば、パスワードを書き留めるなど)。
* どのユーザーがアプリケーションにアクセスできるのかはっきりしません。
* アプリケーションに *アクセスした* ユーザーをはっきりさせることができません。
* アプリケーションへのアクセス権を削除する必要がある場合は、資格情報を更新して、アプリケーションへのアクセスを必要としているすべてのユーザーに再配布する必要があります。

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory アカウントの共有

Azure AD では、共有のアカウントを使用する上で、上記の欠点を排除した新しい方法を提供します。

Azure AD 管理者は、アクセス パネルを使用して、ユーザーがどのアプリケーションにアクセスできるかを構成し、そのアプリケーションに最適なシングル サインオンの種類を選択します。 その中の 1 つである *パスワードベースのシングル サインオン* を選択した場合、Azure AD はアプリケーションに対するサインオン プロセス中に一種のブローカーとしての役割を果たします。

ユーザーは、組織アカウントを使用して 1 度サインインします。 このアカウントは、ユーザーがデスクトップまたは電子メールにアクセスするためによく使うものと同じです。 ユーザーは自分が割り当てられているアプリケーションだけを検出し、アクセスすることができます。 共有アカウントの場合は、このアプリケーション リストに任意の数の共有資格情報を含めることができます。 エンド ユーザーは、使う可能性があるさまざまなアカウントを覚えることも、書き留めておくことも必要ありません。

共有アカウントは、管理作業の強化、使いやすさの向上だけでなく、セキュリティも強化します。 資格情報の使用権限を持つユーザーには、共有パスワードが表示されるのではなく、パスワードを調整された認証フローの一部として使用する権限が与えられます。 さらに、一部のパスワード SSO アプリケーションには、Azure AD を使って定期的にパスワードをロールオーバー (更新) するオプションがあります。 システムは、大規模で複雑なパスワードを使って、アカウントのセキュリティを強化します。 管理者は、アプリケーションへのアクセス権の付与または取り消しを簡単に行うことができ、アカウントへのアクセス権を持つユーザーおよび過去にアプリケーションにアクセスしたユーザーを把握できます。

Azure AD では、あらゆる種類のパスワード シングル サインオン アプリケーションについて、Enterprise Mobility Suite (EMS) または Azure AD Premium ライセンス プランを対象とする共有アカウントがサポートされます。 アプリケーション ギャラリーに事前に統合された何千ものアプリケーションのいずれについてもアカウントを共有することができると共に、 [カスタム SSO アプリケーション](../manage-apps/what-is-single-sign-on.md)を使用して独自のパスワード認証アプリケーションを追加することができます。

アカウントの共有を有効にする Azure AD の機能は、次のとおりです。

* [パスワード シングル サインオン](../manage-apps/sso-options.md#password-based-sso)
* パスワード シングル サインオン エージェント
* [グループの割り当て](groups-self-service-management.md)
* カスタム パスワード アプリケーション
* [アプリケーションの使用状況に関するダッシュボード/レポート](../authentication/howto-sspr-reporting.md)
* エンド ユーザー アクセス ポータル
* [アプリケーション プロキシ](../manage-apps/application-proxy.md)
* [Active Directory マーケットプレース](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>アカウントの共有

Azure AD を使ってアカウントを共有するには、次の操作が必要です。

* アプリケーションを[アプリケーション ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)または[カスタム アプリケーション](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)に追加する
* パスワード シングル サインオン (SSO) に対応するようにアプリケーションを構成する
* [グループ ベースの割り当て](groups-saasapps.md)を使い、共有資格情報を入力するオプションを選ぶ

Multi-Factor Authentication (MFA) で共有アカウントの安全性を強化 (詳細については「[Azure AD によるアプリケーションのセキュリティ保護](../authentication/concept-mfa-howitworks.md)」を参照) すると共に、[Azure AD のセルフ サービス](groups-self-service-management.md) グループ管理を使用してアプリケーションへのアクセス権限を有するユーザーを管理する機能を委任することもできます。

## <a name="next-steps"></a>次のステップ

* [Azure Active Directory のアプリケーション管理](../manage-apps/what-is-application-management.md)
* [条件付きアクセスを使用したアプリケーションの保護](../../active-directory-b2c/overview.md)
* [セルフサービス グループの管理/SSAA](groups-self-service-management.md)