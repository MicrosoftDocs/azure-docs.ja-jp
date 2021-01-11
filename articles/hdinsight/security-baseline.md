---
title: HDInsight 用の Azure セキュリティ ベースライン
description: HDInsight 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7315ef68b3562a28ee2515077c1e7d2f23c3803f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230434"
---
# <a name="azure-security-baseline-for-hdinsight"></a>HDInsight 用の Azure セキュリティ ベースライン

HDInsight 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**:Azure HDInsight での境界セキュリティは、仮想ネットワークを使用して実現されます。 エンタープライズ管理者は、仮想ネットワーク内にクラスターを作成し、ネットワーク セキュリティ グループ (NSG) を使用して仮想ネットワークへのアクセスを制限できます。 Azure HDInsight クラスターと通信できるのは、ネットワーク セキュリティ グループの受信規則で許可されている IP アドレスだけです。 この構成では、境界セキュリティを提供します。 仮想ネットワークにデプロイされるすべてのクラスターには、クラスター ゲートウェイへのプライベート HTTP アクセスのために、その仮想ネットワーク内のプライベート IP アドレスに解決されるプライベート エンドポイントも用意されています。

窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

仮想ネットワーク内に Azure HDInsight をデプロイし、ネットワーク セキュリティ グループを使用してセキュリティ保護する方法: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

Azure Firewall を使用して Azure HDInsight クラスターに対する送信トラフィックを制限する方法: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Security Center を使用して、Azure HDInsight クラスターをセキュリティで保護するために使用される仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループのネットワーク保護に関する推奨事項を修正します。 ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Azure Log Analytics ワークスペースに送信し、Azure Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Azure Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Azure Traffic Analytics を有効にして使用する方法:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Azure Security Center によって提供されるネットワークのセキュリティの概要:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用なし。ベンチマークでは、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃からの保護のために、Azure HDInsight がデプロイされている仮想ネットワーク上で Azure DDoS Standard Protection を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

DDoS 保護を構成する方法:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center の統合された脅威インテリジェンスの概要:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**:Azure HDInsight クラスターを保護するために使用されているサブネットに接続された NSG 用に、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にします。 フロー レコードを生成するために、NSG フロー ログを Azure Storage アカウントに記録します。 異常なアクティビティを調査する必要がある場合は、Azure Network Watcher パケット キャプチャを有効にします。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Network Watcher を有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:要件は、Azure セキュリティ コントロール ID 1.1 に適合できます。Azure HDInsight クラスターを仮想ネットワークにデプロイし、ネットワーク セキュリティ グループ (NSG) によるセキュリティ保護を行います。

受信トラフィックを必要とする Azure HDInsight の依存関係は複数あります。 インバウンド管理トラフィックはファイアウォール デバイスを介して送信できません。 必要な管理トラフィックのソース アドレスは既知であり、公開されています。 この情報を使用してネットワーク セキュリティ グループの規則を作成し、クラスターへの受信トラフィックをセキュリティ保護したうえで、信頼済みの場所のみからトラフィックを許可します。

窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

仮想ネットワーク内で HDInsight をデプロイし、ネットワーク セキュリティ グループを使用してセキュリティ保護する方法: https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

HDInsight の依存関係とファイアウォールの使用状況の概要: https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight の管理 IP アドレス: https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用なし。ベンチマークでは、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:仮想ネットワーク サービス タグを使用して、Azure HDInsight クラスターがデプロイされているサブネットに接続されたネットワーク セキュリティ グループ (NSG) に対して、ネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure HDInsight でのサービス タグの概要と使用方法:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure HDInsight クラスターに関連するネットワーク リソースの標準的なセキュリティ構成を定義して、実装します。 Azure HDInsight クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" と "Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

使用可能な Azure Policy エイリアスを表示する方法:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint の作成方法:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure HDInsight クラスターに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

仮想ネットワークを作成する方法:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

セキュリティ構成を使用して NSG を作成する方法:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure HDInsight デプロイに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が行われたときにトリガーされるアラートを Azure Monitor 内で作成します。

Azure アクティビティ ログ イベントを表示して取得する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Azure HDInsight クラスター コンポーネントのタイム ソースは Microsoft によって管理されており、コンピューティング デプロイの時刻同期を更新することができます。

Azure コンピューティング リソースの時刻同期を構成する方法:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:クラスターによって生成されたセキュリティ データを集計するために、Azure Monitor に Azure HDInsight クラスターをオンボードできます。 カスタム クエリを利用して、環境内の脅威を検出し、それに対応します。 

Azure Monitor に Azure HDInsight クラスターをオンボードする方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Azure HDInsight クラスターのカスタム クエリを作成する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:HDInsight クラスター用に Azure Monitor を有効にして、Log Analytics ワークスペースにダイレクトします。 これにより、すべての Azure HDInsight クラスター ノードの関連するクラスター情報と OS メトリックがログに記録されます。

HDInsight クラスターでのログ記録を有効にする方法:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

HDInsight ログを照会する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:Azure Monitor に Azure HDInsight クラスターをオンボードします。 使用される Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

Azure Monitor に Azure HDInsight クラスターをオンボードする方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics ワークスペースの保有期間を構成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor に Azure HDInsight クラスターをオンボードします。 使用される Azure Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

Azure Monitor に Azure HDInsight クラスターをオンボードする方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial

Log Analytics ワークスペースの保有期間を構成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Log Analytics ワークスペース クエリを使用して Azure HDInsight ログを照会します。

Azure HDInsight クラスター用のカスタム クエリを作成する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure Log Analytics ワークスペースを使用して、Azure HDInsight クラスターに関連するセキュリティ ログやイベントでの異常なアクティビティを監視し、アラート通知を行います。

Azure Security Center でアラートを管理する方法:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Log Analytics のログ データに関するアラートを送信する方法:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされ、クラスター ノード イメージに対して有効になっています。ただし、ソフトウェアを管理し、Clamscan によって生成されたログを手動で集計/監視する必要があります。

Clamscan の概要:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:DNS ログ記録用のサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**:ノード単位でのコンソールのログ記録を手動で構成します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure HDInsight クラスターのクラスター プロビジョニング中に作成されたローカル管理者アカウントや、作成したその他のすべてのアカウントのレコードを管理します。 さらに、Azure AD の統合が使用されている場合、Azure AD には、明示的に割り当てる必要があるためにクエリ可能な組み込みのロールが用意されています。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Security Center を使用して ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:クラスターをプロビジョニングする場合、Azure では、Web ポータルと Secure Shell (SSH) アクセス用の新しいパスワードを作成する必要があります。 変更する既定のパスワードはありませんが、SSH と Web ポータル アクセスに対して異なるパスワードを指定できます。

Azure HDInsight クラスターをプロビジョニングする場合にパスワードを設定する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:Azure HDInsight クラスターの認証を Azure Active Directory と統合します。 専用の管理者アカウントの使用に関するポリシーと手順を作成します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

Azure HDInsight の認証を Azure Active Directory と統合する方法:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Azure Security Center を使用して ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure HDInsight ID ブローカーを使用して、パスワードを指定せずに Multi-Factor Authentication を使って Enterprise セキュリティ パッケージ (ESP) クラスターにサインインします。 Azure portal などの他の Azure サービスに既にサインインしている場合は、シングル サインオン (SSO) エクスペリエンスによって Azure HDInsight クラスターにサインインできます。

Azure HDInsight ID ブローカーを有効にする方法:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD MFA を有効にして、Azure Security Center ID とアクセス管理の推奨事項に従います。 Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受け、ビッグ データ ジョブを実行することができます。 Multi-Factor Authentication (MFA) が有効になった状態で認証を行うと、ユーザーは 2 つ目の認証要素を提供するように求められます。

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure HDInsight クラスターと関連リソースを構成します。

特権アクセス ワークステーションについて:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**:Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用して認証を受けることができます。 AAD 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生した場合に、Azure Active Directory (AAD) セキュリティ レポートを使用してログおよびアラートを生成することができます。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

危険なアクティビティのフラグが設定された AAD ユーザーを特定する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用して認証を受けることができます。 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

Azure でネームド ロケーションを構成する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory (AAD) を中央認証と承認システムとして使用します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、AAD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Enterprise セキュリティ パッケージ (ESP) が構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受けることができます。

AAD インスタンスを作成して構成する方法:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Azure HDInsight で Azure Active Directory Domain Services を使用して Enterprise セキュリティ パッケージを構成する方法:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure HDInsight クラスターで Azure Active Directory (AAD) の認証を使用します。 AAD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

Azure ID アクセス レビューの使用方法:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**:Azure Active Directory (AAD) サインインと監査ログを使用して、非アクティブ化されたアカウントへのアクセス試行の監視を行います。これらのログは、任意のサードパーティ製 SIEM/監視ツールに統合できます。

このプロセスを効率化するには、AAD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Azure Log Analytics ワークスペース内に目的のアラートを構成します。

Azure Activity Logs を Azure Monitor に統合する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**:Enterprise セキュリティ パッケージ (ESP) が構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受けることができます。  ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (AAD) リスク検出および Identity Protection 機能を使用します。 また、さらに詳しく調査するためにデータを Azure Sentinel に取り込むこともできます。

AAD の危険なサインインを表示する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID Protection のリスク ポリシーを構成して有効にする方法:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:使用できません。Azure HDInsight ではまだカスタマー ロックボックスがサポートされていません。

カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Azure HDInsight デプロイに関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure HDInsight クラスターおよび関連するすべてのストレージ アカウントは、仮想ネットワーク/サブネット別に分離され、適切にタグ付けされて、ネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティ保護される必要があります。 クラスター データは、セキュリティで保護された Azure Storage アカウントまたは Azure Data Lake Storage (Gen1 または Gen2) 内に含まれている必要があります。

Azure HDInsight クラスター用のストレージ オプションを選択する:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Azure Data Lake Storage をセキュリティで保護する方法:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Azure Storage アカウントをセキュリティで保護する方法:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報を格納または処理する Azure HDInsight クラスターの場合は、タグを使用してクラスターと関連リソースを機密としてマークします。 窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure Firewall を使用して Azure HDInsight クラスターに対する送信トラフィックを制限する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure HDInsight クラスターまたはクラスター データ ストア (Azure Storage アカウントまたは Azure Data Lake Storage Gen1/Gen2) に接続しているすべてのクライアントにおいて TLS 1.2 以上をネゴシエートできることを確認します。 Microsoft Azure リソースでは、既定で TLS 1.2 をネゴシエートします。 

Azure Data Lake Storage の転送中の暗号化の概要:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview

Azure Storage アカウントの転送中の暗号化の概要:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**:Azure HDInsight Enterprise セキュリティ パッケージ (ESP) を利用すると、Apache Ranger を使用してファイル、フォルダー、データベース、テーブル、および行/列に格納されているデータに対して、詳細なアクセス制御とデータ難読化ポリシーを作成して管理することができます。 Hadoop 管理者は、Apache Ranger 上でそれらのプラグインを使用して、ロールベースのアクセス制御 (RBAC) を構成して Apache Hive、HBase、Kafka、および Spark をセキュリティ保護できます。

Apache Ranger を使用して RBAC ポリシーを構成すると、組織内のロールにアクセス許可を関連付けることができます。 この抽象化レイヤーを使用すると、より簡単に、ユーザーが作業の責任を果たすために必要なアクセス許可のみを持つようにできます。

HDInsight での Azure Active Directory Domain Services を使用した Enterprise セキュリティ パッケージの構成:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Azure HDInsight のエンタープライズ セキュリティの概要:

https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:機密情報を格納または処理する Azure HDInsight クラスターの場合は、タグを使用してクラスターと関連リソースを機密としてマークします。 Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure SQL Database を使用して Apache Hive と Apache Oozie メタデータを格納する場合は、常に SQL データが暗号化された状態を保証するようにします。 Azure Storage Accounts および Data Lake Storage (Gen1 または Gen2) では、Microsoft による暗号化キーの管理を許可することをお勧めします。ただし、必要に応じて、ご自身のキーを自分で管理することも可能です。

Azure Storage アカウントの暗号化キーを管理する方法:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

カスタマー マネージド暗号化キーを使用して Azure Data Lake Storage を作成する方法:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal

Azure SQL Database の暗号化の概要:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption

カスタマー マネージド キーを使用して SQL Database の Transparent Data Encryption を構成する方法:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:クラスター データに対するすべての CRUD 操作を監視してログに記録するように、Azure HDInsight クラスターに関連付けられている Azure Storage アカウントの診断設定を構成します。 Azure HDInsight クラスターに関連付けられている任意のストレージ アカウントまたは Data Lake Store の監査を有効にします。

Azure Storage アカウントでの追加のログ記録/監査を有効にする方法:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

Azure Data Lake Storage での追加のログ記録/監査を有効にする方法:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:サードパーティの脆弱性管理ソリューションを実装します。

Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションをお持ちの場合は、必要に応じて、スクリプト アクションを使用して Azure HDInsight クラスター ノードに脆弱性評価エージェントをインストールして、各ポータルからノードを管理できます。

Rapid7 エージェントを手動でインストールする方法:

https://insightvm.help.rapid7.com/docs/install

Qualys エージェントを手動でインストールする方法:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf

スクリプト アクションの使用方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:クラスター ノード イメージに対してシステムの自動更新が有効になっていますが、更新が適用されていることを確認するには、定期的にクラスター ノードを再起動する必要があります。

Microsoft では、Azure HDInsight ノードの基本イメージを管理および更新します。

HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**:スクリプト アクションまたはその他のメカニズムを使用して、Azure HDInsight クラスターに修正プログラムを適用します。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

Linux ベースの Azure HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

スクリプト アクションの使用方法:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:時間の経過に伴って脆弱性スキャンを比較できるサードパーティの脆弱性管理ソリューションを実装します。 Rapid7 または Qualys サブスクリプションをお持ちの場合は、そのベンダーのポータルを使用してバックツーバックの脆弱性スキャンを表示および比較できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**:Azure Resource Graph を使用して、Azure HDInsight クラスターを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを確認する方法:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC を理解する:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 現在は使用できません

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

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:クラスター ノード上で承認されていないソフトウェア アプリケーションを監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:Azure Resource Graph を使用して、Azure HDInsight クラスターを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  検出された承認されていない Azure リソースを削除します。 Azure HDInsight クラスター ノードの場合は、承認されていないソフトウェアの削除またはアラート通知を行うためのサードパーティ ソリューションを実装します。

Azure Graph を使用してクエリを作成する方法:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure HDInsight クラスター ノードの場合は、承認されていないソフトウェアの実行を防止するためのサードパーティ ソリューションを実装します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**:Azure HDInsight クラスター ノードの場合は、承認されていないファイルの種類の実行を防止するためのサードパーティ ソリューションを実装します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用なし。クラスターのユーザー (管理者以外) は、ジョブを実行するために個々のノードへのアクセスを必要としないため、これは Azure HDInsight には適用できません。 クラスター管理者には、すべてのクラスター ノードへのルート アクセス権があります。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用なし。ベンチマークでは、Web アプリケーションをホストしている Azure アプリ サービスまたはコンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:HDInsight クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" 名前空間内で Azure Policy エイリアスを使用します。

使用可能な Azure Policy エイリアスを表示する方法:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Azure HDInsight のオペレーティング システム イメージは、Microsoft によって管理および保守されます。 クラスター ノードのオペレーティング システムにセキュリティで保護された構成を実装する責任は、お客様が負います。 


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure HDInsight クラスターと関連リソースにセキュリティで保護された設定を適用するには、Azure Policy の [拒否] と [存在する場合はデプロイする] を使用します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果を理解する:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**:Azure HDInsight のオペレーティング システム イメージは、Microsoft によって管理および保守されます。 OS レベルの状態構成を実装する責任は、お客様が負います。


**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

Azure DevOps にコードを格納する方法:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用なし。カスタム イメージは、Azure HDInsight には適用できません。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**:"Microsoft.HDInsight" 名前空間で Azure Policy エイリアスを使用して、システム構成のアラート通知、監査、適用を行うためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**:クラスター ノードのオペレーティング システムの望ましい状態を保持するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**:HDInsight クラスターの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" 名前空間内で Azure Policy エイリアスを使用します。

使用可能な Azure Policy エイリアスを表示する方法:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:クラスター ノードのオペレーティング システムの状態を監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure HDInsight では、Apache Kafka の BYOK (Bring Your Own Key) に対応しています。 この機能では、保存データの暗号化に使用するキーを所有し、管理できます。

Azure HDInsight 内のマネージド ディスクはすべて、Azure Storage Service Encryption (SSE) によって保護されます。 既定では、これらのディスク上のデータは、Microsoft が管理するキーを使用して暗号化されます。 BYOK を有効にした場合は、Azure HDInsight の暗号化キーを指定し、Azure Key Vault を使ってそれを使用し管理します。

また、Key Vault は、クラスター ストレージ (Azure Storage アカウント、Azure Data Lake Storage) のキーを管理するために、Azure HDInsight デプロイによって使用される場合もあります。

Azure HDInsight 上で Apache Kafka 用に自分のキーを持ち込む方法:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Azure Storage アカウントの暗号化キーを管理する方法:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure HDInsight でマネージド ID を使用すると、クラスターから Azure Active Directory Domain Services へのアクセス、Azure Key Vault へのアクセス、または Azure Data Lake Storage Gen2 内のファイルへのアクセスが可能になります。

Azure HDInsight でのマネージド ID の概要:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Azure HDInsight デプロイに関連するコードを使用する場合は、資格情報スキャナーを実装して、コード内で資格情報を識別することができます。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

資格情報スキャナーを設定する方法:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされ、クラスター ノード イメージに対して有効になっています。ただし、ソフトウェアを管理し、Clamscan によって生成されたログを手動で集計/監視する必要があります。

Azure HDInsight での Clamscan の概要:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Microsoft Antimalware は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Data Lake Storage、Blob Storage など、Azure HDInsight クラスター デプロイに関連する Azure リソースにアップロードされているすべてのファイルを事前にスキャンします。Microsoft では、これらのインスタンス内の顧客データにアクセスできません。

Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされており、クラスター ノード イメージに対して有効になっています。 Clamscan では、エンジンと定義の更新は自動的に実行されますが、ログの集計と管理は手動で実行する必要があります。

Azure HDInsight での Clamscan の概要:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:HDInsight クラスター データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。  Azure HDInsight クラスター データ ストアに対して Azure SQL Database を使用する場合は、アクティブ geo レプリケーションを構成します。

Azure Storage アカウントのストレージ冗長性を構成する方法:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Azure SQL Database の冗長性を構成する方法:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**:Azure HDInsight クラスター データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。 Azure HDInsight のデプロイのいずれかの部分に Azure Key Vault を使用している場合は、キーが確実にバックアップされるようにしてください。

Azure HDInsight クラスター用のストレージ オプションを選択する:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options

Azure Storage アカウントのストレージ冗長性を構成する方法:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Azure 上で Key Vault のキーをバックアップする方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Azure HDInsight のデプロイで Azure Key Vault が使用されている場合は、バックアップされたカスタマー マネージド キーの復元をテストします。

Azure HDInsight 上で Apache Kafka 用に自分のキーを持ち込む方法:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok

Azure で Key Vault のキーを復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**:Azure HDInsight のデプロイで Azure Key Vault が使用されている場合は、Key Vault での論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

Azure Key Vault で論理的な削除を有効にする方法:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center の監視**: 現在は使用できません

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

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。NIST の出版物を参照してください。IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないパーティによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。

Azure Security Center のセキュリティ連絡先を設定する方法:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

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
