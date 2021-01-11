---
title: Azure セキュリティ ベースライン
description: Azure SQL Database と Azure SQL Managed Instance 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 846d66a1cf1bb1d97f7ab9d7dfd7bbcf43d3f8d6
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231029"
---
# <a name="azure-security-baseline-for-azure-sql-database--sql-managed-instance"></a>Azure SQL Database と SQL Managed Instance 用の Azure セキュリティ ベースライン
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: 仮想ネットワーク内のプライベート エンドポイントを経由した Azure PaaS サービス (SQL Database など) や Azure でホストされている顧客/パートナー サービスへのアクセスを許可するために Azure Private Link を有効にすることができます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。

トラフィックが Azure SQL Database に到達できるようにするには、SQL サービス タグを使用して、ネットワーク セキュリティ グループ経由の送信トラフィックを許可します。

仮想ネットワーク規則を使用すると、Azure SQL Database で仮想ネットワーク内の選択されたサブネットから送信された通信のみを受け入れるようにすることができます。

Azure SQL Database 用の Private Link を設定する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

サーバーの仮想ネットワーク サービス エンドポイントとルールを使用する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:お使いの Azure SQL Database のデプロイ先のサブネットには、Azure Security Center およびネットワーク保護の修復に関する推奨事項を使用します。

お使いの Azure SQL Database に接続される Azure 仮想マシン (VM) の場合は、これらの VM を保護するネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Security Center によって提供されるネットワークのセキュリティの概要:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Traffic Analytics を有効にして使用する方法:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Azure Security Center によって提供されるネットワークのセキュリティの概要:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 分散型サービス拒否攻撃からの保護のために、お使いの Azure SQL Database に関連付けられている仮想ネットワークで DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

DDoS 保護を構成する方法:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center の統合された脅威インテリジェンスの概要:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: Azure SQL Database インスタンスに接続される Azure 仮想マシン (VM) の場合は、これらの VM を保護するネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。 異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher を有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Advanced Threat Protection (ATP) for Azure SQL Database を有効にします。  不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Advanced Threat Protection ではまた、アラートが Azure Security Center とも統合されます。

Advanced Threat Protection for Azure SQL Database の概要とその使用方法: https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure SQL Database 用のサービス エンドポイントを使用している場合は、Azure SQL Database のパブリック IP アドレスへの送信が必要です。ネットワーク セキュリティ グループ (NSG) は、接続を許可するために Azure SQL Database IP に対して開かれている必要があります。 これは、Azure SQL Database 用の NSG サービス タグを使用して行うことができます。

Azure SQL Database 用のサービス エンドポイントのサービス タグの概要:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

サービス タグとその使用方法の概要:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用してお使いの Azure SQL Database のネットワーク セキュリティ構成を定義して実装します。 "Microsoft.Sql" 名前空間を使用してカスタム ポリシー定義を定義するか、サーバーのネットワーク保護のために設計されたいずれかの組み込みのポリシー定義を使用できます。 サーバーに適用できる組み込みのネットワーク セキュリティ ポリシーの例を次に示します。"SQL Database では仮想ネットワーク サービス エンドポイントを使用する必要がある"。

Azure Resource Management テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化するには、Azure Blueprints を使用します。 ブループリントを新しいサブスクリプションと環境に容易に適用し、バージョン管理によって制御と管理を微調整します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、お使いのサーバーに関連したネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は、Azure リソースのタイム ソースを保持しています。 コンピューティング デプロイの時刻同期を更新できます。

Azure コンピューティング リソースの時刻同期を構成する方法:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: データベース イベントを追跡し、それを Azure Storage アカウント、Log Analytics ワークスペース、または Event Hubs の監査ログに書き込むには、Azure SQL Database の監査を有効にします。

さらに、Azure SQL 診断テレメトリを、複数のサブスクリプションにわたって Azure SQL Database と Azure SQL Managed Instance のパフォーマンスを大規模に監視するクラウド ソリューションである Azure SQL Analytics にストリーミングできます。 これは、Azure SQL Database のパフォーマンス メトリックの収集および視覚化に役立ち、パフォーマンスのトラブルシューティング用インテリジェンスが組み込まれています。

Azure SQL Database の監査を設定する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Azure Monitor でプラットフォーム ログとメトリックを収集する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

診断を Azure SQL Analytics にストリーミングする方法:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: お使いのサーバーの監査を有効にし、監査ログの保存場所 (Azure Storage、Log Analytics、またはイベント ハブ) を選択します。

Azure SQL Database の監査を有効にする方法:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。このベンチマークは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure SQL Database ログを Log Analytics ワークスペースに格納している場合は、組織のコンプライアンス規則に従ってログの保有期間を設定します。

ログの保有期間のパラメーターを設定する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。 Azure SQL Database インスタンスに関連した異常なアクティビティに関するアラートを生成するには、Azure Security Center の Advanced Threat Protection を使用します。 あるいは、Azure SQL Database インスタンスに関連したメトリック値または Azure アクティビティ ログ エントリに基づいてアラートを構成します。

Advanced Threat Protection および Azure SQL Database に関するアラートの概要:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Azure SQL Database に関するカスタム アラートを構成する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 異常なアクティビティを監視し、それに関するアラートを生成するには、Azure Security Center の Advanced Threat Protection for Azure SQL Database を使用します。 SQL データベース用に Advanced Data Security を有効にします。 Advanced Data Security には、機密データを検出して分類したり、データベースの潜在的な脆弱性を明らかにして軽減したり、データベースへの脅威を示す可能性のある異常なアクティビティを検出したりするための機能が含まれています。

Advanced Threat Protection および Azure SQL Database に関するアラートの概要:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Azure SQL Database 向け Advanced Data Security を有効にする方法:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Azure Security Center でアラートを管理する方法:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:適用できません。Azure SQL Database の場合、マルウェア対策ソリューションは、基になるプラットフォーム上で Microsoft によって管理されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。DNS ログは Azure SQL Database には適用できません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。コマンド ライン監査は Azure SQL Database には適用できません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AAD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 アドホック クエリを実行して、管理グループのメンバーであるアカウントを検出するには、AAD PowerShell モジュールを使用します。

PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory には、既定のパスワードの概念がありません。 Azure SQL Database インスタンスをプロビジョニングしている場合は、Azure Active Directory との認証の統合を選択することをお勧めします。

Azure SQL による Azure Active Directory 認証を構成して管理する方法:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関するポリシーと手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

Azure Security Center ID とアクセスの概要:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: 適用できません。Azure SQL Database と統合するように Azure Active Directory 認証を構成できますが、シングル サインオンはサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (AAD) Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication MFA が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

特権アクセス ワークステーションについて:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。

データベースへのアクセスや悪用のための異常で、かつ有害なおそれのある試みを示す異常なアクティビティを検出するには、Advanced Threat Protection for Azure SQL Database を使用します。

危険なアクティビティのフラグが設定された Azure AD ユーザーを特定する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Advanced Threat Protection および潜在的なアラートを確認する:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: ポータルや Azure Resource Management での IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可するには、条件付きアクセスのネームド ロケーションを使用します。

Azure でネームド ロケーションを構成する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: お使いのサーバーの Azure Active Directory (AAD) 管理者を作成します。

Azure SQL による Azure Active Directory 認証を構成して管理する方法:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

AAD インスタンスを作成して構成する方法:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AAD) では、古いアカウントの検出に役立つログが提供されます。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューして、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Azure ID アクセス レビューの使用方法:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure SQL による Azure Active Directory (AAD) 認証を構成し、Azure Active Directory ユーザー アカウントの診断設定を作成して、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内に目的のアラートを構成します。

Azure SQL による Azure Active Directory 認証を構成して管理する方法:

https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure

Azure Activity Logs を Azure Monitor に統合する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (AAD) Identity Protection とリスク検出を使用します。 また、さらに調査するためにデータを Azure Sentinel に取り込むこともできます。

Azure AD の危険なサインインを表示する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID Protection のリスク ポリシーを構成して有効にする方法:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、Azure カスタマー ロックボックスに、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。

カスタマー ロックボックスについて:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは Vnet/サブネットで分離し、適切にタグ付けして、NSG または Azure Firewall 内でセキュリティで保護する必要があります。 機密データを格納または処理するリソースは分離されている必要があります。 Private Link を使用します。VNet 内に Azure SQL Database をデプロイし、プライベート エンドポイントを使用して非公開で接続します。

追加の Azure サブスクリプションを作成する方法:

https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:

https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure SQL Database 用の Private Link を設定する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報を格納または処理する Azure SQL Database のデータベースでは、タグを使用してデータベースと関連リソースを機密とマークします。 機密情報の窃盗を防止するために、Azure SQL Database インスタンスで Private Link とネットワーク セキュリティ グループのサービス タグを構成します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護された状態に維持されるように、Microsoft は一連の堅牢なデータ保護制御および機能を実装し、それを保持しています。

Azure SQL Database インスタンスでデータの窃盗を防止するように Private Link と NSG を構成する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure SQL Database では、トランスポート層セキュリティを使用して移動中のデータを暗号化することによって、データをセキュリティで保護します。 SQL Database では、すべての接続に対して常に暗号化 (SSL/TLS) が適用されます。 これにより、接続文字列内の Encrypt または TrustServerCertificate の設定には関係なく、すべてのデータがクライアントとサーバーの間の "転送中" に確実に暗号化されます。

Azure SQL の転送中の暗号化の概要:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure SQL Database のデータの検出と分類機能を使用します。 データの検出と分類では、データベース内の機密データを検出、分類、ラベル付け、保護するために Azure SQL Database に組み込まれた高度な機能が提供されます。

Azure SQL Database に対してデータの検出と分類を使用する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure SQL Database インスタンスへのアクセスを認証および制御するために Azure Active Directory (AAD) を使用します。

認証のために Azure SQL Database と Azure Active Directory を統合する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

Azure SQL Database でアクセスを制御する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: Microsoft は、Azure SQL Database 用の基になるインフラストラクチャを管理しており、顧客データの損失または漏洩を防止するための厳密な制御を実装してきました。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Transparent Data Encryption (TDE) を使用すると、保存データを暗号化することで、悪意のあるオフライン アクティビティの脅威から Azure SQL Database、Azure SQL Managed Instance、Azure Data Warehouse を保護できます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 既定では、SQL Database と SQL Managed Instance に新たにデプロイされるすべてのデータベースに対して TDE が有効になります。 TDE 暗号化キーは、Microsoft または顧客のどちらでも管理できます。

Transparent Data Encryption を管理して独自の暗号化キーを使用する方法:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure SQL Database の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

Azure アクティビティ ログ イベントのアラートを作成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure SQL Database 向け Advanced Data Security を有効にし、お使いのサーバーに対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。

Azure SQL Database で脆弱性評価を実行する方法

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

Advanced Data Security を有効にする方法:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Azure Security Center の脆弱性評価の推奨事項を実装する方法:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。このベンチマークは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Azure SQL Database インスタンスに対する定期的な反復スキャンを有効にします。これにより、データベースで週に 1 回スキャンを自動的に実行するための脆弱性評価が構成されます。 スキャン結果の概要は、指定した電子メール アドレスに送信されます。 結果を比較して、脆弱性が修復されたことを確認します。

Azure Security Center で脆弱性評価レポートをエクスポートする方法:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

Azure Security Center のセキュリティ スコアについて:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内の (Azure SQL Database を含む) すべてのリソースをクエリして検出します。  テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC の概要: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加の Azure サブスクリプションを作成する方法:

https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:

https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**:コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアの一覧を定義します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソース上で実行されているアプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、デスクトップまたは Web アプリケーションをホストしている App Service またはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure SQL Database に関する Azure Policy または Azure Security Center の推奨事項を使用して、すべての Azure リソースのセキュリティ構成を保持します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果を理解する:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

Azure DevOps にコードを格納する方法:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.SQL" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Security Center を利用して、Azure SQL Database のベースライン スキャンを実行します。

Azure Security Center の推奨事項を修復する方法

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure SQL Database Transparent Data Encryption (TDE) の暗号化キーを格納するには、Azure Key Vault を使用します。

Azure SQL Database に格納される機密データを保護し、暗号化キーを Azure Key Vault に格納する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure サービスに Azure Active Directory (AAD) で自動的に管理される ID を提供するには、マネージド ID を使用します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、AAD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

マネージド ID を構成する方法:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内の資格情報を識別するための資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft マルウェア対策は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data lake Storage、Blob Storage、Azure SQL Database などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware の概要: https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: ビジネスをデータ損失から保護するために、Azure SQL Database ではデータベースの完全バックアップが毎週、データベースの差分バックアップが 12 時間ごとに、トランザクション ログ バックアップが 5 ～ 10 分ごとに自動的に作成されます。 バックアップは、すべてのサービス レベルで少なくとも 7 日間 RA-GRS ストレージに保存されます。 Basic を除くすべてのサービス レベルで、ポイントインタイム リストアのために、構成可能なバックアップ保有期間 (最大 35 日間) がサポートされます。

各種のコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対してさまざまな保有期間を選択できます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。

Azure SQL Database でのバックアップとビジネス継続性を理解する:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure SQL Database では、7 ～ 35 日間保持されるデータベース バックアップが自動的に作成され、データ センターが使用できない場合でもそれらが確実に保持されるように Azure の読み取りアクセス geo 冗長ストレージ (RA-GRS) が使用されます。 これらのバックアップは自動的に作成されます。 必要に応じて、Azure SQL Database に対する長期間の geo 冗長バックアップを有効にしてください。

Transparent Data Encryption にカスタマー マネージド キーを使用している場合は、キーが確実にバックアップされるようにしてください。

Azure SQL Database でのバックアップを理解する:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

Azure で Key Vault のキーをバックアップする方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Backup 内でコンテンツのデータ復元を定期的に実行できるようにします。 必要に応じて、コンテンツを分離された VLAN にテスト復元してください。 バックアップされたカスタマー マネージド キーの復元をテストします。

Azure で Key Vault のキーを復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

ポイントインタイム リストアを使用して Azure SQL Database のバックアップを復旧する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: 偶発的な削除や悪意のある削除からキーを保護するには、Azure Key Vault で論理的な削除を有効にします。

Key Vault で論理的な削除を有効にする方法:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**:インシデント処理/管理のフェーズと担当者の役割を定義している記述されたインシデント対応計画があることを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

Azure Security Center のセキュリティ アラート: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

次の NIST の出版物を参照できます。IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または承認されていないパーティによってアクセスされたことが検出された場合はユーザーに連絡するために、Microsoft によって使用されます。

Azure Security Center のセキュリティ連絡先を設定する方法:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Sentinel にストリーミングできます。

連続エクスポートを構成する方法:

https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

ワークフローの自動化と Logic Apps を構成する方法:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**:侵入テストが Microsoft のポリシーに違反しないようにするために、Microsoft の活動規則に従ってください。

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
