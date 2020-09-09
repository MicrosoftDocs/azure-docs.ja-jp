---
title: Batch 用の Azure セキュリティ ベースライン
description: Batch 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 76312a55262d316c679bde2a69e8b98844addcb0
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963871"
---
# <a name="azure-security-baseline-for-batch"></a>Batch 用の Azure セキュリティ ベースライン

Batch 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: 仮想ネットワーク内に Azure Batch プールをデプロイします。 プールのコンピューティング ノードが他の仮想マシン、またはオンプレミス ネットワークと安全に通信できるようにするために、そのプールを Azure 仮想ネットワークのサブネット内にプロビジョニングできます。 また、プールを仮想ネットワーク内にデプロイすると、個々のノードのネットワーク インターフェイス (NIC) やサブネットをセキュリティで保護するために使用されるネットワーク セキュリティ グループ (NSG) の制御も可能になります。 その NSG を、インターネット上の信頼できる IP または場所からのトラフィックのみを許可するように構成します。

仮想ネットワーク内に Azure Batch プールを作成する方法:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:Vnet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Batch プールに関連付けられている仮想ネットワーク/ネットワーク セキュリティ グループ (NSG) に関連した Azure Security Center およびネットワーク保護の修復に関する推奨事項を使用します。 Batch プールを保護するために使用される NSG に関するフロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。 また、NSG フロー ログを Azure Log Analytics ワークスペースに送信し、Azure Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Azure Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Azure Security Center によって提供されるネットワークのセキュリティの概要:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃からの保護のために、Azure Batch プールを保護する仮想ネットワークで Azure DDoS (分散型サービス拒否) Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

DDoS 保護を構成する方法:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center の統合された脅威インテリジェンスの概要:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**: Azure Batch プールを保護するために使用されるネットワーク セキュリティ グループ (NSG) に関するフロー ログを有効にし、トラフィックの監査のために Azure Storage アカウントにログを送信します。

NSG フロー ログを有効にする方法:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システムをデプロイする

**ガイダンス**: コンプライアンスのために必要な場合は、Azure Marketplace から、ペイロード検査機能と共に侵入検出システム (IDS) および侵入防止システム (IPS) 機能をサポートするネットワーク仮想アプライアンスを選択します。

ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

パブリック IP アドレスを持つ Azure Firewall を Azure Batch プール ノードと同じ仮想ネットワーク内にデプロイします。 インターネット上の信頼できる場所と個々のプール ノードのプライベート IP アドレスの間のネットワーク アドレス変換 (NAT) 規則を構成します。 Azure Firewall の [脅威インテリジェンス] で [警告して拒否] を構成して、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告してブロックします。 これらの IP アドレスおよびドメインのソースは Microsoft の脅威インテリジェンス フィードであり、最も信頼度の高いレコードのみが含まれます。 

仮想ネットワーク内に Azure Batch プールを作成する方法:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure Firewall をデプロイする方法:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。ベンチマークは、Azure App Service または IaaS インスタンス上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Batch プールに関連付けられているネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

サービス タグとその使用方法の概要:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を保持する

**ガイダンス**: Azure Policy を使用して、Azure Batch プールに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Batch プールのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" および "Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Batch プールに関連付けられているネットワーク セキュリティおよびトラフィック フローに関連したネットワーク サービス グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

仮想ネットワークを作成する方法:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

NSG を作成する方法:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: ネットワーク リソース構成を監視し、Azure Batch プールに関連したネットワーク リソースの変更を検出するには、Azure アクティビティ ログを使用します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Azure アクティビティ ログ イベントを表示して取得する方法: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Azure Monitor でアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Azure Batch では、既定では Microsoft が時刻同期を提供します。ただし、特定の時刻同期要件がある場合は、これらの変更を実装できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: クラスター デバイスによって生成されたセキュリティ データを集計するには、Azure Batch アカウントを Azure Monitor にオンボードします。 カスタム クエリを利用して、環境内の脅威を検出し、それに対応します。  Azure Batch リソース レベルの監視では、Batch API を使用して各リソース (ジョブ、タスク、ノード、プールなど) の状態の監視またはクエリを実行します。

Azure Batch アカウントを Azure Monitor にオンボードする方法:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Batch アカウント レベルの監視では、Azure Monitor の機能を使用して各 Batch アカウントを監視します。 Azure Monitor では、Batch アカウントのレベルでスコープ指定されたリソース (プール、ジョブ、タスクなど) のメトリックと、オプションで診断ログが収集されます。 このデータを手動またはプログラムによって収集および利用して、Batch アカウント内のアクティビティを監視し、問題を診断します。

Azure Batch リソース レベルの監視では、Azure Batch API を使用して各リソース (ジョブ、タスク、ノード、プールなど) の状態の監視またはクエリを実行します。

Azure Batch アカウント レベルの監視とログ記録を構成する方法:

https://docs.microsoft.com/azure/batch/monitoring-overview

Batch リソース レベルの監視の概要:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-system"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure Monitor では、Azure Batch アカウント内のリソースのメトリックと診断ログが収集されます。 これらのデータを収集し、さまざまな方法で消費して Azure Batch アカウントを監視し、問題を診断します。 また、メトリックが指定された値に達したら通知を受信するようにメトリック アラートを構成することもできます。

必要に応じて、Secure Shell (SSH) またはリモート デスクトップ プロトコル (RDP) 経由で個々のプール ノードに接続して、ローカル オペレーティング システム ログにアクセスできます。

Azure Batch アカウントから診断ログを収集する方法:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Azure Batch プール ノードにリモート接続する方法:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログの保有期間を構成する

**ガイダンス**: Azure Batch アカウントを Azure Monitor にオンボードします。 使用される Azure Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

Azure Batch の監視とログ記録を構成する方法:

https://docs.microsoft.com/azure/batch/monitoring-overview

Azure Log Analytics ワークスペースの保有期間を構成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: 指定されたメトリックの値が特定のしきい値を超えたらトリガーされる Azure Batch メトリック アラートを作成します。

Azure Batch メトリック アラートを構成する方法:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 指定されたメトリックの値が特定のしきい値を超えたらトリガーされる Azure Batch メトリック アラートを作成します。

Azure Batch メトリック アラートを構成する方法:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Batch ノードで Windows Defender を使用します。Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: DNS ログ用のサードパーティ ソリューションを実装します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンド ライン監査のログ記録を有効にする

**ガイダンス**: ノードごとにコンソール ログ記録と PowerShell トランスクリプションを手動で構成します。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 管理者アカウントのインベントリを保持する

**ガイダンス**: Azure Batch プールのプロビジョニング中に作成されたローカル管理者アカウントや、作成したその他のすべてのアカウントのレコードを保持します。 さらに、Azure Active Directory (AAD) 統合が使用されている場合、AAD には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 アドホック クエリを実行して、管理グループのメンバーであるアカウントを検出するには、AAD PowerShell モジュールを使用します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

PowerShell を使用して AAD でディレクトリ ロールを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して AAD でディレクトリ ロールのメンバーを取得する方法:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Batch プールをプロビジョニングしている場合は、ローカル コンピューター アカウントを作成するオプションが与えられます。 変更すべき既定のパスワードはありませんが、Secure Shell (SSH) とリモート デスクトップ プロトコル (RDP) のアクセスに対して異なるパスワードを指定できます。 Azure Batch プールが構成されたら、Azure portal 内で、または Azure Resource Manager API 経由で個々のノードに対してランダム ユーザーを生成できます。

特定のコンピューティング ノードにユーザーを追加する方法:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントの使用を保証する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory と統合します。 専用管理者アカウントの使用に関するポリシーと手順を作成します。

さらに、Azure Security Center ID とアクセス管理の推奨事項を使用できます。

Azure Active Directory を使用して Batch アプリケーションを認証する方法:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Azure Active Directory でシングル サインオン (SSO) を利用する

**ガイダンス**: 適用できません。Azure Batch は Azure AD 認証をサポートしていますが、シングル サインオンはサポートされていません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory (AAD) と統合します。 AAD 多要素認証 (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

 

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center で ID とアクセスを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用のコンピューター (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証 (MFA) が構成された PAW (特権アクセス ワークステーション) を使用してログインし、Azure Batch リソースを構成します。

特権アクセス ワークステーションについて:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory (AAD) と統合している場合は、環境内で疑わしいアクティビティや安全でないアクティビティが発生したとき、ログとアラートの生成に Azure Active Directory セキュリティ レポートを使用します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

危険なアクティビティのフラグが設定された Azure AD ユーザーを特定する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

Azure Security Center でユーザーの ID とアクセス アクティビティを監視する方法:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8:承認された場所からの Azure リソースのみを管理する

**ガイダンス**: Azure Batch アプリケーションの認証を Azure Active Directory と統合している場合は、条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可できます。

Azure でネームド ロケーションを構成する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (AAD) を中央の認証および承認システムとして使用し、Azure Batch アプリケーションの認証を AAD と統合します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、AAD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

AAD インスタンスを作成して構成する方法:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

AAD を使用して Batch アプリケーションを認証する方法:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AAD) では、古いアカウントの検出に役立つログが提供されます。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューして、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Azure ID アクセス レビューの使用方法:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Azure Log Analytics ワークスペースに送信します。 Azure Log Analytics ワークスペース内に目的のアラートを構成します。

Azure Activity Logs を Azure Monitor に統合する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラート

**ガイダンス**: ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (AAD) リスク検出および Identity Protection 機能を使用します。 また、さらに詳しく調査するためにデータを Azure Sentinel に取り込むこともできます。

AAD の危険なサインインを表示する方法:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

ID Protection のリスク ポリシーを構成して有効にする方法:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel をオンボードする方法:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする<br></div>

**ガイダンス**: 使用できません。Azure Batch ではまだカスタマー ロックボックスがサポートされていません。
 
カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Batch プールは仮想ネットワーク/サブネットで分離し、適切にタグ付けして、ネットワーク セキュリティ グループ (NSG) を使用してセキュリティで保護する必要があります。 Azure Batch データは、セキュリティで保護された Azure Storage アカウント内に含まれている必要があります。

仮想ネットワーク内に Azure Batch プールを作成する方法:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Azure Storage アカウントをセキュリティで保護する方法:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報を含む Azure Batch プールに関連付けられている Azure Storage アカウントの場合は、タグを使用してそれを機密としてマークし、Azure ベスト プラクティスを使用してセキュリティで保護します。

Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure Storage アカウントをセキュリティで保護する方法:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Microsoft Azure リソースは、既定では TLS 1.2 をネゴシエートします。 Azure Batch プールまたはデータ ストア (Azure Storage アカウント) に接続しているすべてのクライアントが TLS 1.2 以降をネゴシエートできることを確認します。

Azure Batch データを含むストレージ アカウントにアクセスするには HTTPS が必要であることを確認します。

Azure Storage アカウントの転送中の暗号化の概要:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 機密情報を含む Azure Batch プールに関連付けられている Azure Storage アカウントの場合は、タグを使用してそれを機密としてマークし、Azure ベスト プラクティスを使用してセキュリティで保護します。

Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Azure Storage アカウントをセキュリティで保護する方法:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Batch アカウント、Batch プール、ストレージ アカウントを含む Azure リソースの管理プレーンへのアクセスを制御するには、Azure Active Directory (AAD) のロールベースのアクセス制御 (RBAC) を使用します。

Azure RBAC を理解する:

https://docs.microsoft.com/azure/role-based-access-control/overview

Azure で RBAC を構成する方法:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホスト ベースのデータ損失防止を使用してアクセス制御を適用する

**ガイダンス**: Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure での顧客データの保護の概要:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Batch アカウントに関連付けられているストレージ アカウントの場合は、Microsoft が暗号化キーを管理できるようにすることをお勧めします。ただし、必要に応じて、独自のキーを管理するオプションがあります。

Azure Storage アカウントの暗号化キーを管理する方法:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Batch アカウント/プールに関連した、または関連付けられている重要な Azure リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

Azure Batch プールに関連付けられているストレージ アカウントの診断設定を、プール データに対するすべての CRUD 操作を監視してログに記録するように構成します。

Azure アクティビティ ログ イベントのアラートを作成する方法:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure Storage アカウントの追加のログ記録/監査を有効にする方法:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure Batch プール ノードの場合は、脆弱性管理ソリューションを管理する責任をユーザーが負います。

必要に応じて、Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションを持っている場合は、Batch プール ノードに脆弱性評価エージェントを手動でインストールし、各ポータルからノードを管理できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Microsoft は、Azure Batch プール ノードの基本イメージを管理および更新します。 Azure Batch プール ノードのオペレーティング システムが、クラスターの有効期間にわたって確実に修正プログラムが適用された状態に維持されるようにします。これには、自動更新の有効化、ノードの監視、または定期的な再起動の実行が必要になることがあります。


**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:自動化されたサードパーティ ソフトウェア修正プログラム管理ソリューションをデプロイする

**ガイダンス**: Azure Batch プール ノードのサードパーティ アプリケーションが、クラスターの有効期間にわたって確実に修正プログラムが適用された状態に維持されるようにします。これには、自動更新の有効化、ノードの監視、または定期的な再起動の実行が必要になることがあります。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Rapid7、Qualys、またはその他の脆弱性管理プラットフォーム サブスクリプションを持っている場合は、そのベンダーのポータルを使用してバックツーバックの脆弱性スキャンを表示および比較できます。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける。

**ガイダンス**: 一般的なリスク スコアリング プログラム (Common Vulnerability Scoring System など)、またはサードパーティのスキャン ツールによって提供される既定のリスク評価を使用します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワークなど) のクエリまたは検出を実行するには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager (ARM) リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを確認する方法:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC を理解する:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加の Azure サブスクリプションを作成する方法:

https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法:

https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを保持する

**ガイダンス**: コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアの一覧を定義します。


**Azure Security Center の監視**: 適用なし

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

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: Azure Batch プール ノードの場合は、承認されていないファイルの種類の実行を防止するためのサードパーティ ソリューションを実装します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する</div>

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内のスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。

Azure Batch プールのユーザー (管理者以外のユーザー) は、ジョブを実行するために個々のノードへのアクセスを必要としないため、これは Azure Batch には適用できません。 クラスター管理者には、既にすべてのノードへのルート アクセス権があります。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。ベンチマークは、Azure App Service または IaaS インスタンス上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースのセキュリティで保護された構成を確立する

**ガイダンス**: Azure Batch アカウントおよびプールの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。

使用可能な Azure Policy エイリアスを表示する方法:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2:オペレーティング システムのセキュリティで保護された構成を確立する

**ガイダンス**: Batch プール ノードのオペレーティング システムのセキュリティで保護された構成を確立します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3:すべての Azure リソースのセキュリティで保護された構成を保持する

**ガイダンス**: ご自分の Batch アカウントおよびプールに関連した Azure リソース (仮想ネットワーク、サブネット、Azure Firewall、Azure Storage アカウントなど) にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。 次の名前空間の Azure Policy エイリアスを使用してカスタム ポリシーを作成できます。

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果の概要: https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4:オペレーティング システムのセキュリティで保護された構成を保持する

**ガイダンス**: Azure Batch プールのオペレーティング システム イメージは、Microsoft によって管理および保守されます。 OS レベルの状態構成を実装する責任はユーザーが負います。

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:お使いの Azure Batch アカウント、プール、または関連リソースにカスタマイズした Azure Policy 定義を使用している場合は、Azure Repos を使用してご自分のコードを安全に格納して管理します。

Azure DevOps にコードを格納する方法:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos のドキュメント:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Azure Batch プールにカスタム イメージを使用している場合は、ロールベースのアクセス制御 (RBAC) を使用して、承認されたユーザーのみがイメージにアクセスできるようにします。

Azure での RBAC を理解する:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Azure で RBAC を構成する方法:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Batch 関連のリソース構成を警告処理、監査、適用するには、組み込みの Azure Policy 定義を使用します。  Azure Batch アカウントおよびプールのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: Azure Batch プール ノードのオペレーティング システムの望ましい状態を保持するためのサードパーティ ソリューションを実装します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Batch インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Batch" 名前空間で Azure Policy エイリアスを使用します。 また、Azure Batch や Azure Batch によって使用されるリソース用に特別に作成された次のような組み込みのポリシーのいずれかを使用することもできます。

- サブネットはネットワーク セキュリティ グループに関連付けられている必要がある

-ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要がある

- Batch アカウントで診断ログを有効にする必要がある

使用可能な Azure Policy エイリアスを表示する方法: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Batch プール ノードのオペレーティング システムの状態を監視するためのサードパーティ ソリューションを実装します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="711-securely-manage-azure-secrets"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Batch のデプロイで Azure Key Vault を使用すると、Azure Storage アカウント内のプール ストレージのキーを管理できます。

Azure マネージド ID と統合する方法:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Azure Key Vault を作成する方法:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-securely-and-automatically-manage-identities"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: 使用できません。Azure Batch ではマネージド サービス ID がサポートされていません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Azure Batch プール ノードで Windows Defender を使用します。Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Antimalware は、Azure サービス (Azure Batch など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。Microsoft は、これらのインスタンス内の顧客データにアクセスできません。

Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Windows オペレーティング システムの場合は、個々の Azure Batch プール ノードで Windows Defender を使用し、自動更新が確実に有効になるようにします。 Linux を使用している場合は、独自のマルウェア対策ソリューションを指定します。


**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Batch プール データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。 

Azure Storage アカウントのストレージ冗長性を構成する方法:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Batch プール データ ストアに Azure Storage アカウントを使用している場合は、適切な冗長性オプション (LRS、ZRS、GRS、RA-GRS) を選択します。  Azure Batch のデプロイのいずれかの部分に Azure Key Vault を使用している場合は、キーが確実にバックアップされるようにしてください。

Azure Storage アカウントのストレージ冗長性を構成する方法:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

Azure で Key Vault のキーをバックアップする方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Storage アカウントまたは Azure Batch の実装に関連したその他のリソースのための独自のキーを管理している場合は、バックアップされたキーの復元を定期的にテストします。

Azure で Key Vault のキーをバックアップする方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

PowerShell を使用してカスタマー マネージド キーを復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Key Vault が Azure Batch プール ストレージ アカウントに関連したキーを保持するために使用されている場合は、偶発的な削除や悪意のある削除からキーを保護するために、Azure Key Vault で論理的な削除を有効にします。

Azure Key Vault で論理的な削除を有効にする方法:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 担当者の役割を定義している記述されたインシデント対応計画があること、およびインシデント処理/管理のフェーズが存在することを確認します。

Azure Security Center 内でワークフロー自動化を構成する方法:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center は、アラートに重要度を割り当て、各アラートに対処する優先順位を付けることができます。これにより、リソースが侵害されたときに、すぐにアクセスできるようになります。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT の計画と機能に関するテスト、トレーニング、演習プログラムのガイド: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成する&nbsp;

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または承認されていないパーティによってアクセスされたことが検出された場合はユーザーに連絡するために、Microsoft によって使用されます。

Azure Security Center のセキュリティ連絡先を設定する方法:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

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

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの定期的な侵入テストを実行し、すべての重要なセキュリティ調査結果を 60 日以内に確実に修復する

**ガイダンス**: 侵入テストが Microsoft のポリシーに違反しないようにするために、Microsoft の活動規則に従ってください。

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、次を参照してください。 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
