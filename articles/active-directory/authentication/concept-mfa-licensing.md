---
title: Azure AD Multi-Factor Authentication のバージョンと従量課金プラン
description: Azure AD Multi-Factor Authentication クライアントと、使用可能なさまざまな方法およびバージョンについて説明します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d880146a0b068a5d097c452c14b28db4907098
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96743957"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication の機能とライセンス

組織内のユーザー アカウントを保護するには、多要素認証を使用する必要があります。 リソースへの特権アクセスが認められているアカウントでは、この機能が特に重要となります。 Microsoft 365 および Azure Active Directory (Azure AD) の管理者は、追加料金なしで基本的な多要素認証機能を利用できます。 管理者向けの機能をアップグレードしたり、多要素認証を他のユーザーに拡張したりといった希望がある場合には、いくつかの方法で Azure AD Multi-Factor Authentication を購入できます。

> [!IMPORTANT]
> この記事では、Azure AD Multi-Factor Authentication のライセンスを取得して使用するさまざまな方法について詳しく説明します。 価格と課金の詳細については、「[Azure AD Multi-Factor Authentication の価格に関するページ](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」を参照してください。

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication の使用可能なバージョン

Azure AD Multi-Factor Authentication は、組織のニーズに応じて、いくつかの異なる方法で使用し、ライセンスを取得することができます。 現在お使いの Azure AD、EMS、Microsoft 365 のライセンスによっては、Azure AD Multi-Factor Authentication を使用する権利を既に持っている場合があります。 次の表では、Azure AD Multi-Factor Authentication を入手するさまざまな方法と、それぞれの機能およびユース ケースについて詳しく説明します。

| 次のユーザーの場合 | 機能とユース ケース |
| --- | --- |
| Microsoft 365 Business Premium および EMS または Microsoft 365 E3 および E5 | EMS E3、Microsoft 365 E3、Microsoft 365 Business Premium には Azure AD Premium P1 が含まれています。 EMS E5 または Microsoft 365 E5 には、Azure AD Premium P2 が含まれています。 次のセクションに記載されている同じ条件付きアクセス機能を使用して、ユーザーに多要素認証を提供できます。 |
| Azure AD Premium P1 | [Azure AD 条件付きアクセス](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)を使用して、ビジネス要件に合わせて特定のシナリオやイベントの際に多要素認証をユーザーに求めることができます。 |
| Azure AD Premium P2 | 最も強力なセキュリティのポジションと、向上したユーザー エクスペリエンスを提供します。 [リスク ベースの条件付きアクセス](../conditional-access/howto-conditional-access-policy-risk.md)を Azure AD Premium P1 の機能に追加することで、ユーザーのパターンに適応し、多要素認証の回数を最小限に抑えます。 |
| すべての Microsoft 365 プラン | Azure AD Multi-Factor Authentication は、[セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用して、[ユーザーごとに有効にする](howto-mfa-userstates.md)か、すべてのユーザーに対して有効または無効にすることができます。 Azure AD Multi-Factor Authentication の管理は、Microsoft 365 ポータルを通じて行います。 ユーザー エクスペリエンスを向上させるには、Azure AD Premium P1 または P2 にアップグレードし、条件付きアクセスを使用します。 詳細については、[多要素認証を使用した Microsoft 365 リソースのセキュリティ保護](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication)に関するページを参照してください。 |
| Azure AD Free | [セキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用して、すべてのユーザーに対して多要素認証を有効にすることができます。 有効となるユーザーまたはシナリオをきめ細かく制御することはできませんが、追加のセキュリティ措置を提供することはできます。<br /> すべてのユーザーの多要素認証を有効にするセキュリティの既定値群が使用されていない場合でも、*Azure AD 全体管理者* ロールに割り当てられたユーザーは、多要素認証を使用するように構成できます。 Free レベルのこの機能により、重要な管理者アカウントが多要素認証によって保護されます。 |

## <a name="feature-comparison-of-versions"></a>バージョンごとの機能の比較

次の表に、さまざまなバージョンの Azure AD Multi-Factor Authentication で使用できる機能の一覧を示します。 ユーザー認証のセキュリティ保護に必要なものを詳しく検討し、その要件を満たす方法を決定します。 たとえば、Azure AD Free は Azure AD Multi-Factor Authentication を提供するセキュリティの既定値群を提供しますが、認証プロンプトに使用できるのはモバイル認証アプリだけであり、電話や SMS は使用できません。 モバイル認証アプリがユーザーの個人のデバイスにインストールされていることを保証できない場合、この方法は制約を受けるかもしれません。

| 特徴量 | Azure AD Free - セキュリティの既定値群 | Azure AD Free - Azure AD 全体管理者 | Microsoft 365 アプリ | Azure AD Premium P1 または P2 |
| --- |:---:|:---:|:---:|:---:|
| MFA で Azure AD テナント管理者アカウントを保護する | ● | ● (*Azure AD 全体管理者* アカウントの場合のみ) | ● | ● |
| モバイル アプリを 2 番目の要素にする | ● | ● | ● | ● |
| 音声通話を 2 番目の要素にする | | ● | ● | ● |
| SMS を 2 番目の要素にする | | ● | ● | ● |
| 検証方法の管理制御 | | ● | ● | ● |
| 不正アクセスのアラート | | | | ● |
| MFA レポート | | | | ● |
| 音声通話のカスタムあいさつ文 | | | | ● |
| 音声通話のカスタム発信元 ID | | | | ● |
| 信頼できる IP | | | | ● |
| 信頼済みデバイスの MFA の記憶 | | ● | ● | ● |
| オンプレミス アプリケーション用の MFA | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication を購入して有効にする

Azure AD Multi-Factor Authentication を使用するには、資格のある Azure AD のレベルに登録するか、ご購入ください。 Azure AD には、Free、Microsoft 365 アプリ、Premium P1、および Premium P2 の 4 つのエディションが用意されています。

Free エディションは、Azure サブスクリプションに含まれます。 セキュリティの既定値群を使用する方法、または *Azure AD 全体管理者* ロールでアカウントを保護する方法についての情報は、[下記のセクション](#azure-ad-free-tier)を参照してください。

Azure AD Premium エディションは、Microsoft の担当者、[Open Volume License プログラム](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)、および [Cloud Solution Providers プログラム](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)から入手できます。 Azure および Microsoft 365 のサブスクライバーは Azure Active Directory Premium P1 および P2 をオンラインで購入することもできます。 [サインイン](https://portal.office.com/Commerce/Catalog.aspx)して購入します。

必要な Azure AD レベルを購入した後、[Azure AD Multi-Factor Authentication を計画してデプロイします](howto-mfa-getstarted.md)。

### <a name="azure-ad-free-tier"></a>Azure AD Free レベル

Azure AD Free テナントのすべてのユーザーは、セキュリティの既定値群を使用して Azure AD Multi-Factor Authentication を使用できます。 モバイル認証アプリは、Azure AD Free のセキュリティの既定値群を使用する場合に Azure AD Multi-Factor Authentication で使用できる、唯一の方法です。

* [Azure AD のセキュリティの既定値群についての詳細情報を確認する](../fundamentals/concept-fundamentals-security-defaults.md)
* [Azure AD Free ユーザーのセキュリティの既定値群を有効にする](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

すべてのユーザーに対して Azure AD Multi-Factor Authentication を有効にしない場合、代わりに *Azure AD 全体管理者* ロールを持つユーザー アカウントのみを保護することを選択できます。 この方法では、重要な管理者アカウントに対して追加の認証プロンプトが表示されます。 使用しているアカウントの種類に応じて、次のいずれかの方法で Azure AD Multi-Factor Authentication を有効にします。

* Microsoft アカウントを使用している場合は、[多要素認証に登録](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)します。
* Microsoft アカウントを使用していない場合は、[Azure AD のユーザーまたはグループの多要素認証を有効にします](howto-mfa-userstates.md)。

## <a name="next-steps"></a>次のステップ

* コストの詳細については、[Azure AD Multi-Factor Authentication の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)に関するページを参照してください。
* [条件付きアクセスとは](../conditional-access/overview.md)

