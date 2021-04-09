---
title: CIS Microsoft Azure Foundations Benchmark 1.1.0 の規制コンプライアンスの詳細
description: CIS Microsoft Azure Foundations Benchmark 1.1.0 規制コンプライアンスの組み込みイニシアチブの詳細。 それぞれのコントロールは、評価を支援する 1 つまたは複数の Azure Policy 定義に対応します。
ms.date: 03/10/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 05cbd8eebf560499ca2187b516569bfc0bf1fccf
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634738"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-110-regulatory-compliance-built-in-initiative"></a>CIS Microsoft Azure Foundations Benchmark 1.1.0 規制コンプライアンスの組み込みイニシアチブの詳細

次の記事では、Azure Policy 規制コンプライアンスの組み込みイニシアチブの定義が、CIS Microsoft Azure Foundations Benchmark 1.1.0 の **コンプライアンス ドメイン** と **コントロール** にどのように対応するのかを、詳しく説明します。
このコンプライアンス標準の詳細については、[CIS Microsoft Azure Foundations Benchmark 1.1.0](https://www.cisecurity.org/benchmark/azure/) に関するページを参照してください。 "_所有権_" については、[Azure Policy のポリシー定義](../concepts/definition-structure.md#type)に関するページと、「[クラウドにおける共同責任](../../../security/fundamentals/shared-responsibility.md)」を参照してください。

次のマッピングは、**CIS Microsoft Azure Foundations Benchmark 1.1.0** コントロールへのマッピングです。 右側のナビゲーションを使用すると、特定の **コンプライアンス ドメイン** に直接移動します。 コントロールの多くは、[Azure Policy](../overview.md) のイニシアチブ定義で実装されています。 完全なイニシアチブ定義を確認するには、Azure portal で **[ポリシー]** を開き、 **[定義]** ページを選択します。
次に、 **[CIS Microsoft Azure Foundations Benchmark 1.1.0]** 規制コンプライアンスの組み込みイニシアチブ定義を見つけて選択します。

この組み込みイニシアチブは、[CIS Microsoft Azure Foundations Benchmark 1.1.0 ブループリント サンプル](../../blueprints/samples/cis-azure-1-1-0.md)の一部としてデプロイされます。

> [!IMPORTANT]
> 以下の各コントロールは、1 つ以上の [Azure Policy](../overview.md) 定義に関連します。
> これらのポリシーは、コントロールに対する[コンプライアンスの評価](../how-to/get-compliance-data.md)に役立つ場合があります。ただし、多くの場合、コントロールと 1 つ以上のポリシーとの間には、一対一での一致、または完全な一致はありません。 そのため、Azure Policy での **準拠** では、ポリシー定義自体のみが示されています。これによって、コントロールのすべての要件に完全に準拠していることが保証されるわけではありません。 また、コンプライアンス標準には、現時点でどの Azure Policy 定義でも対応されていないコントロールが含まれています。 したがって、Azure Policy でのコンプライアンスは、全体のコンプライアンス状態の部分的ビューでしかありません。 このコンプライアンス標準に対するコンプライアンス ドメイン、コントロール、Azure Policy 定義の間の関連付けは、時間の経過と共に変わる可能性があります。 変更履歴を表示するには、[GitHub のコミット履歴](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_1_0.json)に関するページを参照してください。

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>特権のあるすべてのユーザーに対して多要素認証が有効になっていることを確認する

**[ID]** :CIS Azure 1.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する書き込みアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |アカウントまたはリソースに対する侵害を防止するために、書き込み権限を持つすべてのサブスクリプション アカウントに対して 多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[サブスクリプションで所有者アクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |アカウントまたはリソースに対する侵害を防止するために、所有者としてのアクセス許可を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>特権のないすべてのユーザーに対して多要素認証が有効になっていることを確認する

**[ID]** :CIS Azure 1.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションに対する読み取りアクセス許可を持つアカウントに対して MFA を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |アカウントまたはリソースの侵害を防止するために、読み取り権限を持つすべてのサブスクリプション アカウントで多要素認証 (MFA) を有効にする必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-that-there-are-no-guest-users"></a>ゲスト ユーザーがいないことを確認する

**[ID]** :CIS Azure 1.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |監視されていないアクセスを防止するために、所有者アクセス許可を持つ外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[読み取りアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |監視されていないアクセスを防止するために、読み取り権限がある外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[書き込みアクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |監視されていないアクセスを防止するために、書き込み権限がある外部アカウントは、サブスクリプションから削除する必要があります。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>カスタム サブスクリプションの所有者ロールが作成されていないことを確認する

**[ID]** :CIS Azure 1.23 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[カスタム サブスクリプションの所有者ロールが作成されていないこと](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |このポリシーでは、カスタム サブスクリプションの所有者ロールが存在しないことを確認します。 |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Security Center

### <a name="ensure-that-standard-pricing-tier-is-selected"></a>Standard 価格レベルが選択されていることを確認する

**[ID]** :CIS Azure 2.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Security Center の Standard 価格レベルを選択する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1181c5f-672a-477a-979a-7d58aa086233) |Standard 価格レベルでは、ネットワークと仮想マシンの脅威検出が可能になり、Azure Security Center で脅威インテリジェンス、異常検出、動作分析が提供されます |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Standard_pricing_tier.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>[監視エージェントの自動プロビジョニング] が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 2.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[自分のサブスクリプションで Log Analytics エージェントの自動プロビジョニングを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Azure Security Center では、セキュリティの脆弱性と脅威を監視するために、Azure 仮想マシンからデータを収集します。 データは、以前は Microsoft Monitoring Agent (MMA) と呼ばれていた Log Analytics エージェントによって収集されます。これがセキュリティ関連のさまざまな構成とイベント ログをマシンから読み取り、分析のためにデータを Log Analytics ワークスペースにコピーします。 自動プロビジョニングを有効にして、サポートされているすべての Azure VM と新しく作成された VM にこのエージェントを自動的にデプロイすることをお勧めします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>ASC の既定のポリシー設定 [システムの更新プログラムの監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[システム更新プログラムをマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>ASC の既定のポリシー設定 [OS 脆弱性の監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[使用しているマシンでセキュリティ構成の脆弱性を修復する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe1e5fd5d-3e4c-4ce1-8661-7d1873ae6b15) |構成ベースラインを満たしていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_OSVulnerabilities_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>ASC の既定のポリシー設定 [Endpoint Protection の監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>ASC の既定のポリシー設定 [ディスク暗号化の監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.6 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンでディスク暗号化を適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |ディスク暗号化が有効になっていない仮想マシンが、推奨設定として Azure Security Center によって監視されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>ASC の既定のポリシー設定 [ネットワーク セキュリティ グループの監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.7 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アダプティブ ネットワーク強化の推奨事項をインターネット接続仮想マシンに適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F08e6af2d-db70-460a-bfe9-d5bd474ba9d6) |Azure Security Center では、インターネットに接続している仮想マシンのトラフィック パターンを分析し、可能性のある攻撃面を減少させるためにネットワーク セキュリティ グループの規則の推奨事項を提供します |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveNetworkHardenings_Audit.json) |

### <a name="ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>ASC の既定のポリシー設定 [次世代のファイアウォール (NGFW) 監視を有効にする] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.9 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インターネットに接続する仮想マシンは、ネットワーク セキュリティ グループを使用して保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff6de0be7-9a8a-4b8a-b349-43cf02d22f7c) |ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制限することにより、潜在的な脅威から仮想マシンを保護します。 NSG を使用してトラフィックを制御する方法の詳細については、[https://aka.ms/nsg-doc](https://aka.ms/nsg-doc) を参照してください。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnInternetFacingVirtualMachines_Audit.json) |
|[サブネットは、ネットワーク セキュリティ グループに関連付けられている必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe71308d3-144b-4262-b144-efdc3cc90517) |ネットワーク セキュリティ グループ (NSG) を使用してお使いのサブネットへのアクセスを制限することで、潜在的な脅威からサブネットを保護します。 NSG には、お使いのサブネットに対するネットワーク トラフィックを許可または拒否する一連のアクセス制御リスト (ACL) ルールが含まれています。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_NetworkSecurityGroupsOnSubnets_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>ASC の既定のポリシー設定 [脆弱性評価を監視する] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.10 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[脆弱性評価ソリューションを仮想マシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F501541f7-f7e7-4cd6-868c-4190fdad3ac9) |仮想マシンを監査して、サポートされている脆弱性評価ソリューションを実行しているかどうかを検出します。 すべてのサイバーリスクとセキュリティプログラムの中核となるコンポーネントは、脆弱性の特定と分析です。 Azure Security Center の標準価格帯には、追加費用なしで仮想マシン脆弱性スキャンをする機能が含まれています。 また、Security Center では、このツールを自動的にデプロイできます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_ServerVulnerabilityAssessment_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>ASC の既定のポリシー設定 [JIT ネットワーク アクセスの監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.12 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンの管理ポートは、Just-In-Time のネットワーク アクセス制御で保護する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb0f33259-77d7-4c9e-aac6-3aabcfae693c) |可能なネットワークのジャスト イン タイム (JIT) アクセスが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_JITNetworkAccess_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>ASC の既定のポリシー設定 [Monitor Adaptive Application Whitelisting]\(適応型アプリケーションのホワイトリスト登録の監視\) が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.13 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a6b606-51aa-4496-8bb7-64b11cf66adc) |アプリケーション制御を有効にして、マシンで実行されている既知の安全なアプリケーションの一覧を定義し、他のアプリケーションの実行時にアラートを出します。 これは、マルウェアに対してマシンを強化するのに役立ちます。 ルールの構成と保守のプロセスを簡略化するために、Security Center で機械学習を使用して各マシンで実行されているアプリケーションを分析し、既知の安全なアプリケーションの一覧を提示します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_AdaptiveApplicationControls_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>ASC の既定のポリシー設定 [SQL 監査の監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.14 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>ASC の既定のポリシー設定 [SQL 暗号化の監視] が [無効] になっていないことを確認する

**[ID]** :CIS Azure 2.15 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption を SQL データベース上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-security-contact-emails-is-set"></a>[セキュリティ連絡先の電子メール] が設定されていることを確認する

**[ID]** :CIS Azure 2.16 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプションには、セキュリティの問題に備えて連絡先メール アドレスが用意されている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center からのメール通知を受信するセキュリティの連絡先を設定します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>[重要度 - 高についてアラートの電子メール通知を送信する] が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 2.18 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |サブスクリプションの 1 つでセキュリティ違反のおそれがある場合に、組織内の関係するユーザーに通知が送信されるようにするには、Security Center で重要度の高いアラートに関するメール通知を有効にします。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

### <a name="ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>[サブスクリプションの所有者にもメールを送信する] が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 2.19 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[サブスクリプション所有者に対する重要度 - 高のアラートの電子メール通知を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b15565f-aa9e-48ba-8619-45960f2c314d) |サブスクリプションにセキュリティ違反のおそれがある場合にサブスクリプションの所有者に通知が送信されるようにするには、Security Center で、重要度の高いアラートに関するメール通知をサブスクリプションの所有者に設定します。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification_to_subscription_owner.json) |

## <a name="storage-accounts"></a>ストレージ アカウント

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>[安全な転送が必要] が [有効] に設定されていることを確認する

**[ID]** :CIS Azure 3.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントへの安全な転送を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |ストレージ アカウント内の安全な転送の要件を監査します。 安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します |Audit、Deny、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>BLOB コンテナーの "パブリック アクセス レベル" が "プライベート" に設定されていることを確認する

**[ID]** :CIS Azure 3.6 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントのパブリック アクセスを禁止する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合があります。 好ましくない匿名アクセスによるデータ侵害を防ぐために、Microsoft では、シナリオで必要でない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 |audit、deny、disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>ストレージ アカウントの既定のネットワーク アクセス ルールが拒否に設定されていることを確認する

**[ID]** :CIS Azure 3.7 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントではネットワーク アクセスを制限する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |ストレージ アカウントに対するネットワーク アクセスは、制限する必要があります。 許可されているネットワークのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するために、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に対してアクセスを許可することができます。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>ストレージ アカウント アクセスに対して [信頼された Microsoft サービス] が有効になっていることを確認する

**[ID]** :CIS Azure 3.8 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントは、信頼された Microsoft サービスからのアクセスを許可する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |ストレージ アカウントとやり取りする一部の Microsoft サービスは、ネットワーク ルールでアクセスを許可できないネットワークから実行されます。 この種のサービスを意図したとおりに動作させるには、一連の信頼できる Microsoft サービスがネットワーク ルールをバイパスするのを許可します。 そうすると、これらのサービスはストレージ アカウントにアクセスするために強力な認証を使用します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

## <a name="database-services"></a>データベース サービス

### <a name="ensure-that-auditing-is-set-to-on"></a>[監査] が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server の監査を有効にする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |サーバー上のすべてのデータベースについてデータベースのアクティビティを追跡して、監査ログに保存するには、お使いの SQL サーバーに対する監査を有効にする必要があります。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>SQL Server の "監査" ポリシーの "AuditActionGroups" が適切に設定されていることを確認する

**[ID]** :CIS Azure 4.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[重要なアクティビティをキャプチャするには、SQL 監査設定に Action-Groups を構成しなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7ff426e2-515f-405a-91c8-4f2333442eb5) |完全な監査ログを実行するには、AuditActionsAndGroups プロパティに少なくとも SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP、FAILED_DATABASE_AUTHENTICATION_GROUP、BATCH_COMPLETED_GROUP を含める必要があります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_ActionsAndGroups_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>"監査" 保持期間が "90 日を超える" ことを確認する

**[ID]** :CIS Azure 4.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server では、監査データを 90 日以上保持する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |インシデント調査を目的として、SQL サーバーの監査データのデータ保持期間を少なくとも 90 日に設定することをお勧めします。 運用しているリージョンで必要な保持期間の規則を満たしていることを確認します。 これは、規制標準に準拠するために必要になる場合があります。 |AuditIfNotExists、Disabled |[2.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>SQL Server の "Advanced Data Security" が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Managed Instance で Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Advanced Data Security を使用していない各 SQL Managed Instance を監査します。 |AuditIfNotExists、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[SQL Server で Advanced Data Security を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Advanced Data Security を使用していない SQL サーバーの監査 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Azure Active Directory 管理者が構成されていることを確認する

**[ID]** :CIS Azure 4.8 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL Server に対して Azure Active Directory 管理者をプロビジョニングする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Azure AD 認証を可能にする SQL サーバー向けの Azure Active Directory 管理者のプロビジョニングを監査します。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>SQL Database の [データ暗号化] が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.9 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Transparent Data Encryption を SQL データベース上で有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |保存データを保護し、コンプライアンス要件を満たすには、Transparent Data Encryption を有効にする必要があります |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>SQL Server の TDE 保護機能が BYOK (自分のキーの使用) で暗号化されていることを確認する

**[ID]** :CIS Azure 4.10 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[SQL マネージド インスタンスでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[SQL サーバーでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |独自キーを使用して Transparent Data Encryption (TDE) を実装すると、TDE 保護機能の透明性および制御が強化され、HSM で保護された外部サービスによるセキュリティが向上し、職務の分離が促進されます。 この推奨事項は、関連するコンプライアンス要件を持つ組織に適用されます。 |AuditIfNotExists、Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>MySQL データベース サーバーで [SSL 接続を強制する] が [有効] に設定されていることを確認する

**[ID]** :CIS Azure 4.11 **所有権** :Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Azure Database for MySQL は、Secure Sockets Layer (SSL) を使用した、クライアント アプリケーションへの Azure Database for MySQL サーバーへの接続をサポートします。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>サーバー パラメーターである "log_checkpoints" が PostgreSQL データベース サーバー向けに [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.12 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーではログ チェックポイントが有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |このポリシーは、log_checkpoints 設定が有効になっていない環境内のすべての PostgreSQL データベースを監査するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>PostgreSQL データベース サーバーで [SSL 接続を強制する] が [有効] に設定されていることを確認する

**[ID]** :CIS Azure 4.13 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは [SSL 接続を強制する] が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Azure Database for PostgreSQL では、Secure Sockets Layer (SSL) を使用する Azure Database for PostgreSQL サーバーからクライアント アプリケーションへの接続がサポートされています。 お使いのデータベース サーバーとクライアント アプリケーション間に SSL 接続を強制すると、サーバーとお使いのアプリケーション間のデータ ストリームを暗号化することにより、中間者 (man in the middle) 攻撃から保護するのに役立ちます。 この構成では、データベース サーバーへのアクセスに対して SSL が常に有効にされます。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>サーバー パラメーターである "log_connections" が PostgreSQL データベース サーバー向けに [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.14 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーではログ接続が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |このポリシーは、log_connections 設定が有効になっていない環境内のすべての PostgreSQL データベースを監査するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>サーバー パラメーターである "log_disconnections" が PostgreSQL データベース サーバー向けに [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.15 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーの切断はログに記録する必要がある。](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |このポリシーは、log_disconnections が有効になっていない環境内のすべての PostgreSQL データベースを監査するのに役立ちます。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>サーバー パラメーターである "connection_throttling" が PostgreSQL データベース サーバー向けに [オン] に設定されていることを確認する

**[ID]** :CIS Azure 4.17 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[PostgreSQL データベース サーバーでは接続の調整が有効でなければならない](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |このポリシーは、接続の帯域幅調整が有効になっていない環境内のすべての PostgreSQL データベースを監査するのに役立ちます。 この設定により、無効なパスワード ログイン エラーが多すぎる場合に、IP ごとに一時的な接続の帯域幅調整を行うことができます。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

## <a name="logging-and-monitoring"></a>ログ記録と監視

### <a name="ensure-that-a-log-profile-exists"></a>ログ プロファイルが存在することを確認する

**[ID]** :CIS Azure 5.1.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure サブスクリプションにはアクティビティ ログのログ プロファイルが必要](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7796937f-307b-4598-941c-67d3a05ebfe7) |このポリシーでは、アクティビティ ログのエクスポートがログ プロファイルで有効になっているかどうかを確認します。 また、ログをストレージ アカウントまたはイベント ハブにエクスポートするためのログ プロファイルが作成されていないかどうかを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/Logprofile_activityLogs_Audit.json) |

### <a name="ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>アクティビティ ログのリテンション期間が 365 日以上に設定されていることを確認する

**[ID]** :CIS Azure 5.1.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アクティビティ ログを 1 年以上保持する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb02aacc0-b073-424e-8298-42b22829ee0a) |このポリシーは、リテンション期間が 365 日間または無期限 (リテンション日数が 0) に設定されていない場合にアクティビティ ログを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLogRetention_365orGreater.json) |

### <a name="ensure-audit-profile-captures-all-the-activities"></a>監査プロファイルによってすべてのアクティビティがキャプチャされることを確認する

**[ID]** :CIS Azure 5.1.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ログ プロファイルで、"書き込み"、"削除"、"アクション" の各カテゴリのログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1a4e592a-6a6e-44a5-9814-e36264ca96e7) |このポリシーでは、ログ プロファイルで "書き込み"、"削除"、"アクション" の各カテゴリのログが確実に収集されるようにします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllCategories.json) |

### <a name="ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>ログ プロファイルによってグローバルを含むすべてのリージョンのアクティビティ ログがキャプチャされることを確認する

**[ID]** :CIS Azure 5.1.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Monitor ですべてのリージョンからアクティビティ ログを収集する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F41388f1c-2db0-4c25-95b2-35d7f5ccbfa9) |このポリシーでは、グローバルを含め、Azure がサポートするすべてのリージョンからアクティビティをエクスポートしない Azure Monitor ログ プロファイルを監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_CaptureAllRegions.json) |

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>アクティビティ ログを格納するストレージ コンテナーにパブリックにアクセスできないことを確認する

**[ID]** :CIS Azure 5.1.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ストレージ アカウントのパブリック アクセスを禁止する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合があります。 好ましくない匿名アクセスによるデータ侵害を防ぐために、Microsoft では、シナリオで必要でない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 |audit、deny、disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>アクティビティ ログがあるコンテナーを含むストレージ アカウントが BYOK (独自のキーの使用) を使用して暗号化されていることを確認する

**[ID]** :CIS Azure 5.1.6 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アクティビティ ログがあるコンテナーを含むストレージ アカウントは、BYOK を使用して暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |このポリシーは、アクティビティ ログがあるコンテナーを含むストレージ アカウントが BYOK を使用して暗号化されているかどうかを監査します。 このポリシーは、ストレージ アカウントが仕様でアクティビティ ログと同じサブスクリプションに設定されている場合にのみ有効です。 保存時の Azure Storage 暗号化の詳細については、こちら ([https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok)) をご覧ください。  |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Azure KeyVault のログ記録が [有効] になっていることを確認する

**[ID]** :CIS Azure 5.1.7 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault マネージド HSM のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa2a5b911-5617-447e-a49e-59dbe0e0434b) |セキュリティ インシデントが発生したときやネットワークが侵害されたときに調査目的でアクティビティ証跡を再作成する場合は、マネージド HSM のリソース ログを有効にして監査を行います。 [https://docs.microsoft.com/azure/key-vault/managed-hsm/logging](https://docs.microsoft.com/azure/key-vault/managed-hsm/logging) の手順に従ってください。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_AuditDiagnosticLog_Audit.json) |
|[Key Vault のリソース ログを有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |リソース ログが有効になっていることを監査します。 これにより、セキュリティ インシデントが発生した場合やお使いのネットワークが侵害された場合に、調査目的で使用するアクティビティ証跡を再作成できます |AuditIfNotExists、Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>"ポリシー割り当ての作成" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定のポリシー操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |このポリシーは、アクティビティ ログ アラートが構成されていない特定のポリシー操作を監査します。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>"ネットワーク セキュリティ グループの作成または更新" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>"ネットワーク セキュリティ グループの削除" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>"ネットワーク セキュリティ グループ規則の作成または更新" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>"ネットワーク セキュリティ グループ規則の削除" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>"セキュリティ ソリューションの作成または更新" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.6 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |このポリシーは、アクティビティ ログ アラートが構成されていない特定のセキュリティ操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>"セキュリティ ソリューションの削除" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.7 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |このポリシーは、アクティビティ ログ アラートが構成されていない特定のセキュリティ操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>"SQL Server ファイアウォール規則の作成、更新、削除" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.8 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定の管理操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |このポリシーは、アクティビティ ログ アラートが構成されていない特定の管理操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-update-security-policy"></a>"セキュリティ ポリシーの更新" のアクティビティ ログ アラートが存在することを確認する

**[ID]** :CIS Azure 5.2.9 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[特定のセキュリティ操作のアクティビティ ログ アラートが存在する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |このポリシーは、アクティビティ ログ アラートが構成されていない特定のセキュリティ操作を監査します。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

## <a name="networking"></a>ネットワーク

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>インターネットからの RDP アクセスが制限されていることを確認する

**[ID]** :CIS Azure 6.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インターネットからの RDP アクセスをブロックする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |このポリシーは、インターネットからの RDP アクセスを許可するすべてのネットワーク セキュリティ規則を監査します |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>インターネットからの SSH アクセスが制限されていることを確認する

**[ID]** :CIS Azure 6.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インターネットからの SSH アクセスをブロックする必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |このポリシーは、インターネットからの SSH アクセスを許可するすべてのネットワーク セキュリティ規則を監査します |Audit、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Network Watcher が [有効] になっていることを確認する

**[ID]** :CIS Azure 6.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Network Watcher を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 シナリオ レベルの監視により、エンド ツー エンドのネットワーク レベル ビューで問題を診断できるようになります。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Virtual Machines

### <a name="ensure-that-os-disk-are-encrypted"></a>"OS ディスク" が暗号化されていることを確認する

**[ID]** :CIS Azure 7.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンでディスク暗号化を適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |ディスク暗号化が有効になっていない仮想マシンが、推奨設定として Azure Security Center によって監視されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-data-disks-are-encrypted"></a>"データ ディスク" が暗号化されていることを確認する

**[ID]** :CIS Azure 7.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[仮想マシンでディスク暗号化を適用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |ディスク暗号化が有効になっていない仮想マシンが、推奨設定として Azure Security Center によって監視されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted"></a>"アタッチされていないディスク" が暗号化されていることを確認する

**[ID]** :CIS Azure 7.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[アタッチされていないディスクを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |このポリシーは、アタッチされていないディスクで、暗号化が有効でないものすべてを監査します。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>承認済みの拡張機能のみがインストールされていることを確認する

**[ID]** :CIS Azure 7.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[インストールする必要があるのは、許可されている VM 拡張機能のみ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |このポリシーは、承認されていない仮想マシン拡張機能を制御します。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>すべての仮想マシンの最新の OS 修正プログラムが適用されていることを確認する

**[ID]** :CIS Azure 7.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[システム更新プログラムをマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |使用中のサーバーにおけるセキュリティ関連のシステム更新プログラムの不足が、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>すべての仮想マシンの Endpoint Protection がインストールされていることを確認する

**[ID]** :CIS Azure 7.6 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Endpoint Protection の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |Endpoint Protection エージェントがインストールされていないサーバーが、推奨設定として Azure Security Center で監視されます |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>その他のセキュリティの考慮事項

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>すべてのキーに有効期限が設定されていることを確認する

**[ID]** :CIS Azure 8.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault キーには有効期限が必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |暗号化キーには有効期限を定義する必要があり、永続的なものにしてはいけません。 無期限に有効なキーを使用すると、攻撃者がキーを侵害できる時間がそれだけ長くなります。 セキュリティ プラクティスとして、暗号化キーには有効期限を設定することをお勧めします。 |Audit、Deny、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>すべてのシークレットに有効期限が設定されていることを確認する

**[ID]** :CIS Azure 8.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Key Vault シークレットには有効期限が必要である](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |シークレットには有効期限を定義する必要があり、永続的なものにしてはいけません。 シークレットを無期限に有効にすると、潜在的な攻撃者にそれを侵害する時間を多く与えることになります。 セキュリティ プラクティスとして、シークレットには有効期限を設定することをお勧めします。 |Audit、Deny、Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>キー コンテナーが回復可能であることを確認する

**[ID]** :CIS Azure 8.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Key Vault Managed HSM で消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc39ba22d-4428-4149-b981-70acb31fc383) |Azure Key Vault Managed HSM が悪意により削除されると、完全にデータが失われる可能性があります。 組織内の悪意のある内部関係者が、Azure Key Vault Managed HSM の削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除された Azure Key Vault Managed HSM に必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中に Azure Key Vault Managed HSM を消去することはできなくなります。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/ManagedHsm_Recoverable_Audit.json) |
|[キー コンテナーで消去保護が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |悪意でキー コンテナーが削除されると、データが完全に失われる可能性があります。 組織内の悪意のある内部関係者が、キー コンテナーの削除と消去を実行できるおそれがあります。 消去保護では、論理的に削除されたキー コンテナーに必須の保有期間を適用することによって、内部関係者の攻撃から組織を保護します。 組織や Microsoft の内部にいるどのユーザーも、論理的な削除の保有期間中にキー コンテナーを消去することはできなくなります。 |Audit、Deny、Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Azure Kubernetes サービス内でロールベースのアクセス制御 (RBAC) を有効にする

**[ID]** :CIS Azure 8.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Kubernetes Services でロールベースのアクセス制御 (RBAC) を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |ユーザーが実行できるアクションに関して詳細なフィルター処理を行うために、ロールベースのアクセス制御 (RBAC) を使用して、Kubernetes Service クラスター内のアクセス許可を管理し、関連する承認ポリシーを構成します。 |Audit、Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="appservice"></a>AppService

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Azure App Service に App Service 認証が設定されていることを確認する

**[ID]** :CIS Azure 9.1 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリで認証が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |Azure App Service 認証は、匿名の HTTP 要求が API アプリに到達するのを防いだり、トークンを保持するものを API アプリへの到達前に認証したりできる機能です |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[関数アプリで認証が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |Azure App Service 認証は、匿名の HTTP 要求が関数アプリに到達するのを防いだり、トークンを保持するものを関数アプリへの到達前に認証したりできる機能です |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[Web アプリで認証が有効になっている必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |Azure App Service 認証は、匿名の HTTP 要求が Web アプリに到達するのを防いだり、トークンを保持するものを Web アプリへの到達前に認証したりできる機能です |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Web アプリがすべての HTTP トラフィックを Azure App Service の HTTPS にリダイレクトすることを確認する

**[ID]** :CIS Azure 9.2 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Web アプリケーションには HTTPS を介してのみアクセスできるようにする](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |HTTPS を使用すると、サーバー/サービスの認証が確実に実行され、転送中のデータがネットワーク層の傍受攻撃から保護されるようになります。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Web アプリが最新バージョンの TLS 暗号化を使用していることを確認する

**[ID]** :CIS Azure 9.3 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[関数アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Web アプリでは最新の TLS バージョンを使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |最新の TLS バージョンにアップグレードします |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する

**[ID]** :CIS Azure 9.4 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Web アプリで "クライアント証明書 (着信クライアント証明書)" が [オン] に設定されていることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみがアプリにアクセスできます。 |Audit、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[関数アプリで "クライアント証明書 (着信クライアント証明書)" を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |クライアント証明書を使用すると、アプリは受信要求に対して証明書を要求できます。 有効な証明書を持つクライアントのみが、そのアプリにアクセスできるようになります。 |Audit、Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>App Service で [Azure Active Directory に登録する] が有効になっていることを確認する

**[ID]** :CIS Azure 9.5 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[関数アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Web アプリではマネージド ID を使用する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |マネージド ID を使用して認証セキュリティを強化します |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Web アプリの実行に使用された "PHP のバージョン" が最新であることを確認する

**[ID]** :CIS Azure 9.7 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリの一部として使用される "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Web アプリの一部として使用された "PHP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |セキュリティ上の欠陥のため、または追加機能を組み込むために、PHP ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の PHP バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Web アプリの実行に使用された "Python のバージョン" が最新であることを確認する

**[ID]** :CIS Azure 9.8 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリの一部として使用される "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[関数アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Web アプリの一部として使用された "Python のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Python ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Web アプリの実行に使用された "Java のバージョン" が最新であることを確認する

**[ID]** :CIS Azure 9.9 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリの一部として使用される "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java の新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Python バージョンを API アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[関数アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用できるように、最新の Java バージョンを関数アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Web アプリの一部として使用された "Java のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |セキュリティ上の欠陥のため、または追加機能を組み込むために、Java ソフトウェアの新しいバージョンが定期的にリリースされます。 最新バージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の Java バージョンを Web アプリに使用することをお勧めします。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する

**[ID]** :CIS Azure 9.10 **所有権**: Customer

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[API アプリの実行に使用される "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[関数アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Web アプリの実行に使用された "HTTP のバージョン" が最新であることを確認する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |セキュリティ上の欠陥のため、または追加機能を組み込むために、HTTP の新しいバージョンが定期的にリリースされます。 新しいバージョンのセキュリティ修正プログラム (存在する場合) や新機能を利用するために、最新の HTTP バージョンを Web アプリに使用します。 現在、このポリシーは Linux Web アプリにのみ適用されます。 |AuditIfNotExists、Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

> [!NOTE]
> 特定の Azure Policy 定義を利用できるかどうかは、Azure Government とその他の National Clouds で異なる場合があります。

## <a name="next-steps"></a>次のステップ

Azure Policy に関するその他の記事:

- [規制コンプライアンス](../concepts/regulatory-compliance.md)の概要。
- [イニシアチブ定義の構造](../concepts/initiative-definition-structure.md)を参照してください。
- [Azure Policy のサンプル](./index.md)の他の例を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
