---
title: HIPAA HITRUST 9.2 に関する規制コンプライアンスの詳細
description: HIPAA HITRUST 9.2 規制コンプライアンスの組み込みイニシアチブの詳細。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 03/10/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: ebf5d76ee6557312f8f06c980b5288dc9c3ccb4c
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102635486"
---
# <a name="details-of-the-hipaa-hitrust-92-regulatory-compliance-built-in-initiative"></a>HIPAA HITRUST 9.2 規制コンプライアンスの組み込みイニシアチブの詳細

この記事では、Azure Policy 規制コンプライアンスの組み込みイニシアチブの定義が、HIPAA HITRUST 9.2 の **コンプライアンス ドメイン** と **コントロール** にどのように対応するのかを、詳しく説明します。
このコンプライアンス標準の詳細については、[HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html) に関するページを参照してください。 "_所有権_" については、[Azure Policy のポリシー定義](../concepts/definition-structure.md#type)に関するページと、「[クラウドにおける共同責任](../../../security/fundamentals/shared-responsibility.md)」を参照してください。

次のマッピングは、**HIPAA HITRUST 9.2** コントロールに対するものです。 右側のナビゲーションを使用すると、特定の **コンプライアンス ドメイン** に直接移動します。 コントロールの多くは、[Azure Policy](../overview.md) のイニシアチブ定義で実装されています。 完全なイニシアチブ定義を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。
次に、**HITRUST/HIPAA** 規制コンプライアンスの組み込みイニシアチブ定義を見つけて選択します。

この組み込みイニシアチブは、[HIPAA HITRUST 9.2 ブループリント サンプル](../../blueprints/samples/hipaa-hitrust-9-2.md)の一部としてデプロイされます。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../overview.md) 定義に関連します。
> これらのポリシーは、コントロールに対する[コンプライアンスの評価](../how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** では、ポリシー定義自体のみが示されています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス標準に対するコンプライアンス ドメイン、コントロール、Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/HIPAA_HITRUST_audit.json)に関するページを参照してください。

## <a name="privilege-management"></a>特権管理

### <a name="access-to-management-functions-or-administrative-consoles-for-systems-hosting-virtualized-systems-are-restricted-to-personnel-based-upon-the-principle-of-least-privilege-and-supported-through-technical-controls"></a>仮想化システムをホストするシステムの管理機能または管理コンソールへのアクセスは、最小限の特権の原則に基づいて担当者に制限され、技術的な制御によってサポートされます。

**[ID]** :11180.01c3System.6 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="privileges-are-formally-authorized-and-controlled-allocated-to-users-on-a-need-to-use-and-event-by-event-basis-for-their-functional-role-eg-user-or-administrator-and-documented-for-each-system-productelement"></a>特権は、正式に承認および制御され、機能ロール (ユーザーや管理者など) に対して必要に応じてイベントごとにユーザーに割り当てられ、システム製品および要素ごとに文書化されます。

**[ID]** :1143.01c1System.123 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-explicitly-authorizes-access-to-specific-security-relevant-functions-deployed-in-hardware-software-and-firmware-and-security-relevant-information"></a>組織は、特定のセキュリティ関連機能 (ハードウェア、ソフトウェア、およびファームウェアにデプロイされている機能) とセキュリティ関連の情報へのアクセスを明示的に承認します。

**[ID]** :1144.01c1System.4 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="role-based-access-control-is-implemented-and-capable-of-mapping-each-user-to-one-or-more-roles-and-each-role-to-one-or-more-system-functions"></a>ロールベースのアクセス制御が実装されており、各ユーザーを 1 つ以上のロールにマップし、各ロールを 1 つ以上のシステム機能にマップすることができます。

**[ID]** :1145.01c2System.1 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[複数の所有者がサブスクリプションに割り当てられている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |管理者アクセスの冗長性を確保するため、複数のサブスクリプション所有者を指定することをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="the-organization-promotes-the-development-and-use-of-programs-that-avoid-the-need-to-run-with-elevated-privileges-and-system-routines-to-avoid-the-need-to-grant-privileges-to-users"></a>組織は、特権をユーザーに付与する必要がないように、昇格された特権とシステム ルーチンで実行する必要のないプログラムの開発と使用を促進します。

**[ID]** :1146.01c2System.23 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |監視されていないアクセスを防止するために、所有者アクセス許可を持つ外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |

### <a name="elevated-privileges-are-assigned-to-a-different-user-id-from-those-used-for-normal-business-use-all-users-access-privileged-services-in-a-single-role-and-such-privileged-access-is-minimized"></a>昇格された特権は通常の業務用に使用されるものとは異なるユーザー ID に割り当てられ、すべてのユーザーは 1 つのロールで特権サービスにアクセスし、そのような特権アクセスは最小限に抑えられます。

**[ID]** :1147.01c2System.456 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Febb62a0c-3560-49e1-89ed-27e074e9f8ad) |所有者としてのアクセス許可を持つ非推奨のアカウントは、サブスクリプションから削除する必要があります。  非推奨のアカウントは、サインインがブロックされているアカウントです。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveDeprecatedAccountsWithOwnerPermissions_Audit.json) |

### <a name="the-organization-restricts-access-to-privileged-functions-and-all-security-relevant-information"></a>組織は、特権機能とセキュリティ関連のすべての情報へのアクセスを制限します。

**[ID]** :1148.01c2System.78 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |
|[Windows マシンは [セキュリティ オプション - アカウント] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fee984370-154a-4ee8-9726-19d900e56fc0) |Windows コンピューターでは、空白のパスワードとゲスト アカウント状態でのローカル アカウントの使用を制限するため、[セキュリティ オプション - アカウント] カテゴリでグループ ポリシー設定が指定されている必要があります。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAccounts_AINE.json) |

### <a name="the-organization-facilitates-information-sharing-by-enabling-authorized-users-to-determine-a-business-partners-access-when-discretion-is-allowed-as-defined-by-the-organization-and-by-employing-manual-processes-or-automated-mechanisms-to-assist-users-in-making-information-sharingcollaboration-decisions"></a>組織は、承認されたユーザーが組織によって定義された裁量に従ってビジネス パートナーのアクセスを決定できるようにし、ユーザーによる情報共有やコラボレーションの決定を支援するための手動プロセスや自動メカニズムを導入することで、情報の共有を促進します。

**[ID]** :1149.01c2System.9 - 01.c **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services でロールベースのアクセス制御 (RBAC) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |ユーザーが実行できるアクションに関して詳細なフィルター処理を行うために、ロールベースのアクセス制御 (RBAC) を使用して、Kubernetes Service クラスター内のアクセス許可を管理し、関連する承認ポリシーを構成します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="the-access-control-system-for-the-system-components-storing-processing-or-transmitting-covered-information-is-set-with-a-default-quotdeny-allquot-setting"></a>カバーされる情報を格納、処理、または転送するシステム コンポーネントのアクセス制御システムは、既定の &quot;すべて拒否&quot; の設定を使用して設定されます。

**[ID]** :1150.01c2System.10 - 01.c **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="the-organization-limits-authorization-to-privileged-accounts-on-information-systems-to-a-pre-defined-subset-of-users"></a>組織は、情報システムの特権アカウントに対する承認を、事前に定義されたユーザーのサブセットに制限します。

**[ID]** :1151.01c3System.1 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-audits-the-execution-of-privileged-functions-on-information-systems-and-ensures-information-systems-prevent-non-privileged-users-from-executing-privileged-functions"></a>組織は、情報システムでの特権機能の実行を監査し、情報システムで特権のないユーザーが特権機能を実行できないようにします。

**[ID]** :1152.01c3System.2 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[複数の所有者がサブスクリプションに割り当てられている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |管理者アクセスの冗長性を確保するため、複数のサブスクリプション所有者を指定することをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="all-file-system-access-not-explicitly-required-is-disabled-and-only-authorized-users-are-permitted-access-to-only-that-which-is-expressly-required-for-the-performance-of-the-users-job-duties"></a>明示的に要求されていないすべてのファイル システム アクセスは無効になっており、承認されたユーザーのみが、ユーザーのジョブの役割の実行に明らかに必要なものだけへのアクセスを許可されます。

**[ID]** :1153.01c3System.35 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services でロールベースのアクセス制御 (RBAC) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |ユーザーが実行できるアクションに関して詳細なフィルター処理を行うために、ロールベースのアクセス制御 (RBAC) を使用して、Kubernetes Service クラスター内のアクセス許可を管理し、関連する承認ポリシーを構成します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="contractors-are-provided-with-minimal-system-and-physical-access-only-after-the-organization-assesses-the-contractors-ability-to-comply-with-its-security-requirements-and-the-contractor-agrees-to-comply"></a>請負業者には、セキュリティ要件に準拠する請負業者の能力を組織が評価し、契約者が準拠することに同意した後でのみ、最小限のシステム アクセスと物理アクセスが提供されます。

**[ID]** :1154.01c3System.4 - 01.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

## <a name="user-authentication-for-external-connections"></a>外部接続のユーザー認証

### <a name="strong-authentication-methods-such-as-multi-factor-radius-or-kerberos-for-privileged-access-and-chap-for-encryption-of-credentials-for-dialup-methods-are-implemented-for-all-external-connections-to-the-organizations-network"></a>組織ネットワークへのすべての外部接続について、多要素、Radius または Kerberos (特権アクセス向け)、CHAP (ダイヤルアップ方式の資格情報の暗号化向け) などの強力な認証方法が実装されています。

**[ID]** :1116.01j1Organizational.145 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="remote-access-by-vendors-and-business-partners-eg-for-remote-maintenance-is-disableddeactivated-when-not-in-use"></a>ベンダーおよびビジネス パートナーによるリモート アクセス (リモート メンテナンスなど) は、使用されていないときに無効化または非アクティブ化されます。

**[ID]** :1117.01j1Organizational.23 - 01.j **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="organizations-implement-encryption-eg-nbspvpn-solutions-or-private-lines-and-logs-remote-access-to-the-organizations-network-by-employees-contractors-or-third-party-eg-vendors"></a>組織は、暗号化 (VPN&nbsp;ソリューションやプライベート回線など) を実装し、従業員、請負業者、サードパーティ (ベンダーなど) による組織のネットワークへのリモート アクセスをログに記録します。

**[ID]** :1118.01j2Organizational.124 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="network-equipment-is-checked-for-unanticipated-dial-up-capabilities"></a>ネットワーク機器で、予期しないダイヤルアップ機能がチェックされます。

**[ID]** :1119.01j2Organizational.3 - 01.j **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="remote-administration-sessions-are-authorized-encrypted-and-employ-increased-security-measures"></a>リモート管理セッションは承認されて暗号化され、強化されたセキュリティ対策が採用されます。

**[ID]** :1121.01j3Organizational.2 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="if-encryption-is-not-used-for-dial-up-connections-the-cio-or-hisher-designated-representative-provides-specific-written-authorization"></a>ダイヤルアップ接続に暗号化が使用されない場合、CIO または CIO によって指定された担当者が、特定の書面による承認を提供します。

**[ID]** :1173.01j1Organizational.6 - 01.j **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="the-organization-protects-wireless-access-to-systems-containing-sensitive-information-by-authenticating-both-users-and-devices"></a>組織は、ユーザーとデバイスの両方を認証することにより、機密情報が含まれているシステムへのワイヤレス アクセスを保護します。

**[ID]** :1174.01j1Organizational.7 - 01.j **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="remote-access-to-business-information-across-public-networks-only-takes-place-after-successful-identification-and-authentication"></a>パブリック ネットワーク経由でのビジネス情報へのリモート アクセスは、ID の確認と認証が成功した後でのみ行われます。

**[ID]** :1175.01j1Organizational.8 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="the-organization-requires-a-callback-capability-with-re-authentication-to-verify-dial-up-connections-from-authorized-locations"></a>組織では、承認された場所からのダイヤルアップ接続を検証するため、再認証を伴うコールバック機能が必要です。

**[ID]** :1176.01j2Organizational.5 - 01.j **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="user-ids-assigned-to-vendors-are-reviewed-in-accordance-with-the-organizations-access-review-policy-at-a-minimum-annually"></a>ベンダーに割り当てられたユーザー ID は、組織のアクセス レビュー ポリシーに従って、少なくとも 1 年に 1 回確認されます。

**[ID]** :1177.01j2Organizational.6 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="node-authentication-including-cryptographic-techniques-eg-machine-certificates-serves-as-an-alternative-means-of-authenticating-groups-of-remote-users-where-they-are-connected-to-a-secure-shared-computer-facility"></a>セキュリティで保護された共有コンピューター機能に接続されるリモート ユーザーのグループの認証に対する代替手段として、暗号化手法 (コンピューターの証明書など) を含むノード認証が提供されます。

**[ID]** :1178.01j2Organizational.7 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-monitors-and-controls-remote-access-methods"></a>情報システムは、リモート アクセス方法を監視および制御します。

**[ID]** :1179.01j3Organizational.1 - 01.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

## <a name="remote-diagnostic-and-configuration-port-protection"></a>リモート診断と構成ポートの保護

### <a name="access-to-network-equipment-is-physically-protected"></a>ネットワーク機器へのアクセスは、物理的に保護されます。

**[ID]** :1192.01l1Organizational.1 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="controls-for-the-access-to-diagnostic-and-configuration-ports-include-the-use-of-a-key-lock-and-the-implementation-of-supporting-procedures-to-control-physical-access-to-the-port"></a>診断および構成ポートへのアクセスの制御には、キー ロックの使用や、ポートへの物理的アクセスを制御するためのサポート手順の実装が含まれます。

**[ID]** :1193.01l2Organizational.13 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートを閉じておく必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22730e10-96f6-4aac-ad84-9383d35b5917) |リモート管理ポートが開かれているため、お使いの VM がインターネットベースの攻撃を受ける高レベルのリスクにさらされています。 これらの攻撃では、資格情報に対するブルート フォース攻撃を行ってマシンへの管理者アクセス権の取得を試みます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OpenManagementPortsOnVirtualMachines_Audit.json) |

### <a name="ports-services-and-similar-applications-installed-on-a-computer-or-network-systems-which-are-not-specifically-required-for-business-functionality-are-disabled-or-removed"></a>ポート、サービスと、コンピューターまたはネットワーク システムにインストールされている同様のアプリケーションは、ビジネス機能に特に必要ではない場合、無効化または削除されます。

**[ID]** :1194.01l2Organizational.2 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリケーションのリモート デバッグを無効にする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |リモート デバッグを実行するには、受信ポートが Web アプリケーション上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="the-organization-reviews-the-information-system-within-every-three-hundred-and-sixty--five-365-days-to-identify-and-disables-unnecessary-and-non-secure-functions-ports-protocols-andor-services"></a>組織は、365 日以下の期間ごとに情報システムをレビューして、必要がなく、セキュリティで保護されていない機能、ポート、プロトコル、またはサービスを特定して無効にします。

**[ID]** :1195.01l3Organizational.1 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-identifies-unauthorized-blacklisted-software-on-the-information-system-prevents-program-execution-in-accordance-with-a-list-of-unauthorized-blacklisted-software-programs-employs-an-allow-all-deny-by-exception-policy-to-prohibit-execution-of-known-unauthorized-blacklisted-software-and-reviews-and-updates-the-list-of-unauthorized-blacklisted-software-programs-annually"></a>組織は、情報システム上にある承認されていない (ブラックリストに登録されている) ソフトウェアを識別し、承認されていない (ブラックリストに登録されている) ソフトウェア プログラムの一覧に従ってプログラムの実行を禁止し、"すべて許可、例外的に拒否" ポリシーを使用して承認されていない (ブラックリストに登録されている) 既知のソフトウェアの実行を禁止し、承認されていない (ブラックリストに登録されている) ソフトウェア プログラムの一覧を毎年確認して更新します。

**[ID]** :1196.01l3Organizational.24 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |リモート デバッグを実行するには、受信ポートが API アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-disables-bluetooth-and-peer-to-peer-networking-protocols-within-the-information-system-determined-to-be-unnecessary-or-non-secure"></a>組織は、情報システム内にある不要または危険と判断された Bluetooth およびピアツーピア ネットワーク プロトコルを無効にします。

**[ID]** :1197.01l3Organizational.3 - 01.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

## <a name="segregation-in-networks"></a>ネットワークでの分離

### <a name="the-organizations-security-gateways-eg-firewalls-enforce-security-policies-and-are-configured-to-filter-traffic-between-domains-block-unauthorized-access-and-are-used-to-maintain-segregation-between-internal-wired-internal-wireless-and-external-network-segments-eg-the-internet-including-dmzs-and-enforce-access-control-policies-for-each-of-the-domains"></a>組織のセキュリティ ゲートウェイ (ファイアウォールなど) は、セキュリティ ポリシーを実施し、ドメイン間のトラフィックをフィルター処理し、不正アクセスをブロックするように構成されています。また、内部のワイヤード、内部のワイヤレス、および DMZ を含む外部ネットワーク セグメント (インターネットなど) 間の分離を維持し、各ドメインにアクセス制御ポリシーを適用するために使用されます。

**[ID]** :0805.01m1Organizational.12 - 01.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての App Service を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Cosmos DB を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのイベント ハブを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[ゲートウェイ サブネットをネットワーク セキュリティ グループで構成してはならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |このポリシーは、ゲートウェイ サブネットがネットワーク セキュリティ グループで構成されている場合に拒否します。 ネットワーク セキュリティ グループをゲートウェイ サブネットに割り当てると、ゲートウェイが機能しなくなります。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Key Vault を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Service Bus を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての SQL Server を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="the-organizations-network-is-logically-and-physically-segmented-with-a-defined-security-perimeter-and-a-graduated-set-of-controls-including-subnetworks-for-publicly-accessible-system-components-that-are-logically-separated-from-the-internal-network-based-on-organizational-requirements-and-traffic-is-controlled-based-on-functionality-required-and-classification-of-the-datasystems-based-on-a-risk-assessment-and-their-respective-security-requirements"></a>組織のネットワークは、組織の要件に基づいて、定義済みのセキュリティ境界と、段階的な制御のセット (内部ネットワークから論理的に分離された、パブリック アクセス可能なシステム コンポーネントに対するサブネットワークなど) により、論理的かつ物理的にセグメント化されます。また、トラフィックは、必要な機能と、リスク評価およびそれぞれのセキュリティ要件に基づくデータとシステムの分類に基づいて、制御されます。

**[ID]** :0806.01m2Organizational.12356 - 01.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての App Service を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Cosmos DB を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのイベント ハブを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[ゲートウェイ サブネットをネットワーク セキュリティ グループで構成してはならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |このポリシーは、ゲートウェイ サブネットがネットワーク セキュリティ グループで構成されている場合に拒否します。 ネットワーク セキュリティ グループをゲートウェイ サブネットに割り当てると、ゲートウェイが機能しなくなります。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Key Vault を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Service Bus を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての SQL Server を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

### <a name="networks-are-segregated-from-production-level-networks-when-migrating-physical-servers-applications-or-data-to-virtualized-servers"></a>物理サーバー、アプリケーション、またはデータを仮想化されたサーバーに移行するとき、ネットワークは運用レベルのネットワークから分離されます。

**[ID]** :0894.01m2Organizational.7 - 01.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての App Service を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|[Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Cosmos DB を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[仮想ネットワーク作成時の Network Watcher のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa9b99dd8-06c5-4317-8629-9d86a3c6e7d9) |このポリシーは、仮想ネットワークのあるリージョンに Network Watcher リソースを作成します。 Network Watcher インスタンスをデプロイするために使用される、networkWatcherRG という名前のリソース グループが存在することを確認する必要があります。 |DeployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Deploy.json) |
|[イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのイベント ハブを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |
|[ゲートウェイ サブネットをネットワーク セキュリティ グループで構成してはならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35f9c03a-cc27-418e-9c0c-539ff999d010) |このポリシーは、ゲートウェイ サブネットがネットワーク セキュリティ グループで構成されている場合に拒否します。 ネットワーク セキュリティ グループをゲートウェイ サブネットに割り当てると、ゲートウェイが機能しなくなります。 |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroupOnGatewaySubnet_Deny.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Key Vault を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |
|[Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Service Bus を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |
|[SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての SQL Server を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |
|[ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |

## <a name="network-connection-control"></a>ネットワーク接続コントロール

### <a name="network-traffic-is-controlled-in-accordance-with-the-organizations-access-control-policy-through-firewall-and-other-network-related-restrictions-for-each-network-access-point-or-external-telecommunication-services-managed-interface"></a>ネットワーク トラフィックは、ファイアウォールと、各ネットワーク アクセス ポイントまたは外部通信サービスの管理インターフェイスに対するその他のネットワーク関連の制限を介し、組織のアクセス制御ポリシーに従って制御されます。

**[ID]** :0809.01n2Organizational.1234 - 01.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="transmitted-information-is-secured-and-at-a-minimum-encrypted-over-open-public-networks"></a>送信される情報はセキュリティで保護され、少なくとも、オープンなパブリック ネットワークでは暗号化されます。

**[ID]** :0810.01n2Organizational.5 - 01.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="exceptions-to-the-traffic-flow-policy-are-documented-with-a-supporting-missionbusiness-need-duration-of-the-exception-and-reviewed-at-least-annually-traffic-flow-policy-exceptions-are-removed-when-no-longer-supported-by-an-explicit-missionbusiness-need"></a>トラフィック フロー ポリシーの例外は、サポートしているミッションとビジネス ニーズおよび例外の期間と共に文書化され、少なくとも年に 1 回確認されます。トラフィック フロー ポリシーの例外は、明示的なミッションとビジネス ニーズによってサポートされなくなった時点で削除されます。

**[ID]** :0811.01n2Organizational.6 - 01.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="remote-devices-establishing-a-non-remote-connection-are-not-allowed-to-communicate-with-external-remote-resources"></a>リモート以外の接続を確立するリモート デバイスは、外部 (リモート) リソースと通信することを許可されません。

**[ID]** :0812.01n2Organizational.8 - 01.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="the-ability-of-users-to-connect-to-the-internal-network-is-restricted-using-a-deny-by-default-and-allow-by-exception-policy-at-managed-interfaces-according-to-the-access-control-policy-and-the-requirements-of-clinical-and-business-applications"></a>ユーザーが内部ネットワークに接続できるかどうかは、アクセス制御ポリシーと、臨床およびビジネス アプリケーションの要件に従って、マネージド インターフェイスで、"既定で拒否、例外で許可" ポリシーを使用して制限されます。

**[ID]** :0814.01n1Organizational.12 - 01.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |
|[仮想マシンは、承認された仮想ネットワークに接続する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd416745a-506c-48b6-8ab1-83cb814bcaa3) |このポリシーは、承認されていない仮想ネットワークに接続されているすべての仮想マシンを監査します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/ApprovedVirtualNetwork_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="user-identification-and-authentication"></a>ユーザーの識別と認証

### <a name="the-organization-ensures-that-redundant-user-ids-are-not-issued-to-other-users-and-that-all-users-are-uniquely-identified-and-authenticated-for-both-local-and-remote-access-to-information-systems"></a>組織は、重複したユーザー ID が他のユーザーに発行されないこと、およびすべてのユーザーが情報システムへのローカル アクセスとリモート アクセスの両方に対して一意に識別および認証されることを確認します。

**[ID]** :11109.01q1Organizational.57 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="non-organizational-users-all-information-system-users-other-than-organizational-users-such-as-patients-customers-contractors-or-foreign-nationals-or-processes-acting-on-behalf-of-non-organizational-users-determined-to-need-access-to-information-residing-on-the-organizations-information-systems-are-uniquely-identified-and-authenticated"></a>組織の情報システム上に存在する情報へのアクセスが必要であると判断された、組織以外のユーザー (患者、顧客、請負業者、外国人などの、組織のユーザー以外のすべての情報システム ユーザー)、または組織以外のユーザーの代理として機能するプロセスは、一意に識別されて認証されます。

**[ID]** :11110.01q1Organizational.6 - 01.q **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |

### <a name="when-pki-based-authentication-is-used-the-information-system-validates-certificates-by-constructing-and-verifying-a-certification-path-to-an-accepted-trust-anchor-including-checking-certificate-status-information-enforces-access-to-the-corresponding-private-key-maps-the-identity-to-the-corresponding-account-of-the-individual-or-group-and-implements-a-local-cache-of-revocation-data-to-support-path-discovery-and-validation-in-case-of-an-inability-to-access-revocation-information-via-the-network"></a>PKI ベースの認証を使用する場合、情報システムでは、受け入れられたトラスト アンカーへの証明パスを構築して検証することによって証明書を検証し (証明書の状態情報の確認を含む)、対応する秘密キーに対してアクセスを適用し、個人またはグループの対応するアカウントに ID をマップし、ネットワーク経由で失効情報にアクセスできない場合にパスの検出と検証をサポートするための失効データのローカル キャッシュを実装します。

**[ID]** :11111.01q2System.4 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="the-information-system-employs-replay-resistant-authentication-mechanisms-such-as-nonce-one-time-passwords-or-time-stamps-to-secure-network-access-for-privileged-accounts-and-for-hardware-token-based-authentication-employs-mechanisms-that-satisfy-minimum-token-requirements-discussed-in-nist-sp-800-63-2-electronic-authentication-guideline"></a>情報システムでは、nonce、ワンタイム パスワード、タイムスタンプなどのリプレイに対する抵抗力のある認証メカニズムを導入して、特権アカウントに対するネットワーク アクセスをセキュリティで保護します。また、ハードウェア トークン ベースの認証の場合は、NIST SP 800-63-2 の電子認証ガイドラインに記載されている最小トークン要件を満たすメカニズムを採用します。

**[ID]** :11112.01q2Organizational.67 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには最大 3 人の所有者を指定する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f11b553-d42e-4e3a-89be-32ca364cad4c) |セキュリティ侵害を受けたサブスクリプション所有者が侵害を引き起こす可能性を下げるため、指定する所有者は最大 3 人までにすることをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateLessThanXOwners_Audit.json) |

### <a name="the-organization-requires-that-electronic-signatures-unique-to-one-individual-cannot-be-reused-by-or-reassigned-to-anyone-else"></a>組織では、1 人の個人に固有の電子署名を、他のユーザーが再利用したり、他のユーザーに割り当て直したりできないことを要求します。

**[ID]** :11208.01q1Organizational.8 - 01.q **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[複数の所有者がサブスクリプションに割り当てられている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09024ccc-0c5f-475e-9457-b7c0d9ed487b) |管理者アクセスの冗長性を確保するため、複数のサブスクリプション所有者を指定することをお勧めします。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_DesignateMoreThanOneOwner_Audit.json) |

### <a name="electronic-signatures-and-handwritten-signatures-executed-to-electronic-records-shall-be-linked-to-their-respective-electronic-records"></a>電子記録に対して実行される電子署名および手書き署名は、それぞれの電子記録にリンクされている必要があります。

**[ID]** :11210.01q2Organizational.10 - 01.q **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrators グループ内に指定されたメンバーが存在する Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 ポリシー パラメーターに指定されたメンバーがローカルの Administrators グループに含まれている場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="signed-electronic-records-shall-contain-information-associated-with-the-signing-in-human-readable-format"></a>署名された電子記録には、人が判読できる形式の署名に関連付けられた情報が含まれている必要があります。

**[ID]** :11211.01q2Organizational.11 - 01.q **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrators グループ内の指定されたメンバーが欠けている Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 ポリシー パラメーターに指定されたメンバーがローカルの Administrators グループに含まれていない場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

### <a name="users-who-performed-privileged-functions-eg-system-administration-use-separate-accounts-when-performing-those-privileged-functions"></a>特権機能 (システム管理など) を実行するユーザーは、それらの特権機能を実行するときに別のアカウントを使用します。

**[ID]** :1123.01q1System.2 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrators グループに余分なアカウントがある Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3d2a3320-2a72-4c67-ac5f-caa40fbee2b2) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 ポリシー パラメーターに指定されていないメンバーがローカルの Administrators グループに含まれている場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembers_AINE.json) |

### <a name="multi-factor-authentication-methods-are-used-in-accordance-with-organizational-policy-eg-for-remote-network-access"></a>多要素認証の方法は、組織のポリシー (リモート ネットワーク アクセスに対するものなど) に従って使用されます。

**[ID]** :1125.01q2System.1 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrators グループ内に指定されたメンバーが存在する Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F69bf4abd-ca1e-4cf6-8b5a-762d42e61d4f) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 ポリシー パラメーターに指定されたメンバーがローカルの Administrators グループに含まれている場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToExclude_AINE.json) |

### <a name="where-tokens-are-provided-for-multi-factor-authentication-in-person-verification-is-required-prior-to-granting-access"></a>多要素認証に対してトークンが提供される場合、アクセスを許可する前に、本人確認が必要です。

**[ID]** :1127.01q2System.3 - 01.q **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Administrators グループ内の指定されたメンバーが欠けている Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F30f71ea1-ac77-4f26-9fc5-2d926bbd4ba7) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 ポリシー パラメーターに指定されたメンバーがローカルの Administrators グループに含まれていない場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_AdministratorsGroupMembersToInclude_AINE.json) |

## <a name="identification-of-risks-related-to-external-parties"></a>外部パーティに関連するリスクの特定

### <a name="access-to-the-organizations-information-and-systems-by-external-parties-is-not-permitted-until-due-diligence-has-been-conducted-the-appropriate-controls-have-been-implemented-and-a-contractagreement-reflecting-the-security-requirements-is-signed-acknowledging-they-understand-and-accept-their-obligations"></a>外部パーティによる組織の情報やシステムへのアクセスは、デュー デリジェンスが実施され、適切な制御が実装され、パーティがその義務を理解して同意することを確認する、セキュリティ要件を反映した契約が署名されるまで、許可されません。

**[ID]** :1401.05i1Organizational.1239 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="remote-access-connections-between-the-organization-and-external-parties-are-encrypted"></a>組織と外部パーティ間のリモート アクセス接続が暗号化されます。

**[ID]** :1402.05i1Organizational.45 - 05.i **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |

### <a name="access-granted-to-external-parties-is-limited-to-the-minimum-necessary-and-granted-only-for-the-duration-required"></a>外部パーティに許可されるアクセスは、必要最小限に限定され、必要な期間だけ許可されます。

**[ID]** :1403.05i1Organizational.67 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="due-diligence-of-the-external-party-includes-interviews-document-review-checklists-certification-reviews-eg-hitrustnbspor-other-remote-means"></a>外部パーティのデュー デリジェンスには、インタビュー、ドキュメント レビュー、チェックリスト、認定レビュー&nbsp;(HITRUST など)、その他のリモート手段が含まれます。

**[ID]** :1404.05i2Organizational.1 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |

### <a name="the-identification-of-risks-related-to-external-party-access-takes-into-account-a-minimal-set-of-specifically-defined-issues"></a>外部パーティのアクセスに関連するリスクの識別では、明確に定義された問題の最小セットが考慮されます。

**[ID]** :1418.05i1Organizational.8 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-organization-obtains-satisfactory-assurances-that-reasonable-information-security-exists-across-their-information-supply-chain-by-performing-an-annual-review-which-includes-all-partnersthird-party-providers-upon-which-their-information-supply-chain-depends"></a>組織は、年間レビューを実行することで、情報サプライ チェーン全体に適切な情報セキュリティが存在することについて、満足できる保証を得ます。これには、情報サプライ チェーンが依存しているすべてのパートナーとサードパーティ プロバイダーが含まれます。

**[ID]** :1450.05i2Organizational.2 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="cloud-service-providers-design-and-implement-controls-to-mitigate-and-contain-data-security-risks-through-proper-separation-of-duties-role-based-access-and-least-privilege-access-for-all-personnel-within-their-supply-chain"></a>クラウド サービス プロバイダーは、サプライ チェーン内のすべての担当者に対する、適切な職務の分離、ロールベースのアクセス、および最小特権アクセスにより、データ セキュリティ リスクを軽減および抑止するためのコントロールを設計および実装します。

**[ID]** :1451.05iCSPOrganizational.2 - 05.i **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

## <a name="audit-logging"></a>監査ログ

### <a name="a-secure-audit-record-is-created-for-all-activities-on-the-system-create-read-update-delete-involving-covered-information"></a>対象となる情報を含むシステムでのすべてのアクティビティ (作成、読み取り、更新、削除) に対して、セキュリティで保護された監査レコードを作成します。

**[ID]** :1202.09aa1System.1 - 09.aa **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Lake Store のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[仮想マシン スケール セットにシステムの更新プログラムをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc3f317a7-a95c-4547-b7e7-11017ebdf2fe) |お使いの Windows と Linux の仮想マシン スケール セットが確実にセキュリティで保護されるようにするため、インストールする必要のあるシステムのセキュリティ更新プログラムおよび重要な更新プログラムが不足していないかどうかを監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingSystemUpdates_Audit.json) |

### <a name="audit-records-include-the-unique-user-id-unique-data-subject-id-function-performed-and-datetime-the-event-was-performed"></a>監査レコードには、一意のユーザー ID、一意のデータ サブジェクト ID、実行された機能、およびイベントが実行された日時が含まれます。

**[ID]** :1203.09aa1System.2 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Logic Apps のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |

### <a name="the-activities-of-privileged-users-administrators-operators-etc-include-the-successfailure-of-the-event-time-the-event-occurred-the-account-involved-the-processes-involved-and-additional-information-about-the-event"></a>特権ユーザー (管理者、オペレーターなど) のアクティビティには、イベントの成功または失敗、イベント発生日時、関連するアカウント、関連するプロセス、およびイベントに関する追加情報が含まれます。

**[ID]** :1204.09aa1System.3 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[IoT Hub のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |

### <a name="logs-of-messages-sent-and-received-are-maintained-including-the-date-time-origin-and-destination-of-the-message-but-not-its-contents"></a>送受信されたメッセージのログでは、メッセージの日付、時刻、送信元、送信先が保持されますが、その内容は保持されません。

**[ID]** :1205.09aa2System.1 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Batch アカウントのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |

### <a name="auditing-is-always-available-while-the-system-is-active-and-tracks-key-events-successfailed-data-access-system-security-configuration-changes-privileged-or-utility-use-any-alarms-raised-nbspactivation-and-de-activation-of-protection-systems-eg-av-and-ids-activation-and-deactivation-of-identification-and-authentication-mechanisms-and-creation-and-deletion-of-system-level-objects"></a>監査は、システムがアクティブな間、常に使用可能であり、主要なイベント、データ アクセスの成功と失敗、システム セキュリティ構成の変更、特権またはユーティリティの使用、発生したアラーム、保護システム (A/V、IDS&nbsp;など) のアクティブ化と非アクティブ化、識別および認証メカニズムのアクティブ化と非アクティブ化、システムレベルのオブジェクトの作成と削除が追跡されます。

**[ID]** :1206.09aa2System.23 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtual Machine Scale Sets のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |インシデントやセキュリティ侵害が発生して調査が必要になった場合に活動証跡を再作成できるように、ログを有効にすることをお勧めします。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

### <a name="audit-records-are-retained-for-90-days-and-older-audit-records-are-archived-for-one-year"></a>監査レコードは 90 日間保持され、古い監査レコードは 1 年間アーカイブされます。

**[ID]** :1207.09aa2System.4 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stream Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[イベント ハブのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |

### <a name="audit-logs-are-maintained-for-management-activities-system-and-application-startupshutdownerrors-file-changes-and-security-policy-changes"></a>監査ログは、管理アクティビティ、システムとアプリケーションの起動、シャットダウン、エラー、ファイルの変更、およびセキュリティ ポリシーの変更に関して保持されます。

**[ID]** :1208.09aa3System.1 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Search サービスのリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[Service Bus のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |

### <a name="the-information-system-generates-audit-records-containing-the-following-detailed-information-i-filename-accessed-ii-program-or-command-used-to-initiate-the-event-and-iii-source-and-destination-addresses"></a>情報システムでは、次の詳細情報を含む監査レコードを生成します。(i) アクセスされたファイルの名前、(ii) イベントを開始するために使用されたプログラムまたはコマンド、(iii) 送信元と送信先のアドレス。

**[ID]** :1209.09aa3System.2 - 09.aa **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Services における診断ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |アプリ上で診断ログが有効になっているかどうかを監査します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |

### <a name="all-disclosures-of-covered-information-within-or-outside-of-the-organization-are-logged-including-type-of-disclosure-datetime-of-the-event-recipient-and-sender"></a>組織内または組織外の対象となる情報のすべての開示が、開示の種類、イベントの日時、受信者、送信者と共に記録されます。

**[ID]** :1210.09aa3System.3 - 09.aa **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[診断設定の監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7f89b1eb-583c-429a-8828-af049802c1d9) |選択したリソースの種類の診断設定を監査します |AuditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForTypes_Audit.json) |
|[Data Lake Analytics のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |

### <a name="the-organization-verifies-every-ninety-90-days-for-each-extract-of-covered-information-recorded-that-the-data-is-erased-or-its-use-is-still-required"></a>組織は、90 日ごとに、記録された対象情報の各抽出で、データが消去されていること、またはその使用が依然として必要であることを検証します。

**[ID]** :1211.09aa3System.4 - 09.aa **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |
|[Azure Key Vault マネージド HSM のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |セキュリティ インシデントが発生したときやネットワークが侵害されたときに調査目的でアクティビティ証跡を再作成する場合は、マネージド HSM のリソース ログを有効にして監査を行います。 [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) の手順に従ってください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

## <a name="monitoring-system-use"></a>システムの使用の監視

### <a name="unauthorized-remote-connections-to-the-information-systems-are-monitored-and-reviewed-at-least-quarterly-and-appropriate-action-is-taken-if-an-unauthorized-connection-is-discovered"></a>情報システムへの承認されていないリモート接続は、少なくとも四半期に 1 回監視および確認され、承認されていない接続が検出された場合は適切な処置が実行されます。

**[ID]** :1120.09ab3System.9 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |このポリシーでは、グローバルを含め、Azure がサポートするすべてのリージョンからアクティビティをエクスポートしない Azure Monitor ログ プロファイルを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="the-organization-monitors-the-information-system-to-identify-irregularities-or-anomalies-that-are-indicators-of-a-system-malfunction-or-compromise-and-help-confirm-the-system-is-functioning-in-an-optimal-resilient-and-secure-state"></a>組織は、情報システムを監視して、システムの誤動作やセキュリティ侵害の兆候を示す不規則な動作や異常を特定し、システムが最適で回復性が高く安全な状態で動作していることを確認します。

**[ID]** :12100.09ab2System.15 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |このポリシーは、Log Analytics エージェントがインストールされていない場合に、Windows または Linux の仮想マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-specifies-how-often-audit-logs-are-reviewed-how-the-reviews-are-documented-and-the-specific-roles-and-responsibilities-of-the-personnel-conducting-the-reviews-including-the-professional-certifications-or-other-qualifications-required"></a>組織は、監査ログを確認する頻度、レビューを文書化する方法、およびレビューを実施する担当者の特定の役割と責任を指定します。これには、プロフェッショナル認定や他の資格要件が含まれます。

**[ID]** :12101.09ab1Organizational.3 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |このポリシーは、Log Analytics エージェントがインストールされていない場合に、Windows または Linux の仮想マシン スケール セットを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="the-organization-shall-periodically-test-its-monitoring-and-detection-processes-remediate-deficiencies-and-improve-its-processes"></a>組織は、定期的に監視と検出のプロセスをテストし、欠陥を修復して、プロセスを改善する必要があります。

**[ID]** :12102.09ab1Organizational.4 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics エージェントが想定どおりに接続されていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 エージェントがインストールされていない場合、またはインストールされてはいても、ポリシー パラメーターで指定した ID 以外のワークスペースに登録されていることが COM オブジェクト AgentConfigManager.MgmtSvcCfg から返される場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="all-applicable-legal-requirements-related-to-monitoring-authorized-access-and-unauthorized-access-attempts-are-met"></a>承認されたアクセスと承認されていないアクセスの試行の監視に関するすべての法的要件が満たされています。

**[ID]** :1212.09ab1System.1 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ログ プロファイルで、"書き込み"、"削除"、"アクション" の各カテゴリのログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |このポリシーでは、ログ プロファイルで "書き込み"、"削除"、"アクション" の各カテゴリのログが確実に収集されるようにします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="automated-systems-deployed-throughout-the-organizations-environment-are-used-to-monitor-key-events-and-anomalous-activity-and-analyze-system-logs-the-results-of-which-are-reviewed-regularly"></a>組織の環境全体に展開された自動化システムを使用して、重要なイベントや異常なアクティビティを監視し、システム ログを分析します。その結果を、定期的に確認します。

**[ID]** :1213.09ab2System.128 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="monitoring-includes-privileged-operations-authorized-access-or-unauthorized-access-attempts-including-attempts-to-access-deactivated-accounts-and-system-alerts-or-failures"></a>監視には、特権操作、承認されたアクセスと承認されていないアクセスの試み (非アクティブなアカウントへのアクセスの試みを含む)、システムの警告またはエラーが含まれます。

**[ID]** :1214.09ab2System.3456 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |このポリシーでは、グローバルを含め、Azure がサポートするすべてのリージョンからアクティビティをエクスポートしない Azure Monitor ログ プロファイルを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="auditing-and-monitoring-systems-employed-by-the-organization-support-audit-reduction-and-report-generation"></a>組織で採用されている監査と監視のシステムで、監査の削減とレポートの生成がサポートされています。

**[ID]** :1215.09ab2System.7 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa70ca396-0a34-413a-88e1-b956c1e683be) |このポリシーは、Log Analytics エージェントがインストールされていない場合に、Windows または Linux の仮想マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VirtualMachines_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="automated-systems-are-used-to-review-monitoring-activities-of-security-systems-eg-ipsids-and-system-records-on-a-daily-basis-and-identify-and-document-anomalies"></a>自動システムを使用して、セキュリティ システム (IPS/IDS など) とシステム レコードの監視アクティビティを毎日確認し、異常を特定して文書化します。

**[ID]** :1216.09ab3System.12 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシン スケール セットに Log Analytics エージェントをインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fefbde977-ba53-4479-b8e9-10b957924fbf) |このポリシーは、Log Analytics エージェントがインストールされていない場合に、Windows または Linux の仮想マシン スケール セットを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/VMSS_LogAnalyticsAgent_AuditIfNotExists.json) |

### <a name="alerts-are-generated-for-technical-personnel-to-analyze-and-investigate-suspicious-activity-or-suspected-violations"></a>疑わしいアクティビティや違反の疑いを分析および調査するためのアラートを、技術担当者に対して生成します。

**[ID]** :1217.09ab3System.3 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Log Analytics エージェントが想定どおりに接続されていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6265018c-d7e2-432f-a75d-094d5f6f4465) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 エージェントがインストールされていない場合、またはインストールされてはいても、ポリシー パラメーターで指定した ID 以外のワークスペースに登録されていることが COM オブジェクト AgentConfigManager.MgmtSvcCfg から返される場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsLogAnalyticsAgentConnection_AINE.json) |

### <a name="the-information-system-is-able-to-automatically-process-audit-records-for-events-of-interest-based-on-selectable-criteria"></a>情報システムでは、選択可能な条件に基づいて、対象のイベントの監査レコードを自動的に処理できます。

**[ID]** :1219.09ab3System.10 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ログ プロファイルで、"書き込み"、"削除"、"アクション" の各カテゴリのログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |このポリシーでは、ログ プロファイルで "書き込み"、"削除"、"アクション" の各カテゴリのログが確実に収集されるようにします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="monitoring-includes-inbound-and-outbound-communications-and-file-integrity-monitoring"></a>監視には、受信と送信の通信とファイルの整合性の監視が含まれます。

**[ID]** :1220.09ab3System.56 - 09.ab **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

## <a name="administrator-and-operator-logs"></a>管理者とオペレーターのログ

### <a name="the-organization-ensures-proper-logging-is-enabled-in-order-to-audit-administrator-activities-and-reviews-system-administrator-and-operator-logs-on-a-regular-basis"></a>組織は、管理者のアクティビティを監査するために適切なログが有効なことを確認します。また、システム管理者とオペレーターのログを定期的に確認します。

**[ID]** :1270.09ad1System.12 - 09.ad **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="an-intrusion-detection-system-managed-outside-of-the-control-of-system-and-network-administrators-is-used-to-monitor-system-and-network-administration-activities-for-compliance"></a>システム管理者とネットワーク管理者の制御外で管理されている侵入検出システムを使用して、システムおよびネットワーク管理のコンプライアンス対応状態を監視します。

**[ID]** :1271.09ad1System.1 - 09.ad **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

## <a name="segregation-of-duties"></a>職務の分離

### <a name="separation-of-duties-is-used-to-limit-the-risk-of-unauthorized-or-unintentional-modification-of-information-and-systems"></a>職務の分離は、情報やシステムが不正にまたは意図せずに変更されるリスクを軽減するために使用されます。

**[ID]** :1229.09c1Organizational.1 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services でロールベースのアクセス制御 (RBAC) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |ユーザーが実行できるアクションに関して詳細なフィルター処理を行うために、ロールベースのアクセス制御 (RBAC) を使用して、Kubernetes Service クラスター内のアクセス許可を管理し、関連する承認ポリシーを構成します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

### <a name="no-single-person-is-able-to-access-modify-or-use-information-systems-without-authorization-or-detection"></a>承認または検出なしに情報システムにアクセス、変更、または使用することはできません。

**[ID]** :1230.09c2Organizational.1 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[カスタム RBAC 規則の使用監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa451c1ef-c6ca-483d-87ed-f49761e3ffb5) |エラーが発生しやすいカスタム RBAC ロールの代わりに、所有者、共同作成者、閲覧者などの組み込みロールを監査します。 カスタム ロールの使用は例外として扱われ、厳格なレビューと脅威のモデル化が必要になります |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |

### <a name="access-for-individuals-responsible-for-administering-nbspaccess-controls-is-limited-to-the-minimum-necessary-based-upon-each-users-role-and-responsibilities-and-these-individuals-cannot-access-audit-functions-related-to-these-controls"></a>管理とアクセス制御を担当する個人のアクセス権は、各ユーザーの役割と責任に基づいて必要最小限に制限されます。また、こうした個人はこれらのコントロールに関連する監査機能にアクセスできません。&nbsp;

**[ID]** :1232.09c3Organizational.12 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [ユーザー権利の割り当て] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe068b215-0026-4354-b347-8fb2766f73a2) |ローカル ログオン、RDP、ネットワークからのアクセス、その他多くのユーザー アクティビティを許可するため、Windows マシンでは、[ユーザー権利の割り当て] カテゴリに指定されたグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_UserRightsAssignment_AINE.json) |

### <a name="security-audit-activities-are-independent"></a>セキュリティ監査アクティビティは独立しています。

**[ID]** :1276.09c2Organizational.2 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[カスタム サブスクリプションの所有者ロールが作成されていないこと](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |このポリシーでは、カスタム サブスクリプションの所有者ロールが存在しないことを確認します。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

### <a name="the-initiation-of-an-event-is-separated-from-its-authorization-to-reduce-the-possibility-of-collusion"></a>共謀の可能性を減らすため、イベントの開始を承認から分離します。

**[ID]** :1277.09c2Organizational.4 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [セキュリティ オプション - ユーザー アカウント制御] の要件を満たす必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F492a29ed-d143-4f03-b6a4-705ce081b463) |Windows マシンには、管理者用のモード、昇格時のプロンプトの動作、ファイル仮想化とレジストリ書き込みのエラーについて、カテゴリ [セキュリティ オプション - ユーザー アカウント制御] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsUserAccountControl_AINE.json) |

### <a name="the-organization-identifies-duties-that-require-separation-and-defines-information-system-access-authorizations-to-support-separation-of-duties-and-incompatible-duties-are-segregated-across-multiple-users-to-minimize-the-opportunity-for-misuse-or-fraud"></a>組織は、分離を必要とする職務を特定し、職務の分離をサポートするための情報システム アクセス承認を定義します。また、互換性のない職務は、誤用や不正行為の機会を最小限にするために、複数のユーザーに分離します。

**[ID]** :1278.09c2Organizational.56 - 09.c **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[カスタム サブスクリプションの所有者ロールが作成されていないこと](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |このポリシーでは、カスタム サブスクリプションの所有者ロールが存在しないことを確認します。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="controls-against-malicious-code"></a>悪意のあるコードに対するコントロール

### <a name="anti-virus-and-anti-spyware-are-installed-operating-and-updated-on-all-end-user-devices-to-conduct-periodic-scans-of-the-systems-to-identify-and-remove-unauthorized-software-server-environments-for-which-the-server-software-developer-specifically-recommends-not-installing-host-based-anti-virus-and-anti-spyware-software-may-address-the-requirement-via-a-network-based-malware-detection-nbmd-solution"></a>ウイルス対策およびスパイウェア対策を、すべてのエンド ユーザー デバイスにインストールし、運用して、更新し、定期的にシステムのスキャンを実行することで、承認されていないソフトウェアを特定して削除します。 サーバー ソフトウェア開発者がホスト ベースのウイルス対策およびスパイウェア対策ソフトウェアをインストールしないことを明示的に推奨しているサーバー環境では、ネットワーク ベースのマルウェア検出 (NBMD) ソリューションによって要件に対処できます。

**[ID]** :0201.09j1Organizational.124 - 09.j **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[Windows Server 用の既定の Microsoft IaaSAntimalware 拡張機能のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2835b622-407b-4114-9198-6f7064cbe0dc) |このポリシーでは、VM にマルウェア対策の拡張機能が構成されていない場合に、既定の構成で Microsoft IaaSAntimalware 拡張機能をデプロイします。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |
|[エンドポイント保護ソリューションを仮想マシン スケール セットにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26a828e1-e88f-464e-bbb3-c134a282b9de) |脅威と脆弱性から保護するため、お使いの仮想マシン スケール セットでのエンドポイント保護ソリューションの存在と正常性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssMissingEndpointProtection_Audit.json) |
|[Azure 向け Microsoft Antimalware は保護定義を自動的に更新するように構成する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc43e4a30-77cb-48ab-a4dd-93f175c63b57) |このポリシーは、Microsoft Antimalware 保護定義の自動更新が構成されていないすべての Windows 仮想マシンを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |
|[システム更新プログラムをマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

## <a name="back-up"></a>バックアップ

### <a name="backup-copies-of-information-and-software-are-made-and-tests-of-the-media-and-restoration-procedures-are-regularly-performed-at-appropriate-intervals"></a>情報とソフトウェアのバックアップ コピーを作成し、メディアおよび復元手順のテストを適切な間隔で定期的に実行します。

**[ID]** :1616.09l1Organizational.16 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |このポリシーは、長期的な geo 冗長バックアップが有効になっていないすべての Azure SQL データベースを監査します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="a-formal-definition-of-the-level-of-backup-required-for-each-system-is-defined-and-documented-including-how-each-system-will-be-restored-the-scope-of-data-to-be-imaged-frequency-of-imaging-and-duration-of-retention-based-on-relevant-contractual-legal-regulatory-and-business-requirements"></a>各システムの復元方法、イメージ化するデータの範囲、イメージ化の頻度、および関連する契約、法律、規制、ビジネス要件に基づく保持期間など、各システムに必要なバックアップのレベルの正式な定義を定義して文書化します。

**[ID]** :1617.09l1Organizational.23 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="the-backups-are-stored-in-a-physically-secure-remote-location-at-a-sufficient-distance-to-make-them-reasonably-immune-from-damage-to-data-at-the-primary-site-and-reasonable-physical-and-environmental-controls-are-in-place-to-ensure-their-protection-at-the-remote-location"></a>プライマリ サイトでのデータの破損から適切に保護されるのに十分な距離だけ離れた物理的に安全な場所にバックアップを保管し、適切な物理的および環境的な制御を適用して、リモートの場所で確実に保護します。

**[ID]** :1618.09l1Organizational.45 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="inventory-records-for-the-backup-copies-including-content-and-current-location-are-maintained"></a>コンテンツと現在の場所を含む、バックアップ コピーのインベントリ レコードを保持します。

**[ID]** :1619.09l1Organizational.7 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="when-the-backup-service-is-delivered-by-the-third-party-the-service-level-agreement-includes-the-detailed-protections-to-control-confidentiality-integrity-and-availability-of-the-backup-information"></a>バックアップ サービスがサードパーティによって提供されるときは、サービス レベル アグリーメントに、バックアップ情報の機密性、整合性、可用性を制御するための詳細な保護を含めます。

**[ID]** :1620.09l1Organizational.8 - 09.l **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに対して Azure Backup を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup を有効にして、Azure Virtual Machines を保護します。 Azure Backup は、Azure 向けの安全で費用対効果の高いデータ保護ソリューションです。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="automated-tools-are-used-to-track-all-backups"></a>自動ツールを使用して、すべてのバックアップを追跡します。

**[ID]** :1621.09l2Organizational.1 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure SQL データベースの長期的な geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd38fc420-0735-4ef3-ac11-c806f651a570) |このポリシーは、長期的な geo 冗長バックアップが有効になっていないすべての Azure SQL データベースを監査します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_SQLDatabase_AuditIfNotExists.json) |

### <a name="the-integrity-and-security-of-the-backup-copies-are-maintained-to-ensure-future-availability-and-any-potential-accessibility-problems-with-the-backup-copies-are-identified-and-mitigated-in-the-event-of-an-area-wide-disaster"></a>将来の可用性を保証するためにバックアップ コピーの整合性とセキュリティを維持し、地域全体の障害が発生した場合には、バックアップ コピーに存在するアクセシビリティの問題の可能性を識別して軽減します。

**[ID]** :1622.09l2Organizational.23 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MySQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

### <a name="covered-information-is-backed-up-in-an-encrypted-format-to-ensure-confidentiality"></a>機密性を保証するため、対象となる情報を暗号化された形式でバックアップします。

**[ID]** :1623.09l2Organizational.4 - 09.l **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-performs-incremental-or-differential-backups-daily-and-full-backups-weekly-to-separate-media"></a>組織は、増分バックアップまたは差分バックアップを毎日実行し、別のメディアへの完全バックアップを毎週実行します。

**[ID]** :1624.09l3Organizational.12 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="three-3-generations-of-backups-full-plus-all-related-incremental-or-differential-backups-are-stored-off-site-and-both-on-site-and-off-site-backups-are-logged-with-name-date-time-and-action"></a>3 世代のバックアップ (完全バックアップと、それに関連するすべての増分バックアップまたは差分バックアップ) をオフサイトに格納し、オンサイトとオフサイト両方のバックアップの両方を、名前、日付、時刻、アクションと共にログに記録します。

**[ID]** :1625.09l3Organizational.34 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに対して Azure Backup を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup を有効にして、Azure Virtual Machines を保護します。 Azure Backup は、Azure 向けの安全で費用対効果の高いデータ保護ソリューションです。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

### <a name="the-organization-ensures-a-current-retrievable-copy-of-covered-information-is-available-before-movement-of-servers"></a>組織は、対象となる情報の現在の取得可能なコピーが利用可能であることを、サーバーを移動する前に確認します。

**[ID]** :1626.09l3Organizational.5 - 09.l **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for PostgreSQL の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F48af4db5-9b8b-401c-8e74-076be876a430) |Azure Database for PostgreSQL を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForPostgreSQL_Audit.json) |

### <a name="the-organization-tests-backup-information-following-each-backup-to-verify-media-reliability-and-information-integrity-and-at-least-annually-thereafter"></a>組織は、各バックアップの後と、それ以降少なくとも 1 年ごとに、バックアップ情報をテストして、メディアの信頼性と情報の整合性を確認します。

**[ID]** :1627.09l3Organizational.6 - 09.l **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB の geo 冗長バックアップを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB を使用すると、データベース サーバーの冗長オプションを選択できます。 これを、geo 冗長バックアップ ストレージに設定できます。これに設定すると、データはサーバーがホストされているリージョン内に格納されるだけでなく、リージョンの障害が発生した場合に復旧オプションを提供するために、ペア リージョンにもレプリケートされます。 バックアップに対して geo 冗長ストレージを構成できるのは、サーバーの作成時だけです。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |

### <a name="workforce-members-roles-and-responsibilities-in-the-data-backup-process-are-identified-and-communicated-to-the-workforce-in-particular-bring-your-own-device-byod-users-are-required-to-perform-backups-of-organizational-andor-client-data-on-their-devices"></a>データ バックアップ プロセスにおける従業員メンバーの役割と責任を明らかにし、従業員に伝えます。特に、Bring Your Own Device (BYOD) ユーザーは、自分のデバイス上にある組織やクライアントのデータのバックアップを実行する必要があります。

**[ID]** :1699.09l1Organizational.10 - 09.l **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンに対して Azure Backup を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Azure Backup を有効にして、Azure Virtual Machines を保護します。 Azure Backup は、Azure 向けの安全で費用対効果の高いデータ保護ソリューションです。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="network-controls"></a>ネットワーク コントロール

### <a name="the-organization-monitors-for-all-authorized-and-unauthorized-wireless-access-to-the-information-system-and-prohibits-installation-of-wireless-access-points-waps-unless-explicitly-authorized-in-writing-by-the-cio-or-hisher-designated-representative"></a>組織は、情報システムへの承認された、および承認されていないワイヤレス アクセスをすべて監視し、CIO または CIO が指定した代表者による書面での明示的な承認がない限り、ワイヤレス アクセス ポイント (WAP) のインストールを禁止します。

**[ID]** :0858.09m1Organizational.4 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ご使用の仮想マシンに関連付けられたネットワーク セキュリティ グループでは、すべてのネットワーク ポートを制限する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9daedab3-fb2d-461e-b861-71790eead4f6) |Azure Security Center により、お使いのネットワーク セキュリティ グループの一部の受信規則について制限が少なすぎると判断されました。 受信規則では、"Any" または "Internet" の範囲からのアクセスを許可しないでください。 これにより、攻撃者がお使いのリソースをターゲットにできる可能性があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnprotectedEndpoints_Audit.json) |
|[仮想マシンの管理ポートは、Just-In-Time ネットワーク アクセス制御によって保護されている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |
|[Windows マシンは [Windows ファイアウォール プロパティ] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F35d9882c-993d-44e6-87d2-db66ce21b636) |Windows マシンの [Windows ファイアウォール プロパティ] カテゴリのファイアウォール状態、接続、ルール管理、通知が、指定されたグループ ポリシーの設定になっている必要があります。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsFirewallProperties_AINE.json) |

### <a name="the-organization-ensures-the-security-of-information-in-networks-availability-of-network-services-and-information-services-using-the-network-and-the-protection-of-connected-services-from-unauthorized-access"></a>組織は、ネットワーク内の情報のセキュリティ、ネットワークを使用するネットワーク サービスと情報サービスの可用性、接続されたサービスの不正アクセスからの保護を保証します。

**[ID]** :0859.09m1Organizational.78 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="the-organization-formally-manages-equipment-on-the-network-including-equipment-in-user-areas"></a>組織は、ユーザー領域の装置など、ネットワーク上の機器を正式に管理します。

**[ID]** :0860.09m1Organizational.9 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ネットワーク セキュリティ グループの診断設定のデプロイ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9c29499-c1d1-4195-99bd-2ec9e3a9dc89) |このポリシーは、ネットワーク セキュリティ グループに対して診断設定を自動的にデプロイします。 '{storagePrefixParameter}{NSGLocation}' という名前のストレージ アカウントが自動的に作成されます。 |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/DiagnosticSettingsForNSG_Deploy.json) |
|[Service Bus は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F235359c5-7c52-4b82-9055-01c75cf9f60e) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Service Bus を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ServiceBus_AuditIfNotExists.json) |

### <a name="to-identify-and-authenticate-devices-on-local-andor-wide-area-networks-including-wireless-networks-nbspthe-information-system-uses-either-a-i-shared-known-information-solution-or-ii-an-organizational-authentication-solution-the-exact-selection-and-strength-of-which-is-dependent-on-the-security-categorization-of-the-information-system"></a>ワイヤレス ネットワークを含む、ローカルおよびワイド エリア ネットワーク上のデバイスを識別して認証するため、情報システムでは、(i)&nbsp;共有の既知の情報ソリューションまたは (ii) 組織の認証ソリューションのいずれかを使用します。その厳密な選択と強度は、情報システムのセキュリティ カテゴリによって決まります。

**[ID]** :0861.09m2Organizational.67 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[App Service は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2d21331d-a4c2-4def-a9ad-ee4e1e023beb) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての App Service を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_AppService_AuditIfNotExists.json) |
|[Windows マシンは [セキュリティ オプション - ネットワーク アクセス] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3ff60f98-7fa4-410a-9f7f-0b00f5afdbdd) |Windows マシンには、匿名ユーザーやローカル アカウントへのアクセスと、レジストリへのリモート アクセスを含むことについて、カテゴリ [セキュリティ オプション - ネットワーク アクセス] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsNetworkAccess_AINE.json) |

### <a name="the-organization-ensures-information-systems-protect-the-confidentiality-and-integrity-of-transmitted-information-including-during-preparation-for-transmission-and-during-reception"></a>組織は、送信の準備と受信の間を含め、送信される情報の機密性と整合性が情報システムによって保護されることを保証します。

**[ID]** :0862.09m2Organizational.8 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fae5d2f14-d830-42b6-9899-df6cfe9c71a3) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての SQL Server を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_SQLServer_AuditIfNotExists.json) |

### <a name="the-organization-builds-a-firewall-configuration-that-restricts-connections-between-un-trusted-networks-and-any-system-components-in-the-covered-information-environment-and-any-changes-to-the-firewall-configuration-are-updated-in-the-network-diagram"></a>組織は、信頼されていないネットワークと、対象の情報環境のシステム コンポーネントとの間の接続を制限する、ファイアウォール構成を構築します。ファイアウォールの構成に対する変更は、ネットワーク図でも更新します。

**[ID]** :0863.09m2Organizational.910 - 09.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[イベント ハブは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd63edb4a-c612-454d-b47d-191a724fcbf0) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのイベント ハブを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_EventHub_AuditIfNotExists.json) |

### <a name="usage-restrictions-and-implementation-guidance-are-formally-defined-for-voip-including-the-authorization-and-monitoring-of-the-service"></a>サービスの承認と監視を含め、VoIP に対する使用制限と実装ガイダンスを正式に定義します。

**[ID]** :0864.09m2Organizational.12 - 09.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Cosmos DB を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |

### <a name="the-organization-i-authorizes-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-through-the-use-of-interconnection-security-agreements-or-other-formal-agreement-ii-documents-each-connection-the-interface-characteristics-security-requirements-and-the-nature-of-the-information-communicated-iii-employs-a-deny-all-permit-by-exception-policy-for-allowing-connections-from-the-information-system-to-other-information-systems-outside-of-the-organization-and-iv-applies-a-default-deny-rule-that-drops-all-traffic-via-host-based-firewalls-or-port-filtering-tools-on-its-endpoints-workstations-servers-etc-except-those-services-and-ports-that-are-explicitly-allowed"></a>組織は、(i) 相互接続セキュリティ契約またはその他の正式な契約を使用して、情報システムから組織外の他の情報システムへの接続を承認し、(ii) 各接続、インターフェイスの特性、セキュリティ要件、伝達される情報の性質を文書化し、(iii) "すべて拒否、例外的に許可" ポリシーを使用して情報システムから組織外の他の情報システムへの接続を許可し、(iv) 明示的に許可されているサービスとポートを除き、ホスト ベースのファイアウォールまたはエンドポイント (ワークステーション、サーバーなど) のポート フィルター ツールを経由するすべてのトラフィックを削除する "既定で拒否" 規則を適用します。

**[ID]** :0865.09m2Organizational.13 - 09.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault は仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fea4d6841-2173-4317-9747-ff522a45120f) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべての Key Vault を監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_KeyVault_Audit.json) |

### <a name="the-organization-describes-the-groups-roles-and-responsibilities-for-the-logical-management-of-network-components-and-ensures-coordination-of-and-consistency-in-the-elements-of-the-network-infrastructure"></a>組織では、ネットワーク コンポーネントの論理的な管理のためのグループ、役割、責任について説明し、ネットワーク インフラストラクチャの要素の調整と整合性を保証します。

**[ID]** :0866.09m3Organizational.1516 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="wireless-access-points-are-placed-in-secure-areas-and-shut-down-when-not-in-use-eg-nights-weekends"></a>ワイヤレス アクセス ポイントをセキュリティで保護された領域に配置し、使用されていないとき (夜間、週末など) はシャットダウンします。

**[ID]** :0867.09m3Organizational.17 - 09.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F60d21c4f-21a3-4d94-85f4-b924e6aeeda4) |このポリシーは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのストレージ アカウントを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_StorageAccount_Audit.json) |

### <a name="the-organization-builds-a-firewall-configuration-to-restrict-inbound-and-outbound-traffic-to-that-which-is-necessary-for-the-covered-data-environment"></a>組織では、対象のデータ環境に必要な範囲で受信トラフィックと送信トラフィックを制限するファイアウォール構成を構築します。

**[ID]** :0868.09m3Organizational.18 - 09.m **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="the-router-configuration-files-are-secured-and-synchronized"></a>ルーター構成ファイルをセキュリティで保護し、同期します。

**[ID]** :0869.09m3Organizational.19 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="access-to-all-proxies-is-denied-except-for-those-hosts-ports-and-services-that-are-explicitly-required"></a>明示的に要求されているホスト、ポート、サービスを除き、すべてのプロキシへのアクセスを拒否します。

**[ID]** :0870.09m3Organizational.20 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

### <a name="authoritative-dns-servers-are-segregated-into-internal-and-external-roles"></a>権限のある DNS サーバーを、内部と外部の役割に分離します。

**[ID]** :0871.09m3Organizational.22 - 09.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナー レジストリは仮想ネットワーク サービス エンドポイントを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |このポリシーでは、仮想ネットワーク サービス エンドポイントを使用するように構成されていないすべてのコンテナー レジストリを監査します。 |Audit、Disabled |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="security-of-network-services"></a>Network Services のセキュリティ

### <a name="agreed-services-provided-by-a-network-service-providermanager-are-formally-managed-and-monitored-to-ensure-they-are-provided-securely"></a>ネットワーク サービス プロバイダーおよびマネージャーから提供される同意済みのサービスは、セキュリティで保護された方法で提供されるように正式に管理および監視されます。

**[ID]** :0835.09n1Organizational.1 - 09.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |
|[仮想マシンを新しい Azure Resource Manager リソースに移行する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1d84d5fb-01f6-4d12-ba4f-4a26081d403d) |ご使用の仮想マシンに新しい Azure Resource Manager を使用して、セキュリティを強化します。これには、アクセス制御の強化 (RBAC)、監査の改善、Azure Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレット取得のためのキー コンテナーへのアクセス、Azure AD に基づく認証、セキュリティ管理を容易にするタグとリソース グループのサポートなどがあります |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |

### <a name="the-organization-formally-authorizes-and-documents-the-characteristics-of-each-connection-from-an-information-system-to-other-information-systems-outside-the-organization"></a>組織は、情報システムから組織外の他の情報システムへの各接続の特性を正式に承認し、文書化します。

**[ID]** :0836.09.n2Organizational.1 - 09.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="formal-agreements-with-external-information-system-providers-include-specific-obligations-for-security-and-privacy"></a>外部情報システム プロバイダーを使用した正式な契約には、セキュリティとプライバシーに関する特定の義務が含まれます。

**[ID]** :0837.09.n2Organizational.2 - 09.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-reviews-and-updates-the-interconnection-security-agreements-on-an-ongoing-basis-verifying-enforcement-of-security-requirements"></a>組織では、セキュリティ要件の適用を検証し、継続的に相互接続セキュリティ契約を見直して更新します。

**[ID]** :0885.09n2Organizational.3 - 09.n **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ネットワーク トラフィック データ収集エージェントを、Linux 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c4380f-3fae-46e8-96c9-30193528f602) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Linux.json) |

### <a name="the-organization-employs-and-documents-in-a-formal-agreement-or-other-document-either-i-allow-all-deny-by-exception-or-ii-deny-all-permit-by-exception-preferred-policy-for-allowing-specific-information-systems-to-connect-to-external-information-systems"></a>組織は、i) すべて許可、例外的に拒否、または ii) すべてを拒否し、例外を許可する (推奨) のいずれかのポリシーを採用して、特定の情報システムが外部情報システムに接続することを許可し、正式な契約書またはその他のドキュメントに文書化します。

**[ID]** :0886.09n2Organizational.4 - 09.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

### <a name="the-organization-requires-externaloutsourced-service-providers-to-identify-the-specific-functions-ports-and-protocols-used-in-the-provision-of-the-externaloutsourced-services"></a>組織は、外部およびアウトソーシングのサービス プロバイダーに、外部サービスおよびアウトソーシングされるサービスのプロビジョニングで使用される特定の機能、ポート、プロトコルを明らかにするように要求します。

**[ID]** :0887.09n2Organizational.5 - 09.n **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ネットワーク トラフィック データ収集エージェントを、Windows 仮想マシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2f2ee1de-44aa-4762-b6bd-0893fc3f306d) |Security Center では Microsoft Dependency Agent を使用して Azure 仮想マシンからネットワーク トラフィック データを収集し、ネットワーク マップでのトラフィックの視覚化、ネットワーク強化の推奨事項、特定のネットワークの脅威などの高度なネットワーク保護機能を有効にします。 |AuditIfNotExists、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ASC_Dependency_Agent_Audit_Windows.json) |

### <a name="the-contract-with-the-externaloutsourced-service-provider-includes-the-specification-that-the-service-provider-is-responsible-for-the-protection-of-covered-information-shared"></a>外部またはアウトソーシング サービスのプロバイダーとのコントラクトには、サービス プロバイダーが保護を担当する、対象となる共有情報の明細が含まれています。

**[ID]** :0888.09n2Organizational.6 - 09.n **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="management-of-removable-media"></a>リムーバブル メディアの管理

### <a name="the-organization-based-on-the-data-classification-level-registers-media-including-laptops-prior-to-use-places-reasonable-restrictions-on-how-such-media-be-used-and-provides-an-appropriate-level-of-physical-and-logical-protection-including-encryption-for-media-containing-covered-information-until-properly-destroyed-or-sanitized"></a>組織は、データ分類レベルに基づいて、使用前にメディア (ラップトップなど) を登録し、そのようなメディアの使用方法に適切な制限を設け、適切に破棄またはサニタイズされるまで対象情報が格納されているメディアに対して適切なレベルの物理的および論理的保護 (暗号化など) を提供します。

**[ID]** :0301.09o1Organizational.123 - 09.o **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption を SQL データベース上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="the-organization-protects-and-controls-media-containing-sensitive-information-during-transport-outside-of-controlled-areas"></a>組織は、制御された領域の外部での転送の間に、機密情報を含むメディアを保護および制御します。

**[ID]** :0302.09o2Organizational.1 - 09.o **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンでディスク暗号化を適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |ディスク暗号化が有効になっていない仮想マシンが、推奨設定として Azure Security Center によって監視されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="digital-and-non-digital-media-requiring-restricted-use-and-the-specific-safeguards-used-to-restrict-their-use-are-identified"></a>制限された使用を必要とするデジタルおよび非デジタル メディアと、その使用を制限するために使用される特定の安全対策を明らかにします。

**[ID]** :0303.09o2Organizational.2 - 09.o **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アタッチされていないディスクを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |このポリシーは、アタッチされていないディスクで、暗号化が有効でないものすべてを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="the-organization-restricts-the-use-of-writable-removable-media-and-personally-owned-removable-media-in-organizational-systems"></a>組織は、組織のシステムでの書き込み可能なリムーバブル メディアと個人所有のリムーバブル メディアの使用を制限します。

**[ID]** :0304.09o3Organizational.1 - 09.o **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Data Lake Store アカウントにおける暗号化が必要](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa7ff3161-0087-490a-9ad9-ad6217f4f43a) |このポリシーは、すべての Data Lake Store アカウントで暗号化を有効にします |deny |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStoreEncryption_Deny.json) |
|[SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="information-exchange-policies-and-procedures"></a>情報交換のポリシーと手順

### <a name="cloud-service-providers-use-an-industry-recognized-virtualization-platform-and-standard-virtualization-formats-eg-open-virtualization-format-ovf-to-help-ensure-interoperability-and-has-documented-custom-changes-made-to-any-hypervisor-in-use-and-all-solution-specific-virtualization-hooks-available-for-customer-review"></a>クラウド サービス プロバイダーは、業界で認められた仮想化プラットフォームと標準の仮想化形式 (Open Virtualization Format (OVF) など) を使用して相互運用性を確保し、顧客が確認できるように、使用中のハイパーバイザーに加えられたカスタムの変更と、すべてのソリューション固有の仮想化フックを文書化しています。

**[ID]** :0662.09sCSPOrganizational.2 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-organization-formally-addresses-multiple-safeguards-before-allowing-the-use-of-information-systems-for-information-exchange"></a>組織は、情報交換に対して情報システムの使用を許可する前に、複数の安全対策に正式に対処します。

**[ID]** :0901.09s1Organizational.1 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |クロス オリジン リソース共有 (CORS) で、Web アプリケーションへのアクセスをすべてのドメインには許可しないでください。 Web アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="remote-external-access-to-the-organizations-information-assets-and-access-to-external-information-assets-for-which-the-organization-has-no-control-is-based-on-clearly-defined-terms-and-conditions"></a>組織の情報資産へのリモート (外部) アクセスと、(組織が制御していない) 外部情報資産へのアクセスは、明確に定義された使用条件に基づいて行われます。

**[ID]** :0902.09s2Organizational.13 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS で関数アプリへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |クロス オリジン リソース共有 (CORS) で、関数アプリへのアクセスをすべてのドメインには許可しないでください。 関数アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="the-organization-establishes-terms-and-conditions-consistent-with-any-trust-relationship-established-with-other-organizations-owning-operating-andor-maintaining-external-information-systems-allowing-authorized-individuals-to-i-access-the-information-system-from-external-information-systems-and-ii-process-store-or-transmit-organization-controlled-information-using-external-information-systems"></a>組織は、外部に所有、運用、保守する他の組織との間で確立した信頼関係と矛盾しない条項を規定し、権限を持つ個人が外部情報システムを使用して、(i) 外部の情報システムから情報システムにアクセスし、(ii) 組織が制御する情報を処理、格納、または送信することができるようにします。

**[ID]** :0911.09s1Organizational.2 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS で API アプリへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F358c20a6-3f9e-4f0e-97ff-c6ce485e2aac) |クロス オリジン リソース共有 (CORS) では、API アプリへのアクセスをすべてのドメインには許可しないでください。 API アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_ApiApp_Audit.json) |

### <a name="cryptography-is-used-to-protect-the-confidentiality-and-integrity-of-remote-access-sessions-to-the-internal-network-and-to-external-systems"></a>内部ネットワークと外部システムに対するリモート アクセス セッションの機密性と整合性を保護するために、暗号化を使用します。

**[ID]** :0912.09s1Organizational.4 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリケーションのリモート デバッグを無効にする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcb510bfd-1cba-4d9f-a230-cb0976f4bb71) |リモート デバッグを実行するには、受信ポートが Web アプリケーション上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_WebApp_Audit.json) |

### <a name="strong-cryptography-protocols-are-used-to-safeguard-covered-information-during-transmission-over-less-trusted--open-public-networks"></a>強力な暗号化プロトコルを使用して、信頼性の低い、オープンなパブリック ネットワークでの送信の間に、対象の情報を保護します。

**[ID]** :0913.09s1Organizational.5 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

### <a name="the-organization-ensures-that-communication-protection-requirements-including-the-security-of-exchanges-of-information-is-the-subject-of-policy-development-and-compliance-audits"></a>組織は、情報交換のセキュリティなどの通信保護の要件が、ポリシーの開発とコンプライアンスの監査の対象であることを確認します。

**[ID]** :0914.09s1Organizational.6 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9c8d085-d9cc-4b17-9cdc-059f1f01f19e) |リモート デバッグを実行するには、受信ポートが API アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_ApiApp_Audit.json) |

### <a name="the-organization-limits-the-use-of-organization-controlled-portable-storage-media-by-authorized-individuals-on-external-information-systems"></a>組織は、外部情報システム上の承認された個人による組織管理のポータブル ストレージ メディアの使用を制限します。

**[ID]** :0915.09s2Organizational.2 - 09.s **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |

### <a name="the-information-system-prohibits-remote-activation-of-collaborative-computing-devices-and-provides-an-explicit-indication-of-use-to-users-physically-present-at-the-devices"></a>情報システムでは、コラボレーション コンピューティング デバイスのリモート アクティブ化を禁止し、デバイスに物理的に存在するユーザーに対して、使用していることを明示的に示します。

**[ID]** :0916.09s2Organizational.4 - 09.s **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS で、Web アプリケーションへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5744710e-cc2f-4ee8-8809-3b11e89f4bc9) |クロス オリジン リソース共有 (CORS) で、Web アプリケーションへのアクセスをすべてのドメインには許可しないでください。 Web アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_WebApp_Audit.json) |

### <a name="cloud-service-providers-use-secure-eg-non-clear-text-and-authenticated-standardized-network-protocols-for-the-import-and-export-of-data-and-to-manage-the-service-and-make-available-a-document-to-consumers-tenants-detailing-the-relevant-interoperability-and-portability-standards-that-are-involved"></a>クラウド サービス プロバイダーは、データのインポートとエクスポートおよびサービスの管理に対してセキュリティで保護され (たとえば、クリアテキストではなく、認証された) 標準化されたネットワーク プロトコルを使用し、関連する相互運用性と移植性の標準に関する詳細が記述されたドキュメントをコンシューマー (テナント) が使用できるようにします。

**[ID]** :0960.09sCSPOrganizational.1 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[CORS で関数アプリへのアクセスをすべてのリソースには許可しない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0820b7b9-23aa-4725-a1ce-ae4558f718e5) |クロス オリジン リソース共有 (CORS) で、関数アプリへのアクセスをすべてのドメインには許可しないでください。 関数アプリの操作に必要なドメインのみを許可します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RestrictCORSAccess_FuntionApp_Audit.json) |

### <a name="personnel-are-appropriately-trained-on-leading-principles-and-practices-for-all-types-of-information-exchange-oral-paper-and-electronic"></a>すべての種類の情報交換 (口頭、紙、電子メール) に対する主要な原則とプラクティスについて、担当者を適切にトレーニングします。

**[ID]** :1325.09s1Organizational.3 - 09.s **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[関数アプリでリモート デバッグを無効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e60b895-3786-45da-8377-9c6b4b6ac5f9) |リモート デバッグを実行するには、受信ポートが関数アプリ上で開かれている必要があります。 リモート デバッグを無効にする必要があります。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_DisableRemoteDebugging_FunctionApp_Audit.json) |

## <a name="on-line-transactions"></a>オンライン トランザクション

### <a name="data-involved-in-electronic-commerce-and-online-transactions-is-checked-to-determine-if-it-contains-covered-information"></a>電子商取引とオンライン取引に関連するデータをチェックし、対象となる情報が含まれているかどうかを確認します。

**[ID]** :0943.09y1Organizational.1 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="protocols-used-to-communicate-between-all-involved-parties-are-secured-using-cryptographic-techniques-eg-ssl"></a>関係するすべてのパーティ間の通信に使用されるプロトコルを、暗号化手法 (SSL など) を使用してセキュリティで保護します。

**[ID]** :0945.09y1Organizational.3 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[指定された証明書が信頼されたルートに含まれていない Windows マシンを監査する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F934345e1-4dfb-4c70-90d7-41990dc9608b) |前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 マシンの信頼されたルート証明書ストア (Cert:\LocalMachine\Root) に、ポリシー パラメーターによって指定した 1 つ以上の証明書が含まれていない場合、マシンは非準拠となります。 |auditIfNotExists |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_WindowsCertificateInTrustedRoot_AINE.json) |

### <a name="the-organization-requires-the-use-of-encryption-between-and-the-use-of-electronic-signatures-by-each-of-the-parties-involved-in-the-transaction"></a>組織では、トランザクションに関係する各パーティの間で暗号化を使用し、各パーティで電子署名を使用することを要求します。

**[ID]** :0946.09y2Organizational.14 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Cache for Redis へのセキュリティで保護された接続のみを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Azure Cache for Redis に対して SSL 経由の接続のみが有効であるかどうかを監査します。 セキュリティで保護された接続を使用することにより、サーバーとサービスの間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッションハイジャックなど) から保護します |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

### <a name="the-organization-ensures-the-storage-of-the-transaction-details-are-located-outside-of-any-publicly-accessible-environments-eg-on-a-storage-platform-existing-on-the-organizations-intranet-and-not-retained-and-exposed-on-a-storage-medium-directly-accessible-from-the-internet"></a>組織では、トランザクションの詳細のストレージがパブリック アクセスできる環境の外部に配置され (たとえば、組織のイントラネット上に存在するストレージ プラットフォーム上)、インターネットから直接アクセスできるストレージ メディアで保持および公開されないようにします。

**[ID]** :0947.09y2Organizational.2 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="where-a-trusted-authority-is-used-eg-for-the-purposes-of-issuing-and-maintaining-digital-signatures-andor-digital-certificates-security-is-integrated-and-embedded-throughout-the-entire-end-to-end-certificatesignature-management-process"></a>信頼できる機関を使用する場合 (デジタル署名やデジタル証明書を発行および管理するために)、エンド ツー エンドの証明書および署名管理プロセス全体にわたってセキュリティを統合し、埋め込みます。

**[ID]** :0948.09y2Organizational.3 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="the-protocols-used-for-communications-are-enhanced-to-address-any-new-vulnerability-and-the-updated-versions-of-the-protocols-are-adopted-as-soon-as-possible"></a>通信に使用されるプロトコルは、新しい脆弱性に対応するように強化し、プロトコルのバージョンが更新されたらできるだけ早く採用します。

**[ID]** :0949.09y2Organizational.5 - 09.y **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb7ddfbdc-1260-477d-91fd-98bd9be789a6) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceApiApp_AuditHTTP_Audit.json) |
|[Function App には HTTPS 経由でのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6d555dd1-86f2-4f1c-8ed7-5abae7c6cbab) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceFunctionApp_AuditHTTP_Audit.json) |
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

## <a name="control-of-operational-software"></a>運用ソフトウェアのコントロール

### <a name="only-authorized-administrators-are-allowed-to-implement-approved-upgrades-to-software-applications-and-program-libraries-based-on-business-requirements-and-the-security-implications-of-the-release"></a>ビジネス要件とリリースのセキュリティへの影響に基づいて、ソフトウェア、アプリケーション、およびプログラム ライブラリへの承認されたアップグレードを実装できるのは、承認された管理者のみです。

**[ID]** :0605.10h1System.12 - 10.h **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[Windows マシンは [セキュリティ オプション - 監査] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F33936777-f2ac-45aa-82ec-07958ec9ade4) |Windows マシンには、セキュリティ監査をログに記録できない場合に監査ポリシー サブカテゴリを強制的に適用してシャットダウンすることについて、カテゴリ [セキュリティ オプション - 監査] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsAudit_AINE.json) |
|[Windows マシンは [システム監査ポリシー - アカウント管理] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94d9aca8-3757-46df-aa51-f218c5f11954) |Windows マシンには、アプリケーション、セキュリティ、ユーザー グループ管理、その他の管理イベントを監査することについて、カテゴリ [システム監査ポリシー - アカウント管理] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesAccountManagement_AINE.json) |

### <a name="applications-and-operating-systems-are-successfully-tested-for-usability-security-and-impact-prior-to-production"></a>アプリケーションとオペレーティング システムは、運用前に、ユーザビリティ、セキュリティ、および影響についてのテストを正常に終えています。

**[ID]** :0606.10h2System.1 - 10.h **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナーのセキュリティ構成の脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker がインストールされているマシンのセキュリティ構成の脆弱性を監査し、Azure Security Center で推奨事項として表示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-uses-its-configuration-control-program-to-maintain-control-of-all-implemented-software-and-its-system-documentation-and-archive-prior-versions-of-implemented-software-and-associated-system-documentation"></a>組織では、構成管理プログラムを使用して、実装されているすべてのソフトウェアとそのシステム ドキュメントの管理を維持し、実装されているソフトウェアと関連するシステム ドキュメントの以前のバージョンをアーカイブします。

**[ID]** :0607.10h2System.23 - 10.h **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

## <a name="change-control-procedures"></a>変更制御の手順

### <a name="managers-responsible-for-application-systems-are-also-responsible-for-the-strict-control-security-of-the-project-or-support-environment-and-ensure-that-all-proposed-system-changes-are-reviewed-to-check-that-they-do-not-compromise-the-security-of-either-the-system-or-the-operating-environment"></a>アプリケーション システムを担当するマネージャーは、プロジェクトまたはサポート環境の厳格な制御 (セキュリティ) も担当し、提案されたすべてのシステム変更が確認され、システムまたは運用環境のセキュリティが侵害されないことを確実にします。

**[ID]** :0635.10k1Organizational.12 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-formally-addresses-purpose-scope-roles-responsibilities-management-commitment-coordination-among-organizational-entities-and-compliance-for-configuration-management-eg-through-policies-standards-processes"></a>組織は、目的、スコープ、役割、責任、管理コミットメント、組織エンティティ間の調整、および構成管理に対するコンプライアンスに (たとえば、ポリシー、標準、プロセスを通じて) 正式に対応します。

**[ID]** :0636.10k2Organizational.1 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-has-developed-documented-and-implemented-a-configuration-management-plan-for-the-information-system"></a>組織では、情報システムの構成管理計画が開発、文書化、および実装されています。

**[ID]** :0637.10k2Organizational.2 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="changes-are-formally-controlled-documented-and-enforced-in-order-to-minimize-the-corruption-of-information-systems"></a>情報システムの破損を最小限に抑えるため、変更は正式に管理、文書化、適用されています。

**[ID]** :0638.10k2Organizational.34569 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="installation-checklists-and-vulnerability-scans-are-used-to-validate-the-configuration-of-servers-workstations-devices-and-appliances-and-ensure-the-configuration-meets-minimum-standards"></a>インストール チェックリストと脆弱性スキャンを使用して、サーバー、ワークステーション、デバイス、アプライアンスの構成が検証され、構成が最低限の標準を満たしていることが確認されています。

**[ID]** :0639.10k2Organizational.78 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="where-development-is-outsourced-change-control-procedures-to-address-security-are-included-in-the-contracts-and-specifically-require-the-developer-to-track-security-flaws-and-flaw-resolution-within-the-system-component-or-service-and-report-findings-to-organization-defined-personnel-or-roles"></a>開発をアウトソーシングする場合は、セキュリティに対処するための変更管理手順が契約に含まれており、システム、コンポーネント、またはサービスでセキュリティの欠陥および欠陥の解決を追跡し、組織で定義されている担当者または役割に対して結果を報告するよう、開発者に明示的に要求されています。

**[ID]** :0640.10k2Organizational.1012 - 10.k **所有権**:Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-does-not-use-automated-updates-on-critical-systems"></a>組織では、重要なシステムに対して自動更新が使用されていません。

**[ID]** :0641.10k2Organizational.11 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-develops-documents-and-maintains-under-configuration-control-a-current-baseline-configuration-of-the-information-system-and-reviews-and-updates-the-baseline-as-required"></a>組織は、情報システムの現在のベースライン構成を開発して文書化し、構成管理のもとで管理し、必要に応じてベースラインをレビューおよび更新します。

**[ID]** :0642.10k3Organizational.12 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-i-establishes-and-documents-mandatory-configuration-settings-for-information-technology-products-employed-within-the-information-system-using-the-latest-security-configuration-baselines-ii-identifies-documents-and-approves-exceptions-from-the-mandatory-established-configuration-settings-for-individual-components-based-on-explicit-operational-requirements-and-iii-monitors-and-controls-changes-to-the-configuration-settings-in-accordance-with-organizational-policies-and-procedures"></a>組織は、(i) 最新のセキュリティ構成ベースラインを使用して、情報システム内で採用されている情報技術製品の必須の構成設定を確立して文書化し、(ii) 明示的な運用要件に基づいて、個々のコンポーネントに対して確立された必須の構成設定からの例外を識別、文書化、承認し、(iii) 組織のポリシーと手順に従って、構成設定の変更を監視および管理します。

**[ID]** :0643.10k3Organizational.3 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

### <a name="the-organization-employs-automated-mechanisms-to-i-centrally-manage-apply-and-verify-configuration-settings-ii-respond-to-unauthorized-changes-to-network-and-system-security-related-configuration-settings-and-iii-enforce-access-restrictions-and-auditing-of-the-enforcement-actions"></a>組織は、(i) 構成設定の一元的な管理、適用、検証、(ii) ネットワークおよびシステムのセキュリティ関連の構成設定に対する承認されていない変更への対応、(iii) 適用アクションのアクセス制限と監査の適用に関して、自動化されたメカニズムを採用します。

**[ID]** :0644.10k3Organizational.4 - 10.k **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [システム監査ポリシー - 詳細追跡] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F58383b73-94a9-4414-b382-4146eb02611b) |Windows マシンには、DPAPI、プロセスの作成と終了、RPC イベント、PNP アクティビティを監査することについて、カテゴリ [システム監査ポリシー - 詳細追跡] で指定されているグループ ポリシー設定が必要です。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SystemAuditPoliciesDetailedTracking_AINE.json) |

## <a name="control-of-technical-vulnerabilities"></a>技術的な脆弱性のコントロール

### <a name="technical-vulnerabilities-are-identified-evaluated-for-risk-and-corrected-in-a-timely-manner"></a>技術的な脆弱性が特定され、リスクの評価と修正が適切なタイミングで行われます。

**[ID]** :0709.10m1Organizational.1 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |
|[コンテナーのセキュリティ構成の脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker がインストールされているマシンのセキュリティ構成の脆弱性を監査し、Azure Security Center で推奨事項として表示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |
|[SQL データベースの脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |脆弱性評価スキャン結果と、データベースの脆弱性を修正する方法についての推奨事項を監視します。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |
|[SQL Managed Instance で脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |定期的な脆弱性評価スキャンが有効になっていない各 SQL Managed Instance を監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[脆弱性評価を SQL サーバー上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |定期的な脆弱性評価スキャンが有効になっていない Azure SQL サーバーを監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |
|[Windows マシンは [セキュリティ オプション - Microsoft ネットワーク サーバー] の要件を満たす必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcaf2d518-f029-4f6b-833b-d7081702f253) |Windows コンピューターでは、SMB v1 サーバーを無効にするため、[セキュリティ オプション - Microsoft ネットワーク サーバー] カテゴリでグループ ポリシー設定が指定されている必要があります。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsMicrosoftNetworkServer_AINE.json) |

### <a name="a-hardened-configuration-standard-exists-for-all-system-and-network-components"></a>すべてのシステムおよびネットワーク コンポーネントに対して、セキュリティが強化された構成の標準が存在します。

**[ID]** :0710.10m2Organizational.1 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance で脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |定期的な脆弱性評価スキャンが有効になっていない各 SQL Managed Instance を監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

### <a name="a-technical-vulnerability-management-program-is-in-place-to-monitor-assess-rank-and-remediate-vulnerabilities-identified-in-systems"></a>システムで特定された脆弱性を監視、評価、優先度付け、修復するための技術的な脆弱性管理プログラムが用意されています。

**[ID]** :0711.10m2Organizational.23 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="patches-are-tested-and-evaluated-before-they-are-installed"></a>パッチは、インストール前にテストおよび評価されます。

**[ID]** :0713.10m2Organizational.5 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-technical-vulnerability-management-program-is-evaluated-on-a-quarterly-basis"></a>技術的な脆弱性管理プログラムは、四半期ごとに評価されます。

**[ID]** :0714.10m2Organizational.7 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="systems-are-appropriately-hardened-eg-configured-with-only-necessary-and-secure-services-ports-and-protocols-enabled"></a>システムが適切に強化されます (たとえば、セキュリティで保護された必要なサービス、ポート、プロトコルのみを有効にして構成されるなど)。

**[ID]** :0715.10m2Organizational.8 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[コンテナーのセキュリティ構成の脆弱性を修復する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8cbc669-f12d-49eb-93e7-9273119e9933) |Docker がインストールされているマシンのセキュリティ構成の脆弱性を監査し、Azure Security Center で推奨事項として表示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ContainerBenchmark_Audit.json) |

### <a name="the-organization-conducts-an-enterprise-security-posture-review-as-needed-but-no-less-than-once-within-every-three-hundred-sixty-five-365-days-in-accordance-with-organizational-is-procedures"></a>組織は、組織の IS 手順に従い、必要に応じて、ただし 365 日ごとに 1 回以上、会社のセキュリティ体制のレビューを実行します。

**[ID]** :0716.10m3Organizational.1 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL データベースの脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffeedbf84-6b99-488c-acc2-71c829aa5ffc) |脆弱性評価スキャン結果と、データベースの脆弱性を修正する方法についての推奨事項を監視します。 |AuditIfNotExists、Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_SQLDbVulnerabilities_Audit.json) |

### <a name="vulnerability-scanning-tools-include-the-capability-to-readily-update-the-information-system-vulnerabilities-scanned"></a>脆弱性スキャン ツールには、キャン対象の情報システムの脆弱性を速やかに更新する機能が含まれています。

**[ID]** :0717.10m3Organizational.2 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシン スケール セットのセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3c735d8a-a4ba-4a3a-b7cf-db7754cf57f4) |攻撃から保護するため、お使いの仮想マシン スケール セットの OS 脆弱性を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_VmssOSVulnerabilities_Audit.json) |

### <a name="the-organization-scans-for-vulnerabilities-in-the-information-system-and-hosted-applications-to-determine-the-state-of-flaw-remediation-monthly-automatically-and-again-manually-or-automatically-when-new-vulnerabilities-potentially-affecting-the-systems-and-networked-environments-are-identified-and-reported"></a>組織は、毎月 (自動で)、およびシステムとネットワーク環境に影響を与える可能性のある新しい脆弱性が特定されて報告されたときに再度 (手動または自動で)、情報システムおよびホストされているアプリケーションの脆弱性をスキャンして、欠陥の修復の状態を確認します。

**[ID]** :0718.10m3Organizational.34 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="the-organization-updates-the-list-of-information-system-vulnerabilities-scanned-within-every-thirty-30-days-or-when-new-vulnerabilities-are-identified-and-reported"></a>組織は、30 日以下の間隔で、または新しい脆弱性が特定されて報告されたときに、スキャンされた情報システムの脆弱性の一覧を更新します。

**[ID]** :0719.10m3Organizational.5 - 10.m **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance で脆弱性評価を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |定期的な脆弱性評価スキャンが有効になっていない各 SQL Managed Instance を監査します。 脆弱性評価は、潜在的なデータベースの脆弱性を検出、追跡、および修正するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |

## <a name="business-continuity-and-risk-assessment"></a>ビジネス継続性とリスク評価

### <a name="the-organization-identifies-the-critical-business-processes-requiring-business-continuity"></a>組織は、ビジネス継続性を必要とする重要なビジネス プロセスを特定します。

**[ID]** :1634.12b1Organizational.1 - 12.b **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ディザスター リカバリーを構成されていない仮想マシンの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |ディザスター リカバリーが構成されていない仮想マシンを監査します。 ディザスター リカバリーの詳細については、[https://aka.ms/asr-doc](https://aka.ms/asr-doc) にアクセスしてください。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

### <a name="information-security-aspects-of-business-continuity-are-i-based-on-identifying-events-or-sequence-of-events-that-can-cause-interruptions-to-the-organizations-critical-business-processes-eg-equipment-failure-human-errors-theft-fire-natural-disasters-acts-of-terrorism-ii-followed-by-a-risk-assessment-to-determine-the-probability-and-impact-of-such-interruptions-in-terms-of-time-damage-scale-and-recovery-period-iii-based-on-the-results-of-the-risk-assessment-a-business-continuity-strategy-is-developed-to-identify-the-overall-approach-to-business-continuity-and-iv-once-this-strategy-has-been-created-endorsement-is-provided-by-management-and-a-plan-created-and-endorsed-to-implement-this-strategy"></a>ビジネス継続性に関する情報セキュリティの側面は、(i) 組織の重要なビジネス プロセスが中断される可能性のあるイベント (またはイベントのシーケンス) (機器の障害、人的エラー、盗難、火災、自然災害など) に基づき、(ii) その後で、リスク評価によって、時間、損害規模、復旧期間に関してこのような中断の確率と影響が判断され、(iii) リスク評価の結果に基づいて、ビジネス継続性戦略を策定して、ビジネス継続性に対する全体的なアプローチが明らかにされ、(iv) この戦略が作成されたら、管理者によって承認されて、この戦略を実装するための計画が作成されて承認されます。

**[ID]** :1635.12b1Organizational.2 - 12.b **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM で消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Azure Key Vault Managed HSM が悪意により削除されると、完全にデータが失われる可能性があります。 組織内の悪意のある内部関係者が、Azure Key Vault Managed HSM の削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除された Azure Key Vault Managed HSM に必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中に Azure Key Vault Managed HSM を消去することはできなくなります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[キー コンテナーで消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |悪意でキー コンテナーが削除されると、データが完全に失われる可能性があります。 組織内の悪意のある内部関係者が、キー コンテナーの削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除されたキー コンテナーに必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中にキー コンテナーを消去することはできなくなります。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="business-impact-analysis-are-used-to-evaluate-the-consequences-of-disasters-security-failures-loss-of-service-and-service-availability"></a>ビジネスへの影響分析は、災害、セキュリティ エラー、サービスの損失、サービスの可用性の結果を評価するために使用されます。

**[ID]** :1637.12b2Organizational.2 - 12.b **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Windows マシンは [セキュリティ オプション - 回復コンソール] の要件を満たしている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff71be03e-e25b-4d0f-b8bc-9b3e309b66c0) |すべてのドライブおよびフォルダーのフロッピー コピーとアクセスを可能にするため、[セキュリティ オプション - 回復コンソール] カテゴリでグループ ポリシー設定が指定されている必要があります。 このポリシーでは、ゲスト構成の前提条件がポリシーの割り当てスコープにデプロイされていることが要求されます。 詳細については、[https://aka.ms/gcpol](https://aka.ms/gcpol) を参照してください。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Guest%20Configuration/GuestConfiguration_SecurityOptionsRecoveryconsole_AINE.json) |

### <a name="business-continuity-risk-assessments-i-are-carried-out-annually-with-full-involvement-from-owners-of-business-resources-and-processes-ii-consider-all-business-processes-and-is-not-limited-to-the-information-assets-but-includes-the-results-specific-to-information-security-and-iii-identifies-quantifies-and-prioritizes-risks-against-key-business-objectives-and-criteria-relevant-to-the-organization-including-critical-resources-impacts-of-disruptions-allowable-outage-times-and-recovery-priorities"></a>ビジネス継続性リスク評価は、(i) ビジネス リソースとプロセスの所有者の全面的な関与を得て毎年実行されます。(ii) すべてのビジネス プロセスを検討し、情報資産に限定されない、情報セキュリティに固有の結果が含まれます。(iii) 重要なリソース、中断の影響、許容される停止時間、回復の優先度など、組織に関連する主要なビジネス目標と基準に対するリスクを特定、定量化、優先順位付けします。

**[ID]** :1638.12b2Organizational.345 - 12.b **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ディザスター リカバリーを構成されていない仮想マシンの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |ディザスター リカバリーが構成されていない仮想マシンを監査します。 ディザスター リカバリーの詳細については、[https://aka.ms/asr-doc](https://aka.ms/asr-doc) にアクセスしてください。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |

> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。

## <a name="next-steps"></a>次のステップ

Azure Policy に関するその他の記事:

- [規制コンプライアンス](../concepts/regulatory-compliance.md)の概要。
- [イニシアチブ定義の構造](../concepts/initiative-definition-structure.md)を参照してください。
- [Azure Policy のサンプル](./index.md)の他の例を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
