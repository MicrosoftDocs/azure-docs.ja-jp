---
title: Identity Governance - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance を使用すると、セキュリティや従業員の生産性に対する組織のニーズと、適切なプロセスや可視性とのバランスを取ることができます。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/25/2018
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7872a43d5000ddb1ba581f76358c8001902ab56b
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579718"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD Identity Governance とは

Azure Active Directory (Azure AD) Identity Governance を使用すると、セキュリティや従業員の生産性に対する組織のニーズと、適切なプロセスや可視性とのバランスを取ることができます。 これは、適切なユーザーが適切なリソースへの適切なアクセス権を確実に持つようにする機能を備え、従業員の生産性を確保しながら、重大な資産を保護、監視、および監査することができます。  

Identity Governance は、従業員、ビジネス パートナーとベンダー、サービスとアプリケーションの間で組織が次のことを実現できるようにします。

- ID ライフサイクルの管理
- アクセスのライフサイクルの管理
- セキュリティで保護された管理

具体的には、組織が次の 4 つの重要な問題に対処できるようにすることを目的としています。

- どのユーザーがどのリソースへのアクセス権を持つ必要があるか。
- それらのユーザーはそのアクセス権によって何を実行するか。
- アクセスを管理するための効果的な組織的管理は存在しているか。
- 監査者は管理が機能していることを確認できるか。

## <a name="identity-lifecycle"></a>ID ライフサイクル

Identity Governance により、組織は、*生産性* (従業員が組織に加わったときなどに、必要なリソースへのアクセスできるようになるまでの時間) と *セキュリティ* (従業員の雇用形態の変更などによって、時間の経過に伴いアクセス権をどのように変更すべきか) とのバランスを取ることができます。  ID ライフサイクル管理は ID 管理の基盤であり、大規模な効果的な管理には、アプリケーションの ID ライフサイクル管理インフラストラクチャの近代化が必要です。

多くの組織では、従業員の ID ライフサイクルは、HCM (ヒューマン キャピタル マネジメント) システムでのそのユーザーの表示に関連付けられています。  Azure AD Premium は、[Workday 受信プロビジョニング (プレビュー) のチュートリアル](../saas-apps/workday-inbound-tutorial.md)で説明されているように、Active Directory と Azure Active Directory の両方の Workday に表示されるユーザーのユーザー ID を自動的に維持します。  Azure AD Premium には [Microsoft Identity Manager](/microsoft-identity-manager/) も含まれていて、SAP、Oracle eBusiness、Oracle PeopleSoft などのオンプレミスの HCM システムからレコードをインポートすることができます。

組織外のユーザーとの共同作業が必要になるシナリオは、ますます増えています。 [Azure AD B2B](/azure/active-directory/b2b/) コラボレーションを使用すると、独自の社内データに対するコントロールを維持した状態で、組織のアプリケーションとサービスをゲスト ユーザーや任意の組織の外部パートナーと安全に共有することができます。

## <a name="access-lifecycle"></a>アクセスのライフサイクル

組織では、ユーザーの ID の作成時にそのユーザー用に最初にプロビジョニングしたアクセス権を上回るアクセス権を管理するプロセスが必要です。  さらに、企業組織では、アクセス ポリシーと管理を継続的に開発し適用できるように、効率的なスケールが可能でなければなりません。

通常、IT 部門は、アクセス承認の決定を管理職意思決定者に委任します。  さらに、ユーザー自身が IT 部門に関与していることがあります。  たとえば、ヨーロッパで会社のマーケティング アプリケーションで顧客の機密データにアクセスするユーザーは、会社のポリシーを把握している必要があります。 ゲスト ユーザーは、招待されている組織のデータの処理要件を理解していない可能性があります。

組織では、[動的グループ](../users-groups-roles/groups-dynamic-membership.md)などのテクノロジを [SaaS アプリ](../saas-apps/tutorial-list.md)または [SCIM と統合されたアプリ](../manage-apps/use-scim-to-provision-users-and-groups.md)と組み合わせて使用して、アクセスのライフサイクル プロセスを自動化することができます。  また、組織では、[オンプレミス アプリケーションへのアクセス権を持つゲスト ユーザー](../b2b/hybrid-cloud-to-on-premises.md)を制御することもできます。  それにより、[Azure AD アクセス レビュー](access-reviews-overview.md)を定期的に利用して、これらのアクセス権を定期的にレビューすることができます。

ユーザーがアプリケーションにアクセスしようとすると、Azure AD は[条件付きアクセス](/azure/active-directory/conditional-access/)ポリシーを適用します。 条件付きアクセス ポリシーには、たとえば、[使用条件](active-directory-tou.md)を表示することと、[ユーザーがそれらの条件に確実に同意した](../conditional-access/require-tou.md)後でアプリケーションにアクセスできるようになることを含めることができます。

## <a name="privileged-access-lifecycle"></a>特権アクセスのライフサイクル

特権アクセスは、従来は他のベンダーによって ID 管理とは別の機能として説明されていました。 ただし、Microsoft では、特権アクセスの管理は ID 管理の重要な部分であると考えています。特に、組織にはそれらの管理者権限に関連する誤用が発生する可能性があるためです。 管理者権限を引き受ける従業員、ベンダー、および契約社員は、管理する必要があります。

Azure AD Privileged Identity Management (PIM) により、Azure AD、Azure、およびその他の Microsoft オンライン サービスの全体で、リソースに対するアクセス権のセキュリティ保護に合わせた管理を追加することができます。  多要素認証と条件付きアクセスに加え、ジャストイン タイム アクセスと、Azure AD PIM によって提供されるロール変更の通知機能は、会社のリソース (ディレクトリ、Office 365、および Azure リソース ロール) のセキュリティ保護に役立つガバナンス コントロールの包括的なセットを提供します。 他の形式のアクセスと同様、組織では、アクセス レビューを使用して、管理者ロールに属するすべてのユーザーの定期的なアクセス再認定を構成することができます。

## <a name="getting-started"></a>使用の開始

すべての顧客にとって完璧なソリューションや推奨事項はありませんが、次の構成は、従業員の安全性と生産性を高めるための、Microsoft が推奨するベースライン ポリシーに関する手引きです。

- [ID とデバイスのアクセスの構成](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [特権アクセスのセキュリティ保護](../users-groups-roles/directory-admin-roles-secure.md)


### <a name="access-reviews"></a>アクセス レビュー

- [アクセス レビューとは](access-reviews-overview.md)
- [アクセス レビューを使用してユーザー アクセスを管理する](manage-user-access-with-access-reviews.md)
- [アクセス レビューを使用してゲスト アクセスを管理する](manage-guest-access-with-access-reviews.md)
- [Azure AD ロールのアクセス レビューを開始する](../privileged-identity-management/pim-how-to-start-security-review.md)

### <a name="terms-of-use"></a>使用条件

- [利用規約でできること](active-directory-tou.md)

### <a name="privileged-identity-management"></a>Privileged Identity Management

- [Azure AD PIMとは](../privileged-identity-management/pim-configure.md)
