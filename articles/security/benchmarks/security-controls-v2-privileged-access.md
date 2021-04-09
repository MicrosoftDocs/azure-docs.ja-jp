---
title: Azure セキュリティ ベンチマーク V2 - 特権アクセス
description: Azure セキュリティ ベンチマーク V2 特権アクセス
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 29d4acc50ed872c37268a0b21c3e34837249a026
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035417"
---
# <a name="security-control-v2-privileged-access"></a>セキュリティ コントロール V2:特権アクセス

特権アクセスには、Azure テナントとリソースへの特権アクセスを保護するためのコントロールが含まれます。 これには、管理モデル、管理アカウント、特権アクセス ワークステーションを、意図的なリスクと偶発的なリスクから保護するためのさまざまなコントロールが含まれています。

該当する組み込み Azure Policy を確認するには、「[Azure セキュリティ ベンチマーク規制コンプライアンスの組み込みイニシアチブ: 特権アクセス](../../governance/policy/samples/azure-security-benchmark.md#privileged-access)」をご覧ください。

## <a name="pa-1-protect-and-limit-highly-privileged-users"></a>PA-1:高い特権を持つユーザーを保護および制限する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-1 | 4.3、4.8 | AC-2 |

高い特権を持つユーザー アカウントの数を制限し、これらのアカウントを昇格されたレベルで保護します。 Azure AD で最も重要な組み込みロールは、全体管理者と特権ロール管理者です。それは、これら 2 つのロールが割り当てられたユーザーが、管理者ロールを委任できるからです。 これらの特権を使用すると、ユーザーは Azure 環境内のすべてのリソースを直接または間接に読み取り、変更できます。

- グローバル管理者:このロールが割り当てられたユーザーは、Azure AD のすべての管理機能に加え、Azure AD ID を使用するサービスにもアクセスできます。

- 特権ロール管理者:このロールが割り当てられたユーザーは、Azure AD と Azure AD Privileged Identity Management (PIM) 内でロールの割り当てを管理できます。 さらに、このロールは、PIM と管理単位のすべての側面を管理できます。

注:特定の特権アクセス許可を割り当てられたカスタム ロールを使用する場合は、管理する必要のある他の重要なロールがある場合があります。 また、重要なビジネス資産の管理者アカウントに同様のコントロールを適用することもできます。

Azure Privileged Identity Management (PIM) を使用して、Azure リソースと Azure AD への Just-In-Time (JIT) の特権アクセスを有効にすることができます。 ユーザーが必要とする場合にのみ特権タスクを実行するための一時的なアクセス許可は、JIT によって付与されます。 PIM を使用すると、Azure AD 組織に不審なアクティビティや安全でないアクティビティがある場合に、セキュリティ アラートを生成することもできます。

- [Azure AD での管理者ロールのアクセス許可](../../active-directory/roles/permissions-reference.md)

- [Azure Privileged Identity Management のセキュリティ アラートを使用する](../../active-directory/privileged-identity-management/pim-how-to-configure-security-alerts.md)

- [Azure AD でのハイブリッドおよびクラウド デプロイ用の特権アクセスをセキュリティで保護する](../../active-directory/roles/security-planning.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [セキュリティ運用](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2:ビジネス クリティカルなシステムへの管理アクセスを制限する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-2 | 13.2、2.10 | AC-2、SC-3、SC-7 |

アカウントが属するサブスクリプションと管理グループへの特権アクセスを許可するアカウントを制限することによって、ビジネス クリティカルなシステムへのアクセスを分離します。 また、ビジネス クリティカルなシステムにインストールされたエージェントを使用して、Active Directory ドメイン コントローラー (DC)、セキュリティ ツール、システム管理ツールなど、ビジネス クリティカルな資産への管理アクセス権を持つ管理、ID、およびセキュリティ システムへのアクセスも制限します。 これらの管理システムおよびセキュリティ システムを侵害した攻撃者は、それらをすぐに悪用してビジネス クリティカルな資産を侵害することができます。 

一貫したアクセス制御を確保するため、自分の企業のセグメント化戦略にすべての種類のアクセス制御を合わせる必要があります。 

電子メール、閲覧、生産性のタスクに使用される標準ユーザー アカウントとは別に、特権アカウントを割り当てます。

- [Azure のコンポーネントと参照モデル](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [管理グループ アクセス](../../governance/management-groups/overview.md#management-group-access)

- [Azure サブスクリプション管理者](../../cost-management-billing/manage/add-change-subscription-administrator.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [セキュリティのアーキテクチャ](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

## <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3:ユーザー アクセスを定期的に確認して調整する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-3 | 4.1、16.9、16.10 | AC-2 |

ユーザー アカウントとアクセスの割り当てを定期的に確認して、アカウントとそのアクセス レベルが有効であることを確実にします。 Azure AD アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てをレビューすることができます。 Azure AD レポートによって、古いアカウントの検出に役立つログが提供されます。 また、Azure AD Privileged Identity Management を使用してアクセス レビュー レポート ワークフローを作成し、レビュー プロセスを容易にすることもできます。
また、Azure Privileged Identity Management を構成して、過剰な数の管理者アカウントが作成されたらアラートを生成したり、古くなったり不適切に構成されている管理者アカウントを識別したりできます。 

注:一部の Azure サービスでは、Azure AD で管理されていないローカル ユーザーとロールがサポートされています。 これらのユーザーは個別に管理する必要があります。

- [Privileged Identity Management (PIM) で Azure リソース ロールのアクセス レビューを作成する](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4:Azure AD で緊急アクセスを設定する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-4 | 16 | AC-2、CP-2 |

Azure AD 組織から誤ってロックアウトされるのを防ぐために、通常の管理者アカウントを使用できない場合にアクセスするための緊急アクセス用アカウントを設定します。 緊急アクセス用アカウントは高い特権を持っており、特定の個人に割り当てることはできません。 緊急アクセス用アカウントは、通常の管理者アカウントを使うことができない "緊急事態" に制限されます。
緊急アクセス用アカウントの資格情報 (パスワード、証明書、スマート カードなど) は安全に保管し、緊急時にのみそれらを使うことを許可された個人のみに知らせる必要があります。

- [Azure AD で緊急アクセス用アカウントを管理する](../../active-directory/roles/security-emergency-access.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [セキュリティ操作 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

## <a name="pa-5-automate-entitlement-management"></a>PA-5:エンタイトルメント管理を自動化する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-5 | 16 | AC-2、AC-5、PM-10 |

Azure AD のエンタイトルメント管理機能を使用して、アクセスの割り当て、レビュー、有効期限など、アクセス要求のワークフローを自動化します。 2 段階または複数段階の承認もサポートされています。
- [Azure AD アクセス レビューとは](../../active-directory/governance/access-reviews-overview.md) 

- [Azure AD エンタイトルメント管理とは](../../active-directory/governance/entitlement-management-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="pa-6-use-privileged-access-workstations"></a>PA-6:特権アクセス ワークステーションを使用する

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-6 | 4.6、11.6、12.12 | AC-2、SC-3、SC-7 |

セキュリティで保護された分離したワークステーションは、管理者、開発者、重要なサービス オペレーターのような機密性の高い役割のセキュリティには非常に重要です。 管理タスクに高度にセキュリティ保護されたユーザー ワークステーションや Azure Bastion を使用します。 Azure Active Directory、Microsoft Defender Advanced Threat Protection (ATP)、または Microsoft Intune を使用して、管理タスクのためにセキュリティで保護されたマネージド ユーザー ワークステーションを展開します。 セキュリティで保護されたワークステーションを一元管理して、強力な認証、ソフトウェアとハードウェアのベースライン、制限された論理アクセスとネットワーク アクセスなどのセキュリティで保護された構成を実施できます。 

- [特権アクセス ワークステーションを理解する](/security/compass/privileged-access-deployment)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ操作 (SecOps)](/azure/cloud-adoption-framework/organize/cloud-security-operations-center)

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7:Just Enough Administration (最小限の特権の原則) に従う

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-7 | 14.6 | AC-2、AC-3、SC-3 |

Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、ロールの割り当てを通じて Azure リソースへのアクセスを管理できます。 これらのロールを、ユーザー、グループ サービス プリンシパル、およびマネージド ID に割り当てることができます。 特定のリソースに対して定義済みの組み込みロールがあります。これらのロールは、Azure CLI、Azure PowerShell、Azure portal などのツールを使用してインベントリまたは照会できます。 Azure RBAC を使用してリソースに割り当てる特権は、常に、ロールで必要なものに制限する必要があります。 制限された特権は、Azure AD Privileged Identity Management (PIM) の Just-In-Time (JIT) アプローチを補完するものであり、それらの特権は定期的に確認する必要があります。

組み込みロールを使用してアクセス許可を割り当て、必要な場合にのみカスタム ロールを作成します。

- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)

- [Azure RBAC を構成する方法](../../role-based-access-control/role-assignments-portal.md)

- [Azure AD の ID およびアクセス レビューの使用方法](../../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [体制管理](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

## <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Microsoft サポートの承認プロセスを選択する 

| Azure ID | CIS コントロール v7.1 ID | NIST SP 800-53 r4 ID |
|--|--|--|--|
| PA-8 | 16 | AC-2、AC-3、AC-4 |

Microsoft が顧客データにアクセスする必要があるサポート シナリオでは、カスタマー ロックボックスによって、各顧客のデータ アクセス要求を明示的に確認して承認または拒否する機能が提供されます。

- [カスタマー ロックボックスについて](../fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**顧客のセキュリティ上の利害関係者** ([詳細](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [アプリケーションのセキュリティと DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [セキュリティ コンプライアンス管理](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management) 

- [ID およびキー管理](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)