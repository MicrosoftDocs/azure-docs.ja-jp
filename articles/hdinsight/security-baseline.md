---
title: HDInsight 用の Azure セキュリティ ベースライン
description: HDInsight セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: bf2360bda55735aa8ef4258da5ae47f673f4d71b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738924"
---
# <a name="azure-security-baseline-for-hdinsight"></a>HDInsight 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが HDInsight に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、HDInsight に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 HDInsight に適用できない **コントロール** は、除外されています。

 
HDInsight を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な HDInsight セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:Azure HDInsight での境界セキュリティは、仮想ネットワークを使用して実現されます。 エンタープライズ管理者は、仮想ネットワーク内にクラスターを作成し、ネットワーク セキュリティ グループ (NSG) を使用して仮想ネットワークへのアクセスを制限できます。 Azure HDInsight クラスターと通信できるのは、ネットワーク セキュリティ グループの受信規則で許可されている IP アドレスだけです。 この構成では、境界セキュリティを提供します。 仮想ネットワークにデプロイされるすべてのクラスターには、クラスター ゲートウェイへのプライベート HTTP アクセスのために、その仮想ネットワーク内のプライベート IP アドレスに解決されるプライベート エンドポイントも用意されています。

窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

- [仮想ネットワーク内に Azure HDInsight をデプロイし、ネットワーク セキュリティ グループを使用してセキュリティ保護する方法](hdinsight-create-virtual-network.md)

- [Azure Firewall を使用して Azure HDInsight クラスターに対する送信トラフィックを制限する方法](hdinsight-restrict-outbound-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Security Center を使用して、Azure HDInsight クラスターをセキュリティで保護するために使用される仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループのネットワーク保護に関する推奨事項を修正します。 ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Azure Log Analytics ワークスペースに送信し、Azure Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Azure Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**:DDoS 攻撃からの保護のために、Azure HDInsight がデプロイされている仮想ネットワーク上で Azure DDoS Standard Protection を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](/azure/virtual-network/manage-ddos-protection)

- [Azure Security Center の統合された脅威インテリジェンスについて](/azure/security-center/security-center-alerts-service-layer)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure HDInsight クラスターを保護するために使用されているサブネットに接続された NSG 用に、ネットワーク セキュリティ グループ (NSG) フロー ログを有効にします。 フロー レコードを生成するために、NSG フロー ログを Azure Storage アカウントに記録します。 異常なアクティビティを調査する必要がある場合は、Azure Network Watcher パケット キャプチャを有効にします。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher を有効にする方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**:要件は、Azure セキュリティ コントロール ID 1.1 に適合できます。Azure HDInsight クラスターを仮想ネットワークにデプロイし、ネットワーク セキュリティ グループ (NSG) によるセキュリティ保護を行います。

受信トラフィックを必要とする Azure HDInsight の依存関係は複数あります。 インバウンド管理トラフィックはファイアウォール デバイスを介して送信できません。 必要な管理トラフィックのソース アドレスは既知であり、公開されています。 この情報を使用してネットワーク セキュリティ グループの規則を作成し、クラスターへの受信トラフィックをセキュリティ保護したうえで、信頼済みの場所のみからトラフィックを許可します。

窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

- [仮想ネットワーク内で HDInsight をデプロイし、ネットワーク セキュリティ グループを使用してセキュリティ保護する方法](hdinsight-create-virtual-network.md)

- [HDInsight の依存関係とファイアウォールの使用状況の概要](hdinsight-restrict-outbound-traffic.md)

- [HDInsight の管理 IP アドレス](hdinsight-management-ip-addresses.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:仮想ネットワーク サービス タグを使用して、Azure HDInsight クラスターがデプロイされているサブネットに接続されたネットワーク セキュリティ グループ (NSG) に対して、ネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [Azure HDInsight でのサービス タグの概要と使用方法](/azure/virtual-network/security-overview#service-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure HDInsight クラスターに関連するネットワーク リソースの標準的なセキュリティ構成を定義して、実装します。 Azure HDInsight クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" と "Microsoft.Network" 名前空間内で Azure Policy エイリアスを使用します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Azure HDInsight クラスターに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure コマンド ライン インターフェイス (CLI) を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure HDInsight デプロイに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log-view)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:クラスターによって生成されたセキュリティ データを集計するために、Azure Monitor に Azure HDInsight クラスターをオンボードできます。 カスタム クエリを利用して、環境内の脅威を検出し、それに対応します。 

- [Azure Monitor に Azure HDInsight クラスターをオンボードする方法](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Azure HDInsight クラスターのカスタム クエリを作成する方法](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**:HDInsight クラスター用に Azure Monitor を有効にして、Log Analytics ワークスペースにダイレクトします。 これにより、すべての Azure HDInsight クラスター ノードの関連するクラスター情報と OS メトリックがログに記録されます。

- [HDInsight クラスターでのログ記録を有効にする方法](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [HDInsight ログを照会する方法](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:Azure Monitor に Azure HDInsight クラスターをオンボードします。 使用される Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

- [Azure Monitor に Azure HDInsight クラスターをオンボードする方法](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics ワークスペースの保有期間を構成する方法](/azure/azure-monitor/platform/manage-cost-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor に Azure HDInsight クラスターをオンボードします。 使用される Azure Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

- [Azure Monitor に Azure HDInsight クラスターをオンボードする方法](hdinsight-hadoop-oms-log-analytics-tutorial.md)

- [Log Analytics ワークスペースの保有期間を構成する方法](/azure/azure-monitor/platform/manage-cost-storage)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Log Analytics ワークスペース クエリを使用して Azure HDInsight ログを照会します。

- [Azure HDInsight クラスター用のカスタム クエリを作成する方法](hdinsight-hadoop-oms-log-analytics-use-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure Log Analytics ワークスペースを使用して、Azure HDInsight クラスターに関連するセキュリティ ログやイベントでの異常なアクティビティを監視し、アラート通知を行います。

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](/azure/azure-monitor/learn/tutorial-response)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされ、クラスター ノード イメージに対して有効になっています。ただし、ソフトウェアを管理し、Clamscan によって生成されたログを手動で集計/監視する必要があります。

- [Clamscan の概要](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:DNS ログ記録用のサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**:ノード単位でのコンソールのログ記録を手動で構成します。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure HDInsight クラスターのクラスター プロビジョニング中に作成されたローカル管理者アカウントや、作成したその他のすべてのアカウントのレコードを管理します。 さらに、Azure Active Directory (Azure AD) 統合が使用されている場合、Azure AD には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:クラスターをプロビジョニングする場合、Azure では、Web ポータルと Secure Shell (SSH) アクセス用の新しいパスワードを作成する必要があります。 変更する既定のパスワードはありませんが、SSH と Web ポータル アクセスに対して異なるパスワードを指定できます。

- [Azure HDInsight クラスターをプロビジョニングする場合にパスワードを設定する方法](hdinsight-hadoop-linux-use-ssh-unix.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:Azure HDInsight クラスターの認証を Azure Active Directory (Azure AD) と統合します。 専用管理者アカウントの使用に関するポリシーと手順を作成します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

- [Azure HDInsight の認証を Azure AD と統合する方法](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**:Azure HDInsight ID ブローカーを使用して、パスワードを指定せずに多要素認証を使って Enterprise セキュリティ パッケージ (ESP) クラスターにサインインします。 Azure portal などの他の Azure サービスに既にサインインしている場合は、シングル サインオン (SSO) エクスペリエンスによって Azure HDInsight クラスターにサインインできます。

- [Azure HDInsight ID ブローカーを有効にする方法](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。 Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受け、ビッグ データ ジョブを実行することができます。 多要素認証が有効になった状態で認証を行うと、ユーザーは 2 つ目の認証要素を提供するように求められます。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure HDInsight クラスターと関連リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用して認証を受けることができます。 Azure AD 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成できます。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**:Enterprise セキュリティ パッケージが構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用して認証を受けることができます。 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Enterprise セキュリティ パッケージ (ESP) が構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受けることができます。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure HDInsight で Azure AD Domain Services を使用して Enterprise セキュリティ パッケージを構成する方法](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure HDInsight クラスターで Azure Active Directory (Azure AD) の認証を使用します。 Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Active Directory (Azure AD) サインインと監査ログを使用して、非アクティブ化されたアカウントへのアクセス試行の監視を行います。これらのログは、任意のサードパーティ製 SIEM または監視ツールに統合できます。

このプロセスを合理化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Azure Log Analytics ワークスペース内に目的のアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:Enterprise セキュリティ パッケージ (ESP) が構成された Azure HDInsight クラスターはドメインに接続できるので、ドメイン ユーザーは自分のドメイン資格情報を使用してクラスターの認証を受けることができます。 ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) リスク検出および Identity Protection 機能を使用します。 また、さらに調査するためにデータを Azure Sentinel に取り込むこともできます。

- [Azure AD の危険なサインインを表示する方法](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:使用できません。Azure HDInsight ではまだカスタマー ロックボックスがサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:Azure HDInsight デプロイに関連するリソースに対してタグを使用すると、機密情報を格納または処理する Azure リソースの追跡に役立ちます。

- [タグを作成して使用する方法](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure HDInsight クラスターおよび関連するすべてのストレージ アカウントは、仮想ネットワーク/サブネット別に分離され、適切にタグ付けされて、ネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティ保護される必要があります。 クラスター データは、セキュリティで保護された Azure Storage アカウントまたは Azure Data Lake Storage (Gen1 または Gen2) 内に含まれている必要があります。

- [Azure HDInsight クラスター用のストレージ オプションを選択する](hdinsight-hadoop-compare-storage-options.md)

- [Azure Data Lake Storage をセキュリティで保護する方法](../data-lake-store/data-lake-store-security-overview.md)

- [Azure Storage アカウントをセキュリティで保護する方法](/azure/storage/common/storage-security-guide)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:機密情報を格納または処理する Azure HDInsight クラスターの場合は、タグを使用してクラスターと関連リソースを機密としてマークします。 窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure HDInsight クラスターに対する送信ネットワーク トラフィックを制限します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure Firewall を使用して Azure HDInsight クラスターに対する送信トラフィックを制限する方法](hdinsight-restrict-outbound-traffic.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure HDInsight クラスターまたはクラスター データ ストア (Azure Storage アカウントまたは Azure Data Lake Storage Gen1/Gen2) に接続しているすべてのクライアントにおいて TLS 1.2 以上をネゴシエートできることを確認します。 Microsoft Azure リソースでは、既定で TLS 1.2 をネゴシエートします。 

- [Azure Data Lake Storage の転送中の暗号化の概要](../data-lake-store/data-lake-store-security-overview.md)

- [Azure Storage アカウントの転送中の暗号化の概要](../storage/blobs/security-recommendations.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**:Azure HDInsight Enterprise セキュリティ パッケージ (ESP) を利用すると、Apache Ranger を使用してファイル、フォルダー、データベース、テーブル、および行/列に格納されているデータに対して、詳細なアクセス制御とデータ難読化ポリシーを作成して管理することができます。 Hadoop 管理者は、Apache Ranger 上でそれらのプラグインを使用して、ロールベースのアクセス制御 (RBAC) を構成して Apache Hive、HBase、Kafka、および Spark をセキュリティ保護できます。

Apache Ranger を使用して RBAC ポリシーを構成すると、組織内のロールにアクセス許可を関連付けることができます。 この抽象化レイヤーを使用すると、より簡単に、ユーザーが作業の責任を果たすために必要なアクセス許可のみを持つようにできます。

- [HDInsight での Azure Active Directory (Azure AD) Domain Services を使用した Enterprise セキュリティ パッケージの構成](domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- [Azure HDInsight のエンタープライズ セキュリティの概要](domain-joined/hdinsight-security-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**:機密情報を格納または処理する Azure HDInsight クラスターの場合は、タグを使用してクラスターと関連リソースを機密としてマークします。 Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure SQL Database を使用して Apache Hive と Apache Oozie メタデータを格納する場合は、常に SQL データが暗号化された状態を保証するようにします。 Azure Storage Accounts および Data Lake Storage (Gen1 または Gen2) では、Microsoft による暗号化キーの管理を許可することをお勧めします。ただし、必要に応じて、ご自身のキーを自分で管理することも可能です。

- [Azure Storage アカウントの暗号化キーを管理する方法](/azure/storage/common/storage-encryption-keys-portal)

- [カスタマー マネージド暗号化キーを使用して Azure Data Lake Storage を作成する方法](../data-lake-store/data-lake-store-get-started-portal.md)

- [Azure SQL Database の暗号化の概要](/azure/sql-database/sql-database-technical-overview#data-encryption)

- [カスタマー マネージド キーを使用して SQL Database の Transparent Data Encryption を構成する方法](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:クラスター データに対するすべての CRUD 操作を監視してログに記録するように、Azure HDInsight クラスターに関連付けられている Azure Storage アカウントの診断設定を構成します。 Azure HDInsight クラスターに関連付けられている任意のストレージ アカウントまたは Data Lake Store の監査を有効にします。

- [Azure Storage アカウントでの追加のログ記録/監査を有効にする方法](/azure/storage/common/storage-monitor-storage-account)

- [Azure Data Lake Storage での追加のログ記録/監査を有効にする方法](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:サードパーティの脆弱性管理ソリューションを実装します。

Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションをお持ちの場合は、必要に応じて、スクリプト アクションを使用して Azure HDInsight クラスター ノードに脆弱性評価エージェントをインストールして、各ポータルからノードを管理できます。

- [Rapid7 エージェントを手動でインストールする方法](https://insightvm.help.rapid7.com/docs/install)

- [Qualys エージェントを手動でインストールする方法](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

- [スクリプト アクションの使用方法](hdinsight-hadoop-customize-cluster-linux.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:クラスター ノード イメージに対してシステムの自動更新が有効になっていますが、更新が適用されていることを確認するには、定期的にクラスター ノードを再起動する必要があります。

Microsoft では、Azure HDInsight ノードの基本イメージを管理および更新します。

- [HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法](hdinsight-os-patching.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:スクリプト アクションまたはその他のメカニズムを使用して、Azure HDInsight クラスターに修正プログラムを適用します。 新しく作成されたクラスターには、利用可能な最新の更新プログラムが常に保持されます (最新のセキュリティ パッチを含む)。

- [Linux ベースの Azure HDInsight クラスターの OS 修正プログラム適用スケジュールを構成する方法](hdinsight-os-patching.md)

- [スクリプト アクションの使用方法](hdinsight-hadoop-customize-cluster-linux.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:時間の経過に伴って脆弱性スキャンを比較できるサードパーティの脆弱性管理ソリューションを実装します。 Rapid7 または Qualys サブスクリプションをお持ちの場合は、そのベンダーのポータルを使用してバックツーバックの脆弱性スキャンを表示および比較できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、Azure HDInsight クラスターを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Azure Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

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

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアの一覧を定義します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリおよび検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**:クラスター ノード上で承認されていないソフトウェア アプリケーションを監視するためのサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Resource Graph を使用して、Azure HDInsight クラスターを含めて、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。  検出された承認されていない Azure リソースを削除します。 Azure HDInsight クラスター ノードの場合は、承認されていないソフトウェアの削除またはアラート通知を行うためのサードパーティ ソリューションを実装します。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**:Azure HDInsight クラスター ノードの場合は、承認されていないソフトウェアの実行を防止するためのサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類

- 許可されるリソースの種類

詳細については、次のリファレンスを参照してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:Azure HDInsight クラスター ノードの場合は、承認されていないファイルの種類の実行を防止するためのサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:HDInsight クラスターのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" 名前空間内で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Azure HDInsight のオペレーティング システム イメージは、Microsoft によって管理および保守されます。 クラスター ノードのオペレーティング システムにセキュリティで保護された構成を実装する責任は、お客様が負います。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure HDInsight クラスターと関連リソースにセキュリティで保護された設定を適用するには、Azure Policy の [拒否] と [存在する場合はデプロイする] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**:Azure HDInsight のオペレーティング システム イメージは、Microsoft によって管理および保守されます。 OS レベルの状態構成を実装する責任は、お客様が負います。

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

- [Azure Repos Git チュートリアル](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/index)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:"Microsoft.HDInsight" 名前空間で Azure Policy エイリアスを使用して、システム構成のアラート通知、監査、適用を行うためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:クラスター ノードのオペレーティング システムの望ましい状態を保持するためのサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**:HDInsight クラスターの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.HDInsight" 名前空間内で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:クラスター ノードのオペレーティング システムの状態を監視するためのサードパーティ ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**:Azure HDInsight では、Apache Kafka の BYOK (Bring Your Own Key) に対応しています。 この機能では、保存データの暗号化に使用するキーを所有し、管理できます。

Azure HDInsight 内のマネージド ディスクはすべて、Azure Storage Service Encryption (SSE) によって保護されます。 既定では、これらのディスク上のデータは、Microsoft が管理するキーを使用して暗号化されます。 BYOK を有効にした場合は、Azure HDInsight の暗号化キーを指定し、Azure Key Vault を使ってそれを使用し管理します。

また、Key Vault は、クラスター ストレージ (Azure Storage アカウント、Azure Data Lake Storage) のキーを管理するために、Azure HDInsight デプロイによって使用される場合もあります。

- [Azure HDInsight 上で Apache Kafka 用に自分のキーを持ち込む方法](/azure/hdinsight/kafka/apache-kafka-byok)

- [Azure Storage アカウントの暗号化キーを管理する方法](/azure/storage/common/storage-encryption-keys-portal)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure HDInsight でマネージド ID を使用すると、クラスターから Azure Active Directory (Azure AD) Domain Services へのアクセス、Azure Key Vault へのアクセス、または Azure Data Lake Storage Gen2 内のファイルへのアクセスが可能になります。

- [Azure HDInsight でのマネージド ID の概要](hdinsight-managed-identities.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**:Azure HDInsight デプロイに関連するコードを使用する場合は、資格情報スキャナーを実装して、コード内で資格情報を識別することができます。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされ、クラスター ノード イメージに対して有効になっています。ただし、ソフトウェアを管理し、Clamscan によって生成されたログを手動で集計/監視する必要があります。

- [Azure HDInsight での Clamscan の概要](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Microsoft Antimalware は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Data Lake Storage、Blob Storage など、Azure HDInsight クラスター デプロイに関連する Azure リソースにアップロードされているすべてのファイルを事前にスキャンします。Microsoft では、これらのインスタンス内の顧客データにアクセスできません。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**:Azure HDInsight には Clamscan がプレインストールされており、クラスター ノード イメージに対して有効になっています。 Clamscan では、エンジンと定義の更新は自動的に実行されますが、ログの集計と管理は手動で実行する必要があります。

- [Azure HDInsight での Clamscan の概要](https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:HDInsight クラスター データ ストア用に Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。  Azure HDInsight クラスター データ ストアに対して Azure SQL Database を使用する場合は、アクティブ geo レプリケーションを構成します。

- [Azure Storage アカウントのストレージ冗長性を構成する方法](../storage/common/storage-redundancy.md)

- [Azure SQL Database の冗長性を構成する方法](/azure/sql-database/sql-database-active-geo-replication)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**:Azure HDInsight クラスター データ ストア用に Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。 Azure HDInsight のデプロイのいずれかの部分に Azure Key Vault を使用している場合は、キーが確実にバックアップされるようにしてください。

- [Azure HDInsight クラスター用のストレージ オプションを選択する](hdinsight-hadoop-compare-storage-options.md)

- [Azure Storage アカウントのストレージ冗長性を構成する方法](../storage/common/storage-redundancy.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Azure HDInsight のデプロイで Azure Key Vault が使用されている場合は、バックアップされたカスタマー マネージド キーの復元をテストします。

- [Azure HDInsight 上で Apache Kafka 用に自分のキーを持ち込む方法](/azure/hdinsight/kafka/apache-kafka-byok)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:Azure HDInsight のデプロイで Azure Key Vault が使用されている場合は、Key Vault での論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [Azure Key Vault で論理的な削除を有効にする方法](/azure/key-vault/key-vault-ovw-soft-delete)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/) 

- [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。 

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
