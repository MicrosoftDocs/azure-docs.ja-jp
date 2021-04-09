---
title: Azure Web Application Firewall の Azure セキュリティ ベースライン
description: Azure Web Application Firewall のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関するレコメンデーションを実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: ae6b4f38772cd6c6755ece78fb5c47834a616204
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211636"
---
# <a name="azure-security-baseline-for-azure-web-application-firewall"></a>Azure Web Application Firewall の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure Web Application Firewall に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Web Application Firewall に適用できる関連ガイダンスによって定義されています。 Azure Web Application Firewall に適用できない **制御** は、除外されています。 

Azure Web Application Firewall を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Web Application Firewall セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Microsoft Azure Web Application Firewall (WAF) を使用して、SQL インジェクションやクロスサイト スクリプティングなどの一般的な悪用や脆弱性から Web アプリケーションを一元的に保護します。 

- 検出モード:このモードを使用してネットワーク トラフィックを学習し、誤検知を理解して確認します。 すべての脅威アラートを監視してログに記録します。 診断と WAF ログが選択され、有効になっていることを確認します。 WAF は検出モードで動作しているときには受信要求をブロックしないことに注意してください。
- 防止モード:規則によって検出された侵入や攻撃をブロックします。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

WAF の検出モードを使用して、ネットワーク トラフィックのベースラインを作成します。 トラフィックのニーズを決定したら、防止モードで WAF を構成して、不要なトラフィックを阻止します。

WAF で保護されていない Web 対応のリソースについては、Security Center の重要度の高い推奨事項に従ってください。  

- [Web アプリケーション ファイアウォールの CRS 規則グループと規則](ag/application-gateway-crs-rulegroups-rules.md) 

- [Application Gateway での WAF のモード](ag/ag-overview.md#waf-modes)

- [Front Door での WAF のモード](afds/afds-overview.md#waf-modes)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:Azure Web Application Firewall (WAF) でカスタム規則を使用して、トラフィックを許可またはブロックします。 たとえば、ある範囲の IP アドレスから送信されるすべてのトラフィックをブロックできます。 規則によって検出された侵入や攻撃をブロックする防止モードで実行するように Azure WAF を構成します。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

- [Application Gateway での WAF のモード](ag/ag-overview.md#waf-modes)

- [Front Door での WAF のモード](afds/afds-overview.md#waf-modes)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**:Azure Web Application Firewall (WAF) は、Azure の Web アプリケーション保護のコア コンポーネントです。 Azure WAF を使用すると、OWASP 上位 10 カテゴリの既知の攻撃署名に対して事前構成されたマネージド ルールセットによって、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護できます。

Web アプリケーションの要件に合わせて Azure WAF の規則と規則グループをカスタマイズし、誤検出を排除できます。 WAF の内側にある各サイトに Azure WAF ポリシーを関連付けて、サイト固有の構成を許可することができます。 Azure WAF では、geo フィルタリング、レート制限、Azure によって管理される既定のルールセットの規則がサポートされています。 また、アプリケーションのニーズに合わせてカスタム規則を作成することもできます。 

特定の期間、検出モードでネットワーク トラフィックのベースラインを設定した後で、防止モードで実行するように Azure WAF を構成します。 Azure WAF は、規則によって検出された侵入や攻撃を防止モードでブロックします。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

- [Application Gateway での WAF のモード](ag/ag-overview.md#waf-modes)

- [Front Door での WAF のモード](afds/afds-overview.md#waf-modes)

- [Web アプリケーション ファイアウォールの CRS 規則グループと規則](ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:Azure サブスクリプション全体でリソースを作成および関連付けし、一般的なアプリケーション (Apache や IIS など) の構成ミスを検出するためのタグを使用して論理的に整理します。 

適用されたタグ メタデータに基づいて、規則と規則グループを Azure Web Application Firewall (WAF) ポリシーに適用します。

- [Application Gateway での WAF ポリシー](/cli/azure/network/application-gateway/waf-policy) 

- [Front Door での WAF ポリシー](/cli/azure/ext/front-door/network/front-door/waf-policy)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure Application Gateway サブネット内の Azure Web Application Firewall (WAF) に関連付けられているネットワーク セキュリティ グループのほか、ネットワーク セキュリティおよびトラフィック フローに関連したその他のリソースにタグを使用します。 個々のネットワーク セキュリティ グループ規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可するすべての規則のビジネス ニーズや期間などを指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を選択して、タグに基づいたリソースの検索やアクションを実行します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Web Application Firewall (WAF) デプロイに関連するネットワーク設定とリソースの変更を検出します。 重要なネットワーク設定またはリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳しくは、「[Azure Security ベンチマーク:ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Azure Web Application Firewall (WAF) のネットワーク規則を作成して、適切なポートとプロトコル (UDP のポート 123 など) で NTP サーバーにアクセスできるようにします。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Sentinel、サードパーティ SIEM などの中央のセキュリティ ログ管理ソリューションに送信されるように、Azure Web Application Firewall (WAF) のログを構成します。 これらのログには、Azure アクティビティ、診断、およびリアルタイムの WAF ログが含まれます。これらのログは、Azure Monitor、Excel、Power BI などのさまざまなツールで表示できます。 Azure Web Application Firewall のログでは、Azure WAF で評価、照合、ブロックされているデータについての分析情報が提供されます。

Azure Sentinel には、Azure WAF のセキュリティ イベントの概要を提供する組み込みの Azure WAF ブックがあります。 このブックにはイベントや一致したルール、ブロックされたルールなど、ファイアウォールのログに記録されるあらゆる情報が含まれます。 このテレメトリを使用して、Azure Sentinel によって収集された WAF イベントに基づいて、プレイブックの自動化を開始し、通知または修復アクションを実行できます。

- [アクティビティ ログを表示する](../azure-resource-manager/management/view-activity-logs.md)

- [Application Gateway の診断ログ](../application-gateway/application-gateway-diagnostics.md)

- [Microsoft Web アプリケーション ファイアウォールから Azure Sentinel にデータを接続する](../sentinel/connect-azure-waf.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:監査ログ、セキュリティ ログ、診断ログにアクセスするために、Azure Web Application Firewall (WAF) のリソースのログ記録を有効にします。 Azure Web Application Firewall は、構成された診断ログで、検出された各脅威に関する詳細なレポートを提供します。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

- [ログ記録の概要](ag/ag-overview.md#logging)

- [Azure Monitor ログ クエリの概要](../azure-monitor/logs/log-query-overview.md)

- [Azure プラットフォーム ログの概要](../azure-monitor/essentials/platform-logs-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Web Application Firewall (WAF) のログをカスタム ストレージ アカウントに送信し、保持ポリシーを定義します。 Azure Monitor を使用して、組織のコンプライアンス要件に基づいて Log Analytics ワークスペースの保持期間を設定します。
- [ストレージ アカウントの監視の設定](../storage/common/manage-storage-analytics-logs.md#configure-logging)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Web Application Firewall (WAF) は、検出された各脅威に関する詳細なレポートを提供します。 ログ記録は Azure Diagnostics ログに統合されています。Azure Diagnostics ログは、監査やトラブルシューティングにとって重要な操作とエラーに関する豊富な情報を提供します。 

Azure WAF インスタンスは Security Center と統合され、レポートのためにアラートと正常性の情報を送信します。 Security Center の推奨事項を使用して、保護されていない Web アプリケーションを検出し、脆弱なリソースを保護します。 

Azure Sentinel には、WAF のセキュリティ イベントの概要を提供する組み込みの WAF ファイアウォール イベント ブックがあります。 これにはイベントや一致したルール、ブロックされたルールなど、ファイアウォールのログに記録されるあらゆる情報が含まれます。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/index.yml) 

- [Azure Application Gateway の診断設定を有効にする方法](../application-gateway/application-gateway-diagnostics.md)

- [Azure Front Door でのメトリックとログの監視](../frontdoor/front-door-diagnostics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure アクティビティ ログの診断設定と Azure WAF の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。 WAF メトリックに基づいて異常なアクティビティについてのアラートを作成します。 たとえば、ブロックされた要求の数が 'X' を超えた場合に 'Y' を実行します。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure 内でアラートを作成する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 

Azure WAF は、一般的な悪用と脆弱性から Web アプリケーションを一元的に保護します。アプリをセキュリティで保護するために、受信 Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、DDoS 攻撃などの攻撃をブロックします。

- [Azure WAF をデプロイする方法](ag/create-waf-policy-ag.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure Active Directory (Azure AD) には、クエリ可能であり、明示的に割り当てる必要がある組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:WAF 内で使用できるローカル管理者の割り当てはありません。 ただし、環境に Azure Active Directory (Azure AD) の管理者ロールが存在する可能性があります。これにより、Azure WAF リソースを管理することができます。
Azure Web Application Firewall (WAF) インスタンスにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成することをお勧めします。 Security Center の ID とアクセス管理の機能を使用して、管理者アカウントの数を監視します。

- [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

- [Azure Database for PostgreSQL で管理者ユーザーを作成する方法について](../postgresql/howto-create-users.md#the-server-admin-account)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) を有効にし、Security Center の ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure Web Application Firewall (WAF) と関連リソースを構成します。 

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Security Center を使用して ID とアクセスのアクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセス ポリシーの場所の条件を構成し、ネームド ロケーションを管理します。 

ネームド ロケーションを使用して、IP アドレス範囲または国や地域の論理グループを作成します。 機密性の高いリソース (Azure Key Vault のシークレットなど) へのアクセスを、構成したネームド ロケーションに制限します。

- [Azure Active Directory 条件付きアクセスの場所の条件の概要](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD では、データの保存時と転送時に強力な暗号化を使用してデータが保護され、さらに、ユーザーの資格情報がソルト化およびハッシュされ、安全に保存されます。
- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 アクティブなユーザーのみがアクセスを継続できるように、定期的にユーザー アクセスを確認します。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、リスク イベントのログ ソースを、Azure Sentinel などの任意の SIEM または監視ツールと統合します。

このプロセスを効率化するには、Azure Active Directory (Azure AD) ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で目的のアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:Azure Active Directory (Azure AD) のリスクおよび Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 さらに詳しく調査するために、Azure Sentinel にデータを取り込みます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:機密情報を格納または処理する Azure Web Application Firewall (WAF) および関連リソースを追跡しやすくするには、タグを使用します。
- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:環境の種類 (例: 開発環境、テスト環境、運用環境) やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 

Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御します。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure Web Application Firewall (WAF) インスタンスと関連リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。

該当する場合、保存時と転送時の暗号化に関する Security Center の推奨事項に従います。

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御します。
- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Web Application Firewall (WAF) と関連リソースを含むすべての Azure リソースに対して、保存時の暗号化を使用します。 Microsoft では、Azure に暗号化キーの管理を許可することをお勧めしていますが、一部のインスタンスでユーザーが自身のキーを管理するという選択肢もあります。

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

- [ユーザーが管理する暗号化キーを構成する方法](../storage/common/customer-managed-keys-configure-key-vault.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:特定の期間、検出モードでネットワーク トラフィックのベースラインを設定した後で、防止モードで実行するように Azure Web Application Firewall (WAF) を構成します。 

Azure WAF は、規則によって検出された侵入や攻撃を防止モードでブロックします。 攻撃者に "403 不正アクセス" の例外が送信され、接続が終了します。 防止モードでは、このような攻撃を WAF ログに記録します。

- [Application Gateway と Azure Security Center の統合の概要](../security-center/security-center-partner-integration.md)

- [Application Gateway での WAF のモード](ag/ag-overview.md#waf-modes)

- [Front Door での WAF のモード](afds/afds-overview.md#waf-modes)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 

テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。 従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:Azure Web Application Firewall (WAF) ポリシーにタグを使用します。 タグはリソースに関連付けられ、論理的に適用して顧客サブスクリプションでこれらのリソースへのアクセスを整理できます。 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用して、Azure WAF と関連リソースを整理し追跡します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに基づいて、構成を含む、承認されたリソースのインベントリを作成します。

Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。
Azure Resource Graph を使用して、サブスクリプション内の Azure Web Application Firewall (WAF) リソースのクエリまたは検出を行います。 環境に存在するすべての Azure WAF と関連リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure Policy で未承認の Azure WAF リソースを監視および削除して、Azure WAF のデプロイを拒否したり、特定の種類の WAF (例: Azure WAF v1 または V2) を拒否したりします。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: Azure Policy を使用して、環境内でプロビジョニングできるサービスを制限します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:Azure Web Application Firewall (WAF) は、ネットワーク、リソース グループ、またはサブスクリプションを介してさまざまな環境に関連付けて、リスクの高いアプリケーションを分離することができます。

- [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)

- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)

- [サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Web Application Firewall (WAF) のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。
Azure Application Gateway、仮想ネットワーク、ネットワーク セキュリティ グループのネットワーク構成を監査または適用し、組み込みポリシー定義を使用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure Web Application Firewall (WAF) および関連リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] の効果を使用します。 

組織に必要な Azure WAF と関連リソースのセキュリティ構成を維持するには、Azure Resource Manager テンプレートを使用します。

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:Azure DevOps を使用して、カスタム Azure ポリシーや Azure Resource Manager テンプレートなどのコードを安全に格納し、管理します。 

Azure Active Directory (Azure AD) (Azure DevOps と統合されている場合)、または Active Directory (Team Foundation Server (TFS) と統合されている場合) で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与するか、それらのアクセス許可を拒否します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy のドキュメント](../governance/policy/index.yml)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 

Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] の効果を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy のドキュメント](../governance/policy/index.yml)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure Key Vault を使用して、証明書を安全に格納します。 Azure Key Vault はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 

Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 

- [Azure PowerShell を使用して、Key Vault 証明書によって SSL 終端を構成する方法](../application-gateway/configure-keyvault-ps.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:また、コード内の資格情報を特定する資格情報スキャナーを実装すると、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。
- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Key Vault に対して論理的な削除が有効になっていることを確認します。 論理的な削除では、削除されたキーコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。

- [Azure Key Vault の論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。
サブスクリプション (運用、非運用など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。
- [NIST の出版物『IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド』をご覧ください](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。
- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 組織の要件に応じて、Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングします。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Security Center のワークフローの自動化機能を使用します。
- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する