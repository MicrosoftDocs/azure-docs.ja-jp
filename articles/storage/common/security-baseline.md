---
title: Azure Storage 用の Azure セキュリティ ベースライン
description: Azure Storage 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6358f9d233b3c09dc4ae4f3ecac7c91dea8bba6e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228275"
---
# <a name="azure-security-baseline-for-azure-storage"></a>Azure Storage 用の Azure セキュリティ ベースライン

Azure Storage 用の Azure セキュリティ ベースラインには、ご自身のデプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**:Azure Storage のファイアウォールを構成して、特定のパブリック IP アドレス範囲からクライアントへのアクセスを制限したり、Azure 上の仮想ネットワーク (VNet) を選択したり、特定の Azure リソースにアクセスしたりできます。 また、プライベート エンドポイントを構成して、トラフィックが、ご自身のエンタープライズからストレージ サービスに、プライベート ネットワーク経由で排他的に転送されるようにすることもできます。

注:従来のストレージ アカウントでは、ファイアウォールと仮想ネットワークはサポートされていません。

- [Azure Storage ファイアウォールを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-network-security#change-the-default-network-access-rule)

- [Azure Storage のプライベート エンドポイントを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-vnet-subnet-and-nic-configuration-and-traffic"></a>1.2:Vnet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Storage では、多層型セキュリティ モデルが提供されています。 ストレージ アカウントへのアクセスを、Azure 仮想ネットワーク (VNet) 内の指定した IP アドレス、IP 範囲、またはサブネットのリストから発信された要求に制限できます。 Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure のネットワーク リソースを保護することができます。 また、ストレージ アカウント ファイアウォールを介してストレージ アカウント用に構成された仮想ネットワークまたはサブネットの NSG フロー ログを有効にし、トラフィックの監査のためにログをストレージ アカウントに送信します。 

ストレージ アカウントにプライベート エンドポイントがアタッチされている場合は、サブネットに対してネットワーク セキュリティ グループ (NSG) 規則を構成できないことに注意してください。 

- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

- [Azure Storage のプライベート エンドポイントについて](https://docs.microsoft.com/azure/storage/common/storage-private-endpoints#known-issues)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Storage アカウント用の Advanced Threat Protection を有効にします。 Advanced Threat Protection for Azure Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 Azure Security Center 統合アラートは、IP アドレスが既知の危険な IP アドレス (例: 既知の Cryptominer) か、以前には危険として認識されていない IP アドレスかどうかにかかわらず、ネットワーク通信が正常に解決された IP アドレスに関連付けられているアクティビティに基づいています。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 

- [Advanced Threat Protection を有効にする方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

- [Azure Security Center の統合された脅威インテリジェンスについて](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**:Network Watcher のパケット キャプチャを使用すると、ストレージ アカウントと仮想マシンの間のトラフィックを追跡するキャプチャ セッションを作成できます。 必要なトラフィックのみを確実にキャプチャするためにキャプチャ セッション用のフィルターが用意されています。 パケット キャプチャは、事後と事前、どちらの場合でもネットワークの異常を診断するのに役立ちます。 その他の用途には、ネットワーク統計の収集や、ネットワークへの侵入に関する情報の取得などがあり、クライアントとサーバー間の通信のデバッグなどに役立ちます。 パケット キャプチャをリモートでトリガーできることで、目的の仮想マシンでパケット キャプチャを手動で実行する負担が軽減されて、貴重な時間の節約になります。 

- [ポータルを使用して Azure Network Watcher でパケット キャプチャを管理する](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-manage-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システムをデプロイする

**ガイダンス**: Advanced Threat Protection for Azure Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは Azure Security Center と統合されます。さらに、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。 

- [Advanced Threat Protection for Azure Storage を構成する](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: ストレージ アカウントへのアクセスが必要な仮想ネットワークのリソースについては、構成した仮想ネットワーク用の仮想ネットワーク サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたはターゲット フィールドにサービス タグ名 (Storage など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

ネットワーク アクセスのスコープを特定のストレージ アカウントに設定する必要がある場合は、仮想ネットワーク サービス エンドポイント ポリシーを使用します。

- [サービス タグの使用に関する詳細について](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Azure Storage の仮想ネットワーク サービス エンドポイント ポリシーに関する詳細について](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: サービス

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を保持する

**ガイダンス**: Azure Policy を使用して、Azure Storage アカウントに関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.Storage" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、ストレージ アカウント リソースのネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 

次のように、ストレージ アカウントに関連する組み込みのポリシー定義を使用することもできます。ストレージ アカウントは仮想ネットワーク サービス エンドポイントを使用する必要がある 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [ストレージに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#storage)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: ネットワーク セキュリティおよびトラフィック フローに関連したネットワーク セキュリティ グループ (NSG) やその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。 すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。 リソースに対するアクションをそのタグに基づいて検索または実行するには、Azure PowerShell または Azure CLI を使用できます。 

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [仮想ネットワークを作成する方法](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure Policy を使用して、ネットワーク リソースへの構成の変更をログに記録します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Monitor でアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-resource"></a>2.1:承認された時刻同期リソースを使用する

**ガイダンス**: 適用できません。Microsoft によって、Azure Storage アカウントのタイム ソースが管理されます。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor を介してログを取り込み、エンドポイント デバイス、ネットワーク リソース、およびその他のセキュリティ システムによって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージには Azure Storage アカウントを使用します。必要に応じて、不変ストレージや保有期間の保持の強制などのセキュリティ機能を使用できます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Storage Analytics には、BLOB、キュー、およびテーブル用のログが用意されています。 Azure portal を使用して、アカウントに対してどのログが記録されるかを構成できます。 

- [Azure Storage アカウントの監視を構成する方法](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-monitoring-for-a-storage-account)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログの保有期間を構成する

**ガイダンス**:Azure Storage アカウントまたは Log Analytics ワークスペースにセキュリティ イベント ログを保存する場合は、組織の要件に従ってアイテム保持ポリシーを設定することができます。 

- [Azure Storage アカウント ログの保持ポリシーを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

- [Log Analytics でデータ保持期間を変更する](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure Storage ログを確認するには、Log Analytics オファリングを使用したクエリなどの通常のオプションと、ログ ファイルを直接表示する独自のオプションがあります。 Azure Storage では、ログは BLOB に格納され、 http://accountname.blob.core.windows.net/ $logs で直接アクセスする必要があります (ログ フォルダーは既定では非表示になっているため、直接移動する必要があります。 リスト コマンドには表示されません) 

また、Azure Storage アカウント用の Advanced Threat Protection を有効にします。 Advanced Threat Protection for Azure Storage では、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンス レイヤーが追加されます。 セキュリティ アラートは、アクティビティで異常が発生したときにトリガーされます。 これらのセキュリティ アラートは Azure Security Center と統合されます。さらに、不審なアクティビティの詳細と、脅威の調査や修復方法に関する推奨事項と共に、サブスクリプション管理者にメールで送信されます。 

- [データをログに記録して確認する](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#how-logs-are-stored)

- [Advanced Threat Protection を有効にする方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Security Center で、ストレージ アカウント用の Advanced Threat Protection を有効にします。 ストレージ アカウントの診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics ワークスペースを Azure Sentinel にオンボードします。これは、セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるためです。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。 

- [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Azure Security Center でアラートを管理する方法](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Log Analytics のログ データに関するアラートを送信する方法](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

- [Azure Storage Analytics のログ](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**:Azure Security Center を使用し、Azure Storage の脅威保護を有効にして、ハッシュ評価分析およびアクティブな Tor 出口ノード (匿名化プロキシ) からの疑わしいアクセスを使用して、Azure Storage へのマルウェアのアップロードを検出します。 

- [Advanced Threat Protection for Azure Storage を構成する](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: Azure Monitor の Azure DNS Analytics (プレビュー) ソリューションを使用して、DNS インフラストラクチャのセキュリティ、パフォーマンス、操作に関する分析情報を収集します。 現時点では、Azure Storage アカウントはサポートされていませんが、サード パーティの DNS ログ ソリューションを使用することはできます。 

- [DNS Analytics プレビュー ソリューションを使用した DNS インフラストラクチャに関する分析情報の収集](https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンド ライン監査のログ記録を有効にする

**ガイダンス**: 適用できません。ベンチマークは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: 管理者アカウントのインベントリを保持する

**ガイダンス**: Azure AD の組み込みロールは、明示的に割り当てる必要があり、クエリ可能でなければなりません。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。 

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Storage アカウントまたは Azure Active Directory には、既定または空のパスワードという概念がありません。 Azure Storage では、Azure のロールベースのアクセス制御 (Azure RBAC) と、共有キーおよび Shared Access Signature (SAS) をサポートするアクセス制御モデルが実装されます。 共有キーと SAS 認証の特徴は、呼び出し元に ID が関連付けられていないため、セキュリティ プリンシパルのアクセス許可ベースの承認を実行できないことです。 

- [Azure Storage 内のデータへのアクセスの承認](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Azure Storage アカウントのセキュリティ プリンシパルとアクセスの制御について](https://docs.microsoft.com/azure/storage/common/storage-introduction)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: ストレージ アカウントにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。 

また、Microsoft サービスの Azure AD Privileged Identity Management の特権ロール、および Azure ARM を使用して、Just-In-Time または Just-Enough-Access を有効にすることもできます。 

- [Azure Security Center ID とアクセスについて](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

- [Privileged Identity Management の概要](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: 可能な限り、サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure Active Directory SSO を使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。 

- [Azure AD を使用した SSO について](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Azure Storage 内のデータへのアクセスの承認](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory 多要素認証を有効にし、Azure Security Center の ID とアクセス管理に関する推奨事項に従って、ストレージ アカウントのリソースを保護します。 

- [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Azure Security Center で ID とアクセスを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用のコンピューター (特権アクセス ワークステーション) を使用する

**ガイダンス**: MFA が構成されている PAW (特権アクセス ワークステーション) を使用してストレージ アカウントのリソースにログインし、構成します。 

- [特権アクセス ワークステーションについて](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Security Center のリスク検出アラートを Azure Monitor に送信し、アクション グループを使用してカスタムのアラートまたは通知を構成します。 Azure Storage アカウント用の Advanced Threat Protection を有効にして、疑わしいアクティビティに関するアラートを生成します。 また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 

- [Azure Storage アカウント用の Advanced Threat Protection を設定する方法](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)

- [Azure AD のリスク検出について](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events)

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8:承認された場所からの Azure リソースのみを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。 

- [Azure でネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD には、ストレージ アカウント内のリソースに対するクライアントのアクセスをきめ細かく制御するロールベースのアクセス制御 (RBAC) が用意されています。  セキュリティのベスト プラクティスとして、より侵害されやすいアカウント キーを使用するのではなく、可能な限り Azure AD 資格情報を使用します。 アプリケーション設計において、BLOB ストレージへのアクセスのために Shared Access Signature が必要な場合は、セキュリティを強化するために、可能な限り、Azure AD 資格情報を使用してユーザー委任 Shared Access Signature (SAS) を作成してください。

- [Azure AD インスタンスを作成して構成する方法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Azure portal で RBAC を使用して Azure Blob とキューのデータへのアクセスを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [Azure Storage 内のデータへのアクセスの承認](https://docs.microsoft.com/azure/storage/common/storage-auth)

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure Active Directory ログを確認して、古いアカウントを検出します。これには、ストレージ アカウントの管理者ロールを使用するアカウントが含まれる可能性があります。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、ストレージ アカウント リソースへのアクセスに使用される可能性のあるエンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 適切なユーザーのみが継続的なアクセス権を持っていることを確認するために、ユーザー アクセスを定期的に確認する必要があります。  

また、Shared Access Signature (SAS) を使用して、データのセキュリティを損なうことなく、ストレージ アカウント内のリソースへのセキュリティで保護された委任アクセスを提供することもできます。 パラメーターの中でも、クライアントがアクセスできるリソース、それらのリソースに対して持つアクセス許可、SAS の有効期間などを制御できます。

また、コンテナーと BLOB への匿名読み取りアクセスについても確認します。 既定では、コンテナーとその中のすべての BLOB には、適切なアクセス許可を与えられたユーザーのみがアクセスできます。 Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。

予想外のユーザー アカウント アクセスのリスクを軽減するための効果的な方法の 1 つは、ユーザーに付与するアクセスの期間を制限することです。 期間限定の SAS URI は、ストレージ アカウントへのユーザー アクセスを自動的に有効期限切れにするための 1 つの効果的な方法です。 さらに、ストレージ アカウント キーを頻繁に交換することで、ストレージ アカウント キーを使用した予期しないアクセスの期間が限定されることを保証できます。

- [Azure AD のレポートについて](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Azure Storage アカウント レベルでアクセスを表示および変更する方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](https://docs.microsoft.com/azure/storage/common/storage-sas-overview)

- [コンテナーと BLOB への匿名読み取りアクセスを管理する](https://docs.microsoft.com/azure/storage/blobs/storage-manage-access-to-resources)

- [Azure Portal でのストレージ アカウントの監視](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

- [ストレージ アカウント アクセス キーを管理する](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセスの試行を監視する

**ガイダンス**: Storage Analytics を使用して、ストレージ サービスに対する要求の成功と失敗についての詳細な情報をログに記録します。 すべてのログは、Storage Analytics をストレージ アカウントに対して有効にしたときに自動的に作成される $logs という名前のコンテナー内のブロック BLOB に格納されます。

Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。 Azure Storage アカウントに対する認証エラーを監視するために、ストレージ リソースのメトリックが特定のしきい値に達したときに通知するアラートを作成できます。 さらに、Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。

- [Azure Storage Analytics のログ](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

- [Azure Storage アカウントのメトリック アラートを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラート

**ガイダンス**: Azure Active Directory のリスクおよび ID Protectoin 機能を使用して、ストレージ アカウント リソースに関連して検出された疑わしいアクションに対する自動応答を構成します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。 

- [Azure AD の危険なサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: Microsoft が顧客データにアクセスする必要のあるサポート シナリオでは、カスタマー ロックボックス (ストレージ アカウントのプレビュー) に、顧客が顧客データへのアクセス要求を確認し、承認または拒否するためのインターフェイスが用意されています。 Microsoft によって、ストレージ アカウント内に格納されている組織のシークレットへのアクセスが必要になったり、要求されたりすることはありません。

- [カスタマー ロックボックスについて](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理するストレージ アカウント リソースを追跡しやすくするには、タグを使用します。 

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境やデータの機密度など、個々のセキュリティ ドメイン用の個別のサブスクリプション、管理グループ、ストレージ アカウントを使用して分離を実装します。  ストレージ アカウントを制限して、使用されるネットワークの種類とサブセットに基づいて、アプリケーションやエンタープライズ環境で求められるストレージ アカウントへのアクセス レベルを制御できます。 ネットワーク ルールを構成すると、指定したネットワークのセットを経由してデータを要求しているアプリケーションのみが、ストレージ アカウントにアクセスできます。 Azure AD RBAC を使用して Azure Storage へのアクセスを制御できます。 また、プライベート エンドポイントを構成してセキュリティを強化することもできます。仮想ネットワークとサービスの間のトラフィックは Microsoft のバックボーン ネットワークを経由することで、パブリック インターネットからの露出が排除されます。 

- [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/storage/common/storage-network-security)

- [仮想ネットワーク サービス エンドポイント](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報を格納または処理するストレージ アカウント リソースの場合は、タグを使用してリソースを機密としてマークします。 窃盗によるデータ損失のリスクを軽減するには、Azure Firewall を使用して Azure Storage アカウントに対する送信ネットワーク トラフィックを制限します。 

さらに、仮想ネットワーク サービス エンドポイント ポリシーを使用すると、サービス エンドポイント経由の Azure Storage アカウントの仮想ネットワーク エグレス トラフィックをフィルター処理し、データ流出を特定の Azure Storage アカウントのみに許可することができます。

- [Azure Storage ファイアウォールおよび仮想ネットワークを構成する](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

- [Azure Storage の仮想ネットワーク サービス エンドポイント ポリシー](https://docs.microsoft.com/azure/private-link/create-private-endpoint-storage-portal)

- [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: ストレージ アカウントの [安全な転送が必須] を有効にすると、HTTPS の使用を強制することができます。 このオプションを有効にすると、HTTP を使った接続は拒否されます。 さらに、Azure Security Center と Azure Policy を使用して、ストレージ アカウントの安全な転送を強制します。

- [Azure Storage で安全な転送を要求する方法](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer)

- [Security Center により監視される Azure セキュリティ ポリシー](https://docs.microsoft.com/azure/security-center/security-center-policy-definitions)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Storage アカウントと関連リソースでは、データ特定機能をまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。 

- [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory (Azure AD) では、ロールベースのアクセス制御 (RBAC) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 BLOB またはキュー データへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールは、Azure Storage によって定義されます。 

- [Azure Storage アカウントに Azure ロールを割り当てる方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal)

- [Azure Storage リソース プロバイダーを使用して管理リソースにアクセスする](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider)

- [Azure portal で RBAC を使用して Azure Blob とキューのデータへのアクセスを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

- [AAD インスタンスを作成して構成する方法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

- [Azure Storage 内のデータへのアクセスの承認](https://docs.microsoft.com/azure/storage/common/storage-auth)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホスト ベースのデータ損失防止を使用してアクセス制御を適用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**:Azure Storage 暗号化はすべてのストレージ アカウントに対して有効になり、無効にすることはできません。 Azure Storage では、データがクラウドに永続化されるときに自動的に暗号化されます。 Azure Storage からデータを読み取ると、Azure Storage によって復号化されてから返されます。 Azure Storage の暗号化を使用すると、コードを変更したり、アプリケーションにコードを追加したりする必要なしに、保存データをセキュリティで保護できます。 

- [Azure Storage の保存時の暗号化について](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、ストレージ アカウント リソースに変更が加えられたときのアラートを作成します。 また、Azure Storage のログ記録を有効にして、Azure Storage に対する各要求がどのように承認されたかを追跡することもできます。 このログでは、OAuth 2.0 トークン、共有キー、または共有アクセス署名 (SAS) を使用して、要求が匿名で行われたかどうかが示されます。 さらに、Azure Monitor を使用して、匿名認証の条件を使用してストレージ アカウントへの匿名アクセスに対するアラートを生成できます。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

- [Azure Storage Analytics のログ](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging)

- [Azure Storage アカウントのメトリック アラートを構成する方法](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Azure Security Center からの推奨事項に従って、ストレージ アカウントの構成を継続的に監査および監視します。 

- [セキュリティの推奨事項 - リファレンス ガイド](https://docs.microsoft.com/azure/security-center/recommendations-reference)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:自動化されたサードパーティ ソフトウェア修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**:適用できません。Microsoft により、ストレージ アカウントをサポートしている基になるシステムに対して脆弱性の管理が実行されます。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける。

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。 

- [Azure Security Center のセキュリティ スコアについて](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (ストレージ アカウントを含む) のクエリや検出を行います。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。 

- [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

- [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供するストレージ アカウント リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。 

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用し、ストレージ アカウントおよび関連リソースの整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。 

また、Advanced Threat Protection for Azure Storage を使用して、承認されていない Azure リソースを検出します。 

- [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

- [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

- [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Advanced Threat Protection for Azure Storage を構成する](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを保持する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースのインベントリを作成する必要があります。 


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。 

 - 許可されないリソースの種類 
 - 許可されるリソースの種類 

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。 これは、ストレージ アカウントを使用する環境など、高セキュリティ ベースの環境に役立ちます。 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: お客様は、お客様の会社のポリシーで求められているように、Azure Policy を使用してリソースの作成や使用を防ぐことができます。 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。 

- 許可されないリソースの種類 
- 許可されるリソースの種類 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、ストレージ アカウントを使用するリソースなど、高セキュリティ環境内のリソースの作成と変更を防ぐことができます。 

- [ARM へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内のスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースのセキュリティで保護された構成を確立する

**ガイダンス**: ストレージ アカウント インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Storage" 名前空間で Azure Policy エイリアスを使用します。 Azure Storage アカウントに次のような組み込みの Azure Policy 定義を使用することもできます。 

ストレージ アカウントに対する制限のないネットワーク アクセスの監査  
ストレージ アカウントに対する Advanced Threat Protection のデプロイ  
ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある  
ストレージ アカウントへの安全な転送を有効にする必要がある  

ストレージ アカウントの安全な構成基準として Azure Security Center の推奨事項を使用します。 

- [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2:オペレーティング システムのセキュリティで保護された構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3:すべての Azure リソースのセキュリティで保護された構成を保持する

**ガイダンス**: ストレージ アカウント リソース全体にセキュリティで保護された設定を適用するには、Azure Policy [拒否] と [存在する場合はデプロイする] を使用します。 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4:オペレーティング システムのセキュリティで保護された構成を保持する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソースにアクセスするには、Azure Active Directory (Azure AD) で定義された (Azure DevOps に統合されている場合)、または Active Directory で定義された (TFS に統合されている場合) 特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与したり、そのアクセス許可を拒否したりできます。

- [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure DevOps でのアクセス許可とグループについて](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: Azure Policy を利用して、ストレージ アカウントのシステム構成をアラート、監査、および適用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。 

- [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: Azure Security Center を利用して、Azure Storage アカウント リソースのベースライン スキャンを実行します。 

- [Azure Security Center の推奨事項を修復する方法](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-securely-manage-azure-secrets"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます。 Microsoft によって管理されるキーを使用してストレージ アカウントを暗号化することも、独自のキーで暗号化を管理することもできます。 顧客が指定したキーを使用している場合は、Azure Key Vault を利用してキーを安全に格納することができます。 

さらに、ストレージ アカウント キーを頻繁に交換して、ストレージ アカウント キーの紛失や漏えいの影響を軽減します。

- [保存データに対する Azure Storage 暗号化](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

- [ストレージ アカウント アクセス キーを管理する](https://docs.microsoft.com/azure/storage/common/storage-account-keys-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-securely-and-automatically-manage-identities"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**:Azure Active Directory とマネージド ID を使用して、Azure Storage アカウント内の BLOB およびキューへのアクセスを承認します。 Azure の BLOB およびキュー ストレージでは、Azure リソースのマネージド ID を使用した Azure Active Directory (Azure AD) 認証がサポートされています。 Azure リソースのマネージド ID により、Azure 仮想マシン (VM) で実行されているアプリケーション、関数アプリ、仮想マシン スケール セット、およびその他のサービスから Azure AD 資格情報を使用して、BLOB およびキューのデータへのアクセスを認証することができます。 Azure リソースのマネージド ID を Azure AD 認証と一緒に使用することで、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。 

- [マネージド ID を使用して Azure BLOB とキュー データへのアクセス権を付与する方法](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Azure Storage の脅威保護を使用して、ハッシュ評価分析およびアクティブな Tor 出口ノード (匿名化プロキシ) からの疑わしいアクセスを使用して、Azure Storage へのマルウェアのアップロードを検出します。 

また、App Service、Data Lake Storage、Blob Storage などのコンピューティング以外の Azure リソースにアップロードする前に、マルウェアの内容を事前にスキャンして、組織の要件を満たすこともできます。

- [Advanced Threat Protection for Azure Storage を構成する](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection?tabs=azure-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft は、基になるプラットフォーム用のマルウェア対策を処理します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Microsoft Azure ストレージ アカウント内のデータは、持続性と高可用性を保証するため、常に自動的にレプリケートされます。 Azure Storage では、計画されたイベントや計画外のイベント (一時的なハードウェア障害、ネットワークの停止または停電、大規模な自然災害など) から保護するためにデータがコピーされます。 同じデータ センター内、同じリージョン内の複数のゾーン データ センター間、または地理的に分離されたリージョン間でデータをレプリケートすることもできます。 

また、Azure Automation を有効にして、BLOB のスナップショットを定期的に取得することもできます。

- [Azure Storage の冗長性とサービス レベル アグリーメントについて](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

- [BLOB のスナップショットを作成する](https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob)

- [Azure Automation の概要](https://docs.microsoft.com/azure/automation/automation-intro)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: ストレージ アカウントでサポートされているサービスからデータをバックアップするには、azcopy やサード パーティ製のツールを使用するなど、複数の方法を使用できます。 Azure Blob Storage の不変ストレージを使用すると、ユーザーはビジネスに不可欠なデータ オブジェクトを WORM (Write Once Read Many) 状態で保存できます。 この状態では、ユーザーが指定した期間、データを消去および変更できなくなります。

- [AzCopy を使ってみる](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

- [BLOB ストレージの不変ポリシーを設定および管理する](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutability-policies-manage?tabs=azure-portal)

顧客が管理または指定するキーは、Azure CLI または PowerShell を使用して Azure Key Vault 内でサポートできます。 

- [Azure 上でキー コンテナーのキーをバックアップする方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 次の PowerShell コマンドを使用して、Key Vault の証明書、キー、マネージド ストレージ アカウント、およびシークレットのデータ復元を定期的に行います。 

Restore-AzKeyVaultCertificate Restore-AzKeyVaultKey Restore-AzKeyVaultManagedStorageAccount Restore-AzKeyVaultSecret 

- [Key Vault の証明書を復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0)

- [Key Vault のキーを復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

- [Key Vault のマネージド ストレージ アカウントを復元する方法](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultmanagedstorageaccount)

- [Key Vault のシークレットを復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0)

- [AzCopy は、ストレージ アカウント間での BLOB、ファイル、テーブル データのコピーに利用できるコマンドライン ユーティリティです](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

注:Azure Table Storage サービスとの間でデータをコピーする場合、AzCopy バージョン 7.3 をインストールしてください。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**:ストレージ アカウントでカスタマー マネージド キーを有効にするには、Azure Key Vault を使用してキーを格納する必要があります。 キー コンテナーで [論理的な削除] と [Do Not Purge]\(消去しない\) の両方のプロパティを有効にする必要があります。 キー コンテナーの [論理的な削除] 機能では、削除されたコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。 ストレージ アカウント データを Azure Storage BLOB にバックアップする場合、BLOB または BLOB のスナップショットが削除されたときに、論理的な削除機能でデータを保存および復旧することができます。 バックアップを機密データとして扱い、このベースラインの一部として関連するアクセスとデータ保護制御を適用する必要があります。 さらに、保護を強化するために、ビジネスに不可欠なデータ オブジェクトを WORM (Write Once, Read Many) 状態で格納することもできます。

- [Azure Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

- [Azure Storage Blob の論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [不変ストレージを使用してビジネスに不可欠な BLOB データを保存する](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

- [タグを使用した Azure リソースの整理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成する

**ガイダンス**: セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

- [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

- [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources"></a>11.1:Azure リソースの侵入テストを定期的に実施する

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
