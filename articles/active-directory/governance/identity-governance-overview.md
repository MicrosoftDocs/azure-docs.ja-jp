---
title: Identity Governance - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Identity Governance を使用すると、セキュリティや従業員の生産性に対する組織のニーズと、適切なプロセスや可視性とのバランスを取ることができます。
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: e02df83d4b7874a1d158aae45f1619eb543e0aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92362452"
---
# <a name="what-is-azure-ad-identity-governance"></a>Azure AD Identity Governance とは

Azure Active Directory (Azure AD) Identity Governance を使用すると、セキュリティや従業員の生産性に対する組織のニーズと、適切なプロセスや可視性とのバランスを取ることができます。 適切なリソースに対する適切なアクセス権を適切なユーザーに付与できるようになります。 それらの機能に加え、Azure AD および Enterprise Mobility + Security の関連機能を使用することで、従業員とビジネス パートナーの生産性を確保しながら、重要な資産へのアクセスを保護、監視、監査することによってアクセス リスクを軽減することができます。  

Identity Governance は、オンプレミスであれクラウドであれ、従業員とビジネス パートナーとベンダー、そして、サービスとアプリケーションの垣根を越えて組織が次のタスクを実行できるようにします。

- ID ライフサイクルの管理
- アクセスのライフサイクルの管理
- 管理に使用される特権アクセスのセキュリティ保護

具体的には、組織が次の 4 つの重要な問題に対処できるようにすることを目的としています。

- どのユーザーがどのリソースへのアクセス権を持つ必要があるか。
- それらのユーザーはそのアクセス権によって何を実行するか。
- アクセスを管理するための効果的な組織的管理は存在しているか。
- 監査者は管理が機能していることを確認できるか。

## <a name="identity-lifecycle"></a>ID ライフサイクル

Identity Governance により、組織は、*生産性* (従業員が組織に加わったときなどに、必要なリソースへのアクセスできるようになるまでの時間) と *セキュリティ* (従業員の雇用形態の変更などによって、時間の経過に伴いアクセス権をどのように変更すべきか) とのバランスを取ることができます。  ID ライフサイクル管理は ID 管理の基盤であり、大規模な効果的な管理には、アプリケーションの ID ライフサイクル管理インフラストラクチャの近代化が必要です。

![ID ライフサイクル](./media/identity-governance-overview/identity-lifecycle.png)

多くの組織では、従業員の ID ライフサイクルは、HCM (ヒューマン キャピタル マネジメント) システムでのそのユーザーの表示に関連付けられています。  [Azure Active Directory ユーザー プロビジョニングのためのクラウド人事アプリケーションの計画ガイド](../app-provisioning/plan-cloud-hr-provision.md)で説明されているように、Azure AD Premium によって、Active Directory と Azure Active Directory の両方の Workday と SuccessFactors に表示されるユーザーのユーザー ID が自動的に維持されます。  Azure AD Premium には [Microsoft Identity Manager](/microsoft-identity-manager/) も含まれていて、SAP HCM、Oracle eBusiness、Oracle PeopleSoft などのオンプレミスの HCM システムからレコードをインポートすることができます。

組織外のユーザーとの共同作業が必要になるシナリオは、ますます増えています。 [Azure AD B2B](/azure/active-directory/b2b/) コラボレーションを使用すると、独自の社内データに対するコントロールを維持した状態で、組織のアプリケーションとサービスをゲスト ユーザーや任意の組織の外部パートナーと安全に共有することができます。  [Azure AD エンタイトルメント管理](entitlement-management-overview.md)を使用すると、組織のユーザーの中で、アクセスを要求して組織のディレクトリに B2B ゲストとして登録することが許可されるユーザーを選択すると共に、アクセスが不要になったらそれらのゲストを確実に削除することができます。

## <a name="access-lifecycle"></a>アクセスのライフサイクル

組織では、ユーザーの ID の作成時にそのユーザー用に最初にプロビジョニングしたアクセス権を上回るアクセス権を管理するプロセスが必要です。  さらに、企業組織では、アクセス ポリシーと管理を継続的に開発し適用できるように、効率的なスケールが可能でなければなりません。

![アクセスのライフサイクル](./media/identity-governance-overview/access-lifecycle.png)

通常、IT 部門は、アクセス承認の決定を管理職意思決定者に委任します。  さらに、ユーザー自身が IT 部門に関与していることがあります。  たとえば、ヨーロッパで会社のマーケティング アプリケーションで顧客の機密データにアクセスするユーザーは、会社のポリシーを把握している必要があります。 ゲスト ユーザーは、招待されている組織のデータの処理要件を理解していない可能性があります。

組織では、[動的グループ](../enterprise-users/groups-dynamic-membership.md)などのテクノロジを [SaaS アプリ](../saas-apps/tutorial-list.md)または [SCIM と統合されたアプリ](../app-provisioning/use-scim-to-provision-users-and-groups.md)と組み合わせて使用して、アクセスのライフサイクル プロセスを自動化することができます。  また、組織では、[オンプレミス アプリケーションへのアクセス権を持つゲスト ユーザー](../external-identities/hybrid-cloud-to-on-premises.md)を制御することもできます。  それにより、[Azure AD アクセス レビュー](access-reviews-overview.md)を定期的に利用して、これらのアクセス権を定期的にレビューすることができます。   また、[Azure AD エンタイトルメント管理](entitlement-management-overview.md)を使用することで、グループとチームのメンバーシップ、アプリケーション ロール、SharePoint Online ロールのパッケージ全体で、ユーザーによるアクセス要求の方法を定義することができます。

ユーザーがアプリケーションにアクセスしようとすると、Azure AD は[条件付きアクセス](../conditional-access/index.yml)ポリシーを適用します。 条件付きアクセス ポリシーには、たとえば、[使用条件](../conditional-access/terms-of-use.md)を表示することと、[ユーザーがそれらの条件に確実に同意した](../conditional-access/require-tou.md)後でアプリケーションにアクセスできるようになることを含めることができます。

## <a name="privileged-access-lifecycle"></a>特権アクセスのライフサイクル

特権アクセスは、従来は他のベンダーによって ID 管理とは別の機能として説明されていました。 ただし、Microsoft では、特権アクセスの管理は ID 管理の重要な部分であると考えています。特に、組織にはそれらの管理者権限に関連する誤用が発生する可能性があるためです。 管理者権限を引き受ける従業員、ベンダー、および契約社員は、管理する必要があります。

![特権アクセスのライフサイクル](./media/identity-governance-overview/privileged-access-lifecycle.png)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) により、Azure AD、Azure、およびその他の Microsoft オンライン サービスの全体で、リソースに対するアクセス権のセキュリティ保護に合わせた管理を追加することができます。  多要素認証と条件付きアクセスに加え、ジャストイン タイム アクセスと、Azure AD PIM によって提供されるロール変更の通知機能は、会社のリソース (ディレクトリ、Microsoft 365、および Azure リソース ロール) のセキュリティ保護に役立つガバナンス コントロールの包括的なセットを提供します。 他の形式のアクセスと同様、組織では、アクセス レビューを使用して、管理者ロールに属するすべてのユーザーの定期的なアクセス再認定を構成することができます。

## <a name="governance-capabilities-in-other-azure-ad-features"></a>その他の Azure AD 機能のガバナンス機能

上記の機能に加えて、ID ガバナンスのシナリオを提供するために頻繁に使用されるその他の Azure AD 機能には、次のものがあります。

| 機能 | シナリオ |機能
| ------- | --------------------- |-----|
|ID ライフサイクル (従業員)|管理者は、Workday または SuccessFactors のクラウド HR またはオンプレミス HR からユーザー アカウント プロビジョニングを有効にすることができます。|[クラウド HR から Azure AD へのユーザー プロビジョニング](../app-provisioning/plan-cloud-hr-provision.md)|
|ID ライフサイクル (ゲスト)|管理者は、別の Azure AD テナント、直接フェデレーション、ワンタイム パスコード (OTP)、または Google アカウントからのセルフサービス ゲスト ユーザーのオンボードを有効にすることができます。  ゲスト ユーザーは自動的にプロビジョニングされ、ライフサイクル ポリシーに従ってプロビジョニング解除されます。|[B2B](../external-identities/what-is-b2b.md) を使用した[エンタイトルメント管理](entitlement-management-overview.md)|
|エンタイトルメント管理|リソース所有者は、アプリ、チーム、Azure AD グループと Microsoft 365 グループ、SharePoint Online サイトを含むアクセス パッケージを作成できます。|[エンタイトルメント管理](entitlement-management-overview.md)|
|アクセス要求|エンド ユーザーは、グループのメンバーシップまたはアプリケーションへのアクセスを要求できます。 エンド ユーザー (他の組織のゲストを含む) は、アクセス パッケージへのアクセスを要求できます。|[エンタイトルメント管理](entitlement-management-overview.md)|
|ワークフロー|リソース所有者は、アクセス要求の承認者とエスカレーション承認者、およびロールのアクティブ化要求の承認者を定義できます。  |[エンタイトルメント管理](entitlement-management-overview.md)と [PIM](../privileged-identity-management/pim-configure.md)|
|ポリシーとロールの管理|管理者は、アプリケーションへの実行時アクセスに対して条件付きアクセス ポリシーを定義できます。  リソース所有者は、アクセス パッケージを使用してユーザーのアクセスに対してポリシーを定義できます。|[条件付きアクセス](../conditional-access/overview.md) ポリシーと[エンタイトルメント管理](entitlement-management-overview.md)ポリシー|
|アクセス認定|管理者は、次に対して定期的なアクセスの再認定を有効にすることができます。SaaS アプリまたはクラウド グループのメンバーシップ、Azure AD または Azure リソース ロールの割り当て。 リソース アクセスを自動的に削除し、ゲスト アクセスをブロックして、ゲスト アカウントを削除します。|[アクセス レビュー](access-reviews-overview.md)。[PIM](../privileged-identity-management/pim-how-to-start-security-review.md) にも表示されます|
|フルフィルメントとプロビジョニング|Azure AD 接続されたアプリへの自動プロビジョニングとプロビジョニング解除 (SCIM を介した、および SharePoint Online サイトへの、を含む)。 |[ユーザー プロビジョニング](../app-provisioning/user-provisioning.md)|
|レポートと分析|管理者は、最近のユーザー プロビジョニングおよびサインオン アクティビティの監査ログを取得できます。 アクセス パッケージを使用した Azure Monitor と "アクセス権を持つユーザー" との統合。|[Azure AD レポート](../reports-monitoring/overview-reports.md)と[監視](../reports-monitoring/overview-monitoring.md)|
|特権アクセス|Just-In-Time およびスケジュールを指定したアクセス、アラート、Azure AD ロール (カスタム ロールを含む) と Azure リソース ロールの承認ワークフロー。|[Azure AD PIM](../privileged-identity-management/pim-configure.md)|
|監査|管理者は、管理者アカウントの作成についてアラートを受けることができます。|[Azure AD PIM アラート](../privileged-identity-management/pim-how-to-configure-security-alerts.md)|

## <a name="getting-started"></a>作業の開始

エンタイトルメント管理やアクセス レビューの使用を開始したり、利用規約を確認したりするには、Azure portal の **Identity Governance** の [Getting started]\(作業の開始\) タブを確認します。

![Identity Governance の作業の開始](./media/identity-governance-overview/getting-started.png)


Identity Governance 機能についてフィードバックがありましたら、Azure portal から **[Got feedback?]\(フィードバックがありますか\)** をクリックしてお寄せください。 お客様からのフィードバックは、チームにて定期的に検討しております。

すべての顧客にとって完璧なソリューションや推奨事項はありませんが、従業員の安全性と生産性を高めるために従うことを Microsoft が推奨するベースライン ポリシーについては、次の構成ガイドでもご覧いただけます。

- [ID とデバイスのアクセスの構成](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [特権アクセスのセキュリティ保護](../roles/security-planning.md)

## <a name="appendix---least-privileged-roles-for-managing-in-identity-governance-features"></a>付録 - Identity Governance の機能で管理するための最小特権ロール

Identity Governance で管理タスクを実行するには、最小特権ロールを使用するのがベスト プラクティスです。 必要に応じて Azure AD PIM を使用してロールをアクティブ化し、これらのタスクを実行することをお勧めします。 Identity Governance の機能を構成するための最小特権ディレクトリ ロールを次に示します。

| 機能 | 最小特権ロール |
| ------- | --------------------- |
| エンタイトルメント管理 | ユーザー管理者 (グローバル管理者が必要な、カタログへの SharePoint Online サイトの追加を除きます) |
| アクセス レビュー | ユーザー管理者 (特権ロール管理者が必要な、Azure ロールまたは Azure AD ロールのアクセス レビューを除きます) |
|Privileged Identity Management | 特権ロール管理者 |
| 使用条件 | セキュリティ管理者または条件付きアクセス管理者 |

## <a name="next-steps"></a>次のステップ

- [Azure AD エンタイトルメント管理とは](entitlement-management-overview.md)
- [Azure AD アクセス レビューとは](access-reviews-overview.md)
- [Azure AD Privileged Identity Management とは](../privileged-identity-management/pim-configure.md)
- [利用規約でできること](../conditional-access/terms-of-use.md)