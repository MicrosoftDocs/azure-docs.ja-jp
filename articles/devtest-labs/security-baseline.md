---
title: Azure DevTest Labs 用の Azure のセキュリティ ベースライン
description: Azure DevTest Labs セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: devtest-lab
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 13585173b54c79158161da54f968ba66eb018f6a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705740"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs 用の Azure のセキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure DevTest Labs に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure DevTest Labs に適用できる関連ガイダンスによって定義されています。 Azure DevTest Labs に適用できない **制御** は、除外されています。

Azure DevTest Labs を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure DevTest Labs セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure DevTest Labs リソースをデプロイする場合は、既存の仮想ネットワークを作成または使用する必要があります。 選択した仮想ネットワークで、ネットワーク セキュリティ グループがサブネットに適用され、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が構成されていることを確認します。 ラボ リソースが仮想ネットワークを使用して構成されている場合、それらのリソースはパブリックにアドレス指定できず、仮想ネットワーク内からのみアクセスできます。 また、ネットワーク分離ラボを作成することもできます。このラボでは、ラボ環境と同時に、ストレージ アカウントやキー コンテナーなどのラボ リソースも完全に分離され、それらのリソースには、指定されたエンドポイント経由でのみアクセスできます。 

組織のニーズに応じて、Azure Firewall サービスを使用し、複数のサブスクリプションや仮想ネットワークを対象に、アプリケーションとネットワークの接続ポリシーを一元的に作成、適用、記録することもできます。

- [Azure DevTest Labs 用の仮想ネットワークを構成する方法](devtest-lab-configure-vnet.md)

- [ネットワーク分離された DevTest Labs インスタンスを作成する方法](network-isolation.md)

- [セキュリティ規則を使用してネットワーク セキュリティ グループを作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure DevTest Labs リソースがデプロイされているネットワーク上にネットワーク セキュリティ グループをデプロイします。 トラフィックの監査のために、ネットワーク セキュリティ グループでネットワーク セキュリティ グループのフロー ログを有効にします。

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Resource Manager テンプレートを使用してデプロイされた重要な Web アプリケーションの前に、Azure Web アプリケーション ファイアウォール (WAF) をデプロイします。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: ラボ用の Azure Virtual Network (VNet) をデプロイすると、ラボのセキュリティと分離が強化されます。 アクセスの制限には、サブネット、アクセス制御ポリシー、およびその他の機能も役立ちます。 VNet にデプロイされると、Azure DevTest Labs はパブリックにアドレスを指定することはできないため、VNet 内の仮想マシンとアプリケーションからしかアクセスできません。

DDoS 攻撃から保護するために、Azure Virtual Network で DDoS Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。 Azure Security Center の Just In Time ネットワーク アクセスを使用して、NSG を構成し、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限します。 Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用して、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨します。

- [Azure DevTest Labs 用の仮想ネットワークを構成する方法](devtest-lab-configure-vnet.md)

- [DDoS 保護を構成する方法](/azure/virtual-network/manage-ddos-protection)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](/azure/security-center/threat-protection)

- [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center の Just In Time ネットワーク アクセス制御について](../security-center/security-center-just-in-time.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: ラボ仮想ネットワークで Network Watcher パケット キャプチャを有効にして、異常なアクティビティを調査します。 

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:脅威インテリジェンスが有効になった仮想ネットワーク上でデプロイされる Azure Firewall を使用します。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。 Azure Firewall で提供される機能に加えて、さらに追加の機能が必要な場合は、Azure Marketplace から、IDS/IPS 機能とペイロード検査機能をサポートした適切なプランを選択してください。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)
 

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)
 

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: Web アプリケーションを含んでいる DevTest Labs Azure Resource Manager ベースの環境を使用する場合は、信頼された証明書に対して HTTPS/TLS が有効になっている Azure Application Gateway をデプロイします。

- [Application Gateway をデプロイする方法](../application-gateway/quick-create-portal.md)

- [HTTPS を使用するように Application Gateway を構成する方法](../application-gateway/create-ssl-portal.md)

- [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../application-gateway/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: DevTest Labs リソース用に構成されたネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、Virtual Network サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

また、アプリケーション セキュリティグループを使用して、複雑なセキュリティ構成を簡略化することもできます。 アプリケーション セキュリティ グループを使用すると、ネットワーク セキュリティをアプリケーションの構造の自然な拡張として構成でき、仮想マシンをグループ化して、それらのグループに基づくネットワーク セキュリティ ポリシーを定義できます。

- [サービス タグの概要と使用](../virtual-network/service-tags-overview.md)

- [アプリケーション セキュリティ グループの概要と使用](/azure/virtual-network/security-overview#application-security-groups)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。

Azure Blueprints を使用して、Azure Resource Manager テンプレート、RBAC コントロール、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することで、大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションに適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure DevTest Labs デプロイに関連付けられているネットワーク リソースを分類別に論理的に整理するために、それらのリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用してリソース構成を監視し、Azure リソースに対する変更を検出します。 重要なリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft は、Azure リソースのタイム ソースを保持しています。 しかし、ご自分でコンピューティング リソースに時刻同期設定を管理することもできます。

- [Azure での Windows VM の時刻同期](../virtual-machines/windows/time-sync.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージア カウントにログを送信してアーカイブします。 アクティビティ ログにより、管理プレーン レベルで Azure DevTest Labs インスタンスで実行された操作に関する分析情報が提供されます。 Azure アクティビティ ログのデータを使用すると、DevTest Labs インスタンスの管理プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](/azure/azure-monitor/platform/diagnostic-settings)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージア カウントにログを送信してアーカイブします。 アクティビティ ログにより、管理プレーン レベルで Azure DevTest Labs インスタンスで実行された操作に関する分析情報が提供されます。 Azure アクティビティ ログのデータを使用すると、DevTest Labs インスタンスの管理プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

- [プラットフォーム ログとメトリックを異なる宛先に送信するための診断設定を作成する](/azure/azure-monitor/platform/diagnostic-settings)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure DevTest Labs 仮想マシン (VM) は、顧客によって作成され、所有されます。 したがって、それを監視するのは組織の責任です。 Azure Security Center を使用して、コンピューティング OS を監視できます。 Security Center によってオペレーティング システムから収集されるデータには、OS の種類とバージョン、OS (Windows イベント ログ)、実行中のプロセス、マシン名、IP アドレス、ログイン ユーザーなどがあります。 Log Analytics エージェントは、クラッシュ ダンプ ファイルも収集します。

詳細については、次の記事を参照してください。

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Security Center のデータ収集について](../security-center/security-center-enable-data-collection.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor で、組織のコンプライアンス規則に従って、Azure DevTest Labs インスタンスに関連付けられている Log Analytics ワークスペースのログの保有期間を設定します。

- [詳細については、次の記事を参照してください](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、Azure DevTest Labs 用に収集された可能性があるアクティビティ ログ データに基づいて、多くの他の分析情報を提供します。

詳細については、次の記事を参照してください。

- [Azure アクティビティ ログの診断設定を有効にする方法](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する方法](/azure/azure-monitor/platform/activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Log Analytics ワークスペースを使用して、Azure DevTest Labs に関連するセキュリティ ログやイベントでの異常なアクティビティを監視し、アラート通知を行います。

- [Log Analytics のログ データに関するアラートを送信する方法](/azure/azure-monitor/learn/tutorial-response)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [Azure DevTest Labs のロール](devtest-lab-add-devtest-user.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory (Azure AD) には、既定のパスワードの概念がありません。 パスワードを必要とする他の Azure リソースでは、パスワードが強制的に作成されます。これには複雑な要件と、サービスによって異なるパスワードの最小文字数が適用されます。 既定のパスワードが使用される可能性があるサードパーティ製のアプリケーションと Marketplace サービスについては、お客様が責任を負うものとします。

DevTest Labs には、既定のパスワードという概念がありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある

- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure DevTest Labs のロール](devtest-lab-add-devtest-user.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: DevTest Labs では、ID 管理用の Azure Active Directory (Azure AD) サービスを使用します。 DevTest ラボに基づく環境へのアクセスをユーザーに許可する場合は、次の 2 つの重要な側面を考慮します。
- リソース管理: リソースを管理 (VM の作成、環境の作成、成果物の開始、停止、再起動、削除、適用など) するための Azure portal へのアクセス権を付与します。 Azure でのリソース管理には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。 ユーザーにロールを割り当て、リソースとアクセス レベルのアクセス許可を設定します。
- 仮想マシン (ネットワーク レベル) :既定の構成では、VM にはローカル管理者アカウントが使用されます。 使用できるドメイン (Azure Active Directory Domain Services (Azure AD DS)、オンプレミスのドメイン、またはクラウドベースのドメイン) がある場合は、マシンをドメインに参加させることができます。 ユーザーは、ドメイン参加成果物を使用するドメインベースの ID を使用して、マシンに接続できます。

- [DevTest Labs の参照アーキテクチャ](https://docs.microsoft.com/azure/devtest-labs/devtest-lab-reference-architecture#architecture)

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。 

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)
 
- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/overview-reports.md)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意のセキュリティ情報イベント管理 (SIEM) または監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内にアラートを構成することができます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure DevTest Labs 内のラボへのアクセスを制御するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [DevTest Labs でのロールについて理解する](devtest-lab-add-devtest-user.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: DevTest Labs インスタンスおよび他の重要なリソースまたは関連リソースへの変更が発生したときにアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

- [DevTest Labs アクティビティ ログ イベントのアラートを作成する方法](create-alerts.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (DevTest Labs リソースを含む) にクエリを実行したり検出したりするには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のすべての Azure サブスクリプションとリソースを列挙できることを確認します

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-4.8.0&amp;preserve-view=true)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に応じて論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [DevTest Labs のタグを構成する](devtest-lab-add-tag.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: タグ付け、管理グループ、個別のサブスクリプション、および必要に応じて個別のラボを使用して、ラボやラボ関連のリソースを整理し、追跡することができます。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションからすばやく削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [DevTest Labs を使用してラボを作成する方法](devtest-lab-create-lab.md)

- [タグを作成して使用する方法](devtest-lab-add-tag.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。 サブスクリプション管理者は、構成されたラボ マシンのグループでの実行が許可された一連のアプリケーションを定義するために役立つ、Azure Security Center の機能である適応型アプリケーション制御を使用することもできます。 この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。

- [適応型アプリケーション制御を有効にする方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みポリシー定義を使用してお客様のサブスクリプション内に作成できるリソースの種類に対し、Azure ポリシーを使用して制限を適用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用して、サブスクリプション内のリソースをクエリまたは検出します。 これは、ストレージ アカウントを使用する環境など、高セキュリティ ベースの環境に役立ちます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。 サブスクリプション管理者は、Azure 仮想マシンのインベントリを使用して、サブスクリプション内の DevTest Labs VM 上のすべてのソフトウェアに関する情報の収集を自動化できます。 ソフトウェアの名前、バージョン、発行元、および更新時刻の各プロパティは、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、お客様はゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込む必要があります。

ソフトウェア アプリケーションの監視のための変更の追跡の使用に加えて、Azure Security Center の適応型アプリケーション制御では、マシン上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成するために機械学習を使用します。 この機能により、アプリケーション許可リスト ポリシーを構成して維持するプロセスが大幅に簡略化されるため、環境で望ましくないソフトウェアが使用されることを回避できます。 監査モードまたは強制モードを構成できます。 監査モードでは、保護された VM に対するアクティビティが監査されるだけです。 強制モードでは規則が強制され、実行が許可されていないアプリケーションが確実にブロックされます。 

- [Azure Automation の概要](../automation/automation-intro.md)

- [Azure VM インベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

- [適応型アプリケーション制御の概要](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。 サブスクリプション管理者は、Change Tracking を使用して、DevTest Labs でホストされている VM にインストールされているすべてのソフトウェアを識別できます。 独自のプロセスを実装するか、Azure Automation State Configuration を使用して、承認されていないソフトウェアを削除できます。

- [Azure Automation の概要](../automation/automation-intro.md)

- [Change Tracking ソリューションを使用して環境内の変更を追跡する](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: サブスクリプション管理者は、Azure Security Center の適応型アプリケーション制御を利用して、DevTest Labs でホストされている Azure VM 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることができます。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**:次の組み込みポリシー定義を使用してお客様のサブスクリプション内に作成できるリソースの種類に対し、Azure ポリシーを使用して制限を適用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

参考資料:

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適応型アプリケーション制御は、DevTest Labs でホストされている Azure および Azure 以外のマシン (Windows および Linux) 上でどのアプリケーションが実行できるかを制御できる、Azure Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 DevTest Labs でホストされている基になるコンピューティング リソースに対してこの設定を構成するには、サブスクリプション管理者である必要があることに注意してください。 この設定が組織の要件を満たしていない場合は、サードパーティ製ソリューションを実装します。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: **Microsoft Azure Management** アプリに対して **[アクセスのブロック]** を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: スクリプトの種類によっては、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーが DevTest Labs でホストされている VM 内でスクリプトを実行する機能を制限することができます。 また、Azure Security Center 適応型アプリケーション制御を使用して、基になる Azure VM で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7&amp;preserve-view=true)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: Azure 環境にデプロイされたリスクの高いアプリケーションは、仮想ネットワーク、サブネット、サブスクリプション、管理グループなどを使用して分離できます。 また、Azure Firewall、Web アプリケーション ファイアウォール (WAF)、またはネットワーク セキュリティ グループ (NSG) のいずれかで十分に保護されています。

- [DevTest Labs の仮想ネットワークの構成](devtest-lab-configure-vnet.md)

- [Azure Firewall の概要](../web-application-firewall/overview.md)

- [Web アプリケーション ファイアウォールの概要](../virtual-network/network-security-groups-overview.md)

- [ネットワーク セキュリティの概要](security-baseline.md)

- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)

- [サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: DevTest Labs の一部として作成された Azure リソースの構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 組み込みの Azure Policy 定義を使用することもできます。

また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすかそれを超えるように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からの推奨事項を使用することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-4.8.0&amp;preserve-view=true)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: DevTest Labs の一部として作成されたすべての基になるコンピューティング リソースのセキュリティ構成を維持するには、Azure Security Center の推奨事項を使用します。 さらに、カスタム オペレーティング システム イメージまたは Azure Automation State Configuration または DevTest Labs の成果物を使用して、組織に必要なオペレーティング システムのセキュリティ構成を確立できます。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

- [VHD をアップロードし、それを使用して Azure で新しい Windows VM を作成する](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI を使用してカスタム ディスクから Linux VM を作成する](../virtual-machines/linux/upload-vhd.md)

- [カスタム イメージを作成して複数の DevTest Labs に配布する](image-factory-save-distribute-custom-images.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: DevTest Labs の一部として作成された Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の **deny** と **deploy if not exist** を使用します。 また、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: ラボの一部として作成された基になる Azure コンピューティング リソースに対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。 また、Azure Resource Manager テンプレート、カスタム オペレーティング システム イメージ、または Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を維持できます。 イメージ ファクトリ ソリューションを使用することもできます。これは、必要なすべての構成で定期的にイメージをビルドして配布する、コードとしての構成ソリューションです。

また、Microsoft によって公開された Azure Marketplace の仮想マシン イメージは、Microsoft によって管理および維持されます。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

- [VHD を Azure にアップロードし、新しい VM を作成するサンプル スクリプト](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

- [DevTest Labs でイメージ ファクトリを作成する方法](image-factory-create.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなどのコードを安全に格納して管理するには、Azure DevOps を使用します。 Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) (Azure DevOps に統合されている場合) で定義された特定のユーザー、組み込みセキュリティ グループ、またはグループに対するアクセス許可を付与または拒否できます。

- [Azure Repos Git チュートリアル](/azure/devops/repos/git/gitworkflow)

- [アクセス許可とグループについて](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions?view=azure-devops&amp;tabs=preview-page&amp;preserve-view=true)

- [Azure DevTest Labs と Azure DevOps ワークフローの統合](devtest-lab-dev-ops.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: カスタム イメージを使用している場合は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、確実に承認されたユーザーのみがイメージにアクセスできるようにします。 Shared Image Gallery を使用すると、それを必要とする特定のラボとイメージを共有できます。 コンテナー イメージの場合は、Azure Container Registry に保存し、Azure RBAC を使用して、承認されたユーザーだけがイメージにアクセスできるようにします。

- [Azure RBAC について](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Azure DevTest Labs 用の Shared Image Gallery を構成する](configure-shared-image-gallery.md)

- [コンテナー レジストリの Azure RBAC を理解する](../container-registry/container-registry-roles.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:Azure Policy を使用して、Azure リソースの標準的なセキュリティ構成を定義して実装します。 DevTest Labs で作成された Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 また、特定のリソースに関連する組み込みのポリシー定義を使用することもできます。 さらに、Azure Automation を使用して、構成の変更をデプロイすることもできます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 マシンのオンボード、宣言型構成の割り当て、指定した必要な状態に準拠した各マシンを示すレポートの表示を簡単に行うことができます。 また、組織のポリシーに確実に従うようにすべてのラボ コンピューターにインストールできるカスタム成果物を作成することもできます。 

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../automation/automation-dsc-onboarding.md)

- [DevTest Labs 仮想マシン用のカスタム成果物の作成](devtest-lab-artifact-author.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、DevTest Labs で作成された Azure リソースのベースライン スキャンを実行します。 さらに、Azure Policy を使用して Azure リソース構成をアラート送信および監査します。

- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、ラボで実行されているコンテナーの OS と Docker の設定のベースライン スキャンを実行します。

- [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

- [マネージド ID を構成して DevTest Labs に Azure Resource Manager 環境をデプロイする](use-managed-identities-environments.md)

- [マネージド ID を構成して DevTest Labs に仮想マシンをデプロイする](enable-managed-identities-lab-vms.md)

- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

- [マネージド ID で Key Vault の認証を提供する方法](../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [マネージド ID を構成して DevTest Labs に Azure Resource Manager 環境をデプロイする](use-managed-identities-environments.md)

- [マネージド ID を構成して DevTest Labs に仮想マシンをデプロイする](enable-managed-identities-lab-vms.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:Azure に対して Microsoft Antimalware を使用し、リソースの継続的な監視と防御を行います。 Linux の場合は、サード パーティのマルウェア対策ソリューションを使用します。  さらに、データ サービス向けの Azure Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。 

- [Azure に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md) 

- [Azure Security Center での脅威の防止](/azure/security-center/threat-protection)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure のサービス (例: ラボでホストされている Azure App Service) をサポートする、基になっているホストに対して有効になっていますが、コンテンツに対しては実行されません。 

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされるファイルは、事前にスキャンしてください。 

データ サービス向けの Azure Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。 

- [Azure 向け Microsoft Antimalware について](../security/fundamentals/antimalware.md) 

- [データ サービスに対する Azure Security Center の脅威検出について](/azure/security-center/security-center-alerts-data-services)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure 用 Microsoft Antimalware をデプロイすると、既定で、最新の署名、プラットフォーム、エンジンの更新プログラムが自動的にインストールされます。 Azure Security Center の "計算とアプリ" の推奨事項に従って、DevTest Labs の基になるコンピューティング リソースのすべてのエンドポイントが最新の署名を備え、最新の状態になっているようにします。 Azure Security Center と統合された Microsoft Defender Advanced Threat Protection サービスを使用して、ウイルスやマルウェアベースの攻撃のリスクを限定する追加のセキュリティによって、Windows OS の保護を強化できます。

- [Azure 向け Microsoft Antimalware をデプロイする方法](../security/fundamentals/antimalware.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: 現時点では、Azure DevTest Labs では VM のバックアップとスナップショットはサポートされていません。 ただし、DevTest Labs でホストされている基になる Azure VM で Azure Backup を有効にして構成することができます。 また、基になるコンピューティング リソースへの適切なアクセス権がある限り、自動バックアップに必要な頻度と保有期間を構成することもできます。 

- [Azure VM バックアップの概要](../backup/backup-azure-vms-introduction.md)

- [VM の設定から Azure VM をバックアップする](../backup/backup-azure-vms-first-look-arm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: 現時点では、Azure DevTest Labs では VM のバックアップとスナップショットはサポートされていません。 ただし、基になるコンピューティング リソースへの適切なアクセス権がある限り、PowerShell または REST API を使用して、DevTest Labs でホストされている基になる Azure VM のスナップショットまたはそれらのインスタンスに接続されているマネージド ディスクを作成することができます。 Azure Key Vault 内のすべてのカスタマー マネージド キーをバックアップすることもできます。

ターゲットの Azure VM で Azure Backup を有効にし、必要な頻度と保有期間を有効にします。 これには、完全なシステム状態のバックアップが含まれます。 Azure Disk Encryption を使用している場合は、Azure VM バックアップによって、カスタマー マネージド キーのバックアップが自動的に処理されます。

- [暗号化を使用する Azure VM でのバックアップ](../backup/backup-azure-vms-encryption.md)

- [Azure VM バックアップの概要](../backup/backup-azure-vms-introduction.md)

- [Azure VM バックアップの概要](../backup/backup-azure-vms-introduction.md)

- [Azure 上で Key Vault のキーをバックアップする方法](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Backup 内でコンテンツのデータ復元を定期的に実行できるようにします。 必要に応じて、分離された仮想ネットワークまたはサブスクリプションへのコンテンツの復元をテストします。 また、バックアップされたカスタマー マネージド キーの復元もテストします。

Azure Disk Encryption を使用している場合は、ディスク暗号化キーで Azure VM を復元できます。 ディスク暗号化を使用している場合は、ディスク暗号化キーで Azure VM を復元できます。

- [暗号化を使用する Azure VM でのバックアップ](../backup/backup-azure-vms-encryption.md)

- [Azure VM バックアップからファイルを回復する方法](../backup/backup-azure-restore-files-from-vm.md)

- [Azure でキー コンテナーのキーを復元する方法](https://docs.microsoft.com/powershell/module/az.keyvault/restore-azkeyvaultkey?view=azps-4.8.0&amp;preserve-view=true)

- [暗号化された VM をバックアップおよび復元する方法](../backup/backup-azure-vms-encryption.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Backup を使用してマネージド ディスクをバックアップするとき、保存中の VM は Storage Service Encryption (SSE) を使用して暗号化されます。 Azure Backup では、Azure Disk Encryption を使用して暗号化されている Azure VM もバックアップできます。 Azure Disk Encryption は、シークレットとしてキー コンテナーで保護されている BitLocker 暗号化キー (BEK) と統合されます。 Azure Disk Encryption は、Azure Key Vault キー暗号化キー (KEK) とも統合されます。 Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [VM の論理的な削除](../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault の論理的な削除の概要](../key-vault/general/soft-delete-overview.md)

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

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。 

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [タグを使用した Azure リソースの整理](/azure/azure-resource-manager/resource-group-using-tags)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。 

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。 

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。 

- [連続エクスポートを構成する方法](../security-center/continuous-export.md) 

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。 

- [Security Center でワークフロー自動化を構成する方法](../security-center/workflow-automation.md)

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
