---
title: Privileged Identity Management (PIM) をデプロイする - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) のデプロイを計画する方法について説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036828"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Azure AD Privileged Identity Management (PIM) をデプロイする

この記事は、Azure Active Directory (Azure AD) 組織での Privileged Identity Management (PIM) のデプロイを計画する方法について説明するステップ バイ ステップ ガイドです。 可能であれば高い特権を持つロールのユーザーをあまり強力でない組み込みロールまたはカスタム ロールに再割り当てし、ほとんどの特権ロールに対して Just-In-Time ロール割り当てを計画します。 この記事では、デプロイの計画と実装の両方について推奨事項を紹介します。

> [!TIP]
> この記事全体で、次のようなマークが項目に付けられています。
>
> :heavy_check_mark:**Microsoft のお勧め**
>
> これらは一般的な推奨事項であり、特定の企業のニーズに該当する場合にのみ実装する必要があります。

## <a name="licensing-requirements"></a>ライセンスの要件

Privileged Identity Management を使用するには、お客様のディレクトリに次のいずれかの有料ライセンスまたは試用版ライセンスが必要です。 詳細については、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>PIM のしくみ

このセクションでは、Privileged Identity Management プロセスの関連部分を計画のために再確認します。 詳しくは、「[Azure AD Privileged Identity Management とは](pim-configure.md)」を参照してください。

1. Privileged Identity Management の使用を開始して、ユーザーに特権ロールの資格を付与します。
1. 資格のあるユーザーは、特権ロールを使用する必要がある場合、Privileged Identity Management を使用してロールをアクティブ化します。
1. 設定で、ユーザーに対して次の操作を求めることができます。

    - Multi-Factor Authentication の使用
    - アクティブ化の承認の要求
    - アクティブ化する業務上の理由の提供

1. ユーザーは、自分のロールを正常にアクティブ化した後、設定された期間内にロールのアクセス許可を取得します。
1. 管理者は、監査ログで Privileged Identity Management のすべてのアクティビティの履歴を表示できます。 また、アクセス レビューやアラートなどの Privileged Identity Management 機能を使用して、Azure AD 組織をセキュリティで保護し、コンプライアンスに対応することもできます。

## <a name="roles-that-can-be-managed-by-pim"></a>PIM で管理可能なロール

**Azure AD ロール** は、(グローバル管理者、Exchange 管理者、セキュリティ管理者など) Azure Active Directory 内にあるすべてです。 ロールおよびそれらの機能の詳細については、「[Azure Active Directory の管理者ロール アクセス許可](../roles/permissions-reference.md)」を参照してください。 管理者を割り当てるロールを判断する際に役立つ[タスク別の最小特権ロール](../roles/delegate-by-task.md)に関するページも参照してください。

**Azure ロール** は、Azure リソース、リソース グループ、サブスクリプション、または管理グループにリンクされているロールです。 PIM を使用して、所有者、ユーザー アクセス管理者、共同作成者などの組み込み Azure ロールと[カスタム ロール](../../role-based-access-control/custom-roles.md)の両方に対する Just-In-Time アクセスを提供できます。 Azure ロールの詳細については、[Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)に関するページをご覧ください。

詳細については、「[Privileged Identity Management で管理できないロール](pim-roles.md)」を参照してください。

## <a name="deployment-plan"></a>デプロイ計画

Privileged Identity Management を組織にデプロイする前に、このセクションの手順に従い、概念を理解しておくと、組織の特権 ID の要件に合わせて計画を作成できるようになります。

### <a name="identify-your-stakeholders"></a>利害関係者を識別する

以下のセクションは、プロジェクトに関連するすべての利害関係者を識別し、サインアウト、確認、または最新情報の把握が必要な場合に役立ちます。 これには、Azure AD ロール用の PIM と、Azure ロール用の PIM のデプロイに関する個別の表が含まれます。 ご自分の組織に合わせて、利害関係者を以下の表に追加してください。

- SO = このプロジェクトをサインオフする
- R = このプロジェクトを確認し、情報を提供する
- I = このプロジェクトに関する情報を受け取る

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>利害関係者:Azure AD ロール用の Privileged Identity Management

| 名前 | Role | アクション |
| --- | --- | --- |
| 名前とメール | **ID アーキテクトまたは Azure グローバル管理者**<br/>この変更を組織内の主要な ID 管理インフラストラクチャに合わせる方法を定義する責任がある、ID 管理チームの代表。 | SO/R/I |
| 名前とメール | **サービス所有者 / ライン マネージャー**<br/>サービスまたはサービス グループの IT 所有者の代表。 主に決定を下し、チームの Privileged Identity Management のロールアウトを支援します。 | SO/R/I |
| 名前とメール | **セキュリティ所有者**<br/>計画が組織のセキュリティ要件を満たしていることを承認できるセキュリティ チームの代表。 | SO/R |
| 名前とメール | **IT サポート マネージャー / ヘルプデスク**<br/>ヘルプデスクの観点から、この変更のサポート可能性に関するフィードバックを提供できる、IT サポート組織の代表。 | R/I |
| パイロット ユーザーの名前とメール | **特権ロール ユーザー**<br/>特権 ID 管理が実装されるユーザーのグループ。 Privileged Identity Management が実装されてからそのロールをアクティブ化する方法を把握する必要があります。 | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>利害関係者:Azure ロール用の Privileged Identity Management

| 名前 | Role | アクション |
| --- | --- | --- |
| 名前とメール | **サブスクリプション / リソース所有者**<br/>Privileged Identity Management をデプロイする必要がある各サブスクリプションまたはリソースの IT 所有者の代表 | SO/R/I |
| 名前とメール | **セキュリティ所有者**<br/>計画が組織のセキュリティ要件を満たしていることをサインオフできるセキュリティ チームの代表。 | SO/R |
| 名前とメール | **IT サポート マネージャー / ヘルプデスク**<br/>ヘルプデスクの観点から、この変更のサポート可能性に関するフィードバックを提供できる、IT サポート組織の代表。 | R/I |
| パイロット ユーザーの名前とメール | **Azure ロール ユーザー**<br/>特権 ID 管理が実装されるユーザーのグループ。 Privileged Identity Management が実装されてからそのロールをアクティブ化する方法を把握する必要があります。 | I |

### <a name="start-using-privileged-identity-management"></a>Privileged Identity Management の使用開始

計画プロセスの一環として、「[Privileged Identity Management の使用開始](pim-getting-started.md)」の記事に従って、Privileged Identity Management を準備する必要があります。 Privileged Identity Management を使用すると、デプロイに役立つように設計されているいくつかの機能にアクセスできます。

Azure リソース用の Privileged Identity Management をデプロイするのが目的の場合は、[Privileged Identity Management で管理する Azure リソースの検出](pim-resource-roles-discover-resources.md)に関する記事に従う必要があります。 サブスクリプションと管理グループの所有者だけが、これらのリソースを Privileged Identity Management の管理下に置くことができます。 管理下に置いた後、管理グループ、サブスクリプション、リソース グループ、リソースなど、すべてのレベルの所有者が PIM 機能を利用できるようになります。 Azure リソース用に Privileged Identity Management をデプロイしようとしているグローバル管理者である場合、[すべての Azure サブスクリプションを管理するためにアクセス権を昇格](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)し、検出のためにディレクトリのすべての Azure リソースへのアクセス権を自分自身に与えることができます。 しかし、Privileged Identity Management でリソースを管理する前に、各サブスクリプション所有者から承認を得ることをお勧めします。

### <a name="enforce-principle-of-least-privilege"></a>最小特権の原則を適用する

組織で Azure AD ロールと Azure ロールの両方に対して、最小特権の原則を適用していることを確認するのが重要です。

#### <a name="plan-least-privilege-delegation"></a>最小特権の委任を計画する

Azure AD ロールの場合、組織では、ほとんどの管理者が 1 つまたは 2 つの特定のあまり強力でない管理者ロールのみを必要とする場合に、多くの管理者にグローバル管理者ロールを割り当てるのが一般的です。 多くのグローバル管理者またはその他の高い特権を持つロールがある場合、特権ロールの割り当てを十分に厳密な方法で追跡することは困難です。

Azure AD ロールに対して最小特権の原則を実装するには、次の手順を実行します。

1. 使用可能な [Azure AD 組み込みロール](../roles/permissions-reference.md)に関するページをお読みになり、理解して、ロールの細分性を把握してください。 お客様とそのチームは、特定のタスクの最小特権ロールについて説明されている、[Azure AD の ID タスクごとの管理者ロール](../roles/delegate-by-task.md)に関するページを参照する必要もあります。

1. 組織内の特権ロールを持つユーザーのリスト。 Privileged Identity Management の[検出と分析情報 (プレビュー)](pim-security-wizard.md) を使用して、露出を減らすことができます。

    ![特権ロール経由の露出を減らすための検出と分析情報 (プレビュー) ページ](./media/pim-deployment-plan/new-preview-page.png)

1. 組織内のすべてのグローバル管理者の場合、ロールが必要である理由を確認します。 次に、グローバル管理者ロールからそれらを削除し、Azure Active Directory 内で低い特権を持つ組み込みロールまたはカスタム ロールを割り当てます。 ちなみに、Microsoft には現在、グローバル管理者ロールを持つ約 10 人の管理者のみが存在します。 詳細については、[Microsoft での Privileged Identity Management の使用方法](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)に関するページを参照してください。

1. 他のすべての Azure AD ロールの場合は、割り当てリストを確認し、ロールが不要となった管理者を特定し、それらの割り当てから削除します。

最後の 2 つの手順を自動化するために、Privileged Identity Management でアクセス レビューを使用することができます。 [Privileged Identity Management での Azure AD ロールのアクセス レビューの開始](pim-how-to-start-security-review.md)に関するページの手順に従うことで、1 人以上のメンバーを持つ各 Azure AD ロールに対してアクセス レビューを設定できます。

![Azure AD ロールの [アクセス レビューを作成する] ウィンドウ](./media/pim-deployment-plan/create-access-review.png)

レビュー担当者を **[メンバー (セルフ)]** に設定します。 ロール内のすべてのメンバーに、アクセスが必要かどうかの確認を求めるメールが届きます。 また、詳細設定で **[承認時に理由が必要]** をオンにします。この場合、ユーザーはロールを必要とする理由を示す必要があります。 この情報に基づき、不要なロールからユーザーを削除したり、より細かいレベルの管理者ロールにユーザーを委任したりすることができます。

アクセス レビューは、ロールへのアクセスを確認するように担当者に通知するメールに依存します。 メールがリンクされていない特権アカウントがある場合は、必ず、それらのアカウントに対してセカンダリ メール フィールドを設定してください。 詳細については、[Azure AD の proxyAddresses 属性](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad)に関するページを参照してください。

#### <a name="plan-azure-resource-role-delegation"></a>Azure リソース ロールの委任を計画する

Azure サブスクリプションとリソースについては、同様のアクセス レビュー プロセスを設定し、各サブスクリプションまたはリソースのロールを確認できます。 このプロセスの目標は、各サブスクリプションまたはリソースにアタッチされている所有者およびユーザー アクセス管理者割り当てを最小限にし、不要な割り当てを削除することです。 しかし、組織では多くの場合、そのようなタスクを各サブスクリプションまたはリソースの所有者に委任します。これは、その所有者が特定のロール (特にカスタム ロール) をよりよく理解しているためです。

組織内の Azure ロールに対して PIM をデプロイしようとしているグローバル管理者ロールを持つユーザーは、[すべての Azure サブスクリプションを管理するためにアクセス権を昇格](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)し、各サブスクリプションにアクセスできます。 その後、各サブスクリプション所有者を見つけ、協力して不要な割り当てを削除し、所有者ロールの割り当てを最小限にすることができます。

また、Azure サブスクリプションの所有者ロールを持つユーザーは、[Azure リソースのアクセス レビュー](pim-resource-roles-start-access-review.md)を使用して、Azure AD ロールについて前述したプロセスと同様に、不要なロール割り当てを監査して削除できます。

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Privileged Identity Management で保護する必要があるロール割り当てを決定する

組織内の特権ロール割り当てをクリーンアップした後、Privileged Identity Management で保護するロールを決定する必要があります。

ロールが Privileged Identity Management によって保護されている場合、それに割り当てられている資格のあるユーザーは、そのロールによって付与される特権を使用するために昇格する必要があります。 昇格プロセスには、承認の取得、多要素認証の使用、アクティブ化する理由の提供が含まれる場合もあります。 Privileged Identity Management では、通知および Privileged Identity Management と Azure AD 監査イベント ログを通じて、昇格を追跡することもできます。

Privileged Identity Management で保護するロールを選択することは困難である場合があり、組織ごとに異なります。 このセクションでは、Azure AD ロールと Azure ロールについてのベスト プラクティス アドバイスを提供します。

#### <a name="azure-ad-roles"></a>Azure AD ロール

最も多くのアクセス許可を持つ Azure AD ロールの保護に優先順位を付けることが重要です。 すべての Privileged Identity Management 顧客の使用パターンに基づいて、Privileged Identity Management によって管理されている上位 10 個の Azure AD ロールは次のとおりです。

1. 全体管理者
1. セキュリティ管理者
1. ユーザー管理者
1. Exchange 管理者
1. SharePoint 管理者
1. Intune 管理者
1. セキュリティ閲覧者
1. サービス管理者
1. 課金管理者
1. Skype for Business 管理者

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 最初の手順として、すべてのグローバル管理者とセキュリティ管理者を Privileged Identity Management を使用して管理します。これらは侵害が発生したときに最も害を及ぼす可能性があるユーザーだからです。

組織でどのデータとアクセス許可を最も機密性の高いものと見なすかが重要です。 たとえば、一部の組織では、Privileged Identity Management を使用して Power BI 管理者ロールまたはチーム管理者ロールを保護できます。これらのロールでデータにアクセスしたり、コア ワークフローを変更したりできるためです。

ゲスト ユーザーが割り当てられているロールは、攻撃に対して脆弱です。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: Privileged Identity Management を使用してゲスト ユーザーが割り当てられているすべてのロールを管理し、侵害されたゲスト ユーザー アカウントに関するリスクを減らします。

ディレクトリ閲覧者、メッセージ センター閲覧者、セキュリティ閲覧者などの閲覧者ロールは、書き込みアクセス許可がないため、他のロールより重要でないと見なされる場合があります。 しかし、これらのロールを保護するお客様もいらっしゃいます。これは、これらのアカウントへのアクセス権を得た攻撃者によって、個人データなどの機密データが読み取られる可能性があるためです。 組織内の閲覧者ロールを Privileged Identity Management を使用して管理する必要があるかどうかを判断するときは、この点を考慮してください。

#### <a name="azure-roles"></a>Azure ロール

Azure リソース用の Privileged Identity Management を使用して管理する必要があるロール割り当てを決定するときに、まず、組織で最も重要なサブスクリプション/リソースを特定する必要があります。 このようなサブスクリプション/リソースの例を以下に示します。

- 最も機微なデータをホストするリソース
- 主要な顧客向けアプリケーションが依存するリソース

最も重要なサブスクリプションとリソースをうまく決定できないグローバル管理者は、組織内のサブスクリプション所有者に連絡し、各サブスクリプションで管理されているリソースのリストを収集する必要があります。 その後、サブスクリプション所有者と協力しながら、リソースが侵害された場合の重大度レベル (低、中、高) に基づいて、そのリソースをグループ化します。 この重大度レベルに基づき、Privileged Identity Management でのリソース管理に優先順位を付けます。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 重要なサービスのサブスクリプション/リソース所有者と協力し、機微なサブスクリプション/リソース内のすべてのロールに対して、Privileged Identity Management ワークフローを設定します。

Azure リソース用の Privileged Identity Management では、期限付きのサービス アカウントがサポートされます。 通常のユーザー アカウントを扱う方法とまったく同じように、サービス アカウントを扱う必要があります。

重要でないサブスクリプション/リソースの場合は、すべてのロールに対して Privileged Identity Management を設定する必要はありません。 しかし、所有者ロールとユーザー アクセス管理者ロールについては、Privileged Identity Management で引き続き保護する必要があります。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: Privileged Identity Management を使用して、すべてのサブスクリプション/リソースの所有者ロールとユーザー アクセス管理者ロールを管理します。

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>グループを使用してロールを割り当てるかどうかを決定する

個々のユーザーではなくグループにロールを割り当てるかどうかは、戦略的な意思決定です。 計画するときに、次の場合はグループにロールを割り当ててロール割り当てを管理することを検討してください。

- 多くのユーザーが 1 つのロールに割り当てられる
- ロールの割り当てを委任したい

#### <a name="many-users-are-assigned-to-a-role"></a>多くのユーザーが 1 つのロールに割り当てられる

ロールに割り当てられるユーザーを追跡し、ユーザーがロールを必要とするタイミングに基づいてその割り当てを管理する作業は、手動で行うと時間がかかります。 ロールにグループを割り当てるには、最初に[ロール割り当て可能なグループを作成](../roles/groups-create-eligible.md)してから、そのグループをロールの有資格として割り当てます。 この操作により、グループ内のすべてのユーザーに対して、そのロールに昇格する資格を持つ個々のユーザーと同じアクティブ化プロセスが適用されます。 グループ メンバーは、Privileged Identity Management のアクティブ化要求および承認プロセスを使用して、グループへの割り当てを個別にアクティブ化します。 アクティブ化されるのは、グループではなく、ユーザーのグループ メンバーシップだけです。

#### <a name="you-want-to-delegate-assigning-the-role"></a>ロールの割り当てを委任したい

グループ所有者は、グループのメンバーシップを管理できます。 Azure AD のロール割り当て可能なグループのグループ メンバーシップを管理できるのは、特権ロール管理者、グローバル管理者、グループ所有者だけです。 グループに新しいメンバーを追加すると、そのメンバーは、割り当てが有資格かアクティブかに関係なく、そのグループが割り当てられているロールへのアクセス権を取得します。 グループ所有者を使用して、割り当てられたロールのグループ メンバーシップの管理を委任すると、必要な特権の範囲を縮小できます。 グループの作成時にグループに所有者を割り当てる方法の詳細については、「[Azure Active Directory でロールを割り当て可能なグループを作成する](../roles/groups-create-eligible.md)」を参照してください。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: Azure AD のロール割り当て可能なグループを Privileged Identity Management の管理下に置きます。 ロール割り当て可能なグループを PIM の管理下に置くと、そのグループは特権アクセス グループと呼ばれます。 PIM を使用して、グループのメンバーシップを管理する前に所有者ロールの割り当てをアクティブ化するようにグループ所有者に要求します。 グループを PIM の管理下に置く方法の詳細については、[特権アクセス グループ (プレビュー) を Privileged Identity Management に取り込む](groups-discover-groups.md)方法に関するページをご覧ください。

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>永続的とする、または有資格とする必要があるロール割り当てを決定する

Privileged Identity Management で管理するロールのリストを決定したら、資格のあるロールと永続的にアクティブなロールを取得する必要があるユーザーを決定する必要があります。 永続的にアクティブなロールは、Azure Active Directory および Azure リソースを通じて割り当てられる通常のロールですが、資格のあるロールは Privileged Identity Management でのみ割り当てることができます。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 永続的なグローバル管理者ロールが必要である、推奨される [2 つの非常時の緊急アクセス アカウント](../roles/security-emergency-access.md)を除き、Azure AD ロールと Azure ロールの両方に対する永続的にアクティブな割り当てをゼロにします。

継続的な管理者をゼロにすることをお勧めしますが、組織でこれをすぐに実現するのは困難なことがあります。 この決定を行う際の考慮事項を以下に示します。

- 昇格の頻度 – ユーザーに特権割り当てが必要なのは 1 回のみである場合、永続的な割り当ては必要ありません。 一方、日常業務のロールを必要とし、Privileged Identity Management を使用することで生産性が大幅に低下する場合は、永続的なロールの対象にすることができます。
- 組織に固有のケース – 資格のあるロールを与えられている人が、連絡および適用するには離れたチームに所属しているか、地位の高い役員であり、昇格プロセスが困難な場合は、永続的なロールの対象にすることができます。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 永続的なロールを割り当てるユーザーについて、定期的なアクセス レビューを設定します (該当する場合)。 このデプロイ計画の最後のセクションでは、定期的なアクセス レビューの詳細を確認します

### <a name="draft-your-privileged-identity-management-settings"></a>Privileged Identity Management 設定のドラフトを作成する

Privileged Identity Management ソリューションを実装する前に、組織で使用するすべての特権ロールに対して、Privileged Identity Management 設定のドラフトを作成することをお勧めします。 このセクションには、特定のロールに対する Privileged Identity Management 設定の例がいくつか含まれています (これらは参照目的のみであり、実際の組織では異なる場合があります)。 これらの各設定については、表の後の Microsoft のお勧めで詳しく説明します。

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Azure AD ロール用の Privileged Identity Management 設定

| Role | Require MFA (MFA が必須) | Notification | インシデント チケット | 承認を要求する | 承認者 | アクティブ化期間 | 永続的な管理者 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| グローバル管理者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | その他のグローバル管理者 | 1 時間 | 緊急アクセス アカウント |
| Exchange 管理者 | :heavy_check_mark: | :heavy_check_mark: | :x: | :x: | なし | 2 時間 | なし |
| ヘルプデスク管理者 | :x: | :x: | :heavy_check_mark: | :x: | なし | 8 時間 | なし |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Azure ロール用の Privileged Identity Management 設定

| Role | Require MFA (MFA が必須) | Notification | 承認を要求する | 承認者 | アクティブ化期間 | アクティブな管理者 | アクティブな有効期限 | 有資格の有効期限 |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| 重要なサブスクリプションの所有者 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | サブスクリプションのその他の所有者 | 1 時間 | なし | 該当なし | 3 か月 |
| それほど重要ではないサブスクリプションのユーザー アクセス管理者 | :heavy_check_mark: | :heavy_check_mark: | :x: | なし | 1 時間 | なし | 該当なし | 3 か月 |
| Virtual Machine Contributor | :x: | :heavy_check_mark: | :x: | なし | 3 時間 | なし | 該当なし | 6 か月 |

次の表で各設定について説明します。

| 設定 | 説明 |
| --- | --- |
| Role | 設定を定義するロールの名前。 |
| Require MFA (MFA が必須) | ロールをアクティブ化する前に、資格のあるユーザーが MFA を実行する必要があるかどうか。<br/><br/> :heavy_check_mark:**Microsoft のお勧め**: 特にゲスト ユーザーが割り当てられているロールの場合、すべての管理者ロールに対して MFA を適用します。 |
| Notification | true に設定すると、組織内のグローバル管理者、特権ロール管理者、およびセキュリティ管理者は、資格のあるユーザーがロールをアクティブ化したときに、メール通知を受け取ります。<br/><br/>**注:** 一部の組織ではその管理者アカウントにメール アドレスが関連付けられていません。これらのメール通知を取得するには、管理者がこれらのメールを受け取るように、代替メール アドレスを設定する必要があります。 |
| インシデント チケット | 資格のあるユーザーがロールをアクティブ化するときに、インシデント チケット番号を記録する必要があるかどうか。 この設定は、組織で内部インシデント番号を使用して各アクティブ化を特定し、不要なアクティブ化を軽減するのに役立ちます。<br/><br/> :heavy_check_mark:**Microsoft のお勧め**: 内部システムに Privileged Identity Management を関連付けるために、インシデント チケット番号を活用します。 この方法は、承認者がアクティブ化のコンテキストを必要とする場合に役立ちます。 |
| 承認を要求する | 資格のあるユーザーが、ロールをアクティブ化するために承認を得る必要があるかどうか。<br/><br/> :heavy_check_mark:**Microsoft のお勧め**: 最も多くのアクセス許可を持つロールに対して承認を設定します。 すべての Privileged Identity Management 顧客の使用パターンに基づき、グローバル管理者、ユーザー管理者、Exchange 管理者、セキュリティ管理者、およびパスワード管理者は承認が設定される最も一般的なロールです。 |
| 承認者 | 資格のあるロールをアクティブ化するために承認が必要な場合、要求を承認する必要がある人をリストします。 既定では、Privileged Identity Management により、特権ロール管理者であるすべてのユーザーが承認者として設定されます。永続的であるか、有資格であるかは関係ありません。<br/><br/>**注:** ユーザーが Azure AD ロールの対象であり、さらにロールの承認者である場合、自身を承認することはできません。<br/><br/> :heavy_check_mark:**Microsoft のお勧め**: グローバル管理者ではなく、ロールとそのアクセスの多いユーザーについて最も知識が豊富なユーザーを承認者として選びます。 |
| アクティブ化期間 | 期限が切れる前に、ロールでユーザーがアクティブ化される期間。 |
| 永続的な管理者 | ロールの永続的な管理者になるユーザーのリスト (アクティブ化することはありません)。<br/><br/> :heavy_check_mark:**Microsoft のお勧め**: グローバル管理者以外のすべてのロールに対して、継続的な管理者をゼロにします。 詳細については、この計画の、対象にする必要があるユーザーと、永続的にアクティブにする必要があるユーザーに関するセクションを参照してください。 |
| アクティブな管理者 | Azure リソースの場合、アクティブな管理者は、ロールを使用するためにアクティブ化する必要がないユーザーのリストとなります。 このリストは、Azure AD ロールの場合のように永続的な管理者と見なされません。ユーザーがこのロールを失う有効期限を設定できるためです。 |
| アクティブな有効期限 | Azure ロールのアクティブなロール割り当ての有効期限は、構成された期間後に切れます。 アクティブな期間は、15 日、1 か月、3 か月、6 か月、1 年または永続的から選ぶことができます。 |
| 有資格の有効期限 | Azure ロールの資格のあるロール割り当ての有効期限は、この期間後に切れます。 有資格の期間は、15 日、1 か月、3 か月、6 か月、1 年または永続的から選ぶことができます。 |

## <a name="implementation-plan"></a>実装計画

適切な計画の基盤は、Azure Active Directory で正常にアプリケーションをデプロイできるベースとなります。  インテリジェント セキュリティと統合を提供し、正常なデプロイのための時間を短縮しつつ、オンボードを簡略化します。  この組み合わせにより、エンド ユーザーのダウンタイムを軽減しつつ、確実にアプリケーションが簡単に統合されるようになります。

### <a name="identify-test-users"></a>テスト ユーザーを特定する

このセクションを使用して、実装を検証するために一連のユーザーやユーザー グループを特定します。 計画セクションで選択した設定に基づいて、各ロールに対してテストするユーザーを特定します。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 各 Azure AD ロールのサービス所有者をテスト ユーザーにし、プロセスに慣れ、ロールアウトのための内部アドボケーターになれるようにします。

この表では、ロールの設定が機能していることを確認するテスト ユーザーを特定します。

| ロール名 | テスト ユーザー |
| --- | --- |
| &lt;ロール名&gt; | &lt;ロールをテストするユーザー&gt; |
| &lt;ロール名&gt; | &lt;ロールをテストするユーザー&gt; |

### <a name="test-implementation"></a>実装をテストする

これでテスト ユーザーを特定できたので、以下の手順を使用して、テスト ユーザー用に Privileged Identity Management を構成します。 組織で、Azure portal 内の Privileged Identity Management を使用するのではなく、Privileged Identity Management ワークフローをユーザー独自の内部アプリケーションに組み込む必要がある場合、Privileged Identity Management のすべての操作も[グラフ API](/graph/api/resources/privilegedidentitymanagement-root) を介してサポートされます。

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Azure AD ロール用に Privileged Identity Management を構成する

1. 計画内容に基づいて、[Azure AD ロールの設定を構成](pim-how-to-change-default-settings.md)します。

1. **[Azure AD ロール]** に移動し、 **[ロール]** を選択して、構成したロールを選びます。

1. テスト ユーザーのグループについては、既に永続的な管理者である場合、それらを検索し、その行の 3 つのドットを選択して永続的から有資格に変換することで、資格を付与できます。 まだロールの割り当てがない場合は、[新たに資格のある割り当てを行う](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role)ことができます。

1. テストするすべてのロールについて、手順 1 から 3 を繰り返します。

1. テスト ユーザーを設定したら、[Azure AD ロールをアクティブ化する](pim-how-to-activate-role.md)方法に関するリンクをそれらのユーザーに送信する必要があります。

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Azure ロール用に Privileged Identity Management を構成する

1. テストするサブスクリプションまたはリソース内のロール用に、[Azure リソース ロールの設定を構成](pim-resource-roles-configure-role-settings.md)します。

1. そのサブスクリプションの **[Azure リソース]** に移動し、 **[ロール]** を選択して、構成したロールを選びます。

1. テスト ユーザーのグループについては、既にアクティブな管理者である場合、それらを検索して対象にし、[そのロール割り当てを更新](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment)できます。 まだロールがない場合は、[新しいロールを割り当てる](pim-resource-roles-assign-roles.md#assign-a-role)ことができます。

1. テストするすべてのロールについて、手順 1 から 3 を繰り返します。

1. テスト ユーザーを設定したら、[Azure リソース ロールをアクティブ化する](pim-resource-roles-activate-your-roles.md)方法に関するリンクをそれらのユーザーに送信する必要があります。

このステージを使用して、ロールに対して設定した構成がすべて正しく機能するかどうかを確認する必要があります。 次の表を使用して、テストを文書化します。 また、このステージを使用して、影響を受けるユーザーとの連絡を最適化する必要があります。

| Role | アクティブ化中に予期される動作 | 実際の結果 |
| --- | --- | --- |
| グローバル管理者 | (1) MFA を要求する<br/>(2) 承認を要求する<br/>(3) 承認者が通知を受け取り、承認できる<br/>(4) ロールの有効期限が事前に設定された時間後に切れる |  |
| サブスクリプション *X* の所有者 | (1) MFA を要求する<br/>(2) 資格のある割り当ての有効期限が構成された期間後に切れる |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Privileged Identity Management について影響を受ける利害関係者に連絡する

Privileged Identity Management をデプロイすると、特権ロールのユーザー向けの手順が増えます。 Privileged Identity Management によって特権 ID に関するセキュリティ問題が大幅に減りますが、組織全体のデプロイの前に効率的に連絡するために変更が必要になります。 影響を受ける管理者の数に応じて、組織では、多くの場合、変更に関する内部ドキュメント、ビデオ、あるいはメールを作成することを選択します。 これらの連絡内容には以下のものがよく含まれます。

- PIM とは
- 組織での利点
- 影響を受ける人
- PIM のロールアウトのタイミング
- ユーザーがロールをアクティブ化するために必要な追加手順
    - 以下のドキュメントへのリンクを送信する必要があります。
    - [Azure AD ロールをアクティブ化する](pim-how-to-activate-role.md)
    - [Azure ロールをアクティブ化する](pim-resource-roles-activate-your-roles.md)
- PIM に関する問題についての連絡先情報またはヘルプデスクのリンク

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: ヘルプデスク/サポート チームとの時間を設定し、Privileged Identity Management ワークフローについて説明します (組織に内部 IT サポート チームがある場合)。 適切なドキュメントと連絡先情報を提供します。

### <a name="move-to-production"></a>運用環境に移行する

テストが正常に完了したら、Privileged Identity Management 構成で定義した各ロールのすべてのユーザーについて、テスト フェーズのすべての手順を繰り返し、Privileged Identity Management を運用環境に移行します。 Azure AD ロール用の Privileged Identity Management については、組織は、多くの場合、グローバル管理者用の Privileged Identity Management をテストし、ロールアウトしてから、その他のロール用の Privileged Identity Management をテストしてロールアウトします。 一方、Azure リソースでは、組織は通常、一度に 1 つの Azure サブスクリプションについて、Privileged Identity Management をテストしてロールアウトします。

### <a name="if-a-rollback-is-needed"></a>ロールバックが必要な場合

Privileged Identity Management が運用環境で必要に応じて機能しない場合、以下のロールバック手順が、Privileged Identity Management の設定の前の既知の正常な状態に戻すのに役立ちます。

#### <a name="azure-ad-roles"></a>Azure AD ロール

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。
1. **[Azure AD ロール]** を選択し、 **[ロール]** を選択します。
1. 構成した各ロールについて、資格のある割り当てを持つすべてのユーザーに対する省略記号 ( **...** ) を選択します。
1. **[永続化]** オプションを選択し、ロール割り当てを永続化します。

#### <a name="azure-roles"></a>Azure ロール

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。
1. **[Azure リソース]** を選択してから、ロールバックするサブスクリプションまたはリソースを選択します。
1. **[役割]** を選びます。
1. 構成した各ロールについて、資格のある割り当てを持つすべてのユーザーに対する省略記号 ( **...** ) を選択します。
1. **[永続化]** オプションを選択し、ロール割り当てを永続化します。

## <a name="next-steps-after-deploying"></a>デプロイ後の次の手順

組織の特権 ID のセキュリティ保護の観点から、Privileged Identity Management を運用環境に正常にデプロイすることは重要な一歩です。 Privileged Identity Management をデプロイすることで、セキュリティとコンプライアンスで使用する必要がある追加の Privileged Identity Management 機能が提供されます。

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Privileged Identity Management アラートを使用して特権アクセスを保護する

Privileged Identity Management の組み込みアラート機能を使用して組織を保護する方法の詳細については、「[セキュリティのアラート](pim-how-to-configure-security-alerts.md#security-alerts)」を参照してください。 これらのアラートには、管理者が特権ロールを使用していない、ロールが Privileged Identity Management 外で割り当てられている、ロールのアクティブ化の頻度が高すぎるなどがあります。 組織を完全に保護するには、定期的にアラート リストを確認し、問題を修正する必要があります。 アラートは、次のようにして表示および修正できます。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[Azure AD Privileged Identity Management]** を開きます。
1. **[Azure AD ロール]** を選択し、 **[アラート]** を選択します。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 高重大度のマークが付けられたすべてのアラートを直ちに処理します。 中および低の重大度アラートについては、最新情報を把握し、セキュリティ脅威と思われる場合には変更を加える必要があります。

特定のアラートがいずれも役に立たない場合や、組織に該当しない場合は、アラート ページで常にアラートを無視できます。 Azure AD の設定ページでは、この無視をいつでも元に戻すことができます。

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>組織の特権 ID を定期的に監査するように定期的なアクセス レビューを設定する

アクセス レビューは、特権ロールが割り当てられているユーザーまたは特定のレビュー担当者に、各ユーザーに特権 ID を必要かどうかをたずねる最適な方法です。 アクセス レビューは、攻撃対象領域を減らし、コンプライアンスを維持する必要がある場合に最適です。 アクセス レビューを開始する方法の詳細については、[Azure AD ロールのアクセス レビュー](pim-how-to-start-security-review.md)と [Azure ロールのアクセス レビュー](pim-resource-roles-start-access-review.md)に関するページをご覧ください。 一部の組織では、法令に常に準拠するために定期的なアクセス レビューを行う必要がありますが、その他の組織では、組織全体で最小特権の原則を適用するためにアクセス レビューが最適です。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: すべての Azure AD および Azure ロールに対して、四半期ごとのアクセス レビューを設定します。

ほとんどの場合、Azure AD ロールのレビュー担当者自身はユーザーですが、Azure ロールのレビュー担当者は、そのロールが属するサブスクリプションの所有者です。 しかし、多くの場合、企業には特定の人物のメール アドレスにリンクされていない特権アカウントがあります。 そのような場合、アクセスを読み取り、確認する人はいません。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 定期的に確認されるメール アドレスにリンクされていない、特権ロールが割り当てられているすべてのアカウントに対してセカンダリ メール アドレスを追加します

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>セキュリティとコンプライアンスを向上させるために監査ログを最大限に活用する

監査ログは、最新情報を把握し、規制に準拠できるようにするための場所です。 Privileged Identity Management では、現在、監査ログ内に組織のすべての履歴の以下の 30 日分の履歴が格納されています。

- 資格のあるロールのアクティブ化/非アクティブ化
- Privileged Identity Management の内外でのロールの割り当てアクティビティ
- ロール設定の変更
- 承認を設定したロール アクティブ化の要求/承認/拒否アクティビティ
- アラートの更新

グローバル管理者または特権ロール管理者である場合は、監査ログにアクセスできます。 詳細については、[Azure AD ロールの監査履歴](pim-how-to-use-audit-log.md)と [Azure ロールの監査履歴](azure-pim-resource-rbac.md)に関するページをご覧ください。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: 1 人以上の管理者が、毎週、すべての監査イベントに目を通し、毎月、監査イベントをエクスポートするようにします。

より長い期間、監査イベントを自動的に格納する必要がある場合、Privileged Identity Management の監査ログは、[Azure AD 監査ログ](../reports-monitoring/concept-audit-logs.md)に自動的に同期されます。

> [!TIP]
> :heavy_check_mark:**Microsoft のお勧め**: セキュリティとコンプライアンスを強化するため、Azure ストレージ アカウントに監査イベントをアーカイブするように [Azure ログ監視](../reports-monitoring/concept-activity-logs-azure-monitor.md)を設定します。
