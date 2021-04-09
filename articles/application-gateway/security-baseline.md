---
title: Application Gateway 用の Azure セキュリティ ベースライン
description: Application Gateway セキュリティ ベースラインにより、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: application-gateway
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4fc26d8e154dc0a58bb3436a8161d2e21efa2b86
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952016"
---
# <a name="azure-security-baseline-for-application-gateway"></a>Application Gateway 用の Azure セキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Application Gateway に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、Application Gateway に適用される Azure セキュリティ ベンチマークと関連ガイダンスで定義されている **セキュリティ コントロール** によってグループ化されています。 Application Gateway に適用されきない **コントロール** は、除外されています。

 
Azure セキュリティ ベンチマークに対する Application Gateway の完全なマッピングを確認するには、[Application Gateway の完全なセキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

- [Azure Application Gateway での NSG の使用に関する制限と要件について](configuration-overview.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.1](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合は、NSG フロー ログを有効にし、トラフィック監査のためにログを Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注:Azure Application Gateway サブネットに関連付けられている NSG フローログに、許可されているトラフィックが表示されない場合があります。 構成が次のシナリオに一致する場合は、許可されているトラフィックが NSG フロー ログに表示されません。

- Application Gateway v2 をデプロイした
- アプリケーション ゲートウェイ サブネットに NSG がある
- その NSG 上で NSG フロー ログを有効にした

詳細については、以下の資料を参照してください。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [Azure Application Gateway の診断とログに関する FAQ](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.2](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-2.md)]

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。 

- [Azure Application Gateway の機能について](features.md)

- [Azure WAF について](../web-application-firewall/ag/ag-overview.md)

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Azure Application Gateway の実稼働インスタンスに関連付けられている Azure Virtual Networks で DDoS Standard 保護を有効にして、DDoS 攻撃から保護します。 Azure Security Center の統合された脅威インテリジェンスを使用して、既知の悪意のある IP アドレスとの通信を拒否します。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.4](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-4.md)]

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合は、NSG フロー ログを有効にし、トラフィック監査のためにログを Storage アカウントに送信します。 また、NSG フロー ログを Log Analytics ワークスペースに送信し、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化してホット スポットを特定したり、セキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

注:Azure Application Gateway サブネットに関連付けられている NSG フローログに、許可されているトラフィックが表示されない場合があります。 構成が次のシナリオに一致する場合は、許可されているトラフィックが NSG フロー ログに表示されません。

- Application Gateway v2 をデプロイした
- アプリケーション ゲートウェイ サブネットに NSG がある
- その NSG 上で NSG フロー ログを有効にした

詳細については、以下の資料を参照してください。

- [NSG フロー ログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

- [Azure Application Gateway の診断とログに関する FAQ](./application-gateway-faq.yml#what-types-of-logs-does-application-gateway-provide)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/home/mbaldwin/docs/asb/azure-docs-pr/articles/governance/policy/samples/azure-security-benchmark.md)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/security-center-recommendations.md)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 このコントロールに関連するアラートでは、関連するサービスのために [Azure Defender](/home/mbaldwin/docs/asb/azure-docs-pr/articles/security-center/azure-defender.md) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Network**:

[!INCLUDE [Resource Policy for Microsoft.Network 1.5](../../includes/policy/standards/asb/rp-controls/microsoft.network-1-5.md)]

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出/侵入防止システム (IDS/IPS) をデプロイする

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。 

また、IDS または IPS 機能を含め、Azure Marketplace で入手できる Barracuda WAF for Azure などのマーケットプレースの選択肢が複数あります。

- [Azure Application Gateway の機能について](features.md)

- [Azure WAF について](../web-application-firewall/ag/ag-overview.md)

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Barracuda WAF クラウド サービスの概要](../app-service/environment/app-service-app-service-environment-web-application-firewall.md#configuring-your-barracuda-waf-cloud-service)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 信頼できる証明書に対して HTTPS/SSL を有効にした Web アプリケーションの Azure Application Gateway をデプロイします。

- [Application Gateway をデプロイする方法](quick-create-portal.md)

- [HTTPS を使用するように Application Gateway を構成する方法](create-ssl-portal.md)

- [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall のネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (GatewayManager など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) の場合、Application Gateway v1 SKU の TCP ポート 65503 ～ 65534 と、v2 SKU の TCP ポート 65200 ～ 65535 で、宛先サブネットが [すべて]、ソースが GatewayManager サービス タグである着信インターネット トラフィックを許可する必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 それらのゲートウェイの顧客を含む外部エンティティは、これらのエンドポイントで通信できません。

- [サービス タグの概要と使用](../virtual-network/service-tags-overview.md)

- [Azure Application Gateway 構成の概要](configuration-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**:Azure Application Gateway のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。 Azure Application Gateways、Azure Virtual Networks、ネットワーク セキュリティ グループのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 組み込みのポリシー定義を使用することもできます。

また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、Azure ロールベースのアクセス制御 (Azure RBAC)、ポリシーなどの主要な環境成果物を単一のブループリント定義にパッケージ化することによって大規模な Azure デプロイを簡略化することもできます。 ブループリントを新しいサブスクリプションと環境に簡単に適用し、バージョン管理によって制御と管理を微調整できます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Blueprint を作成する方法](../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Application Gateway サブネットに関連付けられているネットワーク セキュリティ グループ (NSG) のほか、ネットワーク セキュリティおよびトラフィック フローに関連したその他のリソースにタグを使用します。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可する規則のビジネス ニーズや期間 (など) を指定します。

すべてのリソースが確実にタグ付きで作成され、既存のタグ付けされていないリソースがユーザーに通知されるようにするには、タグ付けに関連したいずれかの組み込みの Azure Policy 定義 ([タグとその値が必要] など) を使用します。

Azure PowerShell または Azure CLI を使用して、タグに基づいてリソースを検索したり、リソースに対するアクションを実行したりすることもできます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Application Gateway デプロイに関連するネットワーク設定とリソースの変更を検出します。 重要なネットワーク設定またはリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信します。 Azure アクティビティ ログ データを使用して、Azure Application Gateway と、Azure Application Gateway サブネットを保護するために使用される関連リソース (ネットワークセキュリティグループ (NSGs) など) に対し、コントロール プレーン レベルで実行される書き込み操作 (PUT、POST、DELETE) の "何を、だれが、いつ" を判断できます。

アクティビティ ログに加えて、Azure Application Gateway デプロイの診断設定を構成できます。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

Azure Application Gateway では、Azure Application Insights とのビルトイン統合も提供されます。 Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 Application Insights は、パフォーマンスの異常を自動的に検出するほか、問題の診断や、Web アプリの使用状況の理解に役立つ強力な分析ツールを備えています。 連続エクスポートを有効にすると、Application Insights からのテレメトリを一元化された場所にエクスポートして、標準の保持期間より長い間データを維持することができます。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Application Gateway の診断設定を有効にする方法](application-gateway-diagnostics.md)

- [Application Insights を有効にする方法](../azure-monitor/app/app-insights-overview.md)

- [連続エクスポートを構成する方法](../azure-monitor/app/export-telemetry.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: コントロール プレーンの監査ログ記録については、Azure アクティビティ ログの診断設定を有効にして、Log Analytics ワークスペース、Azure イベント ハブ、または Azure ストレージ アカウントにログを送信します。 Azure アクティビティ ログ データを使用して、Azure Application Gateway と、Azure Application Gateway サブネットを保護するために使用される関連リソース (ネットワークセキュリティグループ (NSGs) など) に対し、コントロール プレーン レベルで実行される書き込み操作 (PUT、POST、DELETE) の "何を、だれが、いつ" を判断できます。

アクティビティ ログに加えて、Azure Application Gateway デプロイの診断設定を構成できます。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

Azure Application Gateway では、Azure Application Insights とのビルトイン統合も提供されます。 Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 Application Insights は、パフォーマンスの異常を自動的に検出するほか、問題の診断や、Web アプリの使用状況の理解に役立つ強力な分析ツールを備えています。 連続エクスポートを有効にすると、Application Insights からのテレメトリを一元化された場所にエクスポートして、標準の保持期間より長い間データを維持することができます。

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Application Gateway の診断設定を有効にする方法](application-gateway-diagnostics.md)

- [Application Insights を有効にする方法](../azure-monitor/app/app-insights-overview.md)

- [連続エクスポートを構成する方法](../azure-monitor/app/export-telemetry.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: Azure Monitor 内で、組織のコンプライアンス規則に従って Log Analytics ワークスペースの保持期間を設定します。 長期/アーカイブ ストレージには Azure Storage アカウントを使用します。

- [Log Analytics ワークスペースのログ保持パラメーターを設定する方法](../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Azure アクティビティ ログの診断設定と Azure Application Gateway の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。

Azure Application Gateways などのデプロイされたすべてのネットワーク リソースの正常性とメトリックを包括的に把握するために、Azure Monitor for Networks を使用します。 

必要に応じて、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Application Gateway の診断設定を有効にする方法](application-gateway-diagnostics.md)

- [Azure Monitor for Networks の使用方法](../azure-monitor/insights/network-insights-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 SKU を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。 WAF は、OWASP (Open Web Application Security Project) コア ルール セット 3.1 (WAF_v2 のみ)、3.0、2.2.9 の規則に基づいています。 

Azure アクティビティ ログの診断設定と Azure WAF の診断設定を有効にし、Log Analytics ワークスペースにログを送信します。 Log Analytics でクエリを実行して、用語の検索、傾向の特定、パターンの分析を行い、収集されたデータに基づいて他の多くの分析情報を提供します。  Log Analytics ワークスペース クエリに基づいてアラートを作成できます。

Azure Application Gateways などのデプロイされたすべてのネットワーク リソースの正常性とメトリックを包括的に把握するために、Azure Monitor for Networks を使用します。 Azure Monitor for Networks コンソール内で Azure Application Gateway のアラートを表示し、作成できます。

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure アクティビティ ログの診断設定を有効にする方法](../azure-monitor/essentials/activity-log.md)

- [Azure Application Gateway の診断設定を有効にする方法](application-gateway-diagnostics.md)

- [Azure Monitor for Networks の使用方法](../azure-monitor/insights/network-insights-overview.md)

- [Azure 内でアラートを作成する方法](../azure-monitor/alerts/tutorial-response.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: 着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。

Azure Application Gateway デプロイの診断設定を構成します。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure WAF の診断設定を構成する方法](application-gateway-diagnostics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**: Azure Active Directory (Azure AD) には、明示的に割り当てる必要があるためにクエリ可能である組み込みロールがあります。 Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出します。

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole?preserve-view=true&view=azureadps-2.0)

- [PowerShell を使用して Azure AD でディレクトリ ロールのメンバーを取得する方法](/powershell/module/azuread/get-azureaddirectoryrolemember?preserve-view=true&view=azureadps-2.0)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Azure Application Gateway へのコントロール プレーン アクセスは、Azure Active Directory (Azure AD) を介して制御されます。 Azure AD には既定のパスワードという概念がありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**: 専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID とアクセス管理を使用して、管理者アカウントの数を監視します。

さらに、専用管理者アカウントを追跡できるように、Azure Security Center または組み込みの Azure ポリシーの次のような推奨事項を使用することもできます。

- 複数の所有者がサブスクリプションに割り当てられている必要がある
- 所有者としてのアクセス許可を持つ非推奨のアカウントをサブスクリプションから削除する必要がある
- 所有者アクセス許可を持つ外部アカウントをサブスクリプションから削除する必要がある

詳細については、以下の資料を参照してください。

- [Azure Security Center を使用して ID およびアクセスを監視する方法 (プレビュー)](../security-center/security-center-identity-access.md)

- [Azure Policy を使用する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: Azure アプリの登録 (サービス プリンシパル) を使用して、API 呼び出しを介して Azure Application Gateways と対話するために使用できるトークンを取得します。

- [Azure REST API を呼び出す方法](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

- [クライアント アプリケーション (サービス プリンシパル) を Azure Active Directory (Azure AD) に登録する方法](/rest/api/azure/#register-your-client-application-with-azure-ad)

- [Azure Recovery Services API に関する情報](/rest/api/recoveryservices/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する

**ガイダンス**: 多要素認証が構成された PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、そのリソースを構成します。

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

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: 条件付きアクセスのネームド ロケーションを使用して、IP アドレス範囲または国/地域の特定の論理グループからのアクセスのみを許可します。

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

- [Azure AD のレポートの概要](../active-directory/reports-monitoring/index.yml)

- [Azure ID アクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) のサインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペースで必要なアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**:ユーザー ID に関連する検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) の Identity Protection 機能とリスク検出機能を使用します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**:タグを使用して、機密情報を格納または処理する Azure リソースの追跡に役立てます。 

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure Application Gateway での NSG の使用に関する制限と要件について](configuration-overview.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 送信トラフィックを信頼できる場所のみに制限して、データ窃盗の脅威を軽減できます。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

- [Azure Application Gateway での NSG の使用に関する制限と要件について](configuration-overview.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: TLS により、Azure Application Gateway のエンドツーエンドの暗号化を構成します。

- [Azure Application Gateway を使用してエンド ツー エンド TLS を構成する方法](end-to-end-ssl-portal.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4.6:ロールベースのアクセス制御を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Azure Application Gateway コントロール プレーン (Azure portal) へのアクセスを制御します。

- [Azure RBAC を構成する方法](../role-based-access-control/role-assignments-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Monitor と Azure アクティビティ ログを使用して、運用環境の Azure Application Gateway インスタンスおよびその他の重要なリソースまたは関連リソースへの変更が発生したときに、アラートを作成します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../azure-monitor/alerts/alerts-activity-log.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**:現在使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、構成関連の脆弱性を軽減します。

- [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](../security-center/features-paas.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: まだ使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、構成関連の脆弱性を軽減します。

- [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](../security-center/features-paas.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: まだ使用できません。Azure Application Gateway では、Azure Security Center の脆弱性評価はまだ使用できません。

Microsoft でスキャンおよび修正される、基になるプラットフォーム。 Azure Application Gateway で利用可能なセキュリティ コントロールを確認して、構成関連の脆弱性を軽減します。

- [Azure PaaS サービスの機能の対応状況 (脆弱性評価を含む)](../security-center/features-paas.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:Azure Resource Graph を使用して、サブスクリプション内のすべてのリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリまたは検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Resource Graph で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription?preserve-view=true&view=azps-4.8.0)

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

さらに、次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

- 許可されないリソースの種類
- 許可されるリソースの種類

詳細については、以下の資料を参照してください。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: Azure Policy を使用して、サブスクリプションで作成できるリソースの種類に制限を設けます。 

Azure Resource Graph を使用して、サブスクリプション内のリソースのクエリまたは検出を行います。  環境に存在するすべての Azure リソースが承認されていることを確認します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md) 

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類
- 許可されるリソースの種類

詳細については、以下の資料を参照してください。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](../governance/policy/samples/built-in-policies.md#general)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: Azure 条件付きアクセスを使用して Azure Resource Manager とやりとりするユーザーの機能を制限するには、"Microsoft Azure 管理" アプリに対して [アクセスのブロック] を構成します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 すべての Virtual Network Azure Application Gateway サブネット デプロイに、アプリケーションの信頼されたポートとソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループ (NSG) があることを確認します。 ネットワーク セキュリティ グループは Azure Application Gateway でサポートされていますが、NSG と Azure Application Gateway が想定どおりに機能するためには、従う必要があるいくつかの制限と要件があります。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

- [Azure Application Gateway での NSG の使用に関する制限と要件について](configuration-overview.md)

- [セキュリティ構成を使用して NSG を作成する方法](../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Application Gateway のデプロイに関連するネットワーク設定の標準的なセキュリティ構成を定義して、実装します。 Azure Application Gateways、Azure Virtual Networks、ネットワーク セキュリティ グループのネットワーク構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.Network" 名前空間で Azure Policy エイリアスを使用します。 組み込みのポリシー定義を使用することもできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias?preserve-view=true&view=azps-4.8.0)

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**:カスタム Azure ポリシー定義を使用する場合は、Azure DevOps または Azure Repos を使ってコードを安全に格納して管理します。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: 組み込みの Azure Policy 定義と "Microsoft.Network" 名前空間の Azure Policy エイリアスを使用して、システム構成のアラート、監査、適用を行うカスタム ポリシーを作成します。 ご利用の Azure リソースの構成を自動的に適用するには、Azure Policy の [audit]、[deny]、[deploy if not exist] を使用します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure Application Gateway に提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure Key Vault を使用して、証明書を安全に格納します。 Azure Key Vault はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway v2 SKU に制限されます。

- [Azure PowerShell を使用して、Key Vault 証明書によって SSL 終端を構成する方法](configure-keyvault-ps.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure Application Gateway に提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

Azure Key Vault を使用して、証明書を安全に格納します。 Azure Key Vault はプラットフォームマネージド シークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway v2 SKU に制限されます。

- [Azure PowerShell を使用して、Key Vault 証明書によって SSL 終端を構成する方法](configure-keyvault-ps.md)

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

**ガイダンス**:着信トラフィックをさらに検査するために、Azure Web アプリケーション ファイアウォール (WAF) v2 を重要な Web アプリケーションの前にデプロイします。 Web アプリケーション ファイアウォール (WAF) は、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護するサービス (Azure Application Gateway の機能) です。 Azure WAF を使用すると、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、および DDoS 攻撃などの攻撃をブロックするために、受信 Web トラフィックを検査することで、Azure App Service Web アプリをセキュリティ保護できます。

Azure Application Gateway デプロイの診断設定を構成します。 診断設定を使用して、選択した送信先 (ストレージ アカウント、Event Hubs、Logs Analytics) へのリソースのプラットフォーム ログおよびメトリックのストリーミング エクスポートを構成します。

- [Azure WAF をデプロイする方法](../web-application-firewall/ag/create-waf-policy-ag.md)

- [Azure WAF の診断設定を構成する方法](application-gateway-diagnostics.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure Web アプリケーション ファイアウォール (WAF) を使用する場合は、WAF ポリシーを構成できます。 WAF ポリシーは、2 種類のセキュリティ規則で構成できます。お客様が作成するカスタム規則と、Azure によって管理される事前構成済みの規則のセットのコレクションであるマネージド規則セットです。 Azure で管理される規則セットでは、一般的なセキュリティ脅威のセットに対する保護をデプロイする簡単な方法が提供されます。 そのような規則セットが Azure によって管理されるので、新しい攻撃シグネチャから保護するために、必要に応じて規則が更新されます。

- [Azure で管理されている WAF 規則セットについて](../web-application-firewall/ag/ag-overview.md#waf-policy-and-rules)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Application Gateway では、お客様のデータが格納されません。 ただし、カスタム Azure ポリシー定義を使用している場合は、Azure DevOps または Azure Repos を使用して、コードを安全に格納して管理します。

Azure DevOps Services では、Azure Storage 機能の多くを活用して、ハードウェア障害、サービスの中断、またはリージョンの災害が発生した場合にデータの可用性を確保しています。 さらに、Azure DevOps チームは、データを偶発的または悪意のある削除から保護する手順に従っています。

- [Azure DevOps のデータ可用性の概要](/azure/devops/organizations/security/data-protection?preserve-view=true&view=azure-devops#data-availability)

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow?preserve-view=true&view=azure-devops)

- [Azure Repos のドキュメント](/azure/devops/repos/?preserve-view=true&view=azure-devops)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Key Vault 内のカスタマー マネージド証明書をバックアップします。

- [Azure で Key Vault の証明書をバックアップする方法](/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: バックアップされたカスタマー マネージド証明書の復元をテストします。

- [Key Vault の証明書を復元する方法](/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Key Vault に対して論理的な削除が有効になっていることを確認します。 論理的な削除では、削除されたキーコンテナーと、キー、シークレット、証明書などのコンテナー オブジェクトを復元できます。

- [Azure Key Vault の論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)

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

**ガイダンス**:定期的にシステムのインシデント対応機能をテストする演習を実施します。 弱点やギャップを特定し、必要に応じて計画を見直します。

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

- 「[Azure セキュリティ ベンチマーク V2 の概要](../security/benchmarks/overview.md)」を参照してください。
- [Azure セキュリティ ベースライン](../security/benchmarks/security-baselines-overview.md)の詳細について学習する
