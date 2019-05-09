---
title: 電子メール検証済みユーザー アカウントのセルフサービス サインアップ - Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) テナントでセルフサービス サインアップを使用します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0cfc31705909710e6a60e0d2fd3963e74e9397ee
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58201264"
---
# <a name="what-is-self-service-signup-for-azure-active-directory"></a>Azure Active Directory のセルフサービス サインアップについて

この記事では、セルフサービス サインアップを使用して Azure Active Directory (Azure AD) に組織を設定する方法について説明します。 アンマネージド Azure AD 組織からドメイン名を引き継ぐ場合は、[ ディレクトリを管理者として引き継ぐ方法](domains-admin-takeover.md)に関する記事をご覧ください。

## <a name="why-use-self-service-signup"></a>セルフサービス サインアップを使用する理由
* 顧客が求めるサービスを迅速に提供できる。
* サービス提供の電子メール ベース プランを構築できる。
* ユーザーが覚えやすい職場の電子メールの別名を使用して ID をすばやく作成することを可能にする、電子メール ベースのサインアップ フローを作成できる。
* セルフサービスで作成された Azure AD ディレクトリを、他のサービスで使用できる管理ディレクトリに変えることができる。

## <a name="terms-and-definitions"></a>用語と定義
* **セルフサービス サインアップ**:ユーザーがこの方法でクラウド サービスにサインアップすると、電子メール ドメインに基づいた ID が Azure AD に自動的に作成されます。
* **管理されていない Azure AD ディレクトリ**:これは、前述の ID が作成されるディレクトリです。 管理されていないディレクトリは、グローバル管理者がいないディレクトリです。
* **電子メール検証済みユーザー**:これは Azure AD のユーザー アカウントの一種です。 セルフサービス プランへのサインアップ後に自動作成された ID を持つユーザーは、電子メール検証済みユーザーです。 電子メール検証済みユーザーは、creationmethod=EmailVerified でタグ付けされたディレクトリの通常メンバーです。

## <a name="how-do-i-control-self-service-settings"></a>セルフサービス設定の管理方法
管理者は、セルフサービスの 2 種類の管理を実行できます。 次の点を管理できます。

* ユーザーが電子メール経由でディレクトリに参加できるかどうか
* ユーザー自身がアプリケーションやサービスのライセンスを取得できるかどうか

### <a name="how-can-i-control-these-capabilities"></a>これらの機能の管理方法
管理者は、Azure AD コマンドレット Set-MsolCompanySettings の次のパラメーターを使用してこれらの機能を構成できます。

* **AllowEmailVerifiedUsers** によって、ユーザーがディレクトリを作成または参加できるかを管理できます。 このパラメーターを $false に設定すると、電子メール検証済みのユーザーはディレクトリに参加できません。
* **AllowAdHocSubscriptions** によって、ユーザーがセルフサービス サインアップを実行できるかどうかを管理できます。 このパラメーターを $false に設定すると、ユーザーはセルフサービス サインアップを実行できません。
  
AllowEmailVerifiedUsers と AllowAdHocSubscriptions は、管理対象ディレクトリにも管理対象外ディレクトリにも適用できるディレクトリ全体の設定です。 次のような例を示します。

* contoso.com などの検証済みドメインを持つディレクトリを管理します
* 別のディレクトリから B2B コラボレーションを使用して、constoso.com のホーム ディレクトリにまだ存在しないユーザー (userdoesnotexist@contoso.com) を招待します
* ホーム ディレクトリでは AllowEmailVerifiedUsers がオンになっています

上記の条件が当てはまる場合、メンバー ユーザーがホーム ディレクトリに作成され、B2B ゲスト ユーザーが招待側のディレクトリに作成されます。

Flow および PowerApps の試用版サインアップは、**AllowAdHocSubscriptions** の設定によって制御されません。 詳細については、次の記事を参照してください。

* [既存のユーザーが Power BI の開始を使用できないようにする方法](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [組織における Flow の Q&A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>これらの管理機能の連携について
これらの 2 つのパラメーターを組み合わせて使用すると、セルフサービス サインアップをさらに細かく管理できるようになります。 たとえば、次のコマンドにより、ユーザーはセルフサービス サインアップを実行できますが、Azure AD のアカウントを既に持っている場合に限られます (つまり、電子メール検証済みのアカウントをまず作成する必要があるユーザーは、セルフサービス サインアップを実行できません)。

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

次のフローチャートは、これらのパラメーターのさまざまな組み合わせと、結果として得られるディレクトリとセルフサービス サインアップの状態を示しています。

![セルフサービス サインアップ コントロールのフローチャート](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

これらのパラメーターの使用方法についての詳細は、「 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure AD にカスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md)
* [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure コマンドレット リファレンス](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
