---
title: Azure Data Factory 用の Azure セキュリティ ベースライン
description: Azure Data Factory セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: d1cbd314861a4d5079a5c8e5213f45c7164344f0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204169"
---
# <a name="azure-security-baseline-for-azure-data-factory"></a>Azure Data Factory 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Azure Data Factory に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Data Factory に適用できる関連ガイダンスによって定義されています。 Azure Data Factory に適用できない **制御** は、除外されています。

Azure Data Factory を完全に Azure セキュリティ ベンチマークにマップする方法については、[完全な Azure Data Factory セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure-SSIS Integration Runtime (IR) を作成する場合、それを仮想ネットワークに参加させるオプションがあります。 これにより、Azure Data Factory でネットワーク セキュリティ グループ (NSG) やロード バランサーなどの特定のネットワーク リソースを作成できるようになります。 また、独自の静的パブリック IP アドレスを提供することも、Azure Data Factory に自動的に作成させることもできます。  Azure Data Factory によって自動的に作成された NSG では、ポート 3389 は既定ですべてのトラフィックに対して開かれます。 これをロックダウンして、管理者のみがアクセスできるようにします。

セルフホステッド IR は、オンプレミスのマシンか、仮想ネットワーク内の Azure 仮想マシンにデプロイできます。 仮想ネットワーク サブネットのデプロイに、管理アクセスのみを許可するように構成された NSG があることを確認します。 Azure-SSIS IR では、保護のために各 IR ノードに対する Windows ファイアウォール規則で、既定でポート 3389 の送信が許可されていません。 仮想ネットワークで構成されたリソースをセキュリティで保護するには、NSG をサブネットに関連付け、厳格な規則を設定します。

Private Link が使用可能な場合は、プライベート エンドポイントを使用して、Azure Data Factory パイプラインにリンクされているすべてのリソース (Azure SQL Server など) をセキュリティで保護します。 Private Link では、仮想ネットワークとサービス間のトラフィックは、Microsoft のバックボーン ネットワーク経由で転送されるため、パブリック インターネットから公開されることがなくなります。

- [Azure-SSIS IR を作成する方法](create-azure-ssis-integration-runtime.md)

- [セルフホステッド IR を作成および構成する方法](create-self-hosted-integration-runtime.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)

- [Azure Private Link について](../private-link/private-link-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用して、Integration Runtime デプロイに関連付けられている仮想ネットワークとネットワーク セキュリティ グループに対するネットワーク保護のレコメンデーションを修復します。

さらに、ネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にして、Integration Runtime デプロイを保護し、トラフィックの監査用に Azure Storage アカウントにログを送信します。

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 分散型サービス拒否攻撃からの保護のために、お使いの Integration Runtime デプロイに関連付けられている仮想ネットワークで DDoS Protection Standard を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: ネットワーク セキュリティ グループ (NSG) の NSG フロー ログを有効にして、Integration Runtime デプロイを保護し、トラフィックの監査用に Azure Storage アカウントにログを送信します。

また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: Azure-SSIS IR からの送信トラフィックを検査する場合は、Azure-SSIS IR から開始されたトラフィックを、Azure ExpressRoute 強制トンネリングを使用して、オンプレミス ファイアウォール アプライアンスに、または IDS/IPS 機能をサポートする Azure Marketplace から、ネットワーク仮想アプライアンス (NVA) にルーティングできます。 ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。

- [Azure-SSIS Integration Runtime を仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ネットワーク セキュリティ グループ (NSG) または Azure Firewall でのネットワーク アクセス制御を定義するには、仮想ネットワーク サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (DataFactoryManagement など) を指定することにより、対応するサービスの受信トラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグの概要と使用](../virtual-network/service-tags-overview.md)

- [Azure Data Factory 固有のサービスタグについて](join-azure-ssis-integration-runtime-virtual-network.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Data Factory インスタンスに関連付けられているネットワーク設定とネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.DataFactory" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、Azure Data Factory インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 ネットワークまたは Azure Data Factory インスタンスに関連する組み込みのポリシー定義を使用することもできます (例: "DDoS Protection Standard を有効にする必要があります")。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](../governance/policy/samples/index.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Data Factory インスタンスのネットワーク セキュリティとトラフィック フローに関連するリソースに、タグを使用することで、メタデータと論理的構成を提供します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Data Factory インスタンスに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、Azure Data Factory によって生成されたセキュリティ データを集計します。 Azure Monitor 内で、Azure Data Factory アクティビティ ログを受信するように構成されている Log Analytics ワークスペースをクエリできます。 他のシステムにデータをエクスポートするには、長期およびアーカイブのログ ストレージまたはイベント ハブに Azure Storage アカウントを使用します。

または、Azure Sentinel またはサードパーティのセキュリティ情報イベント管理 (SIEM) に対してデータを有効にしてオンボードできます。また、Azure Data Factory を Git と統合して、変更を追跡/監査する機能や、バグを招く変更を元に戻す機能など、いくつかのソース管理の利点を利用することもできます。

- [診断設定を構成する方法](/azure/azure-monitor/platform/diagnostic-settings#create-in-azure-portal)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Monitor とサードパーティの SIEM 統合を開始する方法](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

- [Azure Data Factory のソース管理](source-control.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure Event Hubs、または Azure Storage アカウントにログを送信してアーカイブします。 Azure アクティビティ ログのデータを使用すると、Azure リソースのコントロール プレーン レベルで実行された書き込み操作 (PUT、POST、DELETE) について、"いつだれが何を" 行ったのかを特定することができます。

診断設定を使用して、Azure Data Factory でメトリックやパイプライン実行データなどの非コンピューティング リソースの診断ログを構成します。 Azure Data Factory では、パイプライン実行データが 45 日間保存されます。 このデータをさらに長く保持するには、監査または手動検査のためのストレージ アカウントに診断ログを保存し、リテンション期間を日数で指定します。  また、ログを Azure Event Hubs にストリーミングしたり、ログを分析のために Log Analytics ワークスペースに送信したりすることもできます。

- [Azure アクティビティ ログの診断設定を有効にする方法](/azure/azure-monitor/platform/activity-log)

- [Azure Data Factory 診断ログについて](monitor-using-azure-monitor.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure Monitor を使用して仮想マシンからデータを収集できます。 Log Analytics VM 拡張機能をインストールすることにより、Azure Monitor で Azure VM からデータを収集できます。 その後、Azure Security Center では、Virtual Machines のセキュリティ イベント ログの監視を実行できます。 セキュリティ イベント ログによって生成されるデータの量によっては、既定で保存されません。 

組織でそのセキュリティ イベント ログ データを保持する場合は、データ収集レベル内で格納でき、その後 Log Analytics でそのクエリを実行できます。

- [Azure Monitor で Azure 仮想マシンからデータを収集する方法](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Security Center でのデータ収集の有効化](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Data Factory の診断設定を有効にします。 Log Analytics ワークスペースにログを保存することを選択した場合は、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Azure Data Factory の診断設定を有効にする方法](monitor-using-azure-monitor.md)

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Data Factory の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics を使用して異常な動作についてログを分析および監視し、定期的に結果を確認します。 Azure Data Factory のデプロイに関連するすべてのデータ ストアの診断設定も有効にしていることを確認してください。 診断設定を有効にする方法のガイダンスについては、各サービスのセキュリティ ベースラインを参照してください。

Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、VM の診断設定も有効にします。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

- [Log Analytics のスキーマ](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events)

- [Azure Monitor を使用して Azure 仮想マシンからデータを収集する方法](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Data Factory でサポートされているメトリックに対してアラートを生成するには、Azure Monitor の [Alerts &amp; Metrics]\(アラートとメトリック\) セクションに移動します。

Azure Data Factory の診断設定を構成し、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペース内で、事前に定義された一連の条件が発生したときに、アラートが発生するように構成します。 または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。

また、データ ストアに関連するサービスの診断設定が有効になっていることを確認してください。 ガイダンスについては、各サービスのセキュリティ ベースラインを参照できます。

- [Azure Data Factory のアラート](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts)

- [サポートされているすべてのメトリック ページ](/azure/azure-monitor/platform/metrics-supported)

- [Log Analytics ワークスペースでアラートを構成する方法](/azure/azure-monitor/platform/alerts-log)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Azure 仮想マシンで Integration Runtime を実行している場合は、Azure Cloud Services および Virtual Machines 用 Microsoft Antimalware を使用して、Azure Storage アカウントにイベントを記録するように仮想マシンを構成できます。 Storage アカウントからイベントを取り込み、必要に応じてアラートを作成するように、Log Analytics ワークスペースを構成します。 Azure Security Center の[Compute &amp; Apps]\(計算とアプリ\) の推奨事項に従います。 

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md)

- [Virtual Machines のゲストレベルの監視を有効にする方法](../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、コマンドライン監査ログを有効にできます。 Azure Security Center では、Azure VM のセキュリティ イベント ログの監視が提供されます。  自動プロビジョニングを有効にすると、Security Center によって、サポートされているすべての Azure VM と新しく作成される VM に Microsoft Monitoring Agent がプロビジョニングされます。またはエージェントを手動でインストールできます。  エージェントにより、プロセス作成イベント 4688 とイベント 4688 内の CommandLine フィールドが有効になります。 VM に作成された新しいプロセスは EventLog に記録され、Security Center の検出サービスによって監視されます。

- [Azure Security Center でのデータ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Data Factory 内で、定期的にユーザー アクセスを追跡して調整してください。 Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、共同作成者ロールまたは所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。

さらに、テナント レベルで、Azure Active Directory (Azure AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、Azure Data Factory インスタンスのコントロール プレーンへの管理アクセス権を持つ管理グループのメンバーであるアカウントを検出します。

Azure AD はユーザー アクセスを管理するために推奨される方法ですが、Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、VM にローカル アカウントも存在する可能性があることに注意してください。 ローカル アカウントとドメイン アカウントの両方を、通常、最小の占有領域で、確認して管理する必要があります。 さらに、管理権限の可用性を削減するために、ジャストインタイム機能について、Privileged Identity Manager を確認することをお勧めします。

- [Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)

- [Privileged Identity Manager に関する情報](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember)

- [ローカル アカウントに関する情報](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Data Factory では、Azure portal と Azure Data Factory コンソールにアクセスできるようにするために、Azure Active Directory (Azure AD) が使われています。 Azure AD には既定のパスワードという概念はありませんが、カスタム アプリケーションまたはサードパーティ製アプリケーションの既定のパスワードは、お客様が変更または禁止する必要があります。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: Azure コントロール プレーン (Azure portal) と Azure Data Factory コンソールにアクセスするための、専用管理アカウントの使用に関する標準操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、Azure Active Directory (Azure AD) 内の管理アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

Azure 仮想マシンで Integration Runtime を実行している場合、Azure 仮想マシンの管理者アカウントは、Azure Privileged Identity Manager (PIM) で構成することもできます。 Azure Privileged Identity Manager には、アクセス許可を特定の期間だけ使用できるように、ジャストインタイム昇格、多要素認証、委任オプションなどのいくつかのオプションが用意されており、承認するための 2 人目のユーザーが必要です。

- [Azure Security Center ID とアクセスについて](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

- [Privileged Identity Manager に関する情報](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: Azure アプリの登録 (サービス プリンシパル) を使用して、アプリケーションまたは関数で、Recovery Services コンテナーにアクセスして、対話するために使用できるトークンを取得します。

- [Azure REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [クライアント アプリケーション (サービス プリンシパル) を Azure Active Directory (Azure AD) に登録する方法](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API に関する情報](/rest/api/recoveryservices/)

- [Azure Data Factory の REST API に関する情報](/rest/api/datafactory/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: 多要素認証が構成された特権アクセス ワークステーション (PAW) を使用して Azure リソースにログインし、そのリソースを構成します。 

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときに、Azure Active Directory (Azure AD) セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、さらに VM を Azure Sentinel にオンボードすることができます。 Microsoft Azure Sentinel は、スケーラブルでクラウドネイティブ型のセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:データ ファクトリは、特定のデータ ファクトリを表す Azure リソースのマネージド ID に関連付けることができます。 このマネージド ID を Azure SQL Database の認証に使用できます。 この ID を使用して指定したファクトリからデータベースにアクセスし、データベースに、またはデータベースからデータをコピーできます。

Azure 仮想マシンで Integration Runtime (IR) を実行している場合は、マネージド ID を使用すると、コード内に資格情報を記述することなく、Azure Active Directory (Azure AD) 認証をサポートする任意のサービス (Key Vault を含む) に対して認証を行うことができます。 仮想マシンで実行しているコードでは、マネージド ID を使用して、Azure AD 認証をサポートするサービスのアクセス トークンを要求できます。

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)

- [Azure Data Factory を使用して Azure SQL Database のデータをコピーおよび変換する](connector-azure-sql-database.md)

- [Azure SQL Database で Azure AD 認証を構成して管理する方法](../azure-sql/database/authentication-aad-configure.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Azure 仮想マシンでランタイム統合を実行している場合は、ローカル セキュリティ グループとユーザーを確認して、システムを危険にさらす可能性がある予期しないアカウントがないことを確認する必要があります。

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring/)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。 このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、その VM を Azure Sentinel にオンボードします。 Microsoft Azure Sentinel は、スケーラブルでクラウドネイティブ型のセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Azure Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure AD を使用して Event Hubs リソースへのアクセスを承認する](../event-hubs/authorize-access-azure-active-directory.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure SQL Database や Azure 仮想マシンなどの Azure Data Factory リソースの一元的な認証および承認システムとして Azure Active Directory (Azure AD) を使用します。 コントロール プレーン (Azure portal) でのアカウント ログイン動作の偏差について、Azure AD Identity Protection とリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションへの自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [SQL で Azure AD 認証を構成して管理する](https://docs.microsoft.com/azure/azure-sql/database/authentication-aad-configure?tabs=azure-powershell)

- [Azure-SSIS 統合ランタイムの Azure AD 認証の有効化](enable-aad-authentication-azure-ssis-ir.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、Azure カスタマー ロックボックスに、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。 Azure ロックボックスは Azure Data Factory 自体で使用できませんが、Azure ロックボックスでは Azure SQL Database と Azure Virtual Machines がサポートされていることに注意してください。

 

- [カスタマー ロックボックスについて](../security/fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

Azure SQL Database のデータの検出と分類機能を使用します。 データの検出と分類では、データベース内の機密データを検出、分類、ラベル付け、保護するために Azure SQL Database に組み込まれた高度な機能が提供されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure SQL Server に対してデータの検出と分類を使用する方法](../azure-sql/database/data-discovery-and-classification-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Integration Runtime は、仮想ネットワーク (VNet) またはサブネットで分離し、適切にタグを付けるようにします。

プライベート エンドポイントを使用してネットワークの分離を実行することもできます。 Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するためのネットワーク インターフェイスです。 プライベート エンドポイントでは、自分の VNet からのプライベート IP アドレスを使用して、サービスを実質的に VNet に取り込みます。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [プライベート リンクについて](../private-link/private-endpoint-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Data Factory デプロイの機密情報を格納または処理するデータ ソース (Azure SQL Database など) では、タグを使用して関連リソースを機密とマークします。

Private Link が使用可能な場合は、プライベート エンドポイントを使用して、Azure Data Factory パイプラインにリンクされているすべてのリソースをセキュリティで保護します。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 また、ネットワーク セキュリティ グループ (NSG) に対して厳格な一連のアウトバウンド規則を構成し、その NSG をサブネットに関連付けることによって、データ窃盗のリスクを軽減することもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md) 

- [Azure Private Link について](../private-link/private-link-overview.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:クラウド データ ストアが HTTPS または TLS をサポートしている場合、Data Factory のデータ移動サービスとクラウド データ ストア間のデータ転送はすべて、セキュリティで保護されたチャネル HTTPS または TLS を介して行われます。 使用される TLS のバージョンは、1.2 です。 

データベースとの間でのデータ転送中は、Azure SQL Database および Azure Synapse Analytics (旧称 SQL Data Warehouse) へのすべての接続を (SSL/TLS を使用して) 暗号化する必要があります。 JSON を使用してパイプラインを作成する場合は、encryption プロパティを追加し、接続文字列で true に設定します。 Azure Storage では、接続文字列で HTTPS を使用できます。

- [Azure Data Factory での転送中の暗号化について](data-movement-security-considerations.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Data Factory を使用して Azure SQL Database インスタンスをコピーおよび変換する場合は、Azure SQL Database のデータの検出と分類機能を使用します。 データの検出と分類では、データベース内の機密データを検出、分類、ラベル付け、保護するために Azure SQL Database に組み込まれた高度な機能が提供されます。

データの検出と分類機能は、他の Azure サービスではまだ使用できません。

- [Azure SQL Server に対してデータの検出と分類を使用する方法](../azure-sql/database/data-discovery-and-classification-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Data Factory コントロール プレーン (Azure portal) へのアクセスを制御します。

Data Factory インスタンスを作成するには、Azure へのサインインに使用するユーザー アカウントが、共同作成者ロールまたは所有者ロールのメンバーであるか、Azure サブスクリプションの管理者である必要があります。

Azure SQL Database などの Data Factory データ ソースの場合、Azure RBAC に関する詳細について、そのサービスのセキュリティ ベースラインを参照してください。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

- [Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: Azure Storage またはコンピューティング リソースでは、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Data Factory のデプロイに関連するすべてのデータ ストアに対して、データ暗号化メカニズムを有効にすることをお勧めします。 保存データの暗号化に関する詳細については、そのサービスのセキュリティ ベースラインを参照できます。

Azure 仮想マシンで Integration Runtime を実行している場合、Windows Virtual Machines (VM) 上の仮想ディスクは、サーバー側暗号化または Azure Disk Encryption (ADE) を使用して、保存時に暗号化されます。 Azure Disk Encryption では、Windows の BitLocker 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。 カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

資格情報またはシークレット値を Azure Key Vault に格納し、パイプラインの実行時にそれらを使用してアクティビティに渡すことができます。 データ ストアや計算のための資格情報を Azure Key Vault に格納することもできます。 Azure Data Factory は、データ ストア/計算を使うアクティビティの実行時に、資格情報を取得します。

- [Azure Data Factory での保存時の暗号化について](data-movement-security-considerations.md)

- [Azure Managed Disks のサーバー側暗号化](../virtual-machines/disk-encryption.md)

- [Windows VM 用の Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

- [パイプライン アクティビティでの Azure Key Vault シークレットの使用方法](how-to-use-azure-key-vault-secrets-pipeline-activities.md) 

- [Azure Key Vault への資格情報の格納方法](store-credentials-in-key-vault.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、Azure Data Factory と関連リソースに変更が加えられたときのアラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

- [Azure Storage Analytics のログ](../storage/common/storage-analytics-logging.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure SQL Database をデータストアとして使用している場合、Azure SQL Database 向け Advanced Data Security を有効にし、Azure SQL Server での脆弱性評価の実行に関する Azure Security Center のレコメンデーションに従います。

Azure 仮想マシン (VM) で Integration Runtime を実行している場合、VM に対する脆弱性評価の実行に関する Azure Security Center のレコメンデーションに従います。  仮想マシンの脆弱性評価を実行するには、Azure セキュリティの推奨されるソリューションまたはサードパーティ ソリューションを使用します。 

- [Azure SQL Database で脆弱性評価を実行する方法](../azure-sql/database/sql-vulnerability-assessment.md)

- [Advanced Data Security を有効にする方法](../azure-sql/database/azure-defender-for-sql.md)

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure Update Management ソリューションを使用して、VM の更新プログラムと修正プログラムを管理します。  Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure の Update Management ソリューション](../automation/update-management/overview.md)

- [Azure VM の更新プログラムとパッチの管理](../automation/update-management/manage-updates-for-vm.md)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**:Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、サードパーティ修正プログラム管理ソリューションを使用できます。  Azure Update Management のソリューションを使用すると、仮想マシンの更新プログラムと修正プログラムを管理できます。  Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。 

- [Azure の Update Management ソリューション](../automation/update-management/overview.md)

- [Azure VM の更新プログラムとパッチの管理](../automation/update-management/manage-updates-for-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Azure 仮想マシンで Integration Runtime を実行している場合は、スキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理のレコメンデーションを使用する場合は、選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

- [仮想マシン向けの統合された脆弱性スキャナーについて](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure 仮想マシンで Integration Runtime を実行している場合は、ネイティブの脆弱性スキャナーを使用できます。 Azure Security Center に含まれている脆弱性スキャナーは、Qualys を利用しています。 Qualys のスキャナーは、Azure Virtual Machines の脆弱性をリアルタイムで識別するための主要なツールです。

Security Center で脆弱性が識別されると、検出結果と推奨事項としての関連情報が提示されます。 関連情報には、修復手順、関連する CVE、CVSS のスコアなどが含まれます。 1 つ以上のサブスクリプション、または特定の仮想マシンで識別された脆弱性を表示することができます。

- [仮想マシン向けの統合された脆弱性スキャナー](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。

- 許可されないリソースの種類

- 許可されるリソースの種類

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:コンピューティング リソース用に承認された Azure リソースと承認されたソフトウェアを定義します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure 仮想マシン インベントリを利用して、仮想マシン上のすべてのソフトウェアに関する情報の収集を自動化します。 Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。

注:ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、お客様はゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込む必要があります。

- [Azure Automation の概要](../automation/automation-intro.md)

- [Azure VM インベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure 仮想マシンで Integration Runtime を実行している場合は、Azure Automation によって、ワークロードとリソースのデプロイ、運用、および使用停止を完全に制御できます。  Change Tracking を使用して、Virtual Machines にインストールされているすべてのソフトウェアを特定できます。 独自のプロセスを実装するか、Azure Automation State Configuration を使用して、承認されていないソフトウェアを削除できます。

- [Azure Automation の概要](../automation/automation-intro.md)

- [Change Tracking ソリューションを使用して環境内の変更を追跡する](../automation/change-tracking/overview.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure Security Center の適応型アプリケーション制御を使用して、VM 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにします。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適応型アプリケーション制御は、お使いの Azure および Azure 以外のコンピューター (Windows および Linux) 上でどのアプリケーションが実行できるかを制御できる、Azure Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。  これが組織の要件を満たしていない場合は、サードパーティ製ソリューションを実装します。

これは、Integration Runtime が Azure 仮想マシンで実行されている場合にのみ適用されることに注意してください。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: Azure Virtual Machine で Runtime Integration を実行している場合、スクリプトの種類に応じて、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソース内でスクリプトを実行する機能を制限できます。 また、Azure Security Center の適応型アプリケーション制御を利用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: Azure 環境にデプロイされた高リスク アプリケーションは、仮想ネットワーク、サブネット、サブスクリプション、管理グループなどを使用して分離し、Azure Firewall、Web アプリケーション ファイアウォール (WAF)、またはネットワーク セキュリティ グループ (NSG) によって十分に保護できます。 

- [Azure における仮想ネットワークと仮想マシン](../virtual-machines/network-overview.md)

- [Azure Firewall とは](../firewall/overview.md)

- [Azure Web アプリケーション ファイアウォールとは](../web-application-firewall/overview.md)

- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)

- [Azure Virtual Network とは](../virtual-network/virtual-networks-overview.md)

- [Azure 管理グループでリソースを整理する](../governance/management-groups/overview.md)

- [サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Azure Data Factory の標準的なセキュリティ構成を定義して実装します。 Azure Data Factory インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.DataFactory" 名前空間で Azure Policy エイリアスを使用します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: Azure 仮想マシンで Runtime Integration を実行している場合は、Azure Security Center のレコメンデーション [Remediate Vulnerabilities in Security Configurations on your Virtual Machines]\(仮想マシンのセキュリティ構成の脆弱性を修復する\) を使用して、すべてのコンピューティング リソースのセキュリティ構成を維持します。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md)

- [Azure Security Center のレコメンデーションを修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [Azure Resource Manager テンプレートの作成に関する情報](../virtual-machines/windows/ps-template.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、デプロイ用に VM の安全な構成を維持するためのいくつかのオプションがあることに注意してください。
- Azure Resource Manager テンプレート:これらは、Azure portal から VM をデプロイするために使用される JSON ベースのファイルであり、カスタム テンプレートをメンテナンスする必要があります。 Microsoft では、基本テンプレートに対してメンテナンスを実行します。
- カスタム仮想ハードディスク (VHD):他の方法で管理できない複雑な環境を扱う場合など、状況によっては、カスタム VHD ファイルを使用することが必要な場合があります。 
- Azure Automation State Configuration:ベース OS がデプロイされたら、これを使用して設定をより細かく制御し、オートメーション フレームワークを通じて適用できます。

ほとんどのシナリオで、Microsoft ベース VM テンプレートと Azure Automation Desired State Configuration を組み合わせると、セキュリティ要件を満たし、それを維持するために役立つ可能性があります。

- [VM テンプレートをダウンロードする方法に関する情報](/previous-versions/azure/virtual-machines/windows/download-template)

- [Azure Resource Manager テンプレートの作成に関する情報](../virtual-machines/windows/ps-template.md)

- [カスタム VM VHD を Azure にアップロードする方法](../virtual-machines/windows/upload-generalized-managed.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: カスタム イメージを使用している場合は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、確実に承認されたユーザーのみがイメージにアクセスできるようにします。 コンテナー イメージの場合は、Azure Container Registry に保存し、Azure RBAC を利用して、承認されたユーザーのみがイメージにアクセスできるようにします。

Data Factory 共同作成者ロールを使って、データ ファクトリのほか、それらの中の子リソースの作成と管理を行うことができます。

- [Azure RBAC について](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [コンテナー レジストリの Azure RBAC を理解する](../container-registry/container-registry-roles.md) 

- [Azure RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.DataFactory" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: このレコメンデーションは、Azure 仮想マシンで Integration Runtime が 実行されている場合に適用できます。 Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。 

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../automation/automation-dsc-onboarding.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.DataFactory" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**:このレコメンデーションは、Azure 仮想マシンで Integration Runtime が 実行されている場合に適用できます。 Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。 

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../automation/automation-dsc-onboarding.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

資格情報またはシークレット値を Azure Key Vault に格納し、パイプラインの実行時にそれらを使用してアクティビティに渡すこともできます。 論理的な削除が有効にされていることを確認します。

- [Azure マネージド ID と統合する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](/azure/key-vault/quick-create-portal)

- [Key Vault に対して認証を行う方法](../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

- [パイプライン アクティビティでの Azure Key Vault シークレットの使用](how-to-use-azure-key-vault-secrets-pipeline-activities.md)

- [Azure Key Vault の論理的な削除](../key-vault/general/soft-delete-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: データ ファクトリの作成時に、ファクトリの作成に加え、マネージド ID を作成できます。 マネージド ID は、Azure Active Directory (Azure AD) に登録されているマネージド アプリケーションであり、この特定のデータ ファクトリを表します。

- [Azure Data Factory のマネージド ID](data-factory-service-identity.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**: Azure 仮想マシンで Integration Runtime を実行している場合は、Azure Windows Virtual Machines 用 Microsoft Antimalware を使用して、リソースを継続的に監視し、保護することができます。 

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Microsoft Antimalware は、Azure のサービス (例: Azure App Service) をサポートする、基になっているホストに対して有効になっていますが、コンテンツに対しては実行されません。

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。 

データ サービス向けの Azure Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。 

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware について](../security/fundamentals/antimalware.md)

- [データ サービスに対する Azure Security Center の脅威検出について](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure 用 Microsoft Antimalware をデプロイすると、既定で、最新の署名、プラットフォーム、エンジンの更新プログラムが自動的にインストールされます。 Azure Security Center の"計算とアプリ" の推奨事項に従って、すべてのエンドポイントが最新の署名を備え、最新の状態になっているようにします。 Azure Security Center と統合された Microsoft Defender Advanced Threat Protection サービスを使用して、ウイルスやマルウェアベースの攻撃のリスクを限定する追加のセキュリティによって、Windows OS の保護を強化できます。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware をデプロイする方法](../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**:Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure Backup を有効にして、VM のほか、自動バックアップの目的の頻度と保有期間を構成します。

いかなるデータストアの場合も、定期的な自動バックアップの実行方法に関するレコメンデーションについては、そのサービスのセキュリティ ベースラインを参照してください。

- [Azure VM バックアップの概要](../backup/backup-azure-vms-introduction.md)

- [VM の設定から Azure VM をバックアップする](../backup/backup-azure-vms-first-look-arm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行している場合は、Azure Backup とターゲットの Azure VM のほか、必要な頻度と保有期間を有効にします。 Azure Key Vault 内のカスタマー マネージド キーをバックアップします。

いかなるデータストアの場合も、定期的な自動バックアップの実行方法に関するレコメンデーションについては、そのサービスのセキュリティ ベースラインを参照してください。

- [Azure VM バックアップの概要](../backup/backup-azure-vms-introduction.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**:Azure 仮想マシンで Integration Runtime を実行している場合は、Azure Backup 内のコンテンツのデータの復元を定期的に実行する機能を確認してください。 必要に応じて、コンテンツを分離された VLAN にテスト復元してください。 バックアップされたカスタマー マネージド キーの復元を定期的にテストします。

どのデータ ストアの場合でも、バックアップの検証に関するガイダンスについては、そのサービスのセキュリティ ベースラインを参照してください。

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure 仮想マシン (VM) で Integration Runtime を実行していて、Azure Backup でその VM を バックアップする場合、VM は Storage Service Encryption (SSE) によって保存時に暗号化されます。 Azure Backup では、Azure Disk Encryption を使用して暗号化されている Azure VM もバックアップできます。 Azure Disk Encryption は、シークレットとしてキー コンテナーで保護されている BitLocker 暗号化キー (BEK) と統合されます。 Azure Disk Encryption は、Azure Key Vault キー暗号化キー (KEK) とも統合されます。 Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [VM の論理的な削除](../backup/backup-azure-security-feature-cloud.md)

- [Azure Key Vault の論理的な削除の概要](../key-vault/general/soft-delete-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [Azure Security Center 内でワークフロー自動化を構成する方法](../security-center/security-center-planning-and-operations-guide.md)

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

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

- [Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)を参照
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
