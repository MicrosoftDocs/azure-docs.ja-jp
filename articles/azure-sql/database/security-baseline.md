---
title: Azure SQL Database 用の Azure セキュリティ ベースライン
description: Azure SQL Database セキュリティ ベースラインは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースを提供します。
author: msmbaldwin
ms.service: sql-database
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 87072ecc4bff054d64c3d8576f821e725959ea7e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657793"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure SQL Database 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../../security/benchmarks/overview.md) のガイダンスが Azure SQL Database に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、Azure セキュリティ ベンチマークおよび Azure SQL Database に適用できる関連ガイダンスによって定義されている **セキュリティ制御** でグループ化されています。 Azure SQL Database に適用できない **制御** は、除外されています。

Azure SQL Database を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure SQL Database セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: 仮想ネットワーク内のプライベート エンドポイントを経由した Azure PaaS サービス (SQL Database など) や Azure でホストされている顧客/パートナー サービスへのアクセスを許可するために Azure Private Link を有効にすることができます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 

トラフィックが Azure SQL Database に到達できるようにするには、SQL サービス タグを使用して、ネットワーク セキュリティ グループ経由の送信トラフィックを許可します。

仮想ネットワーク規則を使用すると、Azure SQL Database で仮想ネットワーク内の選択されたサブネットから送信された通信のみを受け入れるようにすることができます。

- [Azure SQL Database 用の Private Link を設定する方法](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

- [データベース サーバー用の仮想ネットワーク サービス エンドポイントおよび規則を使用する方法](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure SQL Database サーバーがデプロイされるサブネットには Azure Security Center およびネットワーク保護の修復に関する推奨事項を使用します。 

Azure SQL Database サーバー インスタンスに接続される Azure Virtual Machines (VM) の場合は、これらの VM を保護するネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。 

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../../security-center/security-center-network-recommendations.md)

- [Traffic Analytics を有効にして使用する方法](../../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 分散型サービス拒否攻撃からの保護のために、SQL Server インスタンスに関連付けられている仮想ネットワークで DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](/azure/virtual-network/manage-ddos-protection)

- [Azure Security Center の統合された脅威インテリジェンスについて](/azure/security-center/security-center-alerts-data-services)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure SQL Database インスタンスに接続される Azure 仮想マシン (VM) の場合は、これらの VM を保護するネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。 異常なアクティビティを調査する必要がある場合は、Network Watcher パケット キャプチャを有効にします。

- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher を有効にする方法](../../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Advanced Threat Protection (ATP) for Azure SQL Database を有効にします。  不審なデータベース アクティビティ、潜在的な脆弱性、SQL インジェクション攻撃や、異常なデータベース アクセスやクエリのパターンが見つかった場合に、ユーザーはアラートを受信します。 Advanced Threat Protection ではまた、アラートが Azure Security Center とも統合されます。 

- [Advanced Threat Protection for Azure SQL Database の概要とその使用方法](/azure/sql-database/sql-database-threat-detection-overview)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure SQL Database 用のサービス エンドポイントを使用している場合は、Azure SQL Database のパブリック IP アドレスへの送信が必要です。ネットワーク セキュリティ グループ (NSG) は、接続を許可するために Azure SQL Database IP に対して開かれている必要があります。 これは、Azure SQL Database 用の NSG サービス タグを使用して行うことができます。

- [Azure SQL Database 用のサービス エンドポイントのサービス タグの概要](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

- [サービス タグとその使用方法の概要](../../virtual-network/service-tags-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して Azure SQL Database サーバー インスタンスのネットワーク セキュリティ構成を定義して実装します。 "Microsoft.Sql" 名前空間を使用してカスタム ポリシー定義を定義するか、または Azure SQL Database サーバーのネットワーク保護のために設計されたいずれかの組み込みのポリシー定義を使用できます。 Azure SQL Database サーバーに適用できる組み込みのネットワーク セキュリティ ポリシーの例を次に示します。"SQL Server は仮想ネットワーク サービス エンドポイントを使用する必要がある"。

 

Azure Blueprints を使用して、Azure Resource Management テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化します。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、Azure SQL Database サーバー インスタンスに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: データベース イベントを追跡し、それを Azure Storage アカウント、Log Analytics ワークスペース、または Event Hubs の監査ログに書き込むには、Azure SQL Database の監査を有効にします。

さらに、Azure SQL 診断テレメトリを、複数のサブスクリプションにわたって Azure SQL Database と Azure SQL Managed Instance のパフォーマンスを大規模に監視するクラウド ソリューションである Azure SQL Analytics にストリーミングできます。 これは、Azure SQL Database のパフォーマンス メトリックの収集および視覚化に役立ち、パフォーマンスのトラブルシューティング用インテリジェンスが組み込まれています。

- [Azure SQL Database の監査を設定する方法](/azure/sql-database/sql-database-auditing)

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/sql-database/sql-database-metrics-diag-logging)

- [診断を Azure SQL Analytics にストリーミングする方法](/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure SQL Database サーバー インスタンスの監査を有効にし、監査ログの保存場所 (Azure Storage、Log Analytics、またはイベント ハブ) を選択します。

- [Azure SQL Server の監査を有効にする方法](/azure/sql-database/sql-database-auditing)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-3.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure SQL Database ログを Log Analytics ワークスペースに格納している場合は、組織のコンプライアンス規則に従ってログの保有期間を設定します。

- [ログ保持期間のパラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-5.md)]

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 異常な動作がないかどうかログを分析および監視し、結果を定期的に確認します。 Azure SQL Database インスタンスに関連した異常なアクティビティに関するアラートを生成するには、Azure Security Center の Advanced Threat Protection を使用します。 あるいは、Azure SQL Database インスタンスに関連したメトリック値または Azure アクティビティ ログ エントリに基づいてアラートを構成します。

- [Advanced Threat Protection および Azure SQL Server に関するアラートの概要](/azure/sql-database/sql-database-threat-detection-overview)

- [Azure SQL Database に関するカスタム アラートを構成する方法](alerts-insights-configure-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 異常なアクティビティを監視し、それに関するアラートを生成するには、Azure Security Center の Advanced Threat Protection for Azure SQL Database を使用します。 ご使用の SQL Database に対して Azure Defender for SQL を有効にします。 Azure Defender for SQL には、機密データの検出と分類、データベースの潜在的な脆弱性の検出と軽減、データベースへの脅威を示す可能性がある異常なアクティビティの検出を行う機能が含まれています。

- [Advanced Threat Protection および Azure SQL Database に関するアラートの概要](/azure/sql-database/sql-database-threat-detection-overview)

- [Azure SQL Database に対して Azure Defender for SQL を有効にする方法](azure-defender-for-sql.md)

- [Azure Security Center でアラートを管理する方法](../../security-center/security-center-managing-and-responding-alerts.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 2.7](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-2-7.md)]

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0&amp;preserve-view=true)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0&amp;preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 Azure SQL Database インスタンスをプロビジョニングしている場合は、Azure AD との認証の統合を選択することをお勧めします。

- [Azure SQL での Azure AD 認証を構成して管理する方法](/azure/sql-database/azure-sql/database/authentication-aad-configure)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関するポリシーと手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

- [Azure Security Center ID とアクセスについて](../../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。

Advanced Threat Protection for Azure SQL Database を使用して、データベースへのアクセスや悪用を試みる、害を及ぼす可能性がある通常とは異なる異常なアクティビティを検出します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../../security-center/security-center-identity-access.md)

- [Advanced Threat Protection および潜在的なアラートを確認する](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview#alerts)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: ポータルや Azure Resource Management での IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可するには、条件付きアクセスのネームド ロケーションを使用します。

- [Azure でネームド ロケーションを構成する方法](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure SQL Database サーバー インスタンスの Azure Active Directory (Azure AD) 管理者を作成します。

- [Azure SQL での Azure AD 認証を構成して管理する方法](authentication-aad-configure.md)

- [Azure AD インスタンスを作成して構成する方法](../../active-directory-domain-services/tutorial-create-instance.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 3.9](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-3-9.md)]

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューして、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure ID アクセス レビューの使用方法](../../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure SQL を使用して Azure Active Directory (Azure AD) 認証を構成し、Azure AD ユーザー アカウントの診断設定を作成して、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内に目的のアラートを構成します。

- [Azure SQL での Azure AD 認証を構成して管理する方法](authentication-aad-configure.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) Identity Protection とリスク検出を使用します。 また、さらに調査するためにデータを Azure Sentinel に取り込むこともできます。

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、Azure カスタマー ロックボックスに、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。

- [カスタマー ロックボックスについて](../../security/fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-1.md)]

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは Vnet/サブネットで分離し、適切にタグ付けして、NSG または Azure Firewall 内でセキュリティで保護する必要があります。 機密データを格納または処理するリソースは分離されている必要があります。 Private Link を使用します。Vnet 内に Azure SQL Server をデプロイし、プライベート エンドポイントを使用して非公開で接続します。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

- [Azure SQL Database 用の Private Link を設定する方法](/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報を格納または処理する Azure SQL Database のデータベースでは、タグを使用してデータベースと関連リソースを機密とマークします。 機密情報の窃盗を防止するために、Azure SQL Database インスタンスで Private Link とネットワーク セキュリティ グループのサービス タグを構成します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure SQL Database インスタンスでデータの窃盗を防止するように Private Link と NSG を構成する方法](/azure/sql-database/sql-database-private-endpoint-overview)

- [Azure での顧客データの保護について](../../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure SQL Database のデータの検出と分類機能を使用します。 データの検出と分類では、データベース内の機密データを検出、分類、ラベル付け、保護するために Azure SQL Database に組み込まれた高度な機能が提供されます。

- [Azure SQL Server に対してデータの検出と分類を使用する方法](/azure/sql-database/sql-database-data-discovery-and-classification)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-5.md)]

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure SQL Database インスタンスへのアクセスを認証および制御するために Azure Active Directory (Azure AD) を使用します。

- [認証のために Azure SQL Server と Azure AD を統合する方法](/azure/sql-database/sql-database-aad-authentication)

- [Azure SQL Server でアクセスを制御する方法](/azure/sql-database/sql-database-control-access)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Transparent Data Encryption (TDE) は、保存データを暗号化することによって、Azure SQL Database、Azure SQL マネージド インスタンス、Azure データ ウェアハウスを悪意のあるオフライン アクティビティの脅威から保護するために役立ちます。 データベース、関連付けられているバックアップ、保管されているトランザクション ログ ファイルの暗号化と暗号化解除をリアルタイムで実行することにより、アプリケーションに変更を加えずに暗号化を行うことができます。 既定では、SQL Database と SQL Managed Instance に新たにデプロイされるすべてのデータベースに対して TDE が有効になります。 TDE 暗号化キーは、Microsoft または顧客のどちらでも管理できます。

- [Transparent Data Encryption を管理して独自の暗号化キーを使用する方法](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure SQL Database の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure SQL Database 向け Azure Defender for SQL を有効にし、Azure SQL Server に対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。

- [Azure SQL Database で脆弱性評価を実行する方法](/azure/sql-database/sql-vulnerability-assessment)

- [Azure Defender for SQL を有効にする方法](azure-defender-for-sql.md)

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](/azure/security-center/security-center-vulnerability-assessment-recommendations)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-1.md)]

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Azure SQL Database インスタンスに対する定期的な反復スキャンを有効にします。これにより、データベースで週に 1 回スキャンを自動的に実行するための脆弱性評価が構成されます。 スキャン結果の概要は、指定した電子メール アドレスに送信されます。 結果を比較して、脆弱性が修復されたことを確認します。

- [Azure Security Center で脆弱性評価レポートをエクスポートする方法](/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

- [Azure Security Center のセキュリティ スコアについて](/azure/security-center/security-center-secure-score)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-5-5.md)]

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (Azure SQL Server インスタンスを含む) のクエリや検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC について](../../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](/azure/governance/management-groups/create)

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: すべての Azure リソースのセキュリティ構成を保持するには、Azure SQL Server/Database に関する Azure Policy または Azure Security Center の推奨事項を使用します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops&amp;preserve-view=true)

- [Azure Repos のドキュメント](https://docs.microsoft.com/azure/devops/repos/?view=azure-devops&amp;preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.SQL" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Security Center を利用して、Azure SQL Server/Database のベースライン スキャンを実行します。

- [Azure Security Center の推奨事項を修復する方法](/azure/security-center/security-center-sql-service-recommendations)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure SQL Database Transparent Data Encryption (TDE) の暗号化キーを格納するには、Azure Key Vault を使用します。

- [Azure SQL Server に格納される機密データを保護し、暗号化キーを Azure Key Vault に格納する方法](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure AD 認証をサポートする任意のサービス (Azure Key Vault を含む) に対して認証できます。

- [チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure SQL にアクセスする](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)

- [マネージド ID を構成する方法](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内の資格情報を識別するための資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft マルウェア対策は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data lake Storage、Blob Storage、Azure SQL Server などの非コンピューティング Azure リソースにアップロードされるコンテンツはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内のデータにアクセスできません。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../../security/fundamentals/antimalware.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: ビジネスをデータ損失から保護するために、Azure SQL Database ではデータベースの完全バックアップが毎週、データベースの差分バックアップが 12 時間ごとに、トランザクション ログ バックアップが 5 ～ 10 分ごとに自動的に作成されます。 バックアップは、すべてのサービス レベルで少なくとも 7 日間 RA-GRS ストレージに保存されます。 Basic を除くすべてのサービス レベルで、ポイントインタイム リストアのために、構成可能なバックアップ保有期間 (最大 35 日間) がサポートされます。

各種のコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対してさまざまな保有期間を選択できます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。

- [Azure SQL Server でのバックアップおよびビジネス継続性の概要](/azure/sql-database/sql-database-business-continuity)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure SQL Database では、7 ～ 35 日間保持されるデータベース バックアップが自動的に作成され、データ センターが使用できない場合でもそれらが確実に保持されるように Azure の読み取りアクセス geo 冗長ストレージ (RA-GRS) が使用されます。 これらのバックアップは自動的に作成されます。 必要に応じて、Azure SQL Database に対する長期間の geo 冗長バックアップを有効にしてください。

Transparent Data Encryption にカスタマー マネージド キーを使用している場合は、キーが確実にバックアップされるようにしてください。

- [Azure SQL Server でのバックアップの概要](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database)

- [Azure 上でキー コンテナーのキーをバックアップする方法](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 この制御に関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Sql**:

[!INCLUDE [Resource Policy for Microsoft.Sql 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.sql-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Backup 内でコンテンツのデータ復元を定期的に実行できるようにします。 必要に応じて、コンテンツを分離された VLAN にテスト復元してください。 バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure でキー コンテナーのキーを復元する方法](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [ポイントインタイム リストアを使用して Azure SQL Database のバックアップを復旧する方法](/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: 偶発的な削除や悪意のある削除からキーを保護するには、Azure Key Vault で論理的な削除を有効にします。

- [Key Vault で論理的な削除を有効にする方法](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**:インシデント処理/管理のフェーズと担当者の役割を定義している記述されたインシデント対応計画があることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../../security-center/security-center-planning-and-operations-guide.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または承認されていないパーティによってアクセスされたことが検出された場合はユーザーに連絡するために、Microsoft によって使用されます。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
