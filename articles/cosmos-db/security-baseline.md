---
title: Cosmos DB 用の Azure セキュリティ ベースライン
description: Cosmos DB 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3431ad88a27da9d83b75ed3661d6129f157b9b1f
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87531508"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Cosmos DB 用の Azure セキュリティ ベースライン

Cosmos DB 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1:Virtual Network でネットワーク セキュリティ グループまたは Azure Firewall を使用してリソースを保護する

**ガイダンス**: Azure Private Link を使用すると、プライベート エンドポイント経由で Azure Cosmos アカウントに接続できます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由して、パブリック インターネットからの公開を排除します。 また、ネットワーク セキュリティ グループ (NSG) に対して厳格な一連のアウトバウンド規則を構成し、その NSG をサブネットに関連付けることによって、データ窃盗のリスクを軽減することもできます。

さらに、サービス エンドポイントを使用して Azure Cosmos アカウントをセキュリティで保護することもできます。 サービス エンドポイントを有効にすることで、Azure Virtual Network の特定のサブネットのみからアクセスを許可するように Azure Cosmos アカウントを構成できます。 Azure Cosmos DB サービス エンドポイントを有効にすると、仮想ネットワーク内のサブネットからの接続について Azure Cosmos アカウントへのアクセスを制限することができます。

また、IP ファイアウォールを使用して、Azure Cosmos アカウント内の格納データをセキュリティで保護することもできます。 Azure Cosmos DB では、受信ファイアウォールをサポートするために IP ベースのアクセス制御に対応しています。 Azure Cosmos アカウントに IP ファイアウォールを設定するには、Azure portal、Azure Resource Manager テンプレートを使用するか、Azure CLI または Azure PowerShell を使用します。

Azure Private Link の概要: https://docs.microsoft.com/azure/private-link/private-link-overview

Azure Cosmos DB のプライベート エンドポイントを構成する方法: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

セキュリティ構成を使用してネットワーク セキュリティ グループを作成する方法: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Cosmos DB で IP ファイアウォールを構成する方法: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2:VNet、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Security Center を使用し、ネットワークの保護に関する推奨事項に従って、Azure Cosmos アカウントに関連するネットワーク リソースをセキュリティで保護します。

Azure Cosmos アカウントと同じ仮想ネットワークに仮想マシンがデプロイされている場合、ネットワーク セキュリティ グループ (NSG) を使用して、データ窃盗のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

Azure Security Center によって提供されるネットワークのセキュリティの概要: https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: クロス オリジン リソース共有 (CORS) 機能を有効にして、あるドメインで実行されている Web アプリケーションが別のドメイン内にあるリソースにアクセスできるようにします。 Web ブラウザーでは、Web ページから別のドメインの API を呼び出すことを防ぐために、同一オリジン ポリシーと呼ばれるセキュリティ制限が実装されています。 ただし、CORS を使用すれば、オリジン ドメインから他のドメイン内の API を安全に呼び出すことができます。 Azure Cosmos アカウントに対して CORS のサポートを有効すると、認証済みの要求だけが指定されたルールに従って評価され、それらが許可されるかどうかが判断されます。

クロスオリジン リソース共有の構成: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Cosmos DB の Advanced Threat Protection (ATP) を使用します。 Azure Cosmos DB の ATP は、Azure Cosmos DB アカウントに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。 この保護レイヤーにより、脅威に対処でき、中央のセキュリティ監視システムと統合することができます。

Azure Cosmos DB インスタンスに関連付けられている Virtual Network に対して DDoS Protection 標準を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

Azure Cosmos DB Advanced Threat Protection を構成する方法: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

DDoS 保護を構成する方法: https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Azure Security Center の統合された脅威インテリジェンスの概要: https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets-and-flow-logs"></a>1.5:ネットワーク パケットとフロー ログを記録する

**ガイダンス**:ネットワーク セキュリティ グループ (NSG) フロー ログを有効にし、トラフィック監査のためにログをストレージ アカウントに送信します。 NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して、Azure クラウド内のトラフィック フローに関する分析情報を提供できます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

NSG フロー ログを有効にする方法: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics を有効にして使用する方法: https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: Azure Cosmos DB の Advanced Threat Protection (ATP) を使用します。 Azure Cosmos DB の ATP は、Azure Cosmos DB アカウントに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。 この保護レイヤーにより、脅威に対処でき、中央のセキュリティ監視システムと統合することができます。 

Cosmos DB Advanced Threat Protection を構成する方法: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Cosmos アカウントにアクセスする必要があるリソースについては、Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソース フィールドまたはターゲット フィールドにサービス タグ名 (たとえば AzureCosmosDB) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

サービス タグの使用方法の詳細については、 https://docs.microsoft.com/azure/virtual-network/service-tags-overview を参照してください。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、ネットワーク リソースの標準的なセキュリティ構成を定義し、実装します。 "Microsoft.DocumentDB" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、Azure Cosmos DB インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 また、次のような Azure Cosmos DB 用の組み込みのポリシー定義を使用することもできます。

- Cosmos DB アカウントの Advanced Threat Protection のデプロイ

- Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要がある

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整できます。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint を作成する方法: https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Cosmos DB デプロイに関連付けられているネットワーク リソースを分類別に論理的に整理するために、それらのリソースにタグを使用します。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Cosmos DB インスタンスに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。 

Azure アクティビティ ログ イベントを表示して取得する方法: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Azure Monitor でアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft では、Azure Cosmos DB などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。


**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**:Azure Monitor を介してログを取り込み、Azure Cosmos DB によって生成されたセキュリティ データを集計します。 Azure Monitor 内で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期/アーカイブ ストレージにはストレージ アカウントを使用します。 または、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) に対してデータを有効にしてオンボードすることもできます。 

Azure Cosmos DB の診断ログを有効にする方法: https://docs.microsoft.com/azure/cosmos-db/logging

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: Azure Cosmos DB の診断設定を有効にして、ログを Log Analytics ワークスペースまたはストレージ アカウントに送信します。 Azure Cosmos DB の診断設定は、リソース ログの収集に使用されます。 これらのログは、要求ごとにキャプチャされ、"データ プレーン ログ" とも呼ばれます。 データ プレーン操作の例としては、delete、insert、read などがあります。 また、Azure アクティビティ ログの診断設定を有効にして、それを同一の Log Analytics ワークスペースに送信することもできます。

Azure Cosmos DB の診断設定を有効にする方法: https://docs.microsoft.com/azure/cosmos-db/logging

Azure アクティビティ ログの診断設定を有効にする方法: https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor で、組織のコンプライアンス規則に従って、Azure Cosmos DB インスタンスに関連付けられている Log Analytics ワークスペースのログ保有期間を設定します。

ログ保有期間のパラメーターを設定する方法: https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Log Analytics ワークスペースでクエリを実行して、用語の検索、傾向の特定、パターンの分析を行うことができ、収集した Azure Cosmos DB ログに基づいて他の多くの分析情報を提供することもできます。

Log Analytics ワークスペースで Azure Cosmos DB に対するクエリを実行する方法: https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**:Azure Security Center で、Azure Cosmos DB の Advanced Threat Protection を有効にして、セキュリティ ログおよびイベント内の異常なアクティビティを監視します。 Azure Cosmos DB で診断設定を有効にし、Log Analytics ワークスペースにログを送信します。

 

セキュリティ オーケストレーション自動応答 (SOAR) ソリューションが提供されるため、Log Analytics ワークスペースを Azure Sentinel にオンボードすることもできます。 これにより、プレイブック (自動化されたソリューション) を作成して、セキュリティの問題を修復するために使用できます。 また、Azure Monitor を使用して、Log Analytics ワークスペースでカスタムのログ アラートを作成することもできます。

Azure Cosmos DB 向け脅威保護アラートの一覧: https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor を使用してログ アラートを作成、表示、管理する: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Cosmos DB では、マルウェア対策関連のログの処理や生成を行いません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Cosmos DB では、DNS 関連のログの処理や生成を行いません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure portal の ID およびアクセス管理 (IAM) ペインを使用して、Azure Cosmos DB リソースでロールベースのアクセス制御 (RBAC) を構成し、インベントリを保持することができます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。 組み込みのロールまたはカスタム ロールは、個人とグループに使用できます。

Azure Cosmos DB には、Azure Cosmos DB の一般的な管理シナリオに対応する RBAC が組み込まれています。 Azure Active Directory (AD) にプロファイルを持つ個人は、これらの Azure ロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Cosmos DB リソース上のリソースと操作へのアクセスを許可または拒否できます。

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出することもできます。 

さらに、Azure Cosmos DB での一部のアクションを、Azure Active Directory およびアカウント固有のマスター キーで制御することもできます。  'disableKeyBasedMetadataWriteAccess' アカウント設定を使用してキー アクセスを制御します。

Azure Cosmos DB のロールベースのアクセス制御の概要: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Azure Cosmos DB アクション (Microsoft.DocumentDB 名前空間) を使用して独自のカスタム ロールを作成する: https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Azure Active Directory で新しいロールを作成する: https://docs.microsoft.com/azure/role-based-access-control/custom-roles

PowerShell を使用して Azure Active Directory でディレクトリ ロールを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

PowerShell を使用して Azure Active Directory でディレクトリ ロールのメンバーを取得する方法: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

ユーザー アクセスをデータ操作のみに制限する: https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Azure AD または Azure Cosmos DB に関しては、既定または空白のパスワードの概念は存在しません。 代わりに、Azure Cosmos DB では、2 種類のキー (マスター キーとリソース トークン) を使用してユーザーを認証し、そのデータとリソースへのアクセスを提供しています。 キーは、いつでも再生成することができます。

Azure Cosmos DB のデータへのアクセスのセキュリティ保護の概要: https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Azure Cosmos DB キーを再生成する方法: https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

Azure Active Directory を使用してプログラムでキーにアクセスする方法: https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 適用できません。Azure Cosmos DB は、管理者アカウントをサポートしていません。  すべてのアクセスは、Azure Active Directory と Azure ロールベースのアクセス制御 (Azure RBAC) に統合されています。



**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**:Azure Cosmos DB は、2 種類のキーを使用してユーザーを承認し、データ プレーン レベルでのシングル サインオン (SSO) はサポートされていません。 Cosmos DB のコントロール プレーンへのアクセスは、REST API 経由で行うことができ、SSO がサポートされています。 認証を行うには、Azure Active Directory から取得した要求の Authorization ヘッダーを JSON Web トークンに設定します。

Azure Database for Cosmos DB REST API の概要: https://docs.microsoft.com/rest/api/cosmos-db/

Azure Active Directory での SSO の概要: https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory Multi-Factor Authentication を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Azure Security Center 内で ID とアクセスを監視する方法: https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: Multi-Factor Authentication が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

特権アクセス ワークステーションについて: https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Azure で MFA を有効にする方法: https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティを記録してアラートを生成する

**ガイダンス**: Azure Cosmos DB の Advanced Threat Protection (ATP) を使用します。 Azure Cosmos DB の ATP は、Azure Cosmos DB アカウントに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。 この保護レイヤーにより、脅威に対処でき、中央のセキュリティ監視システムと統合することができます。 

さらに、Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成できます。

Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

Privileged Identity Management (PIM) をデプロイする方法: https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Azure AD のリスク検出の概要: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセス ポリシーの場所の条件を構成し、ネームド ロケーションを管理します。 ネームド ロケーションを使用すると、IP アドレス範囲または国や地域の論理グループを作成できます。 機密性の高いリソース (Azure Cosmos DB インスタンスなど) へのアクセスを、構成したネームド ロケーションに制限することができます。

Azure でネームド ロケーションを構成する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (AD) を中央認証および承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure Active Directory インスタンスを作成して構成する方法: https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Azure SQL による Azure Active Directory 認証を構成して管理する方法: https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory では、古いアカウントの検出に役立つログが提供されます。 さらに、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、ロールの割り当てを効率的に管理できます。 ユーザーのアクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

Azure ID アクセスレビューの使用方法: https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: 非アクティブ化されたアカウントへのアクセス試行を監視する

**ガイダンス**: Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信して、そこで目的のアラートを構成できます。

Azure Activity Logs を Azure Monitor に統合する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Cosmos DB の Advanced Threat Protection (ATP) を使用します。 Azure Cosmos DB の ATP は、Azure Cosmos DB アカウントに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを検出するセキュリティ インテリジェンスの追加レイヤーを提供します。 この保護レイヤーにより、脅威に対処でき、中央のセキュリティ監視システムと統合することができます。 

検出された、ユーザー ID に関連する疑わしいアクションへの自動応答を構成するには、Azure AD Identity Protection とリスク検出機能を使用することもできます。 また、さらに詳しく調査するためにログを Azure Sentinel に取り込むこともできます。

Azure Active Directory の危険なサインインを表示する方法: https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Identity Protection のリスク ポリシーを構成して有効にする方法: https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel をオンボードする方法: https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 現在使用できません。カスタマー ロックボックスでは、Azure Database for Cosmos DB はまだサポートされていません。

カスタマー ロックボックスでサポートされているサービスの一覧: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure Cosmos DB インスタンスを追跡しやすくするには、タグを使用します。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 Azure Cosmos DB インスタンスは、仮想ネットワークまたはサブネットで分離され、適切なタグが付けられ、ネットワーク セキュリティ グループ (NSG) または Azure Firewall 内でセキュリティ保護されます。 機密データを格納する Azure Cosmos DB インスタンスは、分離する必要があります。 Azure Private Link を使用すると、プライベート エンドポイント経由で Azure Cosmos DB インスタンス アカウントに接続できます。 プライベート エンドポイントは、仮想ネットワークのサブネットにある一組のプライベート IP アドレスです。 その後、選択したプライベート IP アドレスへのアクセスを制限できます。 

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Azure Cosmos DB のプライベート エンドポイントを構成する方法: https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

セキュリティ構成を使用してネットワーク セキュリティ グループを作成する方法: https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Cosmos DB の Advanced Threat Protection をデプロイすることができます。これにより、データベースに対して通常と異なる潜在的に有害なアクセスまたは悪用が試行されたことを示す異常なアクティビティが検出されます。 現在、これにより次のアラートがトリガーされる場合があります。

- 通常と異なる場所からのアクセス

- 通常と異なるデータの抽出

さらに、仮想マシンを使用して Azure Cosmos DB インスタンスにアクセスする場合は、Private Link、Firewall、ネットワーク セキュリティ グループ、サービス タグを使用して、データ窃盗の可能性を軽減します。 Microsoft では、Azure Cosmos DB 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Cosmos DB Advanced Threat Protection を構成する方法: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Cosmos DB へのすべての接続で HTTPS がサポートされます。 Azure Cosmos DB では TLS 1.2 もサポートされます。 サーバー側で最低限の TLS バージョンを強制できます。 それを行うには、[azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com) にお問い合わせください。

Cosmos DB セキュリティの概要: https://docs.microsoft.com/azure/cosmos-db/database-security

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Cosmos DB では、データの自動識別、分類、損失防止の各機能はまだ使用できません。 ただし、分類とデータ分析には、Azure Cognitive Search 統合を使用できます。 さらに、コンプライアンスのために、必要に応じて、サードパーティ製のソリューションを実装することもできます。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure Cognitive Search を使用して Azure Cosmos DB データのインデックスを作成する方法: https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&amp;bc=/azure/cosmos-db/breadcrumb/toc.json

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Cosmos DB には、Azure Cosmos DB の一般的な管理シナリオに対応するロールベースのアクセス制御 (RBAC) が組み込まれています。 Azure Active Directory にプロファイルを持つ個人は、これらの Azure ロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Cosmos DB リソース上のリソースと操作へのアクセスを許可または拒否できます。 ロールの割り当ては、Azure Cosmos アカウント、データベース、コンテナー、およびオファー (スループット) へのアクセスを含む、コントロールプレーン アクセスのみに限定されています。

Azure Cosmos DB で RBAC を実装する方法: https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

Microsoft では、Cosmos DB 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

Azure での顧客データの保護の概要: https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Cosmos DB に格納されているユーザー データはすべて、既定により、保存時に暗号化されます。 これをオフにするコントロールはありません。 Azure Cosmos DB では、アカウントが実行されているすべてのリージョンで AES 256 暗号化が使われます。

既定では、お使いの Azure Cosmos アカウントのデータの暗号化に使用するキーは Microsoft が管理します。 必要に応じ、お使いのご自分のキーに 2 番目の暗号化レイヤーの追加を選択できます。

Azure Cosmos DB での保存時の暗号化の概要: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Azure Cosmos DB での保存時の暗号化のためのキー管理の概要: https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Azure Cosmos DB アカウントのカスタマー マネージド キーを構成する方法: https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure アクティビティ ログで Azure Monitor を使用して、Azure Cosmos DB の実稼働インスタンスに対して変更が行われたときのアラートを作成します。

Azure アクティビティ ログ イベントのアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Azure アクティビティ ログ イベントのアラートを作成する方法: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:Azure Security Center からの Azure Cosmos DB インスタンスに関する推奨事項に従います。 

Microsoft では、Azure Cosmos DB インスタンスをサポートする、基になるホストに対してシステム パッチと脆弱性管理を実行します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

Azure Security Center でサポートされている使用可能な機能: https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3:サードパーティの自動化されたソフトウェア修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-azure-asset-discovery"></a>6.1:Azure Asset Discovery を使用する

**ガイダンス**: Azure portal または Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (Azure Cosmos DB に限定されず、コンピューティング、その他のストレージ、ネットワーク、ポート プロトコルなどのリソースも含まれます) を検出します。  テナント内の適切なアクセス許可を持っていること、およびサブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

Azure Resource Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Azure サブスクリプションを表示する方法: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure ロールベースのアクセス制御の概要: https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを使用して Azure Cosmos DB インスタンスと関連リソースにタグを適用し、それらを論理的に整理して分類します。

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

タグをサポートする Azure Cosmos DB リソース: https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: タグ付け、管理グループ、必要に応じて個別のサブスクリプションを使用して、資産 (Azure Cosmos DB リソースも含まれますが、これに限定されません) の整理と追跡を行うことができます。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

追加の Azure サブスクリプションを作成する方法: https://docs.microsoft.com/azure/billing/billing-create-subscription

管理グループを作成する方法: https://docs.microsoft.com/azure/governance/management-groups/create

タグを作成して使用する方法: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4:承認された Azure リソースとソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースと Azure 全体を対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Graph を使用してクエリを作成する方法: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。このベースラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**:適用できません。このガイドラインは、コンピューティング リソースと Azure 全体を対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類 

- 許可されるリソースの種類

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy を使用して特定のリソースの種類を拒否する方法: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-implement-approved-application-list"></a>6.10:承認されたアプリケーションの一覧を実装する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11:スクリプトを使用して Azure Resource Manager を操作するユーザーの権限を制限する

**ガイダンス**: Azure Conditional Access を使用して Azure Resource Manager を操作するユーザーの権限を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。 これにより、高セキュリティ環境内でのリソースの作成や変更を防ぐことができます。

条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**:適用できません。このガイドラインは、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**:Azure Policy を使用して、Cosmos DB インスタンスの標準的なセキュリティ構成を定義して実装します。 "Microsoft.DocumentDB" 名前空間で Azure Policy エイリアスを使用して、Cosmos DB インスタンスの構成を監査または適用するためのカスタム ポリシーを作成します。 また、次のような Azure Cosmos DB 用の組み込みのポリシー定義を使用することもできます。

- Cosmos DB アカウントの Advanced Threat Protection のデプロイ

- Cosmos DB は仮想ネットワーク サービス エンドポイントを使用する必要がある

使用可能な Azure Policy エイリアスを表示する方法: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy の効果の概要: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:Cosmos DB または関連リソースにカスタムの Azure Policy 定義を使用している場合は、Azure Repos を使用してコードを安全に格納して管理します。

Azure Repos のドキュメント: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-system-configuration-management-tools"></a>7.7:システム構成管理ツールをデプロイする

**ガイダンス**: "Microsoft.DocumentDB" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用のシステム構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9:Azure サービスの自動構成監視を実装する

**ガイダンス**: "Microsoft.DocumentDB" 名前空間で Azure Policy エイリアスを使用して、システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成します。 Azure Policy の [audit]、[deny]、[deploy if not exist] を使用して、Azure Cosmos DB インスタンスおよび関連リソースの構成を自動的に適用します。 

Azure Policy を構成して管理する方法: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: Azure Cosmos DB インスタンスへのアクセスに使用されている Azure App Service で実行中の Azure 仮想マシンまたは Web アプリでは、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Cosmos DB のシークレット管理を簡素化し、セキュリティで保護します。 Key Vault の論理的な削除が有効になっていることを確認します。

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

キー コンテナーを作成する方法: https://docs.microsoft.com/azure/key-vault/quick-create-portal

マネージド ID で Key Vault の認証を提供する方法: https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure Cosmos DB インスタンスへのアクセスに使用されている Azure App Service で実行中の Azure 仮想マシンまたは Web アプリでは、マネージド サービス ID を Azure Key Vault と組み合わせて使用して、Azure Cosmos DB のシークレット管理を簡素化し、セキュリティで保護します。

マネージド ID を使用して、Azure Active Directory (AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

マネージド ID を構成する方法: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Azure マネージド ID と統合する方法: https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

資格情報スキャナーを設定する方法: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。 Microsoft Antimalware は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**:Microsoft Antimalware は、Azure サービス (Azure App Service など) をサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Azure Cosmos DB を含む非コンピューティング Azure リソースにアップロードされているファイルを事前にスキャンする必要があります。 Microsoft は、お客様のデータにアクセスできないため、お客様に代わってお客様のコンテンツのマルウェア対策スキャンを実行することはできません。


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。ベンチマークは、コンピューティング リソースを対象にしています。 Microsoft Antimalware は、Azure サービスをサポートする基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。


**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**:Azure Cosmos DB では、4 時間ごとにデータのスナップショットが取得されます。 すべてのバックアップは別々のストレージ サービス内に個別に保存されます。これらのバックアップは、リージョンの障害からの回復性を確保するためにグローバルにレプリケートされます。 どのような場合でも、最新の 2 つのスナップショットのみが保持されます。 ただし、コンテナーまたはデータベースが削除された場合、Azure Cosmos DB には、ある特定のコンテナーまたはデータベースの既存のスナップショットが 30 日間保持されます。 Azure サポートに連絡して、バックアップから復元してください。

Azure Cosmos DB の自動バックアップの概要: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Cosmos DB では、データのバックアップが一定の間隔で自動的に取得されます。 データベースまたはコンテナーを削除した場合、サポート チケットを申請するか、Azure サポートに連絡して、自動オンライン バックアップからデータを復元できます。 Azure サポートは、Standard、Developer、またはそれよりも高度なプランなどの特定のプランでのみ利用できます。 バックアップの特定のスナップショットを復元するには、該当のスナップショットのバックアップ サイクル期間中にデータが利用可能であることが、Azure Cosmos DB から求められます。 

Key Vault を使用して Cosmos DB インスタンスの資格情報を格納する場合は、キーの自動バックアップが定期的に実行されていることを確認してください。

Azure Cosmos DB の自動バックアップの概要: https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Azure Cosmos DB 内のデータを復元する方法: https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Key Vault のキーをバックアップする方法: https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: データベースまたはコンテナーを削除した場合、サポート チケットを申請するか、Azure サポートに連絡して、自動オンライン バックアップからデータを復元できます。 Azure サポートは、Standard、Developer、またはそれよりも高度なプランなどの特定のプランでのみ利用できます。 バックアップの特定のスナップショットを復元するには、該当のスナップショットのバックアップ サイクル期間中にデータが利用可能であることが、Azure Cosmos DB から求められます。

PowerShell を使用して、Azure Key Vault に格納されているシークレットの復元をテストします。 Restore-AzureKeyVaultKey コマンドレットは、指定されたキー コンテナー内にキーを作成します。 このキーは、入力ファイル内のバックアップ キーのレプリカであり、元のキーと同じ名前を持ちます。

Azure Cosmos DB の自動バックアップの概要:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Azure Cosmos DB 内のデータを復元する方法:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Azure Key Vault のシークレットを復元する方法:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**:Cosmos DB に保存されているすべてのユーザー データは、保存時と転送時に暗号化されているため、操作は何も必要ありません。 つまり、保存時の暗号化機能は、既定で "オン" になっています。 オンまたはオフにするコントロールはありません。 Azure Cosmos DB では、アカウントが実行されているすべてのリージョンで AES 256 暗号化が使われます。

Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

Azure Cosmos DB でのデータの暗号化の概要: https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Key Vault で論理的な削除を有効にする方法: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center の監視**: はい

**責任**: 共有

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

独自のインシデント対応計画の作成のために、NIST の「コンピューター セキュリティ インシデント対応ガイド」 (https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf ) を利用することもできます。

Azure Security Center 内でワークフロー自動化を構成する方法: https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Microsoft Security Response Center のインシデントの構造: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された結果または分析における Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある目的があったかどうかの信頼レベルに基づいて決定されます。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: 定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

NIST の出版物を参照してください。IT の計画と機能に関するテスト、トレーニング、演習プログラムのガイド: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。  事後にインシデントをレビューして、問題が解決されていることを確認します。

Azure Security Center のセキュリティ連絡先を設定する方法: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

連続エクスポートを構成する方法: https://docs.microsoft.com/azure/security-center/continuous-export

Azure Sentinel にアラートをストリーミングする方法: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**:セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

ワークフローの自動化と Logic Apps を構成する方法: https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、60 日以内に確実に修復されるようにします

**ガイダンス**:お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の活動規則 https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 に従ってください。

Microsoft マネージド クラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらの https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e で確認できます。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
