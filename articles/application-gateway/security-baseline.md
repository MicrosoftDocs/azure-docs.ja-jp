---
title: Azure Application Gateway 用の Azure セキュリティ ベースライン
description: Azure Application Gateway 用の Azure セキュリティ ベースライン
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 562a0fbd64fca530598a58599160dbdd7e479557
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84485525"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azure Application Gateway 用の Azure セキュリティ ベースライン

Azure Application Gateway 用の Azure セキュリティ ベースラインには、デプロイのセキュリティ体制を改善するために役立つレコメンデーションが含まれています。

このサービス用のベースラインは、ベスト プラクティス ガイダンスを使用して Azure 上のクラウド ソリューションをセキュリティで保護する方法について推奨事項を提供する [Azure セキュリティ ベンチマーク バージョン 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview) に基づいて作成されています。

詳細については、「[Azure セキュリティ ベースラインの概要](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、「[セキュリティ コントロール: ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

* [Azure Application Gateway での NSG の使用に関する制限と要件について](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合は、NSG フロー ログを有効にし、トラフィック監査のためにログを Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注:Azure Application Gateway サブネットに関連付けられている NSG フローログに、許可されているトラフィックが表示されない場合があります。 構成が次のシナリオに一致する場合は、許可されているトラフィックが NSG フロー ログに表示されません。
- Application Gateway v2 をデプロイした
- アプリケーション ゲートウェイ サブネットに NSG がある
- その NSG 上で NSG フロー ログを有効にした

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Azure Security Center によって提供されるネットワークのセキュリティについて](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure Application Gateway の診断とログに関する FAQ](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。

* [Azure Application Gateway の機能について](https://docs.microsoft.com/azure/application-gateway/features)

* [Azure WAF について](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Azure WAF をデプロイする方法](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Application Gateway の実稼働インスタンスに関連付けられている Azure Virtual Networks で DDoS Standard 保護を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。

* [DDoS 保護を構成する方法](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Azure Security Center の統合された脅威インテリジェンスについて](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合は、NSG フロー ログを有効にし、トラフィック監査のためにログを Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注:Azure Application Gateway サブネットに関連付けられている NSG フローログに、許可されているトラフィックが表示されない場合があります。 構成が次のシナリオに一致する場合は、許可されているトラフィックが NSG フロー ログに表示されません。
- Application Gateway v2 をデプロイした
- アプリケーション ゲートウェイ サブネットに NSG がある
- その NSG 上で NSG フロー ログを有効にした

* [NSG フロー ログを有効にする方法](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [Traffic Analytics を有効にして使用する方法](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [Azure Security Center によって提供されるネットワークのセキュリティについて](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Azure Application Gateway の診断とログに関する FAQ](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。

また、IDS または IPS 機能を含め、Azure Marketplace で入手できる Barracuda WAF for Azure などのマーケットプレースの選択肢が複数あります。

* [Azure Application Gateway の機能について](https://docs.microsoft.com/azure/application-gateway/features)

* [Azure WAF について](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Azure WAF をデプロイする方法](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Barracuda WAF クラウド サービスの概要](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 信頼できる証明書に対して HTTPS/SSL を有効にした Web アプリケーションの Azure Application Gateway をデプロイします。

* [Application Gateway をデプロイする方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [HTTPS を使用するように Application Gateway を構成する方法](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (GatewayManager など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合、Application Gateway v1 SKU の TCP ポート 65503 ～ 65534 と、v2 SKU の TCP ポート 65200 ～ 65535 で、宛先サブネットが [すべて]、ソースが GatewayManager サービス タグである着信インターネット トラフィックを許可する必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 それらのゲートウェイの顧客を含む外部エンティティは、これらのエンドポイントで通信できません。

* [サービス タグの概要と使用](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Azure Application Gateway 構成の概要](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Application Gateway のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。 Azure Application Gateways、Azure Virtual Networks、ネットワーク セキュリティ グループのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 組み込みのポリシー定義を使用することもできます。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールベースのアクセス制御 (RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整できます。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint を作成する方法](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) のほか、ネットワーク セキュリティおよびトラフィック フローに関連したその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [仮想ネットワークを作成する方法](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Application Gateway デプロイに関連するネットワーク設定とリソースの変更を検出します。 重要なネットワーク設定とリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

* [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Azure Monitor でアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、「[セキュリティ コントロール: ログ記録と監視](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)」を参照してください。*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1:承認された時刻同期ソースを使用する

**ガイダンス**: Microsoft では、Azure App Service などの Azure リソースに使用するタイム ソースを保持します。

**Azure Security Center の監視**: 適用なし

**責任**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信します。 Azure アクティビティ ログ データを使用して、Azure Application Gateway と、Azure Application Gateway サブネットを保護するために使用される関連リソース (ネットワークセキュリティグループ (NSGs) など) に対し、コントロール プレーン レベルで実行される書き込み操作 (PUT、POST、DELETE) の "何を、だれが、いつ" を判断できます。

アクティビティ ログに加えて、Azure Application Gateway デプロイの診断設定を構成できます。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

Azure Application Gateway では、Azure Application Insights とのビルトイン統合も提供されます。 Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 Application Insights は、パフォーマンスの異常を自動的に検出するほか、問題の診断や、Web アプリの使用状況の理解に役立つ強力な分析ツールを備えています。 連続エクスポートを有効にすると、Application Insights からのテレメトリを一元化された場所にエクスポートして、標準の保持期間より長い間データを維持することができます。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Application Gateway の診断設定を有効にする方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Application Insights を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信します。 Azure アクティビティ ログ データを使用して、Azure Application Gateway と、Azure Application Gateway サブネットを保護するために使用される関連リソース (ネットワークセキュリティグループ (NSGs) など) に対し、コントロール プレーン レベルで実行される書き込み操作 (PUT、POST、DELETE) の "何を、だれが、いつ" を判断できます。

アクティビティ ログに加えて、Azure Application Gateway デプロイの診断設定を構成できます。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

Azure Application Gateway では、Azure Application Insights とのビルトイン統合も提供されます。 Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 Application Insights は、パフォーマンスの異常を自動的に検出するほか、問題の診断や、Web アプリの使用状況の理解に役立つ強力な分析ツールを備えています。 連続エクスポートを有効にすると、Application Insights からのテレメトリを一元化された場所にエクスポートして、標準の保持期間より長い間データを維持することができます。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Application Gateway の診断設定を有効にする方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Application Insights を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

* [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定と Azure Application Gateway の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。

Azure Application Gateways などのデプロイされたすべてのネットワーク リソースの正常性とメトリックを包括的に把握するために、Azure Monitor for Networks を使用します。

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Application Gateway の診断設定を有効にする方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Azure Monitor for Networks の使用方法](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 SKU を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。

Azure アクティビティ ログの診断設定と Azure WAF の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。 Log Analytics ワークスペース クエリに基づいてアラートを作成できます。

Azure Application Gateways などのデプロイされたすべてのネットワーク リソースの正常性とメトリックを包括的に把握するために、Azure Monitor for Networks を使用します。 Azure Monitor for Networks コンソール内で Azure Application Gateway のアラートを表示し、作成できます。

* [Azure WAF をデプロイする方法](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Azure アクティビティ ログの診断設定を有効にする方法](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Azure Application Gateway の診断設定を有効にする方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Azure Monitor for Networks の使用方法](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [Azure 内でアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。

Azure Application Gateway デプロイの診断設定を構成します。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

* [Azure WAF をデプロイする方法](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Azure WAF の診断設定を構成する方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Application Gateway では、ユーザー アクセス可能な DNS 関連のログの処理や生成を行いません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: 適用できません。このガイドラインは、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

## <a name="identity-and-access-control"></a>ID とアクセスの制御

*詳細については、「[セキュリティ コントロール: ID およびアクセス制御](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (AD) には、明示的に割り当てる必要があり、クエリ可能な組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

* [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Application Gateway へのコントロール プレーン アクセスは、Azure Active Directory (AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。
- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

* [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Azure Policy を使用する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: Azure アプリの登録 (サービス プリンシパル) を使用して、API 呼び出しを介して Azure Application Gateways と対話するために使用できるトークンを取得します。

* [Azure REST API を呼び出す方法](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [クライアント アプリケーション (サービス プリンシパル) を Azure AD に登録する方法](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Recovery Services API に関する情報](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**:Azure AD MFA を有効にして、Azure Security Center ID とアクセス管理の推奨事項に従います。

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Azure Security Center で ID とアクセスを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**: MFA が構成されている PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、構成します。

* [特権アクセス ワークステーションについて](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Azure で MFA を有効にする方法](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: 環境内で疑わしいアクティビティまたは安全でないアクティビティが環境で発生したときに、Azure Active Directory セキュリティ レポートを使用して、ログおよびアラートを生成します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

* [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

* [Azure でネームド ロケーションを構成する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**:Azure Active Directory (AAD) を中央認証と承認システムとして使用します。 AAD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、AAD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。

* [AAD インスタンスを作成して構成する方法](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**:Azure AD には、古いアカウントの検出に役立つログが用意されています。 また、Azure ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

* [Azure AD のレポートの概要](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Azure ID アクセス レビューの使用方法](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure AD サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM/監視ツールとの統合が可能です。

このプロセスを効率化するには、Azure Active Directory ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

* [Azure アクティビティ ログを Azure Monitor に統合する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure AD ID Protectoin およびリスク検出機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

* [Azure AD の危険なサインインを表示する方法](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Identity Protection のリスク ポリシーを構成して有効にする方法](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Azure Sentinel をオンボードする方法](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:適用できません。カスタマー ロックボックスは Azure Application Gateway に適用されません。

* [カスタマー ロックボックスでサポートされているサービスの一覧](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 適用なし

## <a name="data-protection"></a>データ保護

*詳細については、「[セキュリティ コントロール: データ保護](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: タグを使用して、機密情報を格納または処理する Azure リソースの追跡に役立てます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure Application Gateway での NSG の使用に関する制限と要件について](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 送信トラフィックを信頼できる場所のみに制限して、データ窃盗の脅威を軽減できます。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

* [Azure Application Gateway での NSG の使用に関する制限と要件について](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: TLS により、Azure Application Gateway のエンドツーエンドの暗号化を構成します。

* [Azure Application Gateway を使用してエンド ツー エンド TLS を構成する方法](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: 適用されません。 Azure Application Gateway では、お客様の保存データが格納されません。

Microsoft では、Azure Application Gateway の基になるインフラストラクチャを管理し、顧客データの損失や漏洩を防ぐための厳格な管理を実施してきました。

* [Azure での顧客データの保護について](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Active Directory (AD) ロールベースのアクセス制御 (RBAC) を使用して、Azure Application Gateway コントロール プレーン (Azure portal) へのアクセスを制御します。

* [Azure で RBAC を構成する方法](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: 適用されません。 Azure Application Gateway では、お客様のデータが格納されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor と Azure アクティビティ ログを使用して、運用環境の Azure Application Gateway インスタンスおよびその他の重要なリソースまたは関連リソースへの変更が発生したときに、アラートを作成します。

* [Azure アクティビティ ログ イベントのアラートを作成する方法](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、「[セキュリティ コントロール: 脆弱性の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:現在使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、サービス構成関連の脆弱性を軽減します。

* [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: まだ使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、サービス構成関連の脆弱性を軽減します。

* [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: まだ使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、サービス構成関連の脆弱性を軽減します。

* [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、「[セキュリティ コントロール: インベントリと資産の管理](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された Asset Discovery ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

* [Azure Resource Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure サブスクリプションを表示する方法](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Azure RBAC について](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に論理的に整理されます。

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: Azure リソースを整理および追跡するには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

さらに、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 承認済みの Azure リソースを定義します。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。 環境に存在するすべての Azure リソースが承認されていることを確認します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Graph を使用してクエリを作成する方法](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

* [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

* [追加の Azure サブスクリプションを作成する方法](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [管理グループを作成する方法](https://docs.microsoft.com/azure/governance/management-groups/create)

* [タグを作成して使用する方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Azure Application Gateway での NSG の使用に関する制限と要件について](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [セキュリティ構成を使用して NSG を作成する方法](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、「[セキュリティ コントロール: セキュリティで保護された構成](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Application Gateway のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。 Azure Application Gateways、Azure Virtual Networks、ネットワーク セキュリティ グループのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 組み込みのポリシー定義を使用することもできます。

* [使用可能な Azure Policy エイリアスを表示する方法](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy の効果について](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure ポリシー定義を使用する場合は、Azure DevOps または Azure Repos を使ってコードを安全に格納して管理します。

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ご利用の Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

* [Azure Policy を構成して管理する方法](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: 適用できません。この推奨事項は、IaaS コンピューティング リソースを対象にしています。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (AD) で自動的に管理される ID を Azure Application Gateway に提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure Key Vault を使用して、証明書を安全に格納します。 Azure Key Vault はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway v2 SKU に制限されます。

* [Azure PowerShell を使用して、Key Vault 証明書によって SSL 終端を構成する方法](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (AD) で自動的に管理される ID を Azure Application Gateway に提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure Key Vault を使用して、証明書を安全に格納します。 Azure Key Vault はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway v2 SKU に制限されます。

* [Azure PowerShell を使用して、Key Vault 証明書によって SSL 終端を構成する方法](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center の監視**: 現在は使用できません

**責任**: Customer

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

* [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、「[セキュリティ コントロール: マルウェアからの防御](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**:着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。

Azure Application Gateway デプロイの診断設定を構成します。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

* [Azure WAF をデプロイする方法](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Azure WAF の診断設定を構成する方法](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: 適用されません。 Azure Application Gateway では、お客様のデータが格納されません。

**Azure Security Center の監視**: 適用なし

**責任**: 適用なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure Web アプリケーション ファイアウォール (WAF) を使用する場合は、WAF ポリシーを構成できます。 WAF ポリシーは、2 種類のセキュリティ規則で構成できます。お客様が作成するカスタム規則と、Azure によって管理される事前構成済みの規則のセットのコレクションであるマネージド規則セットです。 Azure で管理される規則セットでは、一般的なセキュリティ脅威のセットに対する保護をデプロイする簡単な方法が提供されます。 そのような規則セットが Azure によって管理されるので、新しい攻撃シグネチャから保護するために、必要に応じて規則が更新されます。

* [Azure で管理されている WAF 規則セットについて](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure Security Center の監視**: 現在は使用できません

**責任**: 共有

## <a name="data-recovery"></a>データの復旧

*詳細については、「[セキュリティ コントロール: データの復旧](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Application Gateway では、お客様のデータが格納されません。 ただし、カスタム Azure ポリシー定義を使用している場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

Azure DevOps Services では、Azure Storage 機能の多くを活用して、ハードウェア障害、サービスの中断、またはリージョンの災害が発生した場合にデータの可用性を確保しています。 さらに、Azure DevOps チームは、データを偶発的または悪意のある削除から保護する手順に従っています。

* [Azure DevOps のデータ可用性の概要](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Azure DevOps でコードを格納する方法](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Azure Repos のドキュメント](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、カスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Key Vault 内のカスタマー マネージド証明書をバックアップします。

* [Azure で Key Vault の証明書をバックアップする方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップされたカスタマー マネージド証明書の復元をテストします。

* [Key Vault の証明書を復元する方法](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を確保する

**ガイダンス**: Azure Key Vault に対して論理的な削除が有効になっていることを確認します。 論理的な削除では、削除されたキーコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。

* [Azure Key Vault の論理的な削除を使用する方法](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center の監視**: はい

**責任**: Customer

## <a name="incident-response"></a>インシデント対応

*詳細については、「[セキュリティ コントロール: インシデント対応](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

* [Azure Security Center 内でワークフロー自動化を構成する方法](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 稼働、非稼働など) を明確にマークし、Azure リソースを明確に識別および分類するための命名システムを作成します。

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

* [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) で、不正なユーザーまたは権限のないユーザーによるお客様のデータへのアクセスが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

* [Azure Security Center のセキュリティ連絡先を設定する方法](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center の監視**: はい

**責任**: Customer

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**:連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートします。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用して、アラートの Sentinel のストリーミングを実行できます。

* [連続エクスポートを構成する方法](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Azure Sentinel にアラートをストリーミングする方法](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: セキュリティ アラートやセキュリティに関する推奨事項に対して "Logic Apps" 経由で応答を自動的にトリガーするには、Azure Security Center のワークフローの自動化機能を使用します。

* [ワークフローの自動化と Logic Apps を構成する方法](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center の監視**: 適用なし

**責任**: Customer

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、「[セキュリティ コントロール: 侵入テストとレッド チーム演習](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: 

* [お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施の詳細については、こちらを参照してください。](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center の監視**: 適用なし

**責任**: 共有

## <a name="next-steps"></a>次のステップ

- [Azure セキュリティ ベンチマーク](https://docs.microsoft.com/azure/security/benchmarks/overview)に関するページを参照する
- [Azure セキュリティ ベースライン](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
