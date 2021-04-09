---
title: Logic Apps 用の Azure セキュリティ ベースライン
description: Logic Apps セキュリティ ベースラインは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースを提供します。
author: msmbaldwin
ms.service: logic-apps
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: cf4e02f925e0c8c4024137589c0c9c4074c0aa11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596748"
---
# <a name="azure-security-baseline-for-logic-apps"></a>Logic Apps 用の Azure セキュリティ ベースライン

Logic Apps 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つ推奨事項が含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview.md) に基づいて作成されています。

詳細については、[Azure セキュリティ ベースラインの概要](../security/benchmarks/security-baselines-overview.md)に関するページを参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**:"グローバルな" マルチテナント Logic Apps サービスで実行されるコネクタは、Microsoft によってデプロイおよび管理されます。 これらのコネクタでは、Office 365、Azure Blob Storage、SQL Server、Dynamics、Salesforce、SharePoint など、クラウド サービスとオンプレミス システムの一方または両方にアクセスするためのトリガーとアクションが提供されます。 ネットワーク セキュリティ グループまたは Azure Firewall で関連リソースへのアクセスを許可する規則を指定するときに、AzureConnectors サービス タグを使用できます。

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるロジック アプリの場合は、専用のリソースでロジック アプリをビルド、デプロイ、実行できる統合サービス環境 (ISE) を作成できます。 一部の Azure 仮想ネットワークでは、プライベート エンドポイント (Azure Private Link) を使用して、Azure でホストされている Azure Storage、Azure Cosmos DB、Azure SQL Database、パートナー サービス、カスタマー サービスなどの Azure PaaS サービスへのアクセスが提供されます。 ロジック アプリでプライベート エンドポイントを使用する仮想ネットワークにアクセスする必要がある場合は、ISE の内部でそれらのロジック アプリを作成、デプロイ、実行する必要があります。

ISE を作成するときに、内部アクセス エンドポイントと外部アクセス エンドポイントのどちらを使用するかを選択できます。 この選択により、ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかが決まります。 内部および外部アクセス エンドポイントは、実行の入力と出力を含むロジック アプリの実行履歴を仮想ネットワークの内部または外部から表示できるかどうかにも影響します。

お使いの ISE に関連するすべての Virtual Network サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認してください。 ISE でロジック アプリをデプロイする場合は、Private Link を使用します。 Azure Private Link を使用すると、お使いの仮想ネットワーク内のプライベート エンドポイント経由で、Azure PaaS サービスと Azure でホストされている顧客所有のサービスまたはパートナー サービスにアクセスできます。 または、特定のユース ケースがある場合は、Azure Firewall を実装することでこの要件を満たすことができます。 セキュリティ規則を設定する際の複雑さを軽減するには、特定の Azure サービスの IP アドレス プレフィックスのグループを表すサービス タグを使用します。

- [Logic Apps のコネクタについて](../connectors/apis-list.md)

- [Azure のサービス タグについて](../virtual-network/service-tags-overview.md)

- [統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure Virtual Network リソースにアクセスする方法について](connect-virtual-network-vnet-isolated-environment-overview.md)

- [仮想ネットワーク サービス エンドポイントについて](../virtual-network/virtual-network-service-endpoints-overview.md)

- [Azure Private Link について](../private-link/private-link-overview.md)

- [ISE エンドポイント アクセスについて](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../firewall/tutorial-firewall-deploy-portal.md)

- [ISE のアクセスを有効にする方法](connect-virtual-network-vnet-isolated-environment.md#enable-access-for-ise)

**Azure Security Center の監視**: はい

**責任**: 共有

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**:外部アクセス ポイントを使用する統合サービス環境 (ISE) でロジック アプリを実行する場合は、ネットワーク セキュリティ グループ (NSG) を使用して、データ流出のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [ISE エンドポイント アクセスについて](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: 着信した呼び出しや要求を受け取る要求ベースのトリガー (Request トリガーや Webhook トリガーなど) がロジック アプリで使用されている場合、承認されたクライアントだけがそのロジック アプリを呼び出すことができるように、アクセスを制限できます。

統合サービス環境 (ISE) でロジック アプリを実行している場合は、自分の ISE に関連付けられている仮想ネットワークで DDoS Protection 標準を有効にして、DDoS 攻撃から保護してください。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある、または未使用のインターネット IP アドレスとの通信を拒否します。

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスとともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

Azure Security Center の Just In Time ネットワーク アクセスを使用して、NSG を構成し、限られた期間、承認された IP アドレスへのエンドポイントの公開を制限します。

Azure Security Center のアダプティブ ネットワークのセキュリティ強化を使用して、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨します。

- [Logic Apps への受信呼び出しをセキュリティで保護する方法](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [受信 IP アドレスを制限する方法](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

- [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center の Just In Time ネットワーク アクセス制御について](../security-center/security-center-just-in-time.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**:外部アクセス ポイントを使用する統合サービス環境 (ISE) でロジック アプリを実行する場合は、ネットワーク セキュリティ グループ (NSG) を使用して、データ流出のリスクを軽減することができます。 NSG フロー ログを有効にし、トラフィック監査のためにログを Azure Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

追加の保護を施し、ネットワーク トラフィックに関する情報を提供するには、アクセス ログを参照してください。アクセス ログは、各 Application Gateway インスタンスでこれを有効にした場合にのみ生成されます。 このログを使用して Application Gateway のアクセス パターンを表示し、重要な情報を分析できます。 これには、呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。

それ以外の場合は、マーケットプレースのサードパーティ製ソリューションを利用して、この要件を満たすことができます。

- [ISE エンドポイント アクセスについて](connect-virtual-network-vnet-isolated-environment-overview.md#ise-endpoint-access)

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [内部 VNET 内の API Management と Application Gateway を統合する方法](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [WAF アクセス ログを理解する方法](../web-application-firewall/ag/web-application-firewall-logs.md#access-log)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。  ペイロード検査に基づく侵入検出または侵入防止が要件でない場合は、脅威インテリジェンスを備えた Azure Firewall を使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理では、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックを警告処理して拒否できます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

悪意のあるトラフィックを検出および拒否できるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 統合サービス環境 (ISE) でロジック アプリを実行している場合は、Azure Application Gateway をデプロイします。

- [内部 VNet 内の API Management と Application Gateway を統合する方法](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md)

- [HTTPS を使用するように Application Gateway を構成する方法](../application-gateway/create-ssl-portal.md) 

- [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../application-gateway/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Logic Apps インスタンスへのアクセスが必要なリソースについては、仮想ネットワーク サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (LogicApps、LogicAppsManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグの使用に関する詳細](../virtual-network/service-tags-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、対象の Azure Logic Apps インスタンスに関連するネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 Azure Logic Apps インスタンスのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Logic" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用します。 次のように、組み込みのポリシー定義を使用することもできます。

- Logic Apps で診断ログを有効にする必要がある

- DDoS Protection Standard を有効にする必要がある

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ("タグとその値が必要" など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [Logic Apps の Azure Policy 定義の一覧](./policy-reference.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、対象の Azure Logic Apps インスタンスに関連するネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳しくは、「[Azure Security ベンチマーク:ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**:Microsoft では、Azure Logic Apps などの Azure リソースに使用するタイム ソースを、ログ内にタイムスタンプとして保持します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: 実行時にロジック アプリに関するより豊富なデバッグ情報を取得するには、Azure Monitor ログを設定して使用し、トリガー イベント、実行イベント、アクション イベントなどのランタイム データやイベントに関する情報を Log Analytics ワークスペースに記録して格納します。 Azure Monitor を使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 Azure Monitor ログを使用することで、この情報を収集して確認するのに役立つログ クエリを作成できます。 さらに、Azure Storage や Azure Event Hubs などの他の Azure サービスでこの診断データを使用することもできます。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する方法](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 実行時にロジック アプリに関するより豊富なデバッグ情報を取得するには、Azure Monitor ログを設定して使用し、トリガー イベント、実行イベント、アクション イベントなどのランタイム データやイベントに関する情報を Log Analytics ワークスペースに記録して格納します。 Azure Monitor を使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 Azure Monitor ログを使用することで、この情報を収集して確認するのに役立つログ クエリを作成できます。 さらに、Azure Storage や Azure Event Hubs などの他の Azure サービスでこの診断データを使用することもできます。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する方法](monitor-logic-apps-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:ロジック アプリを作成して実行すると、ロジック アプリの実行状態、実行の履歴、トリガー履歴、およびパフォーマンスを確認できます。 リアルタイムでのイベントの監視と高度なデバッグを行うには、Azure Monitor ログを使用してロジック アプリの診断ログを設定します。 この Azure サービスを使用すると、クラウド環境とオンプレミス環境を監視して、可用性とパフォーマンスをより簡単に維持することができます。 これにより、トリガー イベント、実行イベント、アクション イベントなどのイベントを検索して表示できます。 この情報を Azure Monitor ログに格納することで、この情報の検索と分析に役立つログ クエリを作成できます。 この診断データは、Azure Storage や Azure Event Hubs などの他の Azure サービスで使用することもできます。

Azure Monitor で、組織のコンプライアンス規則に従って、Azure Logic Apps インスタンスに関連付けられているログの保持期間を設定します。

- [Azure Logic Apps で実行状態の監視、トリガー履歴の確認、アラートの設定を行う方法](monitor-logic-apps.md)

- [ログ保持期間のパラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: ロジック アプリのログを設定するには、ロジック アプリを作成するときに Log Analytics を有効にするか、既存のロジック アプリの Log Analytics ワークスペースに Logic Apps 管理ソリューションをインストールします。 このソリューションでは、ロジック アプリの実行に関する集約情報が提供され、状態、実行時間、再送信の状態、関連付け ID などの特定の詳細情報が含まれています。 次に、この情報に対してログの記録とクエリの作成を有効にするために、Azure Monitor ログを設定します。

また、Azure アクティビティ ログの診断設定を有効にし、Log Analytics ワークスペースにログを送信することもできます。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、Azure Logic Apps 用に収集された可能性があるアクティビティ ログ データに基づいて、多くの他の分析情報を提供します。

または、Azure Sentinel またはサードパーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure Monitor ログを設定し、Azure Logic Apps の診断データを収集する方法](monitor-logic-apps-log-analytics.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Monitor の Log Analytics で Azure アクティビティ ログを収集して分析する方法](../azure-monitor/essentials/activity-log.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md) 

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Log Analytics と共に Azure Security Center を使用し、セキュリティ ログやイベントで検出される異常なアクティビティに対する監視とアラートの送信を行います。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../azure-monitor/alerts/tutorial-response.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 適用できません。Azure Logic Apps では、マルウェア対策関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Logic Apps では、DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

サインインすることなく他の Azure Active Directory (Azure AD) によって保護される他のリソースに容易にアクセスして ID の認証を行うため、ロジック アプリは、資格情報やシークレットではなくマネージド ID (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。

ロジック アプリのすべての要求エンドポイントは、エンドポイントの URL に Shared Access Signature (SAS) を含みます。 要求ベースのトリガーのエンドポイント URL を他のパーティと共有する場合は、特定のキーを使用する有効期限付きのコールバック URL を生成できます。 そうすることで、キーをシームレスに交換したり、ロジック アプリのトリガーに対するアクセスを特定の期間に基づいて制限したりできます。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する](create-managed-service-identity.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

- [SAS を使用して Azure Logic Apps のアクセスとデータをセキュリティで保護する方法](logic-apps-securing-a-logic-app.md#sas)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Active Directory および Azure Logic Apps には、既定のパスワードという概念はありません。

基本認証を使用している場合は、ユーザー名とパスワードを指定する必要があります。 これらの資格情報を作成するときは、認証用に強力なパスワードを構成してください。

コードとしてのインフラストラクチャを使用している場合は、パスワードをコードに格納せずに、Azure Key Vault を使用して資格情報の格納と取得を行います。

- [Logic Apps のデータをセキュリティで保護してアクセスする方法](logic-apps-securing-a-logic-app.md)

- [Azure Key Vault との間でシークレットの設定と取得を行う方法](../key-vault/general/quick-create-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: Azure アプリの登録 (サービス プリンシパル) を使用して、API 呼び出しを介して Recovery Services コンテナーと対話するために使用できるトークンを取得します。

多くのコネクタでは、対象のロジック アプリでトリガーやアクションを使用する前に、最初に対象のサービスまたはシステムへの接続を作成し、認証資格情報またはその他の構成の詳細を指定する必要があります。 たとえば、ユーザーに代わってデータにアクセスしたり、投稿したりするために Twitter アカウントへの接続を承認する必要があります。

Azure Active Directory (Azure AD) OAuth を使用するコネクタの場合、接続の作成は、Office 365、Salesforce、GitHub などのサービスへのサインインを意味し、そこでアクセス トークンが暗号化され、Azure シークレット ストアに安全に保存されます。 FTP や SQL などのその他のコネクタでは、サーバーのアドレス、ユーザー名、およびパスワードなどの構成の詳細を含む接続が必要です。 これらの接続構成の詳細も暗号化され、安全に保存されます。

- [Azure REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [Azure AD にクライアント アプリケーションを登録する方法](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [ワークフロー トリガー API 情報](/rest/api/logic/workflowtriggers)

- [コネクタの構成について](../connectors/apis-list.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (AD) Multi-Factor Authentication (MFA) を有効にし、Azure Security Center ID とアクセス管理の推奨事項に従います。

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: Multi-Factor Authentication (MFA) が構成された特権アクセス ワークステーション (PAW) を使用してログインし、Azure リソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で MFA を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory (AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全でないアクティビティが発生したときにログとアラートを生成します。

また、Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。

- [Privileged Identity Management (PIM) をデプロイする方法](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure AD のリスク検出の概要](../active-directory/identity-protection/overview-identity-protection.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレスの範囲、国、またはリージョンの特定の論理グループからのみ Azure portal へのアクセスを許可します。

さらに、ロジック アプリのすべての要求エンドポイントは、エンドポイントの URL に Shared Access Signature (SAS) を含みます。 特定の IP アドレスからの要求のみを受け入れるようにロジック アプリを制限できます。

- [Azure でネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

- [Logic Apps で受信 IP アドレスを制限する方法について](logic-apps-securing-a-logic-app.md#restrict-inbound-ip-addresses)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Logic Apps インスタンスの主要な認証および承認システムとして Azure Active Directory (AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Logic Apps でサポートされている場合は、資格情報やシークレットではなくマネージド ID を使用して、Azure Active Directory (Azure AD) によって保護されている他のリソースに簡単にアクセスし、サインインすることなく ID の認証を行います。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。

Azure Logic Apps では、システム割り当てとユーザー割り当ての両方のマネージド ID がサポートされます。 ロジック アプリでは、システムによって割り当てられた ID または単一のユーザー割り当て ID のいずれかを使用できます。これは、ロジック アプリのグループ全体で共有できますが、両方を共有することはできません。 現在、特定の組み込みトリガーおよびアクションでのみマネージド ID がサポートされ、マネージド コネクタや接続はサポートされません。以下に例を示します。

-  HTTP

-  Azure Functions

-  Azure API Management

-  Azure App Service 

- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する](create-managed-service-identity.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**:Azure Logic Apps インスタンスの主要な認証および承認システムとして Azure Active Directory (AD) を使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

Azure AD サインイン アクティビティ、監査、リスク イベント ログのソースにアクセスできるため、Azure Sentinel またはサードパーティの SIEM と統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics 内で必要なログ アラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD のリスクおよび ID Protectoin 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。 

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md) 

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md) 

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:現時点では利用できません。Azure Logic Apps では、カスタマー ロックボックスはまだサポートされていません。

- [カスタマー ロックボックスでサポートされているサービスの一覧](../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: "グローバルな" マルチテナント Logic Apps サービスで実行されるコネクタは、Microsoft によってデプロイおよび管理されます。 これらのコネクタでは、Office 365、Azure Blob Storage、SQL Server、Dynamics、Salesforce、SharePoint など、クラウド サービスとオンプレミス システムの一方または両方にアクセスするためのトリガーとアクションが提供されます。

Azure 仮想ネットワーク内のリソースに直接アクセスする必要があるロジック アプリの場合は、専用のリソースでロジック アプリをビルド、デプロイ、実行できる統合サービス環境 (ISE) を作成できます。 一部の Azure 仮想ネットワークでは、プライベート エンドポイント (Azure Private Link) を使用して、Azure でホストされている Azure Storage、Azure Cosmos DB、Azure SQL Database、パートナー サービス、カスタマー サービスなどの Azure PaaS サービスへのアクセスが提供されます。 ロジック アプリでプライベート エンドポイントを使用する仮想ネットワークにアクセスする必要がある場合は、ISE の内部でそれらのロジック アプリを作成、デプロイ、実行する必要があります。

ISE を作成するときに、内部アクセス エンドポイントと外部アクセス エンドポイントのどちらを使用するかを選択できます。 この選択により、ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかが決まります。

さらに、環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure のロールベースのアクセス制御 (Azure RBAC) を使用して、Azure リソースへのアクセスを制御できます。

- [Logic Apps のコネクタについて](../connectors/apis-list.md)

- [統合サービス環境 (ISE) を使用して、Azure Logic Apps から Azure Virtual Network リソースにアクセスする](connect-virtual-network-vnet-isolated-environment-overview.md)

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md) 

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**:現時点では使用できません。Azure Logic Apps では、データの識別、分類、損失防止機能はまだ使用できません。

機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックする、Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で利用します。 

Microsoft では、Azure Logic Apps 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**:転送中のすべての機密情報を暗号化します。 Azure Logic Apps では、ロジック アプリが実行されている間、すべてのデータが転送中 (トランスポート層セキュリティ (TLS) を使用) と保存時に暗号化されます。 ロジック アプリの実行履歴を表示するときは、Logic Apps によってアクセスの認証が行われた後、各実行の要求と応答に対する入力および出力へのリンクが提供されます。 ただし、パスワード、シークレット、キーなどの秘匿性の高い情報を処理するアクションについては、他のユーザーがそのデータを表示したり利用したりできないようにします。 たとえば、ロジック アプリが HTTP アクションの認証時に使用するシークレットを Azure Key Vault から取得する場合、そのシークレットが見えないようにする必要があります。

Request トリガーでは、受信要求に対してトランスポート層セキュリティ (TLS) 1.2 のみがサポートされます。 対象の Azure リソースに接続しているすべてのクライアントが TLS 1.2 以上をネゴシエートできることを確認します。 HTTP コネクタを使用する送信呼び出しでは、トランスポート層セキュリティ (TLS) 1.0、1.1、および 1.2 がサポートされます。 

該当する場合、保存時の暗号化と転送中の暗号化に関する Azure Security Center の推奨事項に従います。

- [Azure Logic Apps におけるアクセスとデータのセキュリティ保護 - 要求ベースのトリガーへの受信呼び出し](logic-apps-securing-a-logic-app.md#secure-inbound-requests)

- [Azure Logic Apps におけるアクセスとデータのセキュリティ保護 - 他のサービスやシステムへの送信呼び出し](logic-apps-securing-a-logic-app.md#secure-outbound-requests)

- [Azure での転送中の暗号化の概要](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)

- [Azure を使用した保存時のデータの暗号化について](../security/fundamentals/encryption-atrest.md)

- [Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](customer-managed-keys-integration-service-environment.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Logic Apps では、多くのトリガーとアクションに、ロジック アプリの実行履歴からデータを隠すことで入力と出力のどちらかまたは両方をセキュリティで保護するために有効にできる設定があります。

Microsoft では、Azure Logic Apps 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [実行履歴データへのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#access-to-run-history-data)

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: ロジック アプリの管理、編集、表示など、特定のタスクの実行を特定のユーザーまたはグループのみに許可することができます。 それらのアクセス許可を制御するには、カスタマイズされたロールまたは組み込みロールを Azure サブスクリプションのメンバーに割り当てることができるように、Azure のロールベースのアクセス制御 (Azure RBAC) を使用します。

- ロジック アプリの共同作成者: ロジック アプリを管理できますが、アクセス権を変更することはできません。
- ロジック アプリのオペレーター: ロジック アプリの読み取り、有効化、無効化ができますが、編集または更新はできません。

他のユーザーがお客様のロジック アプリを変更したり削除したりしないようにするには、Azure のリソース ロックを使用できます。 この機能を使用すると、他のユーザーは運用リソースを変更または削除できなくなります。

- [Azure Logic Apps 操作へのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft では、Azure Logic Apps 用の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

- [Azure での顧客データの保護](../security/fundamentals/protection-customer-data.md)

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Logic Apps は Azure Storage を利用して、データを格納し、自動的に保存データを暗号化します。 この暗号化によってデータが保護され、組織のセキュリティとコンプライアンスの要件を満たすことができます。 既定では、Azure Storage は Microsoft マネージド キーを使用してデータを暗号化します。

対象のロジック アプリをホストするための統合サービス環境 (ISE) を作成し、Azure Storage で使用される暗号化キーをより詳細に制御したい場合は、Azure Key Vault を使用して、独自のキーを設定、使用、管理することができます。 この機能は "Bring Your Own Key" (BYOK) とも呼ばれ、キーは "カスタマー マネージド キー" と呼ばれます。

- [Azure Logic Apps の統合サービス環境の保存データを暗号化する](customer-managed-keys-integration-service-environment.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Logic Apps およびその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: 適用できません。Microsoft により、Azure Logic Apps をサポートしている基になるシステムに対して脆弱性の管理が実行されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: 適用できません。Microsoft により、Azure Logic Apps をサポートしている基になるシステムに対して脆弱性の管理が実行されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 適用できません。Microsoft により、Azure Logic Apps をサポートしている基になるシステムに対して脆弱性の管理が実行されます。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。  テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Azure RBAC について](../role-based-access-control/overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、Azure Policy を使用し、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類を制限します。

- 許可されないリソースの種類
- 許可されるリソースの種類

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**:組織のニーズに応じて、承認された Azure リソース (コネクタなど) とコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

注:Google のデータおよびプライバシー ポリシーにより、Gmail コネクタは Google によって承認されたサービスでのみ使用できます。 この状況は進展しており、将来的に他の Google コネクタに影響を与える可能性があります。

- [すべての Logic Apps コネクタの一覧](/connectors/connector-reference/connector-reference-logicapps-connectors)

- [Gmail コネクタの問題と制限について](/connectors/gmail/#known-issues-and-limitations)

- [Google のプライバシー ポリシーの詳細](../connectors/connectors-google-data-security-privacy-policy.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

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

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/index.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 業務に必要であっても、組織のリスクが高くなる可能性がある対象のロジック アプリに関連するリソースは、独自の仮想マシンや仮想ネットワーク内に隔離し、Azure Firewall またはネットワーク セキュリティ グループで十分に保護する必要があります。

業務に必要であっても、組織のリスクが高くなる可能性があるロジック アプリは、特定のアクセス許可と Azure RBAC 境界を持つ個別のリソース グループを使用して、可能な限り分離する必要があります。

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md) 

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md) 

- [Azure RBAC を使用してロジック アプリへのアクセスをセキュリティで保護する方法](logic-apps-securing-a-logic-app.md#access-to-logic-app-operations)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Azure Logic Apps インスタンスの標準のセキュリティ構成を定義および実装します。 対象の Logic Apps インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Logic" 名前空間で Azure Policy エイリアスを使用します。 たとえば、アクセスを制限するリソースへの接続を他のユーザーが作成または使用できないようにすることができます。

さらに、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があります。構成が組織のセキュリティ要件を満たしているか超えていることを確認するためにはこれを確認する必要があります。

また、セキュリティで保護されたパラメーターを使用して、機密データやシークレットを保護します。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Logic Apps のコネクタによって作成された接続をブロックする](block-connections-connectors.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps 用の Azure Resource Manager テンプレートをデプロイする方法](logic-apps-deploy-azure-resource-manager-templates.md)

- [セキュリティで保護されたアクション パラメーターについて](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。

Azure Policy を使用して、Azure Logic Apps インスタンスの標準のセキュリティ構成を定義および実装します。 対象の Logic Apps インスタンスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Logic" 名前空間で Azure Policy エイリアスを使用します。 たとえば、アクセスを制限するリソースへの接続を他のユーザーが作成または使用できないようにすることができます。

さらに、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があります。構成が組織のセキュリティ要件を満たしているか超えていることを確認するためにはこれを確認する必要があります。

また、難読化を使用して実行履歴のデータをセキュリティで保護していることを確認します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

- [Azure Logic Apps のコネクタによって作成された接続をブロックする](block-connections-connectors.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [Azure Logic Apps 用の Azure Resource Manager テンプレートをデプロイする方法](logic-apps-deploy-azure-resource-manager-templates.md)

- [実行履歴の入力と出力へのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#obfuscate)

- [パラメーター入力へのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#secure-action-parameters)

- [パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 共有

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタムの Azure Policy 定義を使用する場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

さらに、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があります。構成が組織のセキュリティ要件を満たしているか超えていることを確認するためにはこれを確認する必要があります。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/index?view=azure-devops)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Logic" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ご利用の Azure リソースのネットワーク構成を監査または適用するには、Azure Policy エイリアスを使用してカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Logic" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: 難読化を使用して、ロジック アプリの実行履歴の入力と出力をセキュリティで保護します。 デプロイ先が複数の異なる環境にまたがる場合、環境に応じて変わる値は、ロジック アプリのワークフロー定義内でパラメーター化することを検討してください。 そのようにすると、Azure Resource Manager テンプレートを使用してロジック アプリをデプロイすることでデータのハードコーディングを避け、セキュリティで保護されたパラメーターを定義することで機密データを保護し、パラメーター ファイルを使用することでテンプレートのパラメーターで個別の入力としてそのデータを渡すことができます。 Key Vault を使用して機密データを格納し、デプロイ時に Key Vault からそれらの値を取得する、セキュリティで保護されたテンプレート パラメーターを使用できます。 その後、パラメーター ファイルの中で、キー コンテナーとシークレットを参照することができます。 

対象のロジック アプリをホストするための統合サービス環境 (ISE) を作成し、Azure Storage で使用される暗号化キーをより詳細に制御したい場合は、Azure Key Vault を使用して、独自のキーを設定、使用、管理することができます。 この機能は "Bring Your Own Key" (BYOK) とも呼ばれ、キーは "カスタマー マネージド キー" と呼ばれます。

- [Azure Logic Apps の実行履歴の入力と出力をセキュリティで保護する](logic-apps-securing-a-logic-app.md#obfuscate)

- [パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Azure Logic Apps のパラメーター入力へのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Azure Key Vault を使用して、デプロイ時にセキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)

- [Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](customer-managed-keys-integration-service-environment.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: サインインすることなく他の Azure Active Directory (Azure AD) によって保護される他のリソースに容易にアクセスして ID の認証を行うため、ロジック アプリは、資格情報やシークレットではなくマネージド ID (以前はマネージド サービス ID (MSI) と呼ばれていました) を使用できます。 この ID は、ユーザーの代わりに Azure で管理されます。ユーザーがシークレットを提供したりローテーションしたりする必要がないため、資格情報の保護に役立ちます。

現在、特定の組み込みトリガーおよびアクションでのみマネージド ID がサポートされ、マネージド コネクタや接続はサポートされません。以下に例を示します。

- HTTP
- Azure Functions
- Azure API Management
- Azure App Service

- [Azure Logic Apps でマネージド ID を使用して Azure リソースへのアクセスを認証する方法](create-managed-service-identity.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: 難読化を使用して、ロジック アプリの実行履歴の入力と出力をセキュリティで保護します。 デプロイ先が複数の異なる環境にまたがる場合、環境に応じて変わる値は、ロジック アプリのワークフロー定義内でパラメーター化することを検討してください。 そのようにすると、Azure Resource Manager テンプレートを使用してロジック アプリをデプロイすることでデータのハードコーディングを避け、セキュリティで保護されたパラメーターを定義することで機密データを保護し、パラメーター ファイルを使用することでテンプレートのパラメーターで個別の入力としてそのデータを渡すことができます。 Key Vault を使用して機密データを格納し、デプロイ時に Key Vault からそれらの値を取得する、セキュリティで保護されたテンプレート パラメーターを使用できます。 その後、パラメーター ファイルの中で、キー コンテナーとシークレットを参照することができます。 

また、コード内で資格情報を特定する資格情報スキャナーを実装することもできます。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [Azure Logic Apps の実行履歴の入力と出力をセキュリティで保護する](logic-apps-securing-a-logic-app.md#obfuscate)

- [パラメーターに関するセキュリティの推奨事項](../azure-resource-manager/templates/template-best-practices.md#security-recommendations-for-parameters)

- [Azure Logic Apps のパラメーター入力へのアクセスをセキュリティで保護する](logic-apps-securing-a-logic-app.md#access-to-parameter-inputs)

- [Azure Key Vault を使用して、デプロイ時にセキュリティで保護されたパラメーター値を渡す](../azure-resource-manager/templates/key-vault-parameter.md)

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**: 適用できません。この推奨事項は、コンピューティング リソースを対象にしています。 Microsoft のマルウェア対策は、Azure サービス (Azure Logic Apps など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft Anti-malware は、Azure サービス (Azure Backup など) をサポートしている基になるホストで有効になっていますが、ユーザーのコンテンツに対しては実行されません。 

App Service、Data Lake Storage、Blob Storage などの非コンピューティング Azure リソースにアップロードされようとしているファイルはすべて、事前にスキャンしてください。 

データ サービス向けの Azure Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。 

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Anti-malware について](../security/fundamentals/antimalware.md)

- [データ サービスに対する Azure Security Center の脅威検出について](../security-center/azure-defender.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="data-recovery"></a>データの復旧

*詳しくは、「[Azure Security ベンチマーク:データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: ディザスター リカバリー (DR) ソリューションを適切に実装して、データを保護し、重要なビジネス機能をサポートするリソースをすばやく復元し、業務を継続して事業継続性 (BC) を保つことができるようにします。

このディザスター リカバリー戦略では、Azure Logic Apps も利用可能な別の場所にあるスタンバイまたはバックアップ ロジック アプリにフェールオーバーするようにプライマリ ロジック アプリを設定することに重点を置いています。 こうすることで、プライマリに損失、中断、または障害が発生した場合、セカンダリは作業を引き継ぐことができます。 この戦略では、セカンダリ ロジック アプリと依存リソースがすでに別の場所に展開され、準備ができている必要があります。

さらに、ロジック アプリの基になるワークフロー定義を Azure Resource Manager テンプレートに拡張する必要があります。 このテンプレートでは、ロジック アプリをプロビジョニングおよびデプロイするためのインフラストラクチャ、リソース、パラメーター、およびその他の情報を定義します。

- [Azure Logic Apps の事業継続とディザスター リカバリーの詳細情報](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager テンプレートを使用して Azure Logic Apps のデプロイを自動化する方法](logic-apps-azure-resource-manager-templates-overview.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: ディザスター リカバリー (DR) ソリューションを適切に実装して、データを保護し、重要なビジネス機能をサポートするリソースをすばやく復元し、業務を継続して事業継続性 (BC) を保つことができるようにします。

このディザスター リカバリー戦略では、Azure Logic Apps も利用可能な別の場所にあるスタンバイまたはバックアップ ロジック アプリにフェールオーバーするようにプライマリ ロジック アプリを設定することに重点を置いています。 こうすることで、プライマリに損失、中断、または障害が発生した場合、セカンダリは作業を引き継ぐことができます。 この戦略では、セカンダリ ロジック アプリと依存リソースがすでに別の場所に展開され、準備ができている必要があります。

さらに、ロジック アプリの基になるワークフロー定義を Azure Resource Manager テンプレートに拡張する必要があります。 このテンプレートでは、ロジック アプリをプロビジョニングおよびデプロイするためのインフラストラクチャ、リソース、パラメーター、およびその他の情報を定義します。

ロジック アプリのすべての要求エンドポイントは、エンドポイントの URL に Shared Access Signature (SAS) を含みます。 シークレットを格納するために Azure Key Vault を使用している場合は、必ずキーと URL の定期的な自動バックアップを実行してください。

- [Azure Logic Apps の事業継続とディザスター リカバリーの詳細情報](business-continuity-disaster-recovery-guidance.md)

- [Azure Resource Manager テンプレートを使用して Azure Logic Apps のデプロイを自動化する](logic-apps-azure-resource-manager-templates-overview.md)

- [SAS を使用して Azure Logic Apps のアクセスとデータをセキュリティで保護する](logic-apps-securing-a-logic-app.md#sas)

- [Key Vault のキーをバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: 対象のディザスター リカバリー戦略では、Azure Logic Apps も利用可能な別の場所にあるスタンバイまたはバックアップ ロジック アプリにフェールオーバーするようにプライマリ ロジック アプリを設定することに重点を置く必要があります。 こうすることで、プライマリに損失、中断、または障害が発生した場合、セカンダリは作業を引き継ぐことができます。 この戦略では、セカンダリ ロジック アプリと依存リソースがすでに別の場所に展開され、準備ができている必要があります。

バックアップされたカスタマー マネージド キーの復元をテストします。 これは、統合サービス環境 (ISE) で実行されている Logic Apps にのみ適用されることに注意してください。

- [Azure Logic Apps の事業継続とディザスター リカバリーの詳細情報](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](customer-managed-keys-integration-service-environment.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: 対象のディザスター リカバリー戦略では、Azure Logic Apps も利用可能な別の場所にあるスタンバイまたはバックアップ ロジック アプリにフェールオーバーするようにプライマリ ロジック アプリを設定することに重点を置く必要があります。 こうすることで、プライマリに損失、中断、または障害が発生した場合、セカンダリは作業を引き継ぐことができます。 この戦略では、セカンダリ ロジック アプリと依存リソースがすでに別の場所に展開され、準備ができている必要があります。 

バックアップされたカスタマー マネージド キーを保護します。 これは、統合サービス環境 (ISE) で実行されている Logic Apps にのみ適用されることに注意してください。

Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。

- [Azure Logic Apps の事業継続とディザスター リカバリーの詳細情報](business-continuity-disaster-recovery-guidance.md)

- [Azure Logic Apps の統合サービス環境 (ISE) の保存データを暗号化するためにカスタマー マネージド キーを設定する](customer-managed-keys-integration-service-environment.md)

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md?tabs=azure-portal)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [独自のインシデント対応計画の作成のための NIST の「コンピューター セキュリティ インシデント対応ガイド」](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。 

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物 - IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

- [ワークフローの自動化と Logic Apps を構成する方法](../security-center/workflow-automation.md)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](../security/benchmarks/overview.md)に関するページを参照する
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する