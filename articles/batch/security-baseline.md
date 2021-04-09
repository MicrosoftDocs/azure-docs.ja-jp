---
title: Batch 用の Azure セキュリティ ベースライン
description: Batch セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: batch
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d8ef3efcda55868abec188c10ef904ae40a56722
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728146"
---
# <a name="azure-security-baseline-for-batch"></a>Batch 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Batch に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、Batch に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Batch に適用できない **コントロール** は、除外されています。

 
Batch を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Batch セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:仮想ネットワーク内に Azure Batch プールをデプロイします。 プールのコンピューティング ノードが他の仮想マシン、またはオンプレミス ネットワークと安全に通信できるようにするために、そのプールを Azure 仮想ネットワークのサブネット内にプロビジョニングできます。 また、プールを仮想ネットワーク内にデプロイすると、個々のノードのネットワーク インターフェイス (NIC) やサブネットをセキュリティで保護するために使用されるネットワーク セキュリティ グループ (NSG) の制御も可能になります。 その NSG を、インターネット上の信頼できる IP または場所からのトラフィックのみを許可するように構成します。

必要に応じて、Azure Private Link を使用してパブリック ネットワーク アクセスを無効にし、プライベート エンドポイント経由で Azure Batch アカウントに接続します。 Azure Private Link サービスはセキュリティで保護されており、認証および承認済みのプライベート エンドポイントからの接続のみを受け入れます。 Batch プール内のコンピューティング ノードに対して公開されている RDP/SSH エンドポイントを無効にすることで、接続性と検出可能性をさらに制限できます。

- [仮想ネットワーク内に Azure Batch プールを作成する方法](batch-virtual-network.md)

- [ネットワーク アクセスを無効にして Azure Batch アカウントを作成する方法](private-connectivity.md)

- [プライベート エンドポイントを作成する方法](../private-link/create-private-endpoint-portal.md)

- [RDP/SSH トラフィックへのアクセスを拒否する方法](pool-endpoint-configuration.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Batch プールに関連付けられている仮想ネットワーク/ネットワーク セキュリティ グループ (NSG) に関連した Azure Security Center およびネットワーク保護の修復に関する推奨事項を使用します。 Batch プールを保護するために使用される NSG に関するフロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。 また、NSG フロー ログを Azure Log Analytics ワークスペースに送信し、Azure Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Azure Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃からの保護のために、Azure Batch プールを保護する仮想ネットワークで Azure DDoS (分散型サービス拒否) Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Batch プールを保護するために使用されるネットワーク セキュリティ グループ (NSG) に関するフロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: コンプライアンスのために必要な場合は、Azure Marketplace から、ペイロード検査機能と共に侵入検出システム (IDS) および侵入防止システム (IPS) 機能をサポートするネットワーク仮想アプライアンスを選択します。

ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

パブリック IP アドレスを持つ Azure Firewall を Azure Batch プール ノードと同じ仮想ネットワーク内にデプロイします。 インターネット上の信頼できる場所と個々のプール ノードのプライベート IP アドレスの間のネットワーク アドレス変換 (NAT) 規則を構成します。 Azure Firewall の [脅威インテリジェンス] で [警告して拒否] を構成して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告してブロックします。 これらの IP アドレスおよびドメインのソースは Microsoft の脅威インテリジェンス フィードであり、最も信頼度の高いレコードのみが含まれます。 

- [仮想ネットワーク内に Azure Batch プールを作成する方法](batch-virtual-network.md)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Batch プールに関連付けられているネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグとその使用方法の概要](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Batch プールに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Batch プールのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" および "Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Batch プールに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク サービス グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、Azure Batch プールに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log) 

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: クラスター デバイスによって生成されたセキュリティ データを集計するには、Azure Batch アカウントを Azure Monitor にオンボードします。 カスタム クエリを利用して、環境内の脅威を検出し、それに対応します。  Azure Batch リソース レベルの監視では、Batch API を使用して各リソース (ジョブ、タスク、ノード、プールなど) の状態の監視またはクエリを実行します。

- [Azure Batch アカウントを Azure Monitor にオンボードする方法](batch-diagnostics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Batch アカウント レベルの監視では、Azure Monitor の機能を使用して各 Batch アカウントを監視します。 Azure Monitor では、Batch アカウントのレベルでスコープ指定されたリソース (プール、ジョブ、タスクなど) のメトリックと、オプションで診断ログが収集されます。 このデータを手動またはプログラムによって収集および利用して、Batch アカウント内のアクティビティを監視し、問題を診断します。

Azure Batch リソース レベルの監視では、Azure Batch API を使用して各リソース (ジョブ、タスク、ノード、プールなど) の状態の監視またはクエリを実行します。

- [Azure Batch アカウント レベルの監視とログ記録を構成する方法](monitoring-overview.md)

- [Batch リソース レベルの監視の概要](monitoring-overview.md#batch-resource-monitoring)

**Azure Security Center の監視**: はい

**責任**: Customer

#### <a name="azure-policy-built-in-definitions"></a>Azure Policy の組み込み定義

[!INCLUDE [microsoft.batch-2-3](../../includes/policy/standards/asb/rp-controls/microsoft.batch-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure Monitor では、Azure Batch アカウント内のリソースのメトリックと診断ログが収集されます。 これらのデータを収集し、さまざまな方法で消費して Azure Batch アカウントを監視し、問題を診断します。 また、メトリックが指定された値に達したら通知を受信するようにメトリック アラートを構成することもできます。

必要に応じて、Secure Shell (SSH) またはリモート デスクトップ プロトコル (RDP) 経由で個々のプール ノードに接続して、ローカル オペレーティング システム ログにアクセスできます。

- [Azure Batch アカウントから診断ログを収集する方法](batch-diagnostics.md#batch-diagnostics)

- [Azure Batch プール ノードにリモート接続する方法](./batch-service-workflow-features.md#basic-workflow)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Batch アカウントを Azure Monitor にオンボードします。 使用される Azure Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

- [Azure Batch の監視とログ記録を構成する方法](monitoring-overview.md)

- [Azure Log Analytics ワークスペースの保有期間を構成する方法](../azure-monitor/logs/manage-cost-storage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 指定されたメトリックの値が特定のしきい値を超えたらトリガーされる Azure Batch メトリック アラートを作成します。

- [Azure Batch メトリック アラートを構成する方法](batch-diagnostics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 指定されたメトリックの値が特定のしきい値を超えたらトリガーされる Azure Batch メトリック アラートを作成します。

- [Azure Batch メトリック アラートを構成する方法](batch-diagnostics.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Batch ノードで Windows Defender を使用します。Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**:DNS ログ記録用のサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: ノードごとにコンソール ログ記録と PowerShell トランスクリプションを手動で構成します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Batch プールのプロビジョニング中に作成されたローカル管理者アカウントや、作成したその他のすべてのアカウントのレコードを保持します。 さらに、Azure Active Directory 統合が使用されている場合、Azure AD には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Batch プールをプロビジョニングしている場合は、ローカル コンピューター アカウントを作成するオプションが与えられます。 変更すべき既定のパスワードはありませんが、Secure Shell (SSH) とリモート デスクトップ プロトコル (RDP) のアクセスに対して異なるパスワードを指定できます。 Azure Batch プールが構成されたら、Azure portal 内で、または Azure Resource Manager API 経由で個々のノードに対してランダム ユーザーを生成できます。

- [特定のコンピューティング ノードにユーザーを追加する方法](/rest/api/batchservice/computenode/adduser)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory と統合します。 専用管理者アカウントの使用に関するポリシーと手順を作成します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

- [Azure Active Directory を使用して Batch アプリケーションを認証する方法](batch-aad-auth.md)

- [Azure Security Center を使用して ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory と統合します。 Azure AD の多要素認証を有効にし、Azure Security Center の ID とアクセス管理の推奨事項に従います。

 

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**:多要素認証が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure Batch リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Batch アプリケーションの認証を Azure Active Directory と統合している場合は、環境内で疑わしいアクティビティや安全でないアクティビティが発生したとき、ログとアラートの生成に Azure Active Directory セキュリティ レポートを使用します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory と統合している場合は、条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可できます。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory を中央の認証および承認システムとして使用し、Azure Batch アプリケーションの認証を Azure AD と統合します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

- [Azure AD インスタンスを作成して構成する方法](../active-directory-domain-services/tutorial-create-instance.md)

- [Azure AD を使用して Batch アプリケーションを認証する方法](batch-aad-auth.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューして、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Azure Log Analytics ワークスペース内に目的のアラートを構成します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory リスク検出および Identity Protection 機能を使用します。 また、さらに調査するためにデータを Azure Sentinel に取り込むこともできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする  

**ガイダンス**: 使用できません。Azure Batch ではまだカスタマー ロックボックスがサポートされていません。
 
- [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Batch プールは仮想ネットワーク/サブネットで分離し、適切にタグ付けして、ネットワーク セキュリティ グループ (NSG) を使用してセキュリティで保護する必要があります。 Azure Batch データは、セキュリティで保護された Azure Storage アカウント内に含まれている必要があります。

- [仮想ネットワーク内に Azure Batch プールを作成する方法](batch-virtual-network.md)

- [Azure Storage アカウントをセキュリティで保護する方法](../storage/blobs/security-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報を含む Azure Batch プールに関連付けられている Azure Storage アカウントの場合は、タグを使用してそれを機密としてマークし、Azure ベスト プラクティスを使用してセキュリティで保護します。

Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

- [Azure Storage アカウントをセキュリティで保護する方法](../storage/blobs/security-recommendations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Microsoft Azure リソースは、既定では TLS 1.2 をネゴシエートします。 Azure Batch プールまたはデータ ストア (Azure Storage アカウント) に接続しているすべてのクライアントが TLS 1.2 以降をネゴシエートできることを確認します。

Azure Batch データを含むストレージ アカウントにアクセスするには HTTPS が必要であることを確認します。

- [Azure Storage アカウントの転送中の暗号化の概要](../storage/blobs/security-recommendations.md)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 機密情報を含む Azure Batch プールに関連付けられている Azure Storage アカウントの場合は、タグを使用してそれを機密としてマークし、Azure ベスト プラクティスを使用してセキュリティで保護します。

Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

- [Azure Storage アカウントをセキュリティで保護する方法](../storage/blobs/security-recommendations.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Batch アカウント、Batch プール、ストレージ アカウントを含む Azure リソースの管理プレーンへのアクセスを制御するには、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。

- [Azure RBAC について](../role-based-access-control/overview.md)

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Batch アカウントに関連付けられているストレージ アカウントの場合は、Microsoft が暗号化キーを管理できるようにすることをお勧めします。ただし、必要に応じて、独自のキーを管理するオプションがあります。

Azure ディスク暗号化を使用すると、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすことができます。 すべてのマネージド ディスク、スナップショット、イメージ、および既存のディスクに書き込まれるデータは、保存時に、プラットフォーム マネージド キーを使用して自動的に暗号化されます。

- [Azure Storage アカウントの暗号化キーを管理する方法](../storage/common/customer-managed-keys-configure-key-vault.md)

- [カスタマー マネージド暗号化キーを構成する方法](../storage/common/customer-managed-keys-configure-key-vault.md)

- [ディスク暗号化が有効になっているプールを作成する方法](disk-encryption.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Batch アカウント/プールに関連した、または関連付けられている重要な Azure リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

Azure Batch プールに関連付けられているストレージ アカウントの診断設定を、プール データに対するすべての CRUD 操作を監視してログに記録するように構成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage アカウントでの追加のログ記録/監査を有効にする方法](../storage/common/manage-storage-analytics-logs.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure Batch プール ノードの場合は、脆弱性管理ソリューションを管理する責任をユーザーが負います。

必要に応じて、Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションを持っている場合は、Batch プール ノードに脆弱性評価エージェントを手動でインストールし、各ポータルからノードを管理できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Microsoft は、Azure Batch プール ノードの基本イメージを管理および更新します。 Azure Batch プール ノードのオペレーティング システムが、クラスターの有効期間にわたって確実に修正プログラムが適用された状態に維持されるようにします。これには、自動更新の有効化、ノードの監視、または定期的な再起動の実行が必要になることがあります。

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: Azure Batch プール ノードのサードパーティ アプリケーションが、クラスターの有効期間にわたって確実に修正プログラムが適用された状態に維持されるようにします。これには、自動更新の有効化、ノードの監視、または定期的な再起動の実行が必要になることがあります。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションを持っている場合は、そのベンダーのポータルを使用してバックツーバックの脆弱性スキャンを表示および比較できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**:一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワークなど) のクエリまたは検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Azure Resource Graph Explorer で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアの一覧を定義します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

サブスクリプション内のリソースのクエリまたは検出を行うには、Azure Resource Graph を使用します。 環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure Batch プール ノードの場合は、承認されていないソフトウェア アプリケーションがないかどうかクラスター ノードを監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Batch プール ノードの場合は、承認されていないソフトウェア アプリケーションがないかどうかクラスター ノードを監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: Azure Batch プール ノードの場合は、承認されていないソフトウェアの実行を防止するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。 
- 許可されないリソースの種類 
- 許可されるリソースの種類 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: Azure Batch プール ノードの場合は、承認されていないファイルの種類の実行を防止するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Batch アカウントおよびプールの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: Batch プール ノードのオペレーティング システムのセキュリティで保護された構成を確立します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: ご自分の Batch アカウントおよびプールに関連した Azure リソース (仮想ネットワーク、サブネット、Azure Firewall、Azure Storage アカウントなど) にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。 次の名前空間の Azure Policy エイリアスを使用してカスタム ポリシーを作成できます。

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Batch プールのオペレーティング システム イメージは、Microsoft によって管理および保守されます。 OS レベルの状態構成を実装する責任はユーザーが負います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:お使いの Azure Batch アカウント、プール、または関連リソースにカスタマイズした Azure Policy 定義を使用している場合は、Azure Repos を使用してご自分のコードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Azure Batch プールにカスタム イメージを使用している場合は、ロールベースのアクセス制御 (RBAC) を使用して、承認されたユーザーのみがイメージにアクセスできるようにします。

- [Azure での RBAC の概要](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure で RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: Azure Batch 関連のリソース構成を警告処理、監査、適用するには、組み込みの Azure Policy 定義を使用します。  Azure Batch アカウントおよびプールのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: Azure Batch プール ノードのオペレーティング システムの望ましい状態を保持するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Batch インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。 また、Azure Batch や Azure Batch によって使用されるリソース用に特別に作成された次のような組み込みのポリシーのいずれかを使用することもできます。
- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある -ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要がある
- Batch アカウントで診断ログを有効にする必要がある

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Batch プール ノードのオペレーティング システムの状態を監視するためのサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Batch のデプロイで Azure Key Vault を使用すると、Azure Storage アカウント内のプール ストレージのキーを管理できます。

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Azure キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

- [Key Vault に対して認証を行う方法](../key-vault/general/authentication.md)
- [Key Vault のアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Azure Batch プール ノードで Windows Defender を使用します。Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure サービス (Azure Batch など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内の顧客データにアクセスできません。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Azure Batch プール ノードで Windows Defender を使用し、自動更新が確実に有効になるようにします。 Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Batch プール データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。 

- [Azure Storage アカウントのストレージ冗長性を構成する方法](../storage/common/storage-redundancy.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Batch プール データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。 Azure Batch のデプロイのいずれかの部分に Azure Key Vault を使用している場合は、キーが確実にバックアップされるようにしてください。

- [Azure Storage アカウントのストレージ冗長性を構成する方法](../storage/common/storage-redundancy.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Storage アカウントまたは Azure Batch の実装に関連したその他のリソースのための独自のキーを管理している場合は、バックアップされたキーの復元を定期的にテストします。

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

- [PowerShell を使用してカスタマー マネージド キーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey?preserve-view=true&view=azps-4.8.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Key Vault が Azure Batch プール ストレージ アカウントに関連したキーを保持するために使用されている場合は、偶発的な削除や悪意のある削除からキーを保護するために、Azure Key Vault で論理的な削除を有効にします。

- [Azure Key Vault で論理的な削除を有効にする方法](../key-vault/general/key-vault-recovery.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 担当者の役割を定義している記述されたインシデント対応計画があること、およびインシデント処理/管理のフェーズが存在することを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**:Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または承認されていないパーティによってアクセスされたことが検出された場合はユーザーに連絡するために、Microsoft によって使用されます。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: - [Microsoft の実施ルールに従って、侵入テストが Microsoft のポリシーに違反しないようにしてください](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、以下を参照してください。 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する